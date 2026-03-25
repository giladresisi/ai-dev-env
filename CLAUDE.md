# ai-dev-env Project Instructions

## Version Management

**Before every `git push`, ensure the version in `.claude-plugin/plugin.json` has been bumped relative to `origin/main`.**

Steps:
1. Run `git fetch origin main && git show FETCH_HEAD:.claude-plugin/plugin.json` to get the remote version (note: `origin/main:<path>` doesn't work on Windows — use `FETCH_HEAD:<path>` instead)
2. Compare it to the local version in `.claude-plugin/plugin.json`
3. If they are the same, bump the local patch version (e.g. `1.1.4` → `1.1.5`), then commit with message `chore: bump version to X.Y.Z`
4. If the local version is already higher, skip the bump — it was included in the commit set
5. Then proceed with the push
