## Fix A Wrong Git Commit or Git Author
*Just Fix a wrong git commit or git author in lastest commit*

Shows the most recent commit (-1) and prints only the author’s name and email in the format:
```bash
git log -1 --format="%an <%ae>"
```
or shows the most recent commit (-1)
```bash
git log -1 
```

- Fix Wrong Git Author
```bash
git commit --amend --author="New_User_Name <New_Mail>" --no-edit
# Set Git Config
git config user.email "<your-email>"
git config user.name "<your-name>"
```

- Fix Wrong Git Commit
```bash
git commit --amend
# After we've done, Push Esc. type :wq and Enter (Vim)
# or Ctrl + O to Save, Push Enter and Ctrl + X to exit (Nano)
```

-> And force push
```bash
git push --force origin <branch_you_want_to_push>
# git push --force origin main
```


*If you want to Fix a wrong git commit or git author in previous commit*

Below are the steps to fix a wrong git commit or author in a previous commit (not just the latest one):

1. **Start an interactive rebase:**
```bash
git rebase -i HEAD~N
# git rebase -i --root              #If you want to fix in first commit
```
> Replace `N` with the number of commits you want to go back. This command opens an editor to let you choose which commits to edit.

2. **Fix Wrong Git Author:**
```bash
git commit --amend --author="New_User_Name <New_Mail>" --no-edit
# Set Git Config
git config user.email "<your-email>"
git config user.name "<your-name>"
```
> After marking a commit for edit, use the above commands to change the author information. Replace with your new name and email.

3. **Fix Wrong Git Commit Message or Content:**
```bash
git commit --amend
# After we've done, Push Esc. type :wq and Enter (Vim)
# or Ctrl + O to Save, Push Enter and Ctrl + X to exit (Nano)
```
> This lets you change the commit message or add/remove files from the commit. Save and exit the editor when done.

4. **Continue the rebase process:**
```bash
git rebase --continue
```
> Continue the rebase process after amending the commit. Repeat amend and continue for each commit you want to fix.

5. **Force push the changes to the remote repository:**
```bash
git push --force origin <branch_you_want_to_push>
# git push --force origin main
# git push --force
```
