### Meaning
**ssh**: **S**ecure **Sh**ell — encrypted remote login/execution, file transfer, and tunneling.

### Function
Connect to other machines, run commands there, copy files, and forward ports — safely over TLS-like crypto.

---

## 1) Basics you’ll use daily

```bash
# Connect
ssh user@host                  # default port 22
ssh -p 2222 user@host          # custom port

# Your specific example
ssh -p 3003 ipastore@155.210.134.17
# └─ connect to 155.210.134.17 as user 'ipastore' on port 3003

# Run a single remote command, then exit
ssh user@host 'df -h && whoami'

# Get a TTY (for top/htop/vim), force if needed
ssh -t user@host 'htop'

# Be chatty for debugging
ssh -vvv user@host
```

---

## 2) Keys: generate, install, and permissions

```bash
# Generate a modern key (recommended)
ssh-keygen -t ed25519 -C "yourname@device"    # creates ~/.ssh/id_ed25519 (+ .pub)

# Copy your public key to the server (password once, then key-based)
ssh-copy-id -p 3003 ipastore@155.210.134.17

# Fix permissions (SSH is strict)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_* ~/.ssh/config
chmod 644 ~/.ssh/id_*.pub ~/.ssh/known_hosts
```

**Memory hooks**

- Private key: `~/.ssh/id_ed25519` (keep secret).  
- Public key: `~/.ssh/id_ed25519.pub` (share/copy to servers).  
- First login often uses a password just to install your key.

---

## 3) `~/.ssh/config` — aliases & per-host defaults

Create or edit `~/.ssh/config`:

```sshconfig
# --- Global defaults (optional) ---
Host *
  ServerAliveInterval 30          # keep the connection fresh
  ServerAliveCountMax 3
  ControlMaster auto               # speed: reuse connections
  ControlPath ~/.ssh/cm-%r@%h:%p
  ControlPersist 10m
  IdentitiesOnly yes               # use only keys we specify

# --- Your server alias ---
Host rzx
  HostName 155.210.134.17
  User ipastore
  Port 3003
  IdentityFile ~/.ssh/id_ed25519
```

Now you can simply:

```bash
ssh rzx
scp somefile rzx:/path/           # scp uses the same alias
rsync -avh -e ssh local/ rzx:/path/
```

**Memory hooks**

- `Host` = your short alias  
- `HostName` = actual IP/DNS  
- `User` = login name, `Port` = SSH port  
- `IdentityFile` = which private key to use

---

## 4) Port forwarding (tunnels)

```bash
# Local forward: open localhost:8888 on YOUR machine to remote localhost:8888
ssh -L 8888:localhost:8888 rzx

# Remote forward: open remote :9000 that points back to your local :9000
ssh -R 9000:localhost:9000 rzx

# Dynamic SOCKS proxy (use in browser as SOCKS5 localhost:1080)
ssh -D 1080 rzx

# Run a tunnel only (no remote shell) & background it
ssh -Nf -L 5432:localhost:5432 rzx   # -N no command, -f background
```

**Cheat:** `-L me→remote`, `-R remote→me`, `-D` = SOCKS proxy.

---

## 5) Copying files (scp/sftp/rsync)

```bash
# scp: note uppercase -P for port!
scp -P 3003 file.txt ipastore@155.210.134.17:/tmp/
scp -P 3003 -r dir/ ipastore@155.210.134.17:~/dir/

# sftp (interactive)
sftp -P 3003 ipastore@155.210.134.17
# inside: put/get/ls/cd

# rsync over ssh (fast, resumable)
rsync -avh --progress -e "ssh -p 3003" localdir/ ipastore@155.210.134.17:/data/localdir/
```

---

## 6) Jump hosts (bastions)

```bash
# One-off
ssh -J user@bastion:2222 target

# In config (preferred)
Host bastion
  HostName bastion.example.com
  User me
  Port 2222

Host private-net-1
  HostName 10.0.0.10
  User admin
  ProxyJump bastion
```

Then:

```bash
ssh private-net-1
```

---

## 7) Per-command knobs you’ll actually use

```bash
ssh -p 3003 -i ~/.ssh/id_ed25519 user@host     # pick port + key
ssh -o StrictHostKeyChecking=accept-new host   # auto-accept first host key
ssh -o ConnectTimeout=5 host                   # bail fast if unreachable
ssh -o ForwardAgent=yes host                   # (careful) forward your agent
ssh -N -L 8080:svc:80 host                     # tunnel only, no shell
```

---

## 8) Fast sessions via multiplexing

Add to `~/.ssh/config` (already shown in §3):

```sshconfig
Host *
  ControlMaster auto
  ControlPath ~/.ssh/cm-%r@%h:%p
  ControlPersist 10m
```

Now the first `ssh` creates a control socket; subsequent `ssh/scp/rsync` re-use it — instant!

