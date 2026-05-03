# multicz-action

GitHub Action that installs [multicz](https://github.com/goabonga/multicz) — a
multi-component versioning tool for monorepos — and makes it available on
`PATH` for subsequent steps in the same job.

## Usage

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

## Inputs

| input            | description                                          | default   |
|------------------|------------------------------------------------------|-----------|
| `version`        | multicz version to install (PyPI specifier, or `latest`) | `latest`  |
| `python-version` | Python version uv provides                           | `3.12`    |

## Example: gate a CI matrix on changed components

```yaml
jobs:
  detect:
    runs-on: ubuntu-latest
    outputs:
      list: ${{ steps.changed.outputs.list }}
    steps:
      - uses: actions/checkout@v6
        with:
          fetch-depth: 0
      - uses: goabonga/multicz-action@v1
      - id: changed
        run: |
          LIST=$(multicz changed --output json | jq -c '.changed')
          echo "list=$LIST" >> "$GITHUB_OUTPUT"

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

## Example: bump and push tags

```yaml
- uses: actions/checkout@v6
  with:
    fetch-depth: 0
- uses: goabonga/multicz-action@v1
- run: multicz bump --commit --tag
- run: git push --follow-tags
```

## Versioning

This action follows the standard GitHub Actions versioning scheme:

- `goabonga/multicz-action@v1` — floating major tag, gets new features and
  fixes within `v1.x.x`.
- `goabonga/multicz-action@v1.2.3` — pinned to an exact version.
- `goabonga/multicz-action@<sha>` — pinned to a specific commit (most
  reproducible, recommended for security-sensitive use cases).

## Documentation

The multicz CLI itself is documented at
<https://goabonga.github.io/multicz/>.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Bug reports and feature requests go
through GitHub issues using the templates in `.github/ISSUE_TEMPLATE/`.

## License

[MIT](LICENSE).
