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


