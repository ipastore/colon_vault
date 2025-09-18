
### Meaning
**pip**: “**P**ip **I**nstalls **P**ackages” — Python’s package installer for PyPI and other indexes.

### Function
Install/upgrade/uninstall Python packages into an environment (venv/conda), handle wheels/builds, read `requirements.txt` and constraints, and query metadata.

---

### Main commands (daily flow)
```bash
# Install / Upgrade / Uninstall
pip install package
pip install -U package                    # upgrade
pip uninstall package

# List & info
pip list
pip list --outdated
pip show package

# Freeze (export exact versions)
pip freeze > requirements.txt

# Recreate from a spec
pip install -r requirements.txt
````

---

### Virtual environments (recommended)

```bash
# Pure Python venv
python -m venv .venv
source ./.venv/bin/activate         # Windows: .\.venv\Scripts\activate
pip install -U pip                  # keep pip fresh

# In conda envs just run `pip` inside the activated conda env
conda activate myenv
pip install package
```

_Rule:_ **Do not** use `sudo pip`. Use a venv/conda env or `--user`.

---

### Requirements & Constraints

```bash
# Lock current env (exact pins)
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt

# Use a constraints file (pin transitive deps without forcing top-level)
pip install mypkg -c constraints.txt
# constraints.txt example:
# numpy==1.26.4
# scipy==1.13.1
```

**When to use which**

- **requirements.txt** — recreate exact env (apps).
- **constraints.txt** — steer dependency versions while letting top-level float (libs).

---

### Editable/dev installs & VCS

```bash
# Editable local package (live code)
pip install -e .

# Install from Git
pip install git+https://github.com/user/repo.git@branch#egg=pkgname

# Git subdir (PEP 517)
pip install "git+https://github.com/user/repo.git@tag#subdirectory=python_pkg"

# Extras
pip install "packagename[dev,docs]"
```

---

### Indices, mirrors, and private repos

```bash
# Alternate index or mirror
pip install --index-url https://pypi.org/simple somepkg
pip install --extra-index-url https://my.artifactory/simple privatepkg

# If TLS interception/firewall
pip install --trusted-host my.artifactory --index-url http://my.artifactory/simple privatepkg

# Local wheels / find-links
pip install --find-links ./wheelhouse somepkg
```

---

### Wheels, builds, and isolation

```bash
# Prefer binary wheels (default); build wheelhouse for offline
pip wheel -r requirements.txt -w ./wheelhouse

# Force source build (avoid wheels)
pip install --no-binary :all: mypkg

# Build isolation toggles (PEP 517/518)
pip install --no-build-isolation mypkg      # use current env for build deps
pip install --use-pep517 mypkg              # force PEP 517 build backend
```

---

### Cache & performance

```bash
pip cache dir
pip cache info
pip cache purge              # clear pip cache
pip install --no-cache-dir package   # disable cache for this install
```

---

### Diagnostics & sanity checks

```bash
pip check                    # detect broken dependencies in current env
python -m pip --version      # verify which pip/python you’re using
python -c "import sys; print(sys.executable)"   # path to interpreter
pip debug --verbose          # env, paths, compile flags
```

---

### Upgrading everything (use carefully)

```bash
# Unix-like
pip list --outdated --format=freeze \
 | cut -d= -f1 \
 | xargs -n1 -I {} pip install -U {}

# Windows (PowerShell)
pip list --outdated --format=freeze `
 | % { ($_ -split '==')[0] } `
 | % { pip install -U $_ }
```

_Tip:_ For apps, prefer curated updates via `requirements*.in` + `pip-tools` (below).

---

### pip-tools (sharp knife for locks)

```bash
# Install
pip install pip-tools

# Compile a locked requirements.txt from a minimal spec (.in)
# requirements.in:
#   numpy
#   requests>=2.31
pip-compile requirements.in            # emits pinned requirements.txt
pip-sync requirements.txt              # install exactly those versions
```

---

### pipx (isolated CLI tools)

```bash
# Install user-level CLI apps in isolated envs
python -m pip install --user pipx
pipx ensurepath
pipx install black
pipx upgrade black
pipx uninstall black
```

---

### Memory hooks for common options

| Flag / Word | Meaning | Hook |  
|---:|---------------------|-----------------------------------------|-------------------------------------|  
| `-U` / `--upgrade` | upgrade to newest allowed | “U = **Up**grade” |  
| `-r file` | install from **r**equirements | “read from file” |  
| `-c file` | apply **c**onstraints | “cap versions” |  
| `--no-deps` | don’t install dependencies | “I’ll handle deps” (use sparingly) |  
| `--pre` | allow pre-releases | “pre-release OK” |  
| `--user` | install to user site-packages | “no sudo” |  
| `--index-url URL` | primary package index | “where to look” |  
| `--extra-index-url URL` | additional index | “also look here” |  
| `--find-links PATH/URL` | scan local dir/URL for wheels | “look on disk, too” |  
| `--no-binary :all:` | force source builds | “build from src” |  
| `--no-build-isolation` | build using current env | “reuse my deps” |  
| `--root/--target PATH` | install into alt dir (packaging) | “custom dest” |

---

### Quick recipes

```bash
# New venv + must-have tools
python -m venv .venv && source .venv/bin/activate
pip install -U pip wheel setuptools

# Install specific versions and extras
pip install "numpy==1.26.*" "pandas>=2.2" "fastapi[all]"

# CUDA PyTorch (example; pick the right extra)
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121

# Lock and share
pip freeze > requirements.txt
# later
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# Repro with constraints
pip install -r requirements.txt -c constraints.txt
```

---

### Best practices (pip + conda together)

- In a **conda env**, install as many packages as possible with **conda** first; then use **pip** for what’s missing.
    
- If you mix, export with **both**:
    
    - Minimal env: maintain `env.yml` (conda deps + `pip:` section).
        
    - Exact lock (same platform): `conda list --explicit` **and** `pip freeze`.
        

---

### Troubleshooting patterns

```bash
# Wrong pip (points to different Python)?
which pip && python -m pip --version

# Broken deps after upgrades?
pip check

# Clean install
pip uninstall -y pkg && pip install pkg

# Build errors (missing headers)?
#   - install OS build deps (e.g., `build-essential`, `python3-dev`)
#   - prefer wheel if available: pip install --only-binary :all: pkg
```