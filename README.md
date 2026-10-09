# Reproduction: `prefix-dev/setup-pixi` `pixi-version` is never updated

`action.yml` (a composite action, which the `github-actions` manager scans like a workflow) pins `pixi-version: v0.73.0`. Renovate extracts it as `prefix-dev/pixi` (`github-releases` datasource, `conda` versioning), but the lookup always returns `updates: []`, although newer `prefix-dev/pixi` releases exist.

## Current behavior

```sh
RENOVATE_GITHUB_COM_TOKEN=$(gh auth token) LOG_LEVEL=debug \
  npx renovate --platform=local --dry-run=lookup
```

The `prefix-dev/pixi` dependency is reported with `"versioning": "conda"` and `"updates": []`.

## Expected behavior

An update to the latest `prefix-dev/pixi` release, keeping the `v` prefix that `pixi-version` requires.

## Cause

Conda versioning's `getMajor()` returns `null` for a `v`-prefixed version such as `v0.81.0`, so `getBucket()` returns `null` for every release and all of them are dropped. `prefix-dev/setup-pixi` only accepts `latest` or `vX.Y.Z` for `pixi-version`.
