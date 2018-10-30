### Create a new branch on the local machine and switch to it

```$ git checkout -b [name_of_your_new_branch]```

### Show all branches

```$ git branch```

### Merging

Revert own change in conflict state:

```
git checkout --theirs [filename]
git add [filename]
```

Show all files which are changed in current branch, compared to master.

```
git diff --name-status master
```