---

## 9) Troubleshooting & safety

```bash
ssh -vvv rzx                             # verbose handshake logs
ssh -V                                   # client version
ssh-keygen -R "[155.210.134.17]:3003"    # remove stale known_hosts entry for that host:port
ssh-keyscan -p 3003 155.210.134.17 >> ~/.ssh/known_hosts  # pre-trust key (verify out-of-band!)
```

**Gotchas**

- Wrong `scp` flag: **`-P`** for port (uppercase), `-p` preserves times.  
- Permissions must be strict or SSH refuses keys.  
- Agent forwarding (`-A` / `ForwardAgent yes`) is convenient but exposes your agent to the remote — only use on trusted hops.

---

## 10) Quick “do & log” recipes

```bash
# Run a remote command and capture output locally
ssh rzx 'du -sh ~/*' | tee rzx_sizes.txt

# Keep an SSH session alive
ssh -o ServerAliveInterval=30 -o ServerAliveCountMax=3 rzx

# Mount a remote dir (Linux) via sshfs
sshfs rzx:/data ~/mnt/rzx_data    # fusermount -u ~/mnt/rzx_data to unmount
```

---

## 11) Handy config templates

```sshconfig
# A host with a non-default key and port
Host analytics
  HostName analytics.example.org
  User data
  Port 22022
  IdentityFile ~/.ssh/id_ed25519_analytics
  IdentitiesOnly yes

# SOCKS gateway box
Host socks-gw
  HostName gw.example.net
  User me
  DynamicForward 1080
  ExitOnForwardFailure yes
  ServerAliveInterval 30
```

---

## 12) Cheats for remembering flags

|Flag|Reads as…|
|--:|---|
|`-p PORT`|SSH port (default 22)|
|`-i FILE`|Use this identity (private key)|
|`-J A,B`|Jump via A (then B…)|
|`-L a:b:c`|Local port a → b:c (remote)|
|`-R a:b:c`|Remote port a → b:c (local)|
|`-D PORT`|Make a SOCKS proxy on PORT|
|`-N`|Don’t run a command (tunnels only)|
|`-f`|Go to background after auth|
|`-t`|Force a TTY (interactive apps)|
|`-v/-vv/-vvv`|More debug|

---

_Gotchas:_ host keys change on rebuilds — verify before `ssh-keygen -R …`; don’t store private keys in repos; prefer `ed25519` keys now; for `rsync` with non-22 ports, use `-e "ssh -p …"`.

If you want, I can fold this into your existing Markdown guide as a new **“SSH”** section and tune examples to your actual servers.

# History of commands

## SSH for github
### Use an agent to save your user and reuse the passphrase for THIS shell

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_github_ipastore
```
## Changing git user with ssh for rene

### 1) Change user and email
```bash

git config --local user.name  "Your Name"
git config --local user.email "you@example.com"
git config --local user.useConfigOnly true
git config --show-origin --get user.name
git config --show-origin --get user.email
```

### 2) Make (or reuse) an SSH key for your GitHub
```bash
ssh-keygen -t ed25519 -C "you@example.com" -f ~/.ssh/id_ed25519_github_you
cat ~/.ssh/id_ed25519_github_you.pub
```
### 3) Add ssh key to github
Add the **public** key you just printed to:

**GitHub → Settings → SSH and GPG keys → New SSH key**.

### 4) Verify GitHub auth with that key
```bash
ssh -i ~/.ssh/id_ed25519_github_you -o IdentitiesOnly=yes git@github.com
```

**Expected**:

```bash
Hi <your-github-username>! You've successfully authenticated, but GitHub does not provide shell access.
```

### 5) Tell this repo to use that key (no global changes)

```bash
git config --local core.sshCommand 'ssh -i ~/.ssh/id_ed25519_github_you -o IdentitiesOnly=yes'
```

### 6) Switch the remote from HTTPS → SSH (to your repo or your fork)

```bash
git remote set-url origin git@github.com:ipastore/ColonSuperpoint_demo.git
git remote -v
```
### 7) Test without actually pushing

```bash
git push --dry-run origin HEAD
```

## To use Tensorboard

## forward the port and then inside student launch tensorboard

```bash
ssh -L 6006:localhost:6006 student
```

- -L LOCAL_PORT:REMOTE_HOST:REMOTE_PORT    
    - LOCAL_PORT = port on your Mac (here: 6006)
    - REMOTE_HOST = from the remote’s perspective (here: localhost, meaning _the same remote machine_)
    - REMOTE_PORT = port on the remote to connect to (here: 6006)

# Make a tunnel through other machine and expose a listen/export to the same port number

```bash
ssh -J student -L 6006:localhost:6006 dgx
```


```bash
ssh -J student -L 6006:127.0.0.1:6006 dgx
```
