### Meaning
**rsync**: fast, robust file sync/copy tool with deltas, resume, filtering, and metadata preservation—usually over SSH.

### Function
Mirror directories or copy files locally/over SSH; preserve perms/owners/times; resume; show progress; exclude patterns; verify with checksums.

---

## 1) Basics you’ll use daily

```bash
# Local → Remote (push)
rsync -aP rsrc/ rene:~/dest/

# Remote → Local (pull)
rsync -aP student:~/rsrc/ ./dest/

# NOTE: trailing slash matters:
#  src/  = copy CONTENTS of src into dest/
#  src   = create dest/src then put contents there

# Human numbers + progress (overall & per file)
rsync -ahP --info=progress2 rsrc/ rene:~/dest/
```

**Memory hooks**
- -a = archive (recursive + metadata)
- -h = human sizes
- -P = --partial --progress (resume + progress)
- --info=progress2 = nice overall progress bar
## **2) Fast transfers (pick a profile)**
```bash

# WAN (slow link): compress + deltas
rsync -azahP --info=progress2 rsrc/ rene:~/dest/

# LAN (fast link, same DC): skip compression, send whole files
rsync -aWhP --info=progress2 rsrc/ rene:~/dest/

# Limit bandwidth if needed (e.g., 50 MB/s)
rsync -aP --bwlimit=50m rsrc/ rene:~/dest/
```

**Memory hooks**

- -z compress (great on slow links; skip on fast LAN)
- -W whole file (skip delta algorithm → faster on very fast links)

## **3) Ports & SSH options**

```bash
# Use non-default SSH port (e.g., 2222) + SSH compression
rsync -aP -e 'ssh -p 2222 -C' rsrc/ rene:~/dest/

# Reuse SSH connection (fewer handshakes → faster many small files)
rsync -aP -e 'ssh -o ControlMaster=auto -o ControlPersist=60s -o ControlPath=~/.ssh/cm-%r@%h:%p' rsrc/ rene:~/dest/
```
**Notes**

- **SSH port** is set via -e 'ssh -p PORT'.
- -C enables SSH-layer compression (okay with or without -z—usually use **one**; prefer -z’s --skip-compress smarts, below).

## **4) Compression that doesn’t waste CPU**

```bash
# rsync compress and avoid recompressing already-compressed types
rsync -aP -z --skip-compress=gz/zip/iso/jpg/jpeg/png/mp4/mkv/mp3 rsrc/ rene:~/dest/

# Tune compression level (default varies; 1=fast, 9=max)
rsync -aP -z --compress-level=1 rsrc/ rene:~/dest/     # speed over ratio
```

## **5) Ignore existing vs checksums**

```bash
# Only copy NEW files (don’t touch files that already exist on dest)
rsync -aP --ignore-existing rsrc/ rene:~/dest/

# Only update files that already exist on dest (skip brand new ones)
rsync -aP --existing rsrc/ rene:~/dest/

# Use checksums to detect changes (slow: reads both sides fully)
rsync -aP -c rsrc/ rene:~/dest/

# Safer resume (verify appended data on resume over flaky links)
rsync -aP --append-verify rsrc/ rene:~/dest/
```

**Memory hooks**

- --ignore-existing = _create only new files_; don’t overwrite existing.
- --existing = _update only existing_; don’t create new.
- -c/--checksum = compare file contents (slow, but catches same-size/same-mtime differences).
## **6) Show progress clearly**

```bash
# Per-file bar + overall stats
rsync -ahP --info=progress2 rsrc/ rene:~/dest/

# Log what would change without doing it
rsync -aP -n -i rsrc/ rene:~/dest/    # -n dry run, -i itemize changes
```

## **7) Selective sync (include/exclude)**

```bash
# Exclude patterns
rsync -aP --exclude='*.tmp' --exclude='.git/' rsrc/ rene:~/dest/

# Include a few, exclude rest
rsync -aP --include='*.py' --include='*/' --exclude='*' rsrc/ rene:~/dest/

# From a file
rsync -aP --exclude-from=exclude.txt rsrc/ rene:~/dest/
```

## **8) Mirroring & deletes (careful!)**

```bash
# Make DEST match SRC (delete extras in dest)
rsync -aP --delete rsrc/ rene:~/dest/

# Safer deletes: move to backup dir instead of removing
rsync -aP --delete --backup --backup-dir=~/trash/$(date +%F) rsrc/ rene:~/dest/
```

