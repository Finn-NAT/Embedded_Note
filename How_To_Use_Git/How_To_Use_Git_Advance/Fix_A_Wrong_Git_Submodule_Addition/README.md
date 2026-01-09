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

