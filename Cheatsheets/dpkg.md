### Meaning
**dpkg**: _**D**ebian **P**ac**K**a**G**e_ (low-level) 
### Function
Installs/removes a local `.deb`. No dependency solving.
### Main command
**dpkg -i package.deb**
- `dpkg` = Debian package tool
- `-i` = **i**nstall
### Other Commands
```bash
# Install a local .deb (may break deps)
sudo dpkg -i package.deb

# Uninstall (keep config) / Purge (remove config)
sudo dpkg -r packagename
sudo dpkg -P packagename

# Query / list
dpkg -l | less                 # list installed packages
dpkg -L packagename            # list files installed by a package
dpkg -S /path/to/file          # which package owns this file?

# Inspect a .deb file (without installing)
dpkg-deb -I package.deb        # control info (Name, Version, Arch)
dpkg-deb -c package.deb        # list payload files
dpkg-deb -x package.deb outdir # extract payload to outdir
```
### Memory hooks for flags
|Flag|Meaning|Hook|
|--:|---|---|
|`-i`|install|**i**nstall|
|`-r`|remove|**r**emove|
|`-P`|purge|**P**urge config too|
|`-L`|list files|**L**ist installed files|
|`-S`|search owner|**S**earch by file path|
### Tip
> If `dpkg -i` leaves broken deps, run: `sudo apt --fix-broken install`.