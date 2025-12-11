# How To Use Git (Advance Tutorial)

## Overview
In this tutorial, we’ll address common issues and provide practical solutions that I encountered when working with Git. This guide isn't focused on advanced Git concepts, but instead on troubleshooting and resolving real-world problems.

## Fix A Wrong Git Submodule Addition

When working with Git, it's easy to make mistakes, especially when adding submodules. If you've added the wrong submodule using a command like this:
```bash
git submodule add <lib-git-link> folder1/folder2/.../lib
```

It could be because either the **< lib-git-link >** is incorrect or the path **folder1/folder2/.../lib** is wrong. Git will register this wrong submodule in three places:
- The .gitmodules file
- The local Git configuration (.git/config)
- Git's module metadata (stored in .git/modules/...)

Here’s how to remove that wrong submodule step-by-step.

**Step 1**, you need to do:
```bash
git rm -r --cached folder1/folder2/.../lib    # Remove from Git index
```

Step 2, open .gitmodules and delete block:
```bash
[submodule "folder1/folder2/.../lib"]
    path = folder1/folder2/.../lib
    url = <lib-git-link>
```

Step 3, delete data module:
```bash
rm -rf .git/modules/folder1/folder2/.../lib
```

Step 4 Delete folder submodule in working tree
```bash
rm -rf folder1/folder2/.../lib
```

And if you have already commited the wronng submodule, you need commit again (and push in GitHub like normal)
```bash
git add .gitmodules
git add .  # optional
git commit -m "Remove wrong submodule"
```

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
