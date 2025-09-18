# Conda Cheatsheet (Obsidian-ready)

### Meaning
**conda**: cross-platform **con**tinuous **da**ta env & package manager (envs + packages + channels)

### Function
Create isolated environments, install/update packages from multiple channels (e.g., `conda-forge`), and export/recreate envs. Can coexist with **pip** inside each env.

---

### Main commands (daily flow)
```bash
# Create & activate an environment
conda create -n myenv python=3.11
conda activate myenv

# Install / remove packages (in current env)
conda install numpy pandas
conda remove package_name

# Update (current env)
conda update package_name
conda update --all

# List things
conda info --envs           # all envs
conda list                  # packages in current env
conda search <name>         # search package across channels

# Export / recreate environment
conda env export --from-history > env.yml
conda env create -f env.yml
````

---

### Other useful commands

```bash
# Create env from specific channel(s)
conda create -n geo -c conda-forge gdal

# Add/inspect channel config (user-level)
conda config --add channels conda-forge
conda config --set channel_priority strict
conda config --show channels

# Install in a non-active env
conda install -n myenv scikit-image

# Create env by cloning / renaming (clone ≈ rename)
conda create --name newenv --clone oldenv

# Remove an environment completely
conda deactivate
conda env remove -n myenv

# Export "full lock" (explicit build strings for perfect reproducibility)
conda list --explicit > explicit.txt
# Recreate from explicit file (same platform)
conda create -n pinned --file explicit.txt

# Revision history (time travel)
conda list --revisions
conda install --revision 3   # roll back to rev 3

# Clean caches (disk space / solver speed)
conda clean -a -y            # tarballs, pkg cache, index cache

# Update conda itself
conda update -n base -c defaults conda

# Use YAML with pinned Python & key deps (recommended)
# env.yml:
# name: proj
# channels: [conda-forge]
# dependencies:
#   - python=3.11
#   - numpy
#   - pytorch
#   - pip
#   - pip:
#       - your_pypi_pkg==1.2.3
conda env create -f env.yml
conda env update -f env.yml --prune
```

---

### Conda + pip (best practice)

1. **Prefer conda** packages first (faster solves, compiled wheels).
2. If a package isn’t on conda (or you need a newer one), **install pip inside the env**:

```bash
conda activate myenv
conda install pip
pip install package_not_in_conda
```

3. **Export for sharing**:

- Minimal, portable spec (human-authored): `conda env export --from-history > env.yml`
- Exact rebuild on same platform: `conda list --explicit > explicit.txt`

> Tip: Put pip deps under the `pip:` section in `env.yml` so `conda env create -f env.yml` installs them automatically.

---

### Channels & priority (stability)

```bash
# One-time setup (recommended)
conda config --add channels conda-forge
conda config --set channel_priority strict
```

- **strict**: packages are chosen from the highest-priority channel that can satisfy all deps → fewer mixed builds, more reproducible

---

### Troubleshooting patterns

```bash
# Solver stuck or conflicts?
conda clean -a -y
conda update -n base -c defaults conda
conda config --show channels
conda create -n test -c conda-forge python=3.11 <pkg>  # try fresh env

# Package exists but wrong channel?
conda search -c conda-forge <pkg>
conda install -c conda-forge <pkg>

# Need to revert a bad update?
conda list --revisions
conda install --revision <N>
```

---

### Memory hooks for common options

| Flag / Word | Meaning | Hook |  
|---:|------------------|------------------------------------|---------------------------------------|  
| `-n/--name` | target environment by **name** | **n**ame |  
| `-p/--prefix` | target env by path | **p**refix path |  
| `-c/--channel` | use a specific **c**hannel | “where to get from” |  
| `--yes` / `-y` | auto-confirm | “say **y**es to prompts” |  
| `--freeze-installed` | don’t upgrade installed packages | “freeze what I have” |  
| `--no-deps` | don’t resolve dependencies | “I know what I’m doing” (use sparingly) |  
| `--prune` | remove deps no longer required | “prune dead leaves” |  
| `env export` | write YAML spec of env | “share the recipe” |  
| `list --explicit` | exact rebuild file | “bit-for-bit pin” |

---

### Quick recipes

```bash
# New project env (conda-forge, strict priority)
conda config --add channels conda-forge
conda config --set channel_priority strict
conda create -n proj python=3.11 numpy scipy matplotlib -y
conda activate proj

# Add CUDA PyTorch from conda-forge (example)
conda install -c conda-forge pytorch torchvision pytorch-cuda=12.1

# Mix in a PyPI-only package
conda install pip
pip install some-rare-lib

# Share minimal spec (portable)
conda env export --from-history > env.yml

# Recreate & sync exactly to YAML (remove stray pkgs)
conda env update -n proj -f env.yml --prune
```

---

### Bonus: mamba (faster solver, drop-in)

If installed, you can swap `conda` → `mamba` for most commands:

```bash
mamba create -n proj python=3.11
mamba install -n proj pytorch -c conda-forge
```

Memory hook: **mamba** = **con**da’s faster **solver** (same env format).

# Conda command history

#### Export with or wout history
```bash
conda env export --from-history > conda_env_requierements.yml
conda env export > conda_env_requierements_strict.yml
```

**Memory hook**
- **Export (full)** = “give me everything.”
- **Export from-history** = “just give me packages you installed it with the env or with conda install”

#### Conda envs are stacked
If I activate sequentially, when I deactivate is going to fall in the last activated env. 

**How to check?**

```bash
echo $CONDA_PREFIX       # current active env path (empty = none)
conda info --envs        # all envs, `*` marks the active one
which python             # which Python you’re actually using
```




