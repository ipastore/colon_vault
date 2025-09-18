### Meaning
**apt**: _**A**dvanced **P**ackage **T**ool_ (high-level)
### Function
Talks to repos, resolves dependencies, can also install a local `.deb`.
### Main Command
**sudo apt install ./package.deb**
- `apt` = higher-level package tool
- `install` = action
- `./` = local file (without it, apt searches the repos)

### Other Commands

```bash
# Update & upgrade
sudo apt update
sudo apt upgrade            # safe upgrade
sudo apt full-upgrade       # allows removals if needed

# Install from repos (by name) or a local .deb (with ./)
sudo apt install packagename
sudo apt install ./package.deb

# Remove / Purge / Autoremove
sudo apt remove packagename
sudo apt purge packagename
sudo apt autoremove

# Info & troubleshooting
apt show packagename        # metadata
apt list --installed | less # installed packages
apt policy packagename      # version & origin
sudo apt --fix-broken install
```

### Memory hooks for flags

| Word           | Meaning                         | Hook                     |
| -------------- | ------------------------------- | ------------------------ |
| `update`       | refresh package lists           | “check what’s available” |
| `upgrade`      | upgrade w/o removals            | “safe update”            |
| `full-upgrade` | upgrade with necessary removals | “do what it takes”       |
| `install`      | install package(s)              | literal                  |
| `remove`       | uninstall keep config           | literal                  |
| `purge`        | uninstall + configs             | **purge** everything     |
| `autoremove`   | remove orphaned deps            | auto-clean deps          |
| `show`         | package info                    | show me details          |

### Tip
> Remember: `apt install ./file.deb` resolves deps; `dpkg -i file.deb` doesn’t.