---
name: create-worktree
description: Use when creating a git worktree for feature isolation with automatic environment setup including .env files and dependency installation
---

# Create Worktree with Environment Setup

When this command is invoked with `/create-worktree <branch-name> [base-branch] [path=<folder>]`, execute the following steps:

## Step 1: Parse Arguments

Extract the branch name, optional base branch, and optional path override from the command arguments.

Derive the **worktree folder name**:
- If an explicit `path=<folder>` argument was provided, use that.
- Otherwise, use the **last segment** of the branch name (the part after the final `/`).
  - `autoresearch/multisector-mar22` → `multisector-mar22`
  - `feature/auth` → `auth`
  - `feature-auth` → `feature-auth` (no slash, use as-is)

The worktree will be created at `../{folder}`. The git branch is still created as `{branch-name}` (preserving the full hierarchical name).

## Step 2: Create Git Worktree

Execute the git worktree add command:

```bash
# If base-branch provided:
git worktree add ../{folder} -b {branch-name} {base-branch}

# If no base-branch provided:
git worktree add ../{folder} -b {branch-name}
```

The worktree will be created in the parent directory at `../{folder}`.

## Step 3: Identify Source and Target Paths

- **Source:** Current working directory (the original repo)
- **Target:** `../{folder}` (the new worktree)

## Step 4: Copy .env Files

Use Bash to find and copy all .env files. Because .env files are gitignored, Glob cannot see them — use `find` to discover them recursively:

```bash
# Find and copy all .env* files, excluding tooling directories
find . -maxdepth 6 \( -name ".env" -o -name ".env.local" -o -name ".env.*" \) \
  ! -path "*/.venv/*" ! -path "*/node_modules/*" ! -path "*/.git/*" | while read env_file; do
  env_file="${env_file#./}"
  target_dir=$(dirname "../{folder}/$env_file")
  mkdir -p "$target_dir"
  cp "$env_file" "../{folder}/$env_file"
  echo "Copied $env_file"
done
```

This approach:
1. Discovers all `.env`, `.env.local`, and `.env.*` files at any depth (up to 6 levels)
2. Excludes `.venv`, `node_modules`, and `.git` directories
3. Creates target subdirectories as needed with `mkdir -p`
4. Works with gitignored files that Glob cannot see

## Step 5: Install Python Dependencies

Check which Python package manager the project uses, then install dependencies in the worktree:

**If project uses `uv`** (has `pyproject.toml` + `uv.lock`):
- Run `uv sync` — recreates `.venv` from the lockfile, no copying needed

```bash
# uv projects — run in the worktree backend directory
cd ../{folder}/backend
uv sync
```

**If project uses `pip`** (has `requirements.txt`):
- Copy the existing venv, then run pip install

```bash
# Copy venv (all platforms)
cp -r backend/venv ../{folder}/backend/venv

# Install dependencies
# Windows
cd ..\{folder}\backend && venv\Scripts\python -m pip install -r requirements.txt
# Unix/Mac
cd ../{folder}/backend && venv/bin/python -m pip install -r requirements.txt
```

Check both top-level and `backend/` for `pyproject.toml`/`uv.lock` or `requirements.txt`.

## Step 7: Install npm Dependencies

Use Glob to find all `package.json` files in:
- Top-level: `package.json`
- Frontend: `frontend/package.json`
- Backend: `backend/package.json`

For each package.json found:
1. Navigate to the target worktree directory containing the package.json
2. Run npm install

```bash
# Windows
cd ..\{folder}\frontend
npm install

# Unix/Mac
cd ../{folder}/frontend
npm install
```

**Important:** Install npm dependencies in ALL directories that have a package.json file (top-level, frontend/, backend/, or any other subdirectories).

## Step 8: Report Completion

Provide a summary to the user:
- Worktree created at: `../{folder}`
- .env files copied: [list]
- Python dependencies installed in: [list] (method: uv sync / pip install)
- npm dependencies installed in: [list]

## Platform Detection

Detect the platform using:
```bash
# Check if Windows (look for .bat or .ps1 files, or check OS)
uname -s  # Returns "MINGW64_NT" or similar on Windows Git Bash
```

Use appropriate commands (copy vs cp, robocopy vs cp -r, Scripts vs bin) based on platform.

## Error Handling

- If git worktree add fails, stop and report error
- If .env copy fails, warn but continue
- If uv sync / pip install fails, warn but continue
- If npm install fails, warn but continue
- Always report which steps succeeded and which failed

## Example Execution

User runs: `/create-worktree feature-auth main`

Expected output (branch has no `/`, so folder = branch name):
```
Creating worktree 'feature-auth' based on 'main'...
✓ Worktree created at ../feature-auth

Copying .env files...
✓ Copied backend/.env
✓ Copied frontend/.env

Installing Python dependencies...
✓ uv sync in backend/ (recreated .venv from uv.lock)

Installing npm dependencies...
✓ Installed frontend/package.json (127 packages)
✓ Installed backend/package.json (43 packages)

Worktree setup complete! You can now work in ../feature-auth
```

User runs: `/create-worktree autoresearch/multisector-mar22 master`

Expected output (last segment of branch name used as folder):
```
Creating worktree 'autoresearch/multisector-mar22' based on 'master'...
✓ Worktree created at ../multisector-mar22
  (branch: autoresearch/multisector-mar22, folder: multisector-mar22)
...
Worktree setup complete! You can now work in ../multisector-mar22
```
