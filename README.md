# conman-action

A GitHub Action that runs [`conman check --map`](https://github.com/bronsonhill/conman)
over your repository's Claude Code context stack and fails the job when the stack
goes over budget or trips a gated finding (byte-identical duplication,
conflicting values, stale `/init` boilerplate, dead `@`-imports).

It's a composite action: it sets up Node and shells out to
`npx @bronsonhill/conman`. No Docker image, nothing to pull, and the one command
it runs is visible in [`action.yml`](action.yml).

## Usage

```yaml
name: context

on: [pull_request]

permissions:
  contents: read

jobs:
  conman:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: bronsonhill/conman-action@v1
        with:
          path: .              # directory to scan (default ".")
          args: ""             # extra flags for `conman check --map`, e.g. "--budget 8000"
          version: latest      # npm version or dist-tag of @bronsonhill/conman
```

The minimal version:

```yaml
      - uses: actions/checkout@v4
      - uses: bronsonhill/conman-action@v1
```

### Inputs

| Input          | Default  | Description |
|----------------|----------|-------------|
| `path`         | `.`      | Directory to scan for context-stack entry points. |
| `args`         | *(empty)*| Extra flags passed through to `conman check --map` (`--budget <n>`, `--config <path>`, `--tokenizer <name>`, …). |
| `version`      | `latest` | npm version or dist-tag of `@bronsonhill/conman` to run. Pin this for reproducible CI. |
| `node-version` | `20`     | Node.js version set up before running conman. conman needs Node ≥ 20. |
| `sarif-file`   | *(empty)*| If set, also run a single-entry `conman check <path> --format sarif` and write the result here. See [SARIF](#sarif) below. |
| `upload-sarif` | `false`  | Upload `sarif-file` via `github/codeql-action/upload-sarif`. Ignored unless `sarif-file` is set. |

There are no outputs. The action's result is the exit code of `conman check
--map`: zero passes the step, non-zero fails it.

### SARIF

`conman check --map` has no SARIF form — SARIF is a single-entry feature. When
`sarif-file` is set, the action runs a second, single-entry
`conman check <path> --format sarif` and writes that document. This extra run is
best-effort and does **not** change whether the step passes; the `--map` gate
above it is authoritative. With `upload-sarif: true` the file is sent to
GitHub code scanning (needs `security-events: write` permission).

```yaml
      - uses: bronsonhill/conman-action@v1
        with:
          sarif-file: conman.sarif
          upload-sarif: true
```

## Versioning

Actions use a moving major tag. `bronsonhill/conman-action@v1` tracks the latest
`v1.x.y` release; the maintainer force-updates `v1` to each new release commit,
so `@v1` picks up fixes without a workflow edit. Semantic versions
(`v1.0.0`, `v1.2.3`) are immutable — use one when you want a frozen reference.

- `@v1` — recommended. Moves with `v1.x.y` releases, never across a breaking major.
- `@v1.0.0` — exact release, never moves.
- `@<full-sha>` — maximum paranoia; pin the commit.

Pin `version:` (the conman npm version) alongside whichever tag you choose —
`@v1` still lets a new conman release change the check's behaviour if `version`
is left at `latest`.

## Links

- [conman](https://github.com/bronsonhill/conman) — the tool this wraps: what the
  findings mean, the resolution model, the default budget numbers.
- [`conman check`](https://github.com/bronsonhill/conman#ci) — the CI command and
  its flags.
- [`action.yml`](action.yml) — every step this action runs.

## License

[MIT](LICENSE), matching conman.
