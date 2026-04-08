# CLAUDE.md

@AGENTS.md

## Claude-Specific Tips

- **`/release [patch|minor|major]`** — Bumps version across all plugin config files, commits, tags, and pushes. The Auto Release CI workflow then builds `.skill` artifacts and creates the GitHub release. Preferred over the GitHub Actions UI for local development.
