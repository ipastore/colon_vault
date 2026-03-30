### Meaning
**awk**: pattern-scanning & processing language (Aho–Weinberger–Kernighan)

### Function
Reads input line by line, splits into fields, matches **patterns**, runs **actions**. Great for filtering, column selection, aggregation, quick reports.

### Main Command
**awk -F',' 'NR>1 {print $1, $3}' OFS='\t' file.csv**
- `-F','` = input field separator (comma)
- `NR>1` = skip header
- `{print $1, $3}` = print columns 1 and 3
- `OFS='\t'` = output field separator (tab)
- `file.csv` = input file

### Other Commands

```bash
# Anatomy
awk 'pattern { action }' file            # default pattern is "true"; default action is "print $0"

# Quick looks
awk '{print $0}' file                    # print lines (identity)
awk '{print NR, NF, $0}' file            # line no, field count, line
awk 'END {print NR}' file                # count lines

# Select columns
awk -F: '{print $1}' /etc/passwd         # first field (usernames)
awk 'BEGIN{FS=OFS=","} {print $1,$3}' f.csv  # set FS & OFS in script

# Filter rows
awk '$3 > 1000' /etc/passwd              # numeric filter
awk '/ERROR/ && !/ignored/' app.log      # regex include & exclude
awk '$NF=="OK"' report.txt               # match on last field

# Aggregate
awk '{sum+=$2} END{print sum}' sales.txt                # sum a column
awk '{sum[$1]+=$2} END{for(k in sum) print k, sum[k]}'  # group-by key in $1

# Count uniques
awk '{c[$1]++} END{for(k in c) print k, c[k]}' file

# Replace text
awk '{gsub(/foo/,"bar",$1); print}' file                # replace in field 1

# Context with headers
awk 'NR==1; NR>1{print toupper($1), $2}' file           # keep header, transform rest

# Multi-file tricks
awk 'FNR==1{fname=FILENAME} {print fname ": " $0}' f1 f2 # tag lines with filename
awk 'FNR==NR{m[$1]=$2; next} {print $0, m[$1]}' a.map b.txt  # "join" by key in $1

# Inline vs file scripts
awk -e 'BEGIN{print "hi"}' -e '{print $1}' file         # multiple -e
awk -f prog.awk data.txt                                # use a script file

# CSV-ish (simple)
awk -F, 'BEGIN{OFS=","} {print $1,$2,$3}' data.csv
```

### Memory hooks for flags

| Flag        | Meaning                              | Hook                   |
|-------------|--------------------------------------|------------------------|
| `-F SEP`    | set input field separator            | **F**ields split by…   |
| `-v k=v`    | set variable before processing       | pre-**v**ar            |
| `-f file`   | load program from file               | program **f**ile       |
| `-e 'prog'` | program text on CLI                  | inlin**e** program     |

### Handy built-ins
- Variables: `NR` (record #), `FNR` (record # per file), `NF` (# fields), `$0` (whole line), `$1…` (fields)
- Separators: `FS`/`OFS` (field), `RS`/`ORS` (record)

### Tip
> Quote with single quotes to protect `$1` etc. Prefer `-F` to set the splitter. For in-place edits, `gawk -i inplace 'gsub(/old/, "new")' file` (gawk-only). Pattern first, then action: `pattern { action }`.