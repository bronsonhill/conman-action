# Over-budget fixture

This directory ships a `conman.json` that sets `budget.total` to 200 tokens. The
prose below is longer than that on purpose, so `conman check --map` resolves the
stack, measures it over the gated budget, and exits non-zero. The negative smoke
test in `.github/workflows/test.yml` asserts that failure.

## Build

Run `npm ci`, then `npm run build`, then `npm test`. The build compiles
TypeScript from `src/` into `dist/`. Tests run against the compiled output, not
the sources, so a stale `dist/` will pass tests that should fail.

## Deploy

Deploys go out from `main` only. Tag the release, push the tag, and the release
workflow publishes the package and cuts a GitHub release with the changelog
section for that version. Never deploy from a feature branch. Never force-push
`main`. If a deploy half-succeeds, roll forward with a new patch rather than
reverting the tag.

## Style

Two-space indent. No semicolons where the parser does not need them. Prefer
named exports. Keep functions under fifty lines. Write comments that explain why,
not what. Match the surrounding code when in doubt, and leave the file cleaner
than you found it.

## Testing

Every bug fix gets a regression test that fails before the fix and passes after.
Unit tests sit next to the code they cover. Integration tests live under
`test/`. Do not mock what you do not own; wrap it and mock the wrapper instead.
