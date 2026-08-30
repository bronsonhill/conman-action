# Fixture context stack

This is a deliberately tiny `CLAUDE.md` used by the smoke test in
`.github/workflows/test.yml`. It stays well under conman's default 12k token
budget and trips no gated findings, so `conman check --map` exits 0 against it.

## Build

Run `npm ci` then `npm test`.
