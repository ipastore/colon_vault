
### Meaning
**bash**: **B**ourne-**A**gain **Sh**ell — the command language & interpreter you use in most Linux terminals.
### Function
Run programs, glue them with pipes, redirect input/output, automate with scripts, variables, and functions.

---

## 1) Basics you’ll use daily

```bash
# Run, chain, and control flow
cmd1; cmd2                  # do cmd1 then cmd2 regardless
cmd1 && cmd2                # do cmd2 only if cmd1 succeeded (exit code 0)
cmd1 || cmd2                # do cmd2 only if cmd1 failed
cmd1 | cmd2                 # pipe stdout of cmd1 into cmd2's stdin

# Navigation
pwd                         # where am I?
ls -lah                     # list (long, all, human sizes)
cd /path/to/dir             # go there
cd -                        # go back
pushd dir && ... && popd    # temp jump in/out

# Files & dirs
mkdir -p path/to/dir        # make parents as needed
cp -rvi src/ dst/           # copy (recursive, verbose, interactive)
mv -vi a b                  # move/rename (verbose, confirm overwrite)
rm -rf path                 # remove recursively (careful!)

# Inspect disk & system
df -h                       # free disk (human)
du -sh * | sort -h          # sizes of things here
uname -a                    # kernel/arch
````

---

## 2) Redirection, verbosity, and `/dev/null`

```bash
# Redirection
cmd > out.txt               # stdout to file (overwrite)
cmd >> out.txt              # stdout append
cmd 2> err.txt              # stderr to file
cmd > out.txt 2> err.txt    # split stdout/stderr
cmd &> all.txt              # stdout+stderr to file (bash)
cmd > /dev/null 2>&1        # silence everything

# What is /dev/null?
# A "black hole" file: data written there is discarded. Use it to silence noisy commands.

# See output and save it
cmd | tee out.txt           # show + save stdout
cmd | tee -a out.txt        # append

# Verbosity switches (common patterns)
cmd -v                      # verbose (if supported)
cmd -q                      # quiet
set -x                      # bash: print each command as it runs (trace)
set +x                      # stop tracing
```

**Memory hooks**

- `1>` is stdout, `2>` is stderr.
- `2>&1` “make stderr (2) go where stdout (1) goes.”
- `/dev/null` = “say nothing.”

---

## 3) Variables, quoting, and substitution

```bash
name="Fidel"
echo "Hello $name"          # double quotes expand variables
echo 'Hello $name'          # single quotes literal, no expansion

# Command substitution
now="$(date +%F_%H-%M-%S)"

# Default values
echo "${FOO:-default}"      # use $FOO, else "default"

# Arithmetic
echo $((3*7+1))             # 22

# Arrays
arr=(a b c)
echo "${arr[1]}"            # b
for x in "${arr[@]}"; do echo "$x"; done
```

---

## 4) Conditions, loops, and functions

```bash
# Tests (use [[ ... ]] in bash)
if [[ -f "file.txt" ]]; then
  echo "file exists"
elif [[ -d "dir" ]]; then
  echo "dir exists"
else
  echo "missing"
fi

# Common test flags: -f file, -d dir, -n nonempty, -z empty, -e exists, -x executable

# Loops
for f in *.py; do echo "$f"; done
while read -r line; do echo "$line"; done < input.txt

# Case
case "$1" in
  start) echo "starting";;
  stop)  echo "stopping";;
  *)     echo "usage: $0 {start|stop}"; exit 1;;
esac

# Functions (write code "as if talking")
say() { printf '→ %s\n' "$*"; }
ensure_dir() { [[ -d "$1" ]] || mkdir -p "$1"; }
retry() { local n=0; until "$@"; do ((n++)); sleep 1; [[ $n -ge 5 ]] && return 1; done; }
```

---

## 5) Pipes with the usual suspects

```bash
# find + grep
find . -type f -name "*.py" | xargs -r grep -n "TODO"
grep -RIn --line-number mask .

