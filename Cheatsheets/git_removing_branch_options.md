--## Local branches

```bash
# 1) Switch off the branch you want to delete
git switch main   # or: git checkout main

# 2) Delete it (safe: only if fully merged into current branch)
git branch -d feature/my-branch

# 3) Force delete (even if not merged) – be sure you want this
git branch -D feature/my-branch
```

**Memory hooks**
- `-d` = **d**elete (refuses if unmerged)
- `-D` = **D**anger (force)

## Remote branches (on origin)

```bash
# Delete the remote branch
git push origin --delete feature/my-branch   # modern & clear
# (old syntax) git push origin :feature/my-branch
```
## Clean up remote-tracking refs locally

After a remote branch is deleted, prune your local “origin/*” references:

```bash
git fetch --prune            # or: git fetch -p
# or specifically:
git remote prune origin
```

## Delete a remote-tracking ref explicitly (optional)

```bash
git branch -dr origin/feature/my-branch
```

- `-d` = delete, `-r` = remote-tracking ref

## Helpful checks

```bash
git branch            # local branches
git branch -a         # all (local + remote-tracking)
git branch -vv        # shows upstream tracking info
git branch --merged   # branches safely merged into current
git branch --no-merged
```

## Batch: delete all local branches already merged into `main` (use carefully)

```bash
git switch main
git branch --merged main \
 | egrep -v '^\*|main|develop' \
 | xargs -n 1 git branch -d

```

**Gotchas**
- You can’t delete the branch you’re currently on—switch first.
- Use `-D` only if you’re 100% sure those commits aren’t needed (or are on a PR elsewhere).