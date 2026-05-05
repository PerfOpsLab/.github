## What

<!-- One paragraph: what this PR changes. -->

## Why

<!-- Business/technical motivation. Link to Linear ticket if any: YAP-NNN. -->

## Verification

<!-- Commands actually run + their outcome. See AGENTS.md per-language reference. -->

- [ ] Lint / format (`gofmt -l .`, `npm run lint`, `ruff check .`)
- [ ] Unit tests (`go test -race -count=1 ./...`, `npm test`, `pytest`)
- [ ] Integration / smoke (`npm run check`, `k6 run --vus 1 --duration 10s …`)
- [ ] Security gate passes (gitleaks, `govulncheck`, `npm audit`)

## Risk / blast radius

<!-- Reversible? Production-affecting? VPS, DNS, or shared service touched? -->

## Notes for reviewers

<!-- Anything specific to look at. Skipped intentionally? -->