# cut/sort/uniq
cut -d: -f1 file | sort | uniq -c | sort -h

# JSON (if jq installed)
jq '.key' data.json
```

---

## 6) Processes & long-running jobs

```bash
cmd &                         # run in background
jobs -l                       # list background jobs
fg %1                         # bring job 1 to foreground
kill %1                       # stop job 1
pgrep -af python              # find processes
nohup cmd > out.log 2>&1 &    # survive logout; log output
disown                        # detach job from shell
```

---

## 7) Networking & transfers (quick)

```bash
curl -LO https://site/file.tar.gz    # -L follow redirects, -O save as remote name
wget -q --show-progress URL          # quiet with progress
scp -r localdir user@host:/path/
rsync -avh --progress src/ dst/      # sync dirs
nc -zv host 22                       # check if port open (verbose)
```

---

## 8) Git-friendly helpers (when deep in submodules)

```bash
git rev-parse --show-toplevel        # repo root
cd "$(git rev-parse --show-toplevel)" || exit 1

# commit pointer bumps (example)
git add utils/image-matching_models
git commit -m "Bump submodules"
```

---

## 9) Safe script template (robust & chatty)

```bash
#!/usr/bin/env bash
set -euo pipefail            # exit on error/undefined, fail on pipe errors
IFS=$'\n\t'                  # safer word splitting
trap 'echo "✗ Error at ${BASH_SOURCE[0]}:${LINENO}"' ERR

say() { printf '> %s\n' "$*"; }   # talk to me
die() { printf 'ERROR: %s\n' "$*\n" >&2; exit 1; }

: "${PROJECT_ROOT:=$(git rev-parse --show-toplevel 2>/dev/null || pwd)}"
say "Project: $PROJECT_ROOT"

ensure_dir() { [[ -d "$1" ]] || mkdir -p "$1"; }
ensure_dir "$PROJECT_ROOT/logs"

# Example step
step_download() {
  local url="$1" out="$2"
  say "Downloading $url"
  curl -fsSL "$url" -o "$out" || die "Failed to fetch $url"
}
step_download "https://example.com/file.txt" "$PROJECT_ROOT/logs/file.txt"
say "Done."
```

**Memory hooks**

- `set -e` = exit on error; `-u` = undefined var is error; `-o pipefail` = catch pipe failures.
- `trap ... ERR` = print where it broke.
- `say "..."` = write scripts that **sound like you**.

---

## 10) Verbosity knobs (per-command)

- Many tools support:
    
    - `-v`, `-vv`, `-vvv` → **more** verbose
    - `-q`, `-qq` → **less** verbose
    - `--progress` or `--no-progress`

- Examples:
    
    - `tar -v` (list files), `cp -v`, `rsync -avh --progress`, `curl -v`, `git -c advice.statusHints=false status`


---

## 11) Quick “do & log” recipes

```bash
# Run and silence unless error
cmd >/dev/null 2>&1 || echo "cmd failed"

# Run, see output, and log to file
cmd | tee run.log

# Timestamped logs
log="run_$(date +%F_%H-%M-%S).log"; cmd | tee "$log"

# Try up to 5 times
n=0; until cmd; do ((n++)); sleep 1; [[ $n -ge 5 ]] && break; done
```

---

## 12) Cheats for remembering redirections

|Form|Reads as…|
|--:|---|
|`>` / `>>`|stdout → file (overwrite / append)|
|`2>`|**2** (stderr) → file|
|`&>`|stdout **and** stderr → file (bash)|
|`2>&1`|make stderr go where stdout goes|
|`>/dev/null 2>&1`|shut up (discard everything)|
|`< file`|stdin ← file|

---


_Gotchas:_ prefer `printf` over `echo` for portability; always quote variables `"${var}"`; beware of `rm -rf` and wildcards; test scripts with `set -x` trace.