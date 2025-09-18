### Meaning
**tar**: _**T**ape **AR**chiver_ 
### Function
Bundles/unbundles files. Not a package manager. Think “zip for Unix.”
### Main command
**tar -xzvf file.tar.gz**
- `tar` = tape archiver
- `-x` = e**x**tract
- `-z` = g**z**ip (handle `.gz`)
- `-v` = **v**erbose
- `-f` = **f**ile follows (the next arg is the filename)
### Other Commands

```bash
# Extract
tar -xzvf file.tar.gz        # x=extract z=gzip v=verbose f=file
tar -xJf  file.tar.xz        # J=xz
tar -xjf  file.tar.bz2       # j=bzip2
tar -xf   file.tar           # auto-detect often works; add -v for output

# Create
tar -czvf out.tar.gz folder/ # c=create z=gzip v=verbose f=file
tar -cJf  out.tar.xz folder/

# List without extracting
tar -tzf file.tar.gz         # t=list z=gzip f=file

# Handy extras
tar -xzf file.tar.gz -C /target/dir             # extract into directory
tar -xzf file.tar.gz --strip-components=1       # drop top-level folder
```


### Memory hookf for flags
| Flag | Meaning           | Hook                           |
| ---: | ----------------- | ------------------------------ |
| `-c` | create            | **c**reate                     |
| `-x` | extract           | e**x**tract                    |
| `-t` | list (table)      | **t**able of contents          |
| `-f` | file name follows | **f**ile next                  |
| `-v` | verbose           | **v**erbose                    |
| `-z` | gzip              | g**z**ip                       |
| `-J` | xz                | uppercase **J** → x**Z**       |
| `-j` | bzip2             | **j** for b**j**ip2 (mnemonic) |