## **9) Permissions, ownership, and sudo**

```bash
# Preserve perms/owners/times (needs privileges on target for owners/groups)
rsync -aP rsrc/ rene:/srv/data/

# When remote needs sudo to write:
rsync -aP --rsync-path='sudo rsync' rsrc/ rene:/srv/protected/
```

## **10) Recipes for your hosts (student ↔ rene)**

```bash
# LOCAL → rene, fast WAN profile with progress
rsync -azahP --info=progress2 ./project/ rene:~/project/

# rene → LOCAL (pull), custom port 2222
rsync -ahP --info=progress2 -e 'ssh -p 2222' rene:~/data/ ./data/

# student → rene (remote→remote via your machine; requires SSH to both)
rsync -ahP --info=progress2 -e ssh student:~/dump/ rene:~/dump/

# Only new files from student → rene (don’t overwrite)
rsync -ahP --ignore-existing student:~/photos/ rene:~/photos/

# Verify content differences (slow but thorough)
rsync -ahP -c student:~/datasets/ rene:~/datasets/
```

## **11) Quick “do & log” recipes**

```bash
# Resume-friendly big copy with overall progress
rsync -ahP --info=progress2 bigdir/ rene:/data/bigdir/

# Fast LAN bulk move (skip compression, whole-file)
rsync -aWhP --remove-source-files media/ rene:/srv/media/

# Throttle and keep a log
log="rsync_$(date +%F_%H-%M-%S).log"
rsync -ahP --bwlimit=20m src/ rene:~/dst/ | tee "$log"
```

## **12) Cheats for remembering options**

| **Form**                | **Reads as…**                        |
| ----------------------- | ------------------------------------ |
| -a                      | archive (recurse + preserve)         |
| -P                      | partial + progress (resume + show)   |
| --info=progress2        | overall progress                     |
| -z / --compress-level=N | compress / tune level                |
| -W                      | whole files (skip delta)             |
| --ignore-existing       | only create new files on dest        |
| --existing              | update only files that already exist |
| -c                      | checksum compare (slow)              |
| -n -i                   | dry-run + itemize changes            |
| --delete                | delete extras in dest (mirror)       |
| -e 'ssh -p 2222 -C'     | SSH port + compression               |

**Gotchas:** trailing / changes semantics; -c is slow; --delete is dangerous (test with -n); avoid -z for already-compressed media (or use --skip-compress); prefer -P for resume.


## **Bonus: quick verify after transfer**
```bash
# Strong end-to-end verify for a few critical files
sha256sum bigfile.iso | ssh rene 'sha256sum -c -'

# Or rely on rsync’s verification pass
rsync -aP -c rsrc/ rene:~/dest/
```

# History of commands

```bash
rsync -avz --ignore-existing --progress rene:~/ColonSuperpoinTorch/logs/ ./logs/
```

**Without compression** 
```bash
rsync -aWh --info=progress2 --ignore-existing rene:~/ColonSuperpoint_demo/assets/matching/ ~/ColonSuperpoint_demo/assets/matching/
```

```bash
rsync -aWh --info=progress2 --ignore-existing dgx:~/storage/ColonSuperpoinTorch/logs/ ~/ColonSuperpoint_demo/ColonSuperpoinTorch/logs/
```

**In Macos there is no --info=progress2 flag**
```bash
rsync -aWh --progress --ignore-existing rene:~/ColonSuperpoint_demo/matching_outputs/ ~/Documents/MGRCV/COLON/ColonSuperpoint_demo/matching_outputs
```

**data from /media to dgx**

```bash
rsync -aWh --info=progress2 --ignore-existing /media/student/HDD/nacho/glue-factory/data/ dgx:~/storage/glue-factory-colon/data/
```

## To fix duplicate folders

## Enable moving hidden files too, avoid errors on empty dirs
```bash
shopt -s dotglob nullglob
```

```bash
mv ~/ColonSuperpoint_demo/assets/matching/matching/* \
   ~/ColonSuperpoint_demo/assets/matching/
```

``` bash
rmdir ~/ColonSuperpoint_demo/assets/matching/matching
```

```bash
# Restore shell defaults
shopt -u dotglob nullglob
```
