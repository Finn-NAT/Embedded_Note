# How To Use Git (Basic)

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
