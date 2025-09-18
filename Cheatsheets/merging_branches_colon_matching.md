

# Git: Feature → Main (my daily flow)

## Variant A — Linear history (rebase + fast-forward)
```bash
# Use when you want a clean, straight history.

# 0) Make sure everything is saved and staged/committed.
git status

# 1) Update main
git fetch origin
git switch main
git pull --ff-only        # bring origin/main locally without merge commits

# 2) Rebase the feature on top of updated main
git switch feature/mask
git rebase main           # replay feature commits after main
# If conflicts: fix files → git add -A → git rebase --continue
# To cancel: git rebase --abort

# 3) Fast-forward main to include the feature
git switch main
git merge --ff-only feature/mask

# 4) Push the result and clean up
git push origin main
git branch -d feature/mask
git push origin --delete feature/mask    # remove remote branch
```

**Memory hooks:**
- _rebase_ = “make it possible to fast-forward.”
- _ff-only_ = “fast-forward or fail; no merge bubbles.”
---

## Variant B — Keep a merge bubble (no-ff)

```bash
# Use when you want the branch boundary visible (PR-style traceability).

# 0) Status

git status

# 1) Update main

git fetch origin  
git switch main  
git pull # ok to create a merge if needed

# 2) Merge feature with a merge commit (even if ff possible)

git merge --no-ff feature/mask # editor opens → save message (:wq in Vim; Ctrl+O, Enter, Ctrl+X in Nano)

# 3) Push and clean up

git push origin main  
git branch -d feature/mask  
git push origin --delete feature/mask
```


**Memory hooks:**  
- *no-ff* = “always create a merge commit (keep bubble).”

---

## Quick fixes for your original list (typos & intent)
- `git status` ✅  
- `git switch main` ✅  
- **Rebase:** `git switch feature/mask && git rebase main` (not `git rebase? feature/mask`)  
- **Merge (pick one style):**  
  - Linear: `git switch main && git merge --ff-only feature/mask`  
  - Bubble: `git switch main && git merge --no-ff feature/mask`  
- **Delete local branch:** `git branch -d feature/mask` (typo fixed)  
- **Delete remote branch:** `git push origin --delete feature/lightglue_mask` (typo fixed)  
- **Yes, push the merge:** after merging into `main`, you **must** `git push origin main`.

---

## Safety notes
- If you **already pushed** `feature/mask` and you **rebased** it, you’d need:
  - `git push --force-with-lease origin feature/mask` (only if you intend to update the remote feature branch before merging).  
  - If you merge immediately and delete the remote feature, you can skip forcing the feature branch.
- To check divergence quickly:  
  `git log --oneline --graph main..feature/mask` and `git log --oneline --graph feature/mask..main`.

