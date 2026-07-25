# Create a file name say `change-track.sh`

```bash
#!/usr/bin/env bash

set -euo pipefail

usage() {
    cat <<EOF
Usage:
  $(basename "$0") [repository-path] [hours] [--tree]
  $(basename "$0") [hours] [--tree]

Examples:
  $(basename "$0")
  $(basename "$0") 2
  $(basename "$0") --tree
  $(basename "$0") 2 --tree
  $(basename "$0") /path/to/repository
  $(basename "$0") /path/to/repository --tree
  $(basename "$0") /path/to/repository 2 --tree
EOF
}

repo_path='.'
hours='1'
tree_mode=false
repo_set=false
hours_set=false

for argument in "$@"; do
    case "$argument" in
        --tree)
            tree_mode=true
            ;;
        -h|--help)
            usage
            exit 0
            ;;
        --*)
            printf 'Error: unknown option: %s\n\n' "$argument" >&2
            usage >&2
            exit 1
            ;;
        *)
            case "$argument" in
                ''|*[!0-9]*)
                    if [ "$repo_set" = true ]; then
                        printf 'Error: multiple repository paths were provided.\n\n' >&2
                        usage >&2
                        exit 1
                    fi

                    repo_path=$argument
                    repo_set=true
                    ;;
                *)
                    if [ "$hours_set" = true ]; then
                        printf 'Error: multiple hour values were provided.\n\n' >&2
                        usage >&2
                        exit 1
                    fi

                    hours=$argument
                    hours_set=true
                    ;;
            esac
            ;;
    esac
done

command -v git >/dev/null 2>&1 || {
    printf 'Error: Git is not installed or is not in PATH.\n' >&2
    exit 1
}

[ -d "$repo_path" ] || {
    printf 'Error: directory does not exist: %s\n' "$repo_path" >&2
    exit 1
}

repo_root=$(git -C "$repo_path" rev-parse --show-toplevel 2>/dev/null) || {
    printf 'Error: not a Git repository: %s\n' "$repo_path" >&2
    exit 1
}

now=$(date +%s)
cutoff_epoch=$((now - hours * 3600))

tmp_file=$(mktemp "${TMPDIR:-/tmp}/change-track.XXXXXX")
output_file=$(mktemp "${TMPDIR:-/tmp}/change-track-output.XXXXXX")
tree_file=$(mktemp "${TMPDIR:-/tmp}/change-track-tree.XXXXXX")

trap 'rm -f "$tmp_file" "$output_file" "$tree_file"' EXIT HUP INT TERM

if [ -t 1 ] && [ -z "${NO_COLOR:-}" ]; then
    RESET=$(printf '\033[0m')
    BOLD=$(printf '\033[1m')
    DIM=$(printf '\033[2m')
    CYAN=$(printf '\033[36m')
    GREEN=$(printf '\033[32m')
    BLUE=$(printf '\033[34m')
    YELLOW=$(printf '\033[33m')
else
    RESET=''
    BOLD=''
    DIM=''
    CYAN=''
    GREEN=''
    BLUE=''
    YELLOW=''
fi

file_mtime() {
    local file=$1

    # GNU/Linux
    if stat -c %Y -- "$file" >/dev/null 2>&1; then
        stat -c %Y -- "$file"
        return
    fi

    # macOS / BSD
    if stat -f %m -- "$file" >/dev/null 2>&1; then
        stat -f %m -- "$file"
        return
    fi

    # Fallback
    if command -v perl >/dev/null 2>&1; then
        perl -e 'print((stat($ARGV[0]))[9])' "$file"
        return
    fi

    return 1
}

relative_time() {
    local timestamp=$1
    local age value

    age=$((now - timestamp))
    [ "$age" -lt 0 ] && age=0

    if [ "$age" -lt 60 ]; then
        printf 'just now'
    elif [ "$age" -lt 3600 ]; then
        value=$((age / 60))

        if [ "$value" -eq 1 ]; then
            printf '1 minute ago'
        else
            printf '%s minutes ago' "$value"
        fi
    elif [ "$age" -lt 86400 ]; then
        value=$((age / 3600))

        if [ "$value" -eq 1 ]; then
            printf '1 hour ago'
        else
            printf '%s hours ago' "$value"
        fi
    elif [ "$age" -lt 604800 ]; then
        value=$((age / 86400))

        if [ "$value" -eq 1 ]; then
            printf '1 day ago'
        else
            printf '%s days ago' "$value"
        fi
    else
        value=$((age / 604800))

        if [ "$value" -eq 1 ]; then
            printf '1 week ago'
        else
            printf '%s weeks ago' "$value"
        fi
    fi
}

file_url() {
    local absolute_path=$1

    if command -v python3 >/dev/null 2>&1; then
        python3 - "$absolute_path" <<'PY'
import pathlib
import sys

print(pathlib.Path(sys.argv[1]).resolve().as_uri())
PY
    else
        printf 'file://%s\n' "$absolute_path"
    fi
}

make_link() {
    local label=$1
    local absolute_path=$2
    local url

    if [ -t 1 ]; then
        url=$(file_url "$absolute_path")

        # OSC 8 terminal hyperlink; only the filename is clickable.
        printf '\033]8;;%s\033\\%s\033]8;;\033\\' "$url" "$label"
    else
        printf '%s' "$label"
    fi
}

# Recently committed files during the requested time range.
git -C "$repo_root" log \
    --since="@$cutoff_epoch" \
    --name-only \
    --diff-filter=ACMRTD \
    --format= \
    -- >>"$tmp_file"

# Current unstaged changes.
git -C "$repo_root" diff \
    --name-only \
    --diff-filter=ACMRTD \
    -- >>"$tmp_file"

# Current staged changes.
git -C "$repo_root" diff \
    --cached \
    --name-only \
    --diff-filter=ACMRTD \
    -- >>"$tmp_file"

# Current untracked files.
git -C "$repo_root" ls-files \
    --others \
    --exclude-standard \
    -- >>"$tmp_file"

# Create rows:
# timestamp<TAB>relative-path
#
# Sort newest first. Deleted files use timestamp 0 and appear last.
: >"$output_file"

while IFS= read -r file; do
    [ -n "$file" ] || continue

    absolute_path="$repo_root/$file"

    if [ -e "$absolute_path" ]; then
        timestamp=$(file_mtime "$absolute_path" 2>/dev/null || printf '%s' "$now")
    else
        timestamp=0
    fi

    printf '%s\t%s\n' "$timestamp" "$file"
done < <(LC_ALL=C sort -u "$tmp_file" | awk 'NF') |
    LC_ALL=C sort -nr -k1,1 -k2,2 >"$output_file"

count=$(awk 'NF { count++ } END { print count + 0 }' "$output_file")

printf '\n%s%sChanged files%s %s(last %s hour(s), newest first)%s\n' \
    "$BOLD" "$CYAN" "$RESET" "$DIM" "$hours" "$RESET"

printf '%sRepository: %s%s\n\n' "$DIM" "$repo_root" "$RESET"

if [ "$count" -eq 0 ]; then
    printf '  %sNo changed, created, or deleted files found.%s\n\n' \
        "$YELLOW" "$RESET"
    exit 0
fi

render_list() {
    local timestamp file absolute_path modified link

    while IFS="$(printf '\t')" read -r timestamp file; do
        [ -n "$file" ] || continue

        absolute_path="$repo_root/$file"

        if [ -e "$absolute_path" ]; then
            modified=$(relative_time "$timestamp")
            link=$(make_link "$file" "$absolute_path")

            printf '  %s•%s %s%s%s %s[%s]%s\n' \
                "$GREEN" "$RESET" \
                "$BOLD" "$link" "$RESET" \
                "$DIM" "$modified" "$RESET"
        else
            printf '  %s•%s %s%s%s %s[deleted or moved]%s\n' \
                "$YELLOW" "$RESET" \
                "$BOLD" "$file" "$RESET" \
                "$DIM" "$RESET"
        fi
    done <"$output_file"
}

render_tree() {
    command -v python3 >/dev/null 2>&1 || {
        printf 'Error: --tree requires Python 3.\n' >&2
        exit 1
    }

    # Do not pipe Python directly to the shell loop.
    # Writing its output to a file prevents BrokenPipeError.
    python3 - "$output_file" >"$tree_file" <<'PY'
import pathlib
import sys

root = {}

for line in pathlib.Path(sys.argv[1]).read_text().splitlines():
    if not line.strip():
        continue

    try:
        timestamp, path = line.split("\t", 1)
        timestamp = int(timestamp)
    except ValueError:
        continue

    parts = pathlib.PurePosixPath(path).parts
    if not parts:
        continue

    node = root

    for part in parts[:-1]:
        node = node.setdefault(part, {})

    node.setdefault("__files__", []).append((timestamp, parts[-1], path))

def render(node, prefix=""):
    folders = sorted(name for name in node if name != "__files__")

    # Newest first inside each directory.
    files = sorted(
        node.get("__files__", []),
        key=lambda item: (-item[0], item[2]),
    )

    entries = [("folder", name) for name in folders]
    entries.extend(("file", file_entry) for file_entry in files)

    for index, (kind, value) in enumerate(entries):
        is_last = index == len(entries) - 1
        branch = "└── " if is_last else "├── "
        child_prefix = prefix + ("    " if is_last else "│   ")

        if kind == "folder":
            print(f"D\t{prefix}{branch}{value}/")
            render(node[value], child_prefix)
        else:
            timestamp, filename, full_path = value
            print(f"F\t{prefix}{branch}{filename}\t{timestamp}\t{full_path}")

render(root)
PY

    local entry_type display timestamp file absolute_path
    local tree_prefix filename modified link

    while IFS="$(printf '\t')" read -r entry_type display timestamp file; do
        if [ "$entry_type" = 'D' ]; then
            printf '  %s%s%s\n' "$BLUE" "$display" "$RESET"
            continue
        fi

        [ "$entry_type" = 'F' ] || continue
        [ -n "$file" ] || continue

        absolute_path="$repo_root/$file"

        # For example: "│   ├── app.ts"
        # Keep tree symbols plain; make only the filename clickable.
        tree_prefix=${display%── *}
        filename=${display##*── }

        if [ -e "$absolute_path" ]; then
            modified=$(relative_time "$timestamp")
            link=$(make_link "$filename" "$absolute_path")

            printf '  %s%s%s%s %s[%s]%s\n' \
                "$GREEN" "$tree_prefix" "$link" "$RESET" \
                "$DIM" "$modified" "$RESET"
        else
            printf '  %s%s%s %s[deleted or moved]%s\n' \
                "$YELLOW" "$display" "$RESET" \
                "$DIM" "$RESET"
        fi
    done <"$tree_file"
}

if [ "$tree_mode" = true ]; then
    render_tree
else
    render_list
fi

printf '\n%s%s file(s) found%s\n\n' "$DIM" "$count" "$RESET"

```

# register in .zshrc

```bash
changetrack() {
  local hours="${1:-1}"
  local repo_path

  repo_path="$(git rev-parse --show-toplevel 2>/dev/null)" || {
    echo "changetrack: not inside a Git repository" >&2
    return 1
  }

  {path}/change-track.sh \
    "$repo_path" \
    "$hours"
}

```