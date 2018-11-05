### Push / Pull

Push a local branch to remote for pull request

```git push -u origin <branch>```

### Branching

Show all branches

```$ git branch```

Create a new branch on the local machine and switch to it

```$ git checkout -b <branch>```

### Merging

Get updated from master:

```
git checkout master
git checkout <branch>
git merge master
```

Revert own change in conflict state:

```
git checkout --theirs [filename]
git add [filename]
```

Show all files which are changed in current branch, compared to master.

```
git diff --name-status master
```
