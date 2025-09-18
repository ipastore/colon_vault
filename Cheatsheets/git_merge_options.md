### Fast-forward merge (default when possible)
```bash
git merge feature/mask
````

- Moves the branch pointer forward if no divergence.
- Keeps linear history.
- No merge commit is created.
---
### Force fast-forward only

```bash
git merge --ff-only feature/mask
```

- Ensures merge **only** if fast-forward is possible.
- Fails otherwise (safe in CI or scripts).
---
### No fast-forward (always create merge commit)
```bash
git merge --no-ff feature/mask
```

- Always creates a merge commit, even if fast-forward is possible.
- Preserves branch structure (visible "bubble" in history).
- Good for team workflows and PR traceability.
---
### Rebase then fast-forward

```bash
git switch feature/mask
git rebase main
git switch main
git merge --ff-only feature/mask
```

- Replays feature branch commits on top of `main`.
- Produces a **linear history** with no merge commits.
- Preferred for solo or clean histories.
---
### Squash merge (one commit only)

```bash
git merge --squash feature/mask
git commit -m "Add feature/mask as one commit"
```

- Combines all commits from branch into a single commit.
- Useful if branch history is messy.
- Original branch commits are not preserved.
---
### Interactive rebase (cleanup before merge)

```bash
git switch feature/mask
git rebase -i main
```

- Lets you **squash, reorder, edit, or drop** commits.
- Great for cleaning history before merging.
---
### Cherry-pick (selective merge)
```bash
git cherry-pick <commit-sha>
```

- Apply a specific commit (or range) from one branch to another.
- Useful when you don’t want the entire branch merged.
---
### Octopus merge (multiple branches at once)

```bash
git merge branch1 branch2 branch3
```

- Merges multiple branches in a single commit.
- Works well only if there are no conflicts.
- Mostly used in automated workflows

---
## Practical tips

- **Solo work:** `rebase + ff-only` → clean, linear history.
- **Team PRs:** `--no-ff` → preserves context of branches.
- **Messy branches:** `squash merge` → neat single commit.
- **Hotfixes:** `cherry-pick` → apply only the needed commit.