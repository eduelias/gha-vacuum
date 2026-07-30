# Contributing to gha-vacuum

Thanks for your interest in improving the action! It is deliberately tiny — a
single composite action ([`action.yaml`](action.yaml)) with two bash steps —
and we would like to keep it that way.

## Ground rules

- Keep it minimal. The action installs vacuum and runs the user's command;
  resist adding wrapper options that duplicate vacuum's own flags.
- Every input documented in `README.md` must exist verbatim in `action.yaml`,
  and vice versa. If you change one, change both.
- Don't pin or vendor vacuum here — installation is delegated to the official
  install script so users always get the latest release.

## Repo layout

```
action.yaml   # the composite action (this is the whole product)
README.md     # user-facing docs
LICENSE       # MIT
```

## Testing your changes

There is no test suite — the action is exercised by running it.

### Locally with act

[`act`](https://github.com/nektos/act) runs GitHub workflows in Docker and
supports composite actions referenced with `uses: ./`. Add a throwaway
workflow, e.g. `.github/workflows/test.yml`:

```yaml
name: Test action
on: push
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ./
        with:
          cmd: vacuum lint -d test/petstore.yaml
```

Then, with a sample spec at `test/petstore.yaml`:

```bash
act push
```

Note: the install step downloads vacuum from quobix.com, so the container
needs network access.

### Manually

The action is two shell commands; you can reproduce them directly:

```bash
curl -fsSL https://quobix.com/scripts/install_vacuum.sh | sh
vacuum lint -d your-spec.yaml
```

If your change to `action.yaml` works when replayed this way and via `act`, it
will work on a runner.

### On GitHub

For the final check, push your branch to a fork and run a workflow that
references the action by your branch:

```yaml
uses: <your-fork>/gha-vacuum@<your-branch>
```

## Making changes

1. Create a branch: `git checkout -b feat/short-description`.
2. Edit `action.yaml`; update `README.md` if inputs or behaviour change.
3. Test with `act` or a fork workflow (see above).
4. Keep commits focused with a clear message.
5. Open a pull request against `main` and describe how you tested it.

## Releasing

Releases are git tags (e.g. `v0.0.1`). Users pin the action by tag, so tag
only tested states of `main`.

## Reporting bugs

Open an issue with the workflow snippet you used (the `with: cmd:` line), the
runner OS, and the relevant log output. Issues in vacuum itself belong
upstream at [daveshanley/vacuum](https://github.com/daveshanley/vacuum).
