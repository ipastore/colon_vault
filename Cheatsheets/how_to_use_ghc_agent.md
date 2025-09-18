https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/customize-the-agent-environment

## Where (and how) to learn GitHub Actions fast

- **Docs home (best starting point):** high-level overview + links to quickstart, workflow syntax, runners, etc. [GitHub Docs](https://docs.github.com/actions?utm_source=chatgpt.com)
    
- **Quickstart:** create your first workflow in minutes. [GitHub Docs](https://docs.github.com/en/actions/get-started/quickstart?utm_source=chatgpt.com)
    
- **Workflow syntax:** _the_ reference for YAML (events, jobs, steps, `runs-on`, `permissions`, etc.). Store files in `.github/workflows/*.yml`. [GitHub Docs](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)
    
- **GitHub-hosted runners:** understand the ephemeral VMs that run your jobs (what’s preinstalled, lifecycle). [GitHub Docs](https://docs.github.com/actions/using-github-hosted-runners/about-github-hosted-runners?utm_source=chatgpt.com)
    
- **`actions/checkout` (checks out repo & submodules):** options like `submodules: recursive`, `lfs: true`, `fetch-depth`. [GitHub](https://github.com/actions/checkout)
    
- **`actions/setup-python`:** pick a Python version and optionally cache pip deps. [GitHub](https://github.com/actions/setup-python?utm_source=chatgpt.com)[GitHub Docs](https://docs.github.com/actions/guides/building-and-testing-python?utm_source=chatgpt.com)
    
- **GitHub Skills (hands-on, free):** interactive, repo-based mini-courses (including Actions). [skills.github.com](https://skills.github.com/?utm_source=chatgpt.com)[GitHub](https://github.com/skills?utm_source=chatgpt.com)
    
- **Microsoft Learn – Actions learning path:** structured, step-by-step courses. [Microsoft Learn+2Microsoft Learn+2](https://learn.microsoft.com/en-us/training/paths/github-actions/?utm_source=chatgpt.com)
## Guide 

Put this file at:

```
.your-repo/.github/workflows/copilot-setup-steps.yml
```

### Complete YAML

```yaml
# .github/workflows/copilot-setup-steps.yml
name: Copilot Setup Steps

on:
  # Allow manual runs to validate the setup, and auto-run when this file changes
  workflow_dispatch:
  push:
    paths: [.github/workflows/copilot-setup-steps.yml]
  pull_request:
    paths: [.github/workflows/copilot-setup-steps.yml]

jobs:
  # IMPORTANT: this job id must be exactly 'copilot-setup-steps'
  # Copilot will run these steps before the agent starts.
  copilot-setup-steps:
    runs-on: ubuntu-latest
    permissions:
      contents: read  # required for actions/checkout

    steps:
      - name: Checkout repository (with submodules; LFS OFF)
        uses: actions/checkout@v5
        with:
          submodules: recursive   # pull IMM + nested submodules
          # lfs: false  # (default is false; omitted intentionally)
          fetch-depth: 0          # full history (helps some tools)

      - name: Select Python toolchain
        uses: actions/setup-python@v5
        with:
          python-version: "3.10"
          cache: "pip"

      # Keep LFS disabled during setup to avoid heavy model downloads
      - name: Disable LFS smudge (do not auto-download LFS objects)
        run: git lfs install --skip-smudge || true

      - name: Sync and initialize submodules to recorded SHAs
        run: |
          git submodule sync --recursive
          git submodule update --init --recursive

      - name: Install Python dependencies (root project)
        run: |
          python -m pip install -U pip setuptools wheel
          pip install .


      # Optional: only enable if you see missing system libs at runtime
      - name: (Optional) Install system libs for OpenCV/Open3D headless
        if: ${{ false }}  # flip to false if needed
        run: |
          sudo apt-get update
          sudo apt-get install -y libgl1 libglib2.0-0
```

#### Why this works (and where it lives)

- The workflow **must** be named `.github/workflows/copilot-setup-steps.yml`, and the job id **must** be `copilot-setup-steps`; Copilot runs it **before** the agent session begins. Only a limited set of job fields are honored (steps, permissions, runs-on, container, services, snapshot, timeout-minutes). ([GitHub Docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/customize-the-agent-environment?utm_source=chatgpt.com "About customizing Copilot coding agent's development ..."))
    
- `actions/checkout` with `submodules: recursive` pulls `IMM` and its nested submodules; we **do not** set `lfs: true`, so LFS stays off. ([GitHub](https://github.com/actions/checkout?utm_source=chatgpt.com "actions/checkout: Action for checking out a repo"))
    
- `actions/setup-python` pins Python 3.10 and enables pip caching for speed. ([GitHub](https://github.com/actions/setup-python?utm_source=chatgpt.com "actions/setup-python: Set up your ..."), [GitHub Docs](https://docs.github.com/actions/guides/building-and-testing-python?utm_source=chatgpt.com "Building and testing Python"))
    
- `git lfs install --skip-smudge` explicitly disables auto-download of LFS objects on checkout/pull (you can later fetch exactly what you need with `git lfs pull`). ([manpages.debian.org](https://manpages.debian.org/testing/git-lfs/git-lfs-smudge.1.en.html?utm_source=chatgpt.com "git-lfs-smudge(1) — git-lfs — Debian testing"), [sabicalija.github.io](https://sabicalija.github.io/git-lfs-intro/?utm_source=chatgpt.com "git-lfs tutorial"))
    
- GitHub-hosted runners are **ephemeral Ubuntu VMs** with common tools preinstalled; your workflow adds only what you need. ([GitHub Docs](https://docs.github.com/actions/using-github-hosted-runners/about-github-hosted-runners?utm_source=chatgpt.com "GitHub-hosted runners"))