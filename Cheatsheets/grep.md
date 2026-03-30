### Meaning
**grep**: _**g**lobal **r**egular **e**xpression **p**rint_

### Function
Searches text for patterns (regex). Prints matching lines; can recurse through directories and filter files.

### Main Command
**grep -Rni --color=auto 'pattern' .**
- `-R` = recursive (descend into subdirs; follows symlinks)
- `-n` = show line numbers
- `-i` = ignore case
- `--color=auto` = highlight matches
- `.` = current directory

### Other Commands

```bash
# Basic searches
grep 'foo' file.txt                    # simple match
grep -E 'foo|bar' file.txt             # extended regex (egrep)
grep -F 'literal text' file.txt        # fixed string (fgrep), fastest for literals
grep -v 'foo' file.txt                 # invert: lines NOT matching

# Recursive + file filtering
grep -R 'pattern' .                    # recurse
grep -R --include='*.py' -n 'def ' src/
grep -R --exclude-dir='.git' 'TODO' .

# Output control
grep -nH 'pat' file.txt                # line numbers + filename
grep -h 'pat' file1 file2              # suppress filename
grep -o 'http[^ ]*' file.txt           # only the match(es)
grep -c 'pat' file.txt                 # count matches per file
grep -l 'pat' -R .                     # list files WITH matches
grep -L 'pat' -R .                     # list files WITHOUT matches

# Match precision
grep -w 'word' file.txt                # whole word
grep -x 'exact line' file.txt          # whole line

# Context around matches
grep -A2 'pat' file.txt                # 2 lines After
grep -B1 'pat' file.txt                # 1 line Before
grep -C3 'pat' file.txt                # 3 lines of Context both sides

# Regex engines / special cases
grep -P 'look(?=ahead)' file.txt       # PCRE (Perl regex), if supported
grep -z 'pat' files0                   # NUL-separated "lines" (works with find -print0)
zgrep 'pat' file.gz                    # search inside .gz
```

### Memory hooks for flags

| Flag               | Meaning                              | Hook                         |
|--------------------|--------------------------------------|------------------------------|
| `-R`               | recursive (follows symlinks)         | dive into folders            |
| `-r`               | recursive (not all symlinks)         | recurse lite                 |
| `-i`               | ignore case                          | case? don’t care             |
| `-n`               | show line numbers                    | number the lines             |
| `-H` / `-h`        | force / suppress filename            | **H**andoff / **h**ush       |
| `-o`               | only matching part                   | only the match               |
| `-v`               | invert match                         | show the others              |
| `-w`               | whole word                           | word boundary                |
| `-x`               | whole line                           | exact line                   |
| `-E`               | extended regex (egrep)               | ORs and `+?()`               |
| `-F`               | fixed strings (fgrep)                | Fast literal                 |
| `-P`               | PCRE regex                           | fancy regex                  |
| `-A N` / `-B N`    | N lines after / before               | After / Before               |
| `-C N`             | N lines of context both sides        | Context                      |
| `--color=auto`     | highlight matches                    | color the hits               |
| `--include/--exclude` | glob filter files                 | only/skip these files        |
| `--exclude-dir=DIR`| skip directory                       | ignore that folder           |

### Tip
> Quote your pattern to avoid shell expansion (`grep 'a.*b'`). Use `-F` for raw text (faster, no regex). For recursive searches with file filtering: `grep -R --include='*.{c,cpp,h}' 'pattern' .`. If you need lookarounds, try `-P` (may not be available on all systems).