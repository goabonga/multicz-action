# Contributing to multicz-action

Thanks for contributing. This action is a thin composite wrapper around the
[multicz](https://github.com/goabonga/multicz) CLI; most changes are to
`action.yml` itself or to the workflows that test it.

## Setup

```bash
git clone https://github.com/goabonga/multicz-action
cd multicz-action
```

There is no language runtime, no build step — just YAML and a small bit of
shell inside the action.

## Testing

The action is tested by being used in `.github/workflows/ci.yml` against the
running PR. Push your branch, open a PR, and the workflow will exercise the
action end-to-end.

For local sanity checking, [act](https://github.com/nektos/act) can run the
workflow on your laptop:

```bash
act -W .github/workflows/ci.yml
```

If you change the shell logic inside `action.yml`, also run [shellcheck](https://www.shellcheck.net/)
or paste the script into [shellcheck.net](https://www.shellcheck.net/) for a
quick review before opening the PR.

## Branch naming

Use `<type>/<kebab-description>`, where `<type>` matches the conventional
commit type the work will produce:

| type        | example                                |
|-------------|----------------------------------------|
| `feat/`     | `feat/input-cache-key`                 |
| `fix/`      | `fix/version-pin-quoting`              |
| `docs/`     | `docs/readme-clarify-versioning`       |
| `ci/`       | `ci/test-on-windows-runner`            |
| `chore/`    | `chore/bump-setup-uv`                  |

## Commit messages

[Conventional Commits](https://www.conventionalcommits.org/) — multicz reads
them to drive its own release pipeline (and this action is versioned with
multicz).

| prefix                                      | bump  |
|---------------------------------------------|-------|
| `fix:`, `perf:`                             | patch |
| `feat:`                                     | minor |
| `feat!:` / `fix!:` / `BREAKING CHANGE:`     | major |
| `docs:`, `chore:`, `refactor:`, `test:`, `ci:`, `build:` | none |

Guidelines:

- One logical change per commit.
- Imperative mood: "add", "fix", "remove" — not "added", "fixes", "removing".
- No trailing period on the summary.
- No `Co-Authored-By` footers unless the work was actually pair-authored.

## Pull requests

- PR title follows Conventional Commits.
- One PR per logical change.
- Update `README.md` if you add or rename an input.
- The CI workflow must pass before merge.

## Code of Conduct

By participating, you agree to abide by the [Code of Conduct](CODE_OF_CONDUCT.md).
Report incidents to <goabonga@pm.me>.

## Security

Found a vulnerability? Don't open a public issue. See
[`.github/SECURITY.md`](.github/SECURITY.md) for the disclosure process.

## License

By contributing, you agree that your contributions will be licensed under
the project's [MIT license](LICENSE).
