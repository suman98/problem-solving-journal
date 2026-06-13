If you want to use the code command from other IDEs like Cursor or create a custom script for VS Code,

```bash
#!/bin/bash
# save as: vscode.sh

# Path to VS Code on macOS
VSCODE_PATH="/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"

# Check if VS Code exists
if [ ! -f "$VSCODE_PATH" ]; then
    echo "Error: VS Code not found at $VSCODE_PATH"
    echo "Please update VSCODE_PATH in this script to match your installation"
    exit 1
fi

# Open VS Code with all arguments passed to the script
"$VSCODE_PATH" "$@"
```

Then in `~.zshrc`


```bash

function vscode() {
    "{pathToTheScript}" "$@"
}

```