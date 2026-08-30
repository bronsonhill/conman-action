# Project agent memory

This file is the project's committed home for project-intrinsic agent knowledge: build, test, release, architecture, and sharp-edge notes that should travel with the code.

- This repo is a single composite GitHub Action: [`action.yml`](action.yml) at the root wraps `npx @bronsonhill/conman check --map`. There is no build step and no `package.json`.
- Smoke tests live in [`.github/workflows/test.yml`](.github/workflows/test.yml) and run the action via `uses: ./` against `test/fixture/` (must pass) and `test/fixture-overbudget/` (must fail — it ships a `conman.json` with a 200-token budget). They pull `@bronsonhill/conman` straight from npm via the `CONMAN_VERSION` env var.
- Behaviour of the check itself is conman's; see https://github.com/bronsonhill/conman.
- Release convention: semver tag `vX.Y.Z` plus a force-moved major tag `vX` (`git tag -f v1`). Never delete the major tag; force-update it to each release commit.

## Maintaining this file

Keep this file for knowledge useful to almost every future agent session in this project.
Do not repeat what the codebase already shows; point to the authoritative file or command instead.
Prefer rewriting or pruning existing entries over appending new ones.
When updating this file, preserve this bar for all agents and keep entries concise.
