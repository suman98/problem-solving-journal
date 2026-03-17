# Adding file to git ignore without need to change the git ignore file

```bash
echo ".playground/" >> .git/info/exclude
```

Where `.playground` is the folder name

# If already tracked
```bash
git rm -r --cached .playground
```