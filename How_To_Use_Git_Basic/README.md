# How To Use Git (Basic Tutorial)

## Overview
This document contains some common Git commands I often use when working with GitHub, GitLab, etc.
It's a quick reference in case I forget any steps.

## Initial Project Setup and Commit

When you just created a project and want to push it to GitHub, GitLab, or other Git platforms:
```bash
git init                                # Initialize Git repository

git config --global user.email "<your-email>"
git config --global user.name "<your-name>"

# Or for only this project:
# git config user.email "<your-email>"
# git config user.name "<your-name>"

# You can check your current user name and email in Git Project by:
# git config user.email
# git config user.name

git add .                               # Add all files and folders
git commit -m "Initial commit"          # Add your commit message (-m "Line 2" -m "Line 3" for multiple lines)
git remote add origin <your-git-link>   # Link your remote repository (replace with your GitHub/GitLab URL)
git branch -M main                      # Rename the default branch to main
git push -u origin main                 # Push to remote and set upstream
```

After the first setup, you only need to add changes, commit, and push:
```bash
git add .                               # Add all changed files and folders
# Or only specific files:
# git add file1.cpp file2.cpp

git commit -m "Your commit message"     # Add your commit message (-m "Line 2" -m "Line 3" for multiple lines)
git push                                # Push to the same branch as upstream
```

When you create a new branch (we will talk more about branches later), you can do:
```bash
git add .                               # Add all changed files
# Or only specific files:
# git add file1.cpp file2.cpp

git commit -m "Your commit message"     # Add your commit message (-m "Line 2" -m "Line 3" for multiple lines)
git push -u origin <your-branch-name>   # Push and set upstream for this new branch
```

Finally If you want to clone project in GitHub:
```bash
git clone <your-git-link>
```

## Setup Project with Submodule

When working on a project, you often need to use external libraries from GitHub (such as lvgl, live555, etc.). These libraries can be quite large, and committing them directly into your repository is usually not practical because it can make the repo heavy and slow to clone.

A better solution is to link your project to the library’s repository using Git submodules. Submodules allow you to include another repository as a dependency, pointing to a specific commit, branch, or tag. This way, you don’t have to copy all the library’s code into your project, but you still have access to it.

For example, in the LVGL_Linux_Example project (https://github.com/Finn-NAT/LVGL_Linux_Example.git), Git submodules are used to include the lvgl library. This keeps the repository lightweight, ensures everyone is using the same version of the library, and makes it easier to update or switch library versions when needed. Submodules are a simple and efficient way to manage large external dependencies in Git.

After you initialize your Git repository and configure your user name and email (as shown in **Initial Project Setup and Commit**), follow these steps:
```bash
# If you've already downloaded and are using your "lib", rename it to "lib.local". In this example, it's lvgl.local and lv_drivers.local. Add them to .gitignore to prevent them from being tracked by Git.
git add .gitignore

# Add the library as a submodule using the Git repo link
git submodule add <lib-git-link> folder1/folder2/.../lib
# Example: git submodule add https://github.com/lvgl/lvgl.git Linux_LVGL_Examples/lvgl
# Example: git submodule add https://github.com/lvgl/lv_drivers.git Linux_LVGL_Examples/lv_drivers

# This will automatically update .gitmodules, which you should also add to your repository
git add .gitmodules
```
Once you've added everything, commit and push your changes as usual.

If you need to pin a specific version of a submodule (whether it’s a branch, tag, or commit), follow these steps after running *git submodule add*:
```bash
cd folder1/folder2/.../lib
# Example: cd Linux_LVGL_Examples/lvgl

# Checkout the specific branch, tag, or commit hash
git checkout <commit-hash>  # Replace <commit-hash> with the actual commit hash you want
# Example: git checkout v8.3.11
```

And if you want to clone project and submodule in GitHub, after clone like normal:
```bash
cd project
git submodule update --init --recursive
```

## Basic Use of .gitignore
```bash
folder1/    # Ignore folder at the same level as .gitignore file

folder1/folder2/.../foldern # Ignore folder at a specific path

folder1/folder2/.../foldern/file.txt # Ignore file at a specific path

**/folder1/  # Ignore any folder named "folder1" anywhere in the project

**/*.local/  # Ignore any folder with ".local" extension anywhere in the project

folder1/
!folder1/file.txt  # Ignore everything in folder1 except file.txt

```

When you first ignore a folder or file, add it to .gitignore like this:
```bash
folder1/folder2/.../foldern/file.txt # Ignore Folder or File
```
If you later want to track that file or folder again, remove it from .gitignore and use:
```bash
git add -f folder1/folder2/.../foldern/file.txt # Force add Folder or File
```

## Basic Use of Git Branch

Working with branches in Git allows you to develop features, fix bugs, or experiment in isolated environments. Here are the most common branch commands:

```bash
# List all local branches
git branch

# List all remote branches
git branch -r

# Create a new branch (does not switch to it)
git branch <branch-name>

# Create and switch to a new branch
git checkout -b <branch-name>
# or (newer versions of Git)
git switch -c <branch-name>

# Rename the current branch
git branch -m <new-branch-name>

# Rename a different branch (not currently checked out)
git branch -m <old-branch-name> <new-branch-name>

# If you already pushed the old branch to remote, delete the old branch and push the new one:
git push origin :<old-branch-name> <new-branch-name>
git push -u origin <new-branch-name>

# Switch to an existing branch
git checkout <branch-name>
# or
git switch <branch-name>

# Merge another branch into your current branch
git merge <branch-name>

# Delete a local branch (must not be checked out and if merged)
git branch -d <branch-name>
# Force delete (if not merged)
git branch -D <branch-name>

# If you want to delete brand have already pushed in remote
git push origin --delete <branch-name>

# Push a new branch to remote and set upstream
git push -u origin <branch-name>

# Delete a remote branch
git push origin --delete <branch-name>
```

**Typical workflow:**
1. Create and switch to a new branch for your feature or fix.
2. Make changes, commit them.
3. Push the branch to remote if you want to share or back it up.
4. When done, switch to your main branch and merge your feature branch.
5. Delete the feature branch if no longer needed.
