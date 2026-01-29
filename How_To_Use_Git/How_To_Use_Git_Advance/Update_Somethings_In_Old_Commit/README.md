## Update Something in an Old Commit

Suppose you want to update a previous commit that has already been pushed. Follow these steps:

**Step 1: Stash your current changes**
```sh
git stash
```

**Step 2: Find the commit to edit**
```sh
git log --oneline
```
Count how many commits ago the target commit is from HEAD (e.g., 3 commits).

**Step 3: Start an interactive rebase**
Replace `N` with the number of commits (e.g., 3):
```sh
git rebase -i HEAD~N
# git rebase -i --root              #If you want to fix in first commit
```

**Step 4: Mark the commit for editing**
In the editor, find the line with "Fix hanging modbus serial" and change `pick` to `edit`. Save and close the editor.

**If using Vim editor:**
You'll see a file like `git-rebase-todo` (shown at the bottom as something like `1,4 Top` and `32L, 1524B`).

To change `pick` to `edit`:
1. Press `i` (enter Insert mode)
2. Use arrow keys to navigate to the word `pick` and change it to `edit` (or just `e`)
3. Press `Esc` (exit Insert mode)
4. Type `:wq` and press `Enter` (save and quit)

Then continue with the rebase process in your terminal.

**To abort the rebase if needed:**
```sh
git rebase --abort
```

**Step 5: Apply your changes**
Retrieve your stashed changes:
```sh
git stash pop
```
Add your changes:
```sh
git add .
```
Amend the commit:
```sh
git commit --amend --no-edit
```
Continue the rebase:
```sh
git rebase --continue
```

**Step 6: Force push to update the remote**
```sh
git push --force
```


