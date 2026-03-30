### Meaning
**du**: disk usage — estimate file/directory space on filesystem blocks

### Function
Summarizes how much disk space files and directories consume. Can limit depth, stay on one filesystem, include files, or show human-readable units.

### Main Command
**du -h -d 1 -x . 2>/dev/null \| sort -hr \| head**
- `-h` = human-readable sizes
- `-d 1` (BSD/macOS) / `--max-depth=1` (GNU) = only this directory’s immediate children
- `-x` = stay on one filesystem (don’t cross mount points)
- `.` = current directory
- `2>/dev/null` = silence permission errors
- `sort -hr | head` = show largest first (top entries)

### Other Commands

```bash
# Quick summaries
du -sh .                             # total size of current dir
du -sh /var/log /home/user           # per-target summary
du -sh *                             # each item in cwd (shell expands *)

# Depth control (GNU vs BSD/macOS)
du -h --max-depth=2 /var             # GNU: two levels deep
du -h -d 2 /var                      # BSD/macOS: two levels deep

# Include files (not just directories)
du -ah -d 1 . | sort -hr | head      # files + dirs, 1 level deep

# Stay on one filesystem (avoid mounted volumes)
du -sh -x /                          # root filesystem only

# Grand total
du -ch /var | tail -n 1              # GNU: cumulative total with -c
du -h -d 0 /var                      # BSD/macOS: similar single total

# Filter large items (GNU)
du -h --max-depth=1 --threshold=100M .  # show only ≥100M

# Choose block size (GNU)
du -B1M -d 1 .                       # sizes rounded in MiB
BLOCKSIZE=1M du -d 1 .               # alt: env var on BSD/macOS

# Apparent vs on-disk size
du -h --apparent-size file.iso       # logical bytes (sparse files look big)
du -h file.iso                       # on-disk blocks (sparse files look small)

# Exclude patterns (GNU)
du -h --max-depth=2 --exclude='*.git*' .
du -h --exclude-from=exclude.txt .

# Follow symlinks (be careful!)
du -h -L -d 1 .                      # follow all symlinks
du -h -H -d 1 .                      # follow symlinks on command line only

# Largest items anywhere (recursive, top 20)
du -xh . 2>/dev/null | sort -hr | head -n 20

# Per-user home sizes (example)
sudo du -sh /home/* 2>/dev/null | sort -hr
```

### Memory hooks for flags

| Flag / Option            | Meaning                                 | Hook                         |
|--------------------------|------------------------------------------|------------------------------|
| `-h`                     | human-readable units                     | human sizes                  |
| `-s`                     | summarize (one total per arg)            | single total                 |
| `-a`                     | include files (not just dirs)            | all entries                  |
| `-d N` / `--max-depth=N` | limit recursion depth                     | dive N levels                |
| `-x`                     | stay on one filesystem                    | don’t cross mounts           |
| `-c` (GNU/BSD)           | grand total at end                        | cumulative                   |
| `-B SIZE` (GNU)          | block size (e.g., `-B1M`)                 | size blocks                  |
| `--apparent-size` (GNU)  | logical bytes instead of disk blocks      | what it *appears* to be      |
| `--exclude=PAT` (GNU)    | skip matching paths                       | leave those out              |
| `-L` / `-H`              | follow all / only CLI symlinks            | link-following               |

### Tip
> Use `du` to measure *where space goes*; use `df -h` to see *free/used per filesystem*. On macOS/BSD prefer `-d N`; on GNU/Linux prefer `--max-depth=N`. If you hit permission errors, prefix with `sudo` or silence with `2>/dev/null`.