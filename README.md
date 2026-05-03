# multicz-action

GitHub Action that installs [multicz](https://github.com/goabonga/multicz) — a
multi-component versioning tool for monorepos — and exposes every CLI
subcommand as a typed sub-action.

## Usage

The top-level action installs the `multicz` binary on `PATH` for the rest
of the job:

```yaml
- uses: goabonga/multicz-action@v1
- run: multicz --version
- run: multicz changed
```

### Pin a specific version

```yaml
- uses: goabonga/multicz-action@v1
  with:
    version: '0.4.0'
```

### Pick a Python version

```yaml
- uses: goabonga/multicz-action@v1
  with:
    python-version: '3.13'
```

### Inputs

| input            | description                                              | default  |
|------------------|----------------------------------------------------------|----------|
| `version`        | multicz version to install (PyPI specifier, or `latest`) | `latest` |
| `python-version` | Python version uv provides                               | `3.12`   |

## Sub-actions

Every multicz subcommand is also published as a standalone composite
action under `goabonga/multicz-action/<command>@v1`. Each one:

- auto-installs multicz if it's not already on `PATH` (so the top-level
  setup step is optional)
- accepts every CLI flag as a typed `with:` input
- exposes `outputs.stdout` (raw command output) and `outputs.json`
  (populated when called with `output: json` for commands that support
  it)
- accepts a `multicz-version` input (default `latest`) to pin the
  install fallback

| sub-action                                  | wraps                  | typical use                       |
|---------------------------------------------|------------------------|-----------------------------------|
| `goabonga/multicz-action@v1`                | `uv tool install multicz` | install multicz on PATH        |
| `goabonga/multicz-action/init@v1`           | `multicz init`         | scaffold a `multicz.toml`         |
| `goabonga/multicz-action/status@v1`         | `multicz status`       | brief pending bump summary        |
| `goabonga/multicz-action/plan@v1`           | `multicz plan`         | full bump plan with reasons       |
| `goabonga/multicz-action/state@v1`          | `multicz state`        | inspect the state file            |
| `goabonga/multicz-action/changed@v1`        | `multicz changed`      | matrix gating                     |
| `goabonga/multicz-action/artifacts@v1`      | `multicz artifacts`    | list publishable artifacts        |
| `goabonga/multicz-action/release-notes@v1`  | `multicz release-notes`| render release notes              |
| `goabonga/multicz-action/explain@v1`        | `multicz explain`      | why a component bumps             |
| `goabonga/multicz-action/bump@v1`           | `multicz bump`         | apply the plan, commit, tag, push |
| `goabonga/multicz-action/get@v1`            | `multicz get`          | read a current version            |
| `goabonga/multicz-action/changelog@v1`      | `multicz changelog`    | per-component log                 |
| `goabonga/multicz-action/validate@v1`       | `multicz validate`     | config + repo sanity check        |
| `goabonga/multicz-action/check@v1`          | `multicz check`        | conventional-commits commit-msg hook |

Boolean inputs are passed as the strings `'true'` / `'false'`. Repeatable
flags (such as `--component` or `--force` on `bump`) take a multi-line
input — one value per line.

## Example: gate a CI matrix on changed components

```yaml
jobs:
  detect:
    runs-on: ubuntu-latest
    outputs:
      list: ${{ steps.list.outputs.list }}
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 0

      - id: changed
        uses: goabonga/multicz-action/changed@v1
        with:
          output: json

      - id: list
        env:
          CHANGED_JSON: ${{ steps.changed.outputs.json }}
        run: |
          echo "list=$(echo "$CHANGED_JSON" | jq -c '.changed')" >> "$GITHUB_OUTPUT"

  build:
    needs: detect
    if: needs.detect.outputs.list != '[]'
    strategy:
      matrix:
        component: ${{ fromJson(needs.detect.outputs.list) }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - run: echo "building ${{ matrix.component }}"
```

## Example: bump, tag, push

```yaml
- uses: actions/checkout@v6
  with:
    fetch-depth: 0
- uses: goabonga/multicz-action/bump@v1
  with:
    commit: 'true'
    tag: 'true'
    push: 'true'
```

Add `sign: 'true'` (and configure `GPG_PRIVATE_KEY` + `GPG_PASSPHRASE`
secrets at the workflow level) to produce a GPG-signed release commit
and tag.

## Example: explain why a component is bumping

```yaml
- uses: goabonga/multicz-action/explain@v1
  with:
    component: api
```

## Versioning

This action follows the standard GitHub Actions versioning scheme:

- `goabonga/multicz-action@v1` — floating major tag, gets new features
  and fixes within `v1.x.x`.
- `goabonga/multicz-action@v1.2.3` — pinned to an exact version.
- `goabonga/multicz-action@<sha>` — pinned to a specific commit (most
  reproducible, recommended for security-sensitive use cases).

## Documentation

The multicz CLI itself is documented at
<https://goabonga.github.io/multicz/>.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Bug reports and feature requests
go through GitHub issues using the templates in
`.github/ISSUE_TEMPLATE/`.

## License

[MIT](LICENSE).
