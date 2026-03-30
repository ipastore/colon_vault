### Meaning
**xargs**: build & execute command lines from standard input (turns lists into arguments)

### Function
Reads items (usually filenames) from stdin and runs a command with them as arguments. Can batch (`-n`), parallelize (`-P`), and handle NUL-separated input safely (`-0`).

### Main Command
**find . -type f -name '*.log' -print0 \| xargs -0 -n1 -P 4 grep -Hn 'ERROR'**
- `-print0` + `-0` = NUL-safe (handles spaces/newlines)
- `-n1` = one item per command (safer with tools that expect single file)
- `-P 4` = run up to 4 commands in parallel
- `grep -Hn` = show filename and line numbers

### Other Commands

```bash
# Basics
printf '%s\n' file1 file2 file3 | xargs rm -v            # delete listed files
printf '%s\0' *.jpg | xargs -0 -n 50 cp -t /backup/      # copy in batches of 50 (GNU cp -t)

# With placeholders
cat files.txt | xargs -I{} echo "Processing: {}"
find src -name '*.c' -print0 | xargs -0 -I{} sh -c 'wc -l "{}"'

# Parallel work
cat urls.txt | xargs -n1 -P 6 curl -O                    # download 6 at a time
find . -name '*.png' -print0 | xargs -0 -n1 -P 8 optipng # optimize images in parallel

# Batch sizes / command length
git ls-files '*.py' | xargs -n 20 autoflake -i            # 20 files per invocation
git ls-files | xargs -s 100000 echo > /dev/null           # cap command size (bytes)

# Safe deletion
find . -type f -name '*.tmp' -print0 | xargs -0 rm -v     # remove *.tmp safely

# Combine with grep/sed/awk
git ls-files | xargs -n1 grep -Hn 'TODO'                  # search tracked files
grep -Rl 'foo' . | xargs -n1 sed -i 's/foo/bar/g'         # in-place replace in matches

# Deal with commands that read stdin (use placeholder and redirect)
find logs -name '*.gz' -print0 | xargs -0 -I{} sh -c 'zgrep -Hn ERROR "{}"'

# Build an archive from a list
find . -name '*.csv' -print0 | xargs -0 tar -cf data.tar  # pass files to tar

# NUL input from other tools
git -c core.quotePath=false ls-files -z | xargs -0 wc -l  # count lines NUL-safely
```

### Memory hooks for flags

| Flag       | Meaning                          | Hook                        |
| ---------- | -------------------------------- | --------------------------- |
| `-0`       | NUL-separated input              | from `-print0`, safe names  |
| `-n N`     | N args per command               | batch size                  |
| `-P N`     | run N commands in parallel       | parallelism                 |
| `-I {}`    | placeholder substitution         | inject at `{}`              |
| `-L N`     | N input lines per command        | line batch                  |
| `-d DELIM` | custom input delimiter (GNU)     | split on this               |
| `-r`       | do nothing if input empty (GNU)  | **r**un only if items exist |
| `-p`       | prompt before each command       | **p**rompt me               |
| `-t`       | print the command before running | **t**race commands          |
| `-s BYTES` | limit command line size          | cap length                  |

### Tip
> Prefer `-print0 | xargs -0` for filenames. Use `-n` to control batch size and `-P` to speed up independent work. If the wrapped tool reads stdin, wrap with `sh -c '...'` and pass the file as `"{}"` instead of piping. For portability (macOS/BSD), avoid GNU-only flags like `-r` and `-d` unless you have GNU xargs installed.