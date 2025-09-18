### Meaning

**git**: distributed version control (Linus’ tongue-in-cheek name; think **keep history of everything**)

### Function

Track changes, branch/merge, sync with remotes, review history, and manage submodules.

---

### Main commands (daily trio)

**git status** → what changed  
**git add -A** → stage all changes  
**git commit -m "msg"** → save a snapshot  
**git push** → send to remote

- `status` = working tree + index summary
- `add -A` = stage **A**ll (new, modified, deleted)
- `commit -m` = message inline (**m**essage)
- `push` = upload to remote tracking branch

---

## Other Commands

```bash
# --- Repo lifecycle ---
git init                               # new repo in current folder
git clone <url> [dir]                  # copy remote locally
git remote -v                          # list remotes
git remote add origin <url>            # add default remote
git fetch --all --prune                # update refs, drop deleted

# --- Identity & config ---
git config --global user.name "You"
git config --global user.email "you@email"
git config --global pull.rebase false  # or true, your choice
git config --global init.defaultBranch main

# --- Branching & switching ---
git branch                             # list local branches
git branch -r                          # list remote branches
git switch -c feature/thing            # create + switch
git switch main                        # switch branches
# (older) git checkout -b <name>  / git checkout <name>

# --- Sync with remote ---
git fetch origin                       # download new commits/branches
git pull                               # fetch + merge into current
git pull --rebase                      # fetch + rebase current
git push -u origin <branch>            # first push (set upstream)

# --- Staging & committing ---
git add <path>                         # stage specific path
git add -A                             # stage all changes
git restore --staged <path>            # unstage
git commit -m "msg"                    # commit staged changes
git commit --amend                     # edit last commit (msg or staged)

# --- Merging & rebasing ---
git merge <branch>                     # merge named branch into current
git merge --no-ff <branch>             # keep merge commit bubble
git rebase main                        # replay current on top of main
git rebase -i HEAD~5                   # interactive rebase last 5

# --- Inspecting changes & history ---
git log --oneline --graph --decorate   # compact branching view
git log -p                             # show patches
git diff                               # unstaged changes
git diff --staged                      # staged changes
git blame <file>                       # who changed which line

# --- Tags & releases ---
git tag                                # list tags
git tag -a v1.2.3 -m "release"         # annotated tag
git push origin v1.2.3                 # push a single tag
git push origin --tags                 # push all tags

# --- Stash (shelve work) ---
git stash push -u -m "wip"             # save incl. untracked
git stash list                         # view stashes
git stash pop                          # restore & drop
git stash apply stash@{2}              # restore keep stash

# --- Undo/repair (safe to dangerous) ---
git restore <file>                     # restore from HEAD to working tree
git checkout -- <file>                 # old alias for restoring file
git reset HEAD~1                       # move branch back 1 commit (keep files)
git reset --hard HEAD~1                # drop last commit & file changes
git revert <commit>                    # make new commit that undoes one
git reflog                             # browse "where HEAD has been"
git cherry-pick <commit>               # copy a commit onto current

# --- Clean workspace ---
git clean -fd                          # remove untracked files/dirs (DANGEROUS!)
git rm -r --cached <path>              # stop tracking but keep files (use with .gitignore)

# --- .gitignore helpers ---
echo "data/*.csv" >> .gitignore
git add .gitignore && git commit -m "ignore csv data"

# --- Submodules (superproject + children) ---
git submodule add <url> path           # add submodule
git submodule update --init --recursive          # get all children
git submodule update --remote --recursive        # update to tracked branch tips
git submodule status                              # show SHAs & dirty state
git submodule summary                             # pending commits vs recorded SHAs
git submodule set-branch --branch main path       # track branch 'main'
git submodule sync --recursive                    # sync URLs if changed
# Bump a child pointer:
git add path/to/submodule && git commit -m "Bump submodule" && git push
# Push all if children changed locally:
git push --recurse-submodules=on-demand

# --- Collaboration niceties ---
git fetch upstream                           # if you added an 'upstream' remote
git merge --ff-only upstream/main            # fast-forward only (no merge commit)
git rebase upstream/main                     # linearize on upstream
git push --force-with-lease                  # safe force push (checks remote state)

```

### Remove branch

```bash
# 1) Switch off the branch you want to delete
git switch main   # or: git checkout main

# 2) Delete it (safe: only if fully merged into current branch)
git branch -d feature/my-branch

# 3) Force delete (even if not merged) – be sure you want this
git branch -D feature/my-branch

```

---

## Memory hooks for subcommands & common flags

|Word/Flag|Meaning|Hook|
|---|---|---|
|`status`|show state|“what changed?”|
|`add`|stage|“**add** to staging”|
|`-A` (add)|all changes|**A**ll (new/modified/deleted)|
|`commit`|save snapshot|“make a **commit** to history”|
|`-m` (commit)|message|**m**essage inline|
|`--amend`|edit last commit|“fix the previous snapshot”|
|`push`|upload to remote|“send”|
|`-u` / `--set-upstream`|remember remote/branch|“link this branch”|
|`fetch`|download only|“get but don’t mix”|
|`pull`|fetch + merge/rebase|“bring into mine”|
|`--rebase` (pull)|rebase instead of merge|“keep line straight”|
|`merge`|combine histories|“join branches”|
|`--no-ff`|always create merge commit|“bubble to keep context”|
|`rebase`|replay commits elsewhere|“lift & re-place”|
|`-i` (rebase)|interactive|“edit/squash/fixup”|
|`switch -c`|create + switch|“new branch here”|
|`restore`|restore file content/staging|“put it back”|
|`reset`|move branch / unstage|“point elsewhere / unstage”|
|`--soft` (reset)|move HEAD only|keep index & files|
|`--mixed` (default)|move HEAD + unstage|keep files|
|`--hard`|move HEAD + wipe changes|CAREFUL|
|`revert`|make commit that undoes|safe undo for shared branches|
|`log --oneline --graph --decorate`|compact tree|“map of history”|
|`diff --staged`|staged vs HEAD|“what I’ll commit”|
|`blame`|annotate lines|“who/when for each line”|
|`tag -a`|annotated tag|“label with message”|
|`stash push -u`|stash incl. untracked|“save everything”|
|`clean -fd`|delete untracked dirs/files|**f**orce **d**irs (danger)|
|`--recurse-submodules`|act through children|include submodules|
|`submodule update --init --recursive`|get/initialize all|first-time setup|
|`submodule update --remote`|move to tracked branch head|update children|

---

## Quick “what do I run?” recipes

```bash
# New feature branch
git switch -c feature/x
# Work, then:
git add -A && git commit -m "x: first pass"
git push -u origin feature/x

# Update my branch with latest main (linear)
git fetch origin
git rebase origin/main
# If conflicts: fix files -> git add -A -> git rebase --continue

# Merge feature to main and push
git switch main
git pull
git merge --no-ff feature/x
git push

# Undo last commit but keep changes staged
git reset --soft HEAD~1

# Accidentally committed secrets? (after removing from files)
git reset --soft HEAD~1
git commit -m "redo without secret"
# consider git filter-repo or BFG for history rewrite if already pushed

# Submodule edit flow (leaf -> parent -> root)
# (in submodule) commit & push
# (in parent) git add submodule && git commit -m "Bump child" && git push
# (in root)   git add parent && git commit -m "Bump parent" && git push

```

## History of git Commands
###  Merging Branchs

[[git_merge_options]]

### Removing branchs

[[git_removing_branch_options]]

### Workflow for merging branches in colon_matching

[[merging_branches_colon_matching]]

### Changing folder name of a submodule

[[reordering_subomdules_paths_from_a_repo]]

### Resetting to branch if I have made some mistakes

```bash
git fetch origin
git reset --hard origin/main
git clean -fd
```

### Resetting to last commit if I have made some mistakes

```bash
git reset --hard HEAD
git clean -fd
```

### Add upstream repo

This is always manual, no default config for having the remote automatically with it´s remote from the original repo

```bash
git remote add upstream https://github.com/alexstoken/image-matching-models
```
