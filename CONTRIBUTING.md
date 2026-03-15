# Contributing to pyside-navicube

Thanks for your interest. Here's how the project is set up and how to contribute without breaking things.

---

## Branch structure

```
main   — production. never commit directly here.
dev    — integration branch. all work lands here first.
```

Feature branches are cut from `dev` and merged back into `dev` via PR. When `dev` is stable and ready for a release, it gets merged into `main` via PR.

```
your-feature  →  dev  →  main
```

---

## Day-to-day workflow

```bash
# 1. Start from dev
git checkout dev
git pull origin dev

# 2. Cut a feature branch
git checkout -b feat/my-thing

# 3. Do your work, commit using conventional commits (see below)
git commit -m "feat: add Y-up coordinate mode"

# 4. Push and open a PR targeting dev
git push origin feat/my-thing
```

Open the PR against `dev`, not `main`. CI will run tests on all platforms automatically.

---

## Commit message format

This project uses [Conventional Commits](https://www.conventionalcommits.org/). The format is what drives automatic version bumps and changelog generation — so please follow it.

```
<type>: <short description>

[optional body]

[optional footer]
```

| Type | When to use | Version bump |
|---|---|---|
| `feat` | New feature or capability | Minor `0.x.0` |
| `fix` | Bug fix | Patch `0.0.x` |
| `perf` | Performance improvement | Patch |
| `docs` | Documentation only | None |
| `refactor` | Code change, no behaviour change | None |
| `test` | Adding or fixing tests | None |
| `ci` | CI/CD changes | None |
| `chore` | Maintenance, dependency updates | None |

For a **breaking change**, add `!` after the type or include `BREAKING CHANGE:` in the footer:

```
feat!: change push_camera() argument order

BREAKING CHANGE: dx/dy/dz now come before ux/uy/uz
```

Breaking changes trigger a major version bump.

---

## Releasing

You don't do this manually. Here's what happens automatically:

1. Every merge to `main` triggers [release-please](https://github.com/googleapis/release-please)
2. release-please opens (or updates) a **Release PR** that contains:
   - An updated `CHANGELOG.md` with everything since the last release
   - A version bump in `pyproject.toml`
3. When a maintainer merges that Release PR:
   - A GitHub Release is created with the changelog as release notes
   - The package is built and published to PyPI automatically

So the release flow is: merge `dev` → `main`, then merge the Release PR that appears.

---

## Merging dev into main

When `dev` has been tested and is ready to ship:

1. Open a PR from `dev` → `main`
2. Make sure CI passes
3. Get a review if possible (even self-review for solo projects)
4. Merge — this triggers release-please to create/update the Release PR
5. Review the Release PR (check the changelog looks right, version bump is correct)
6. Merge the Release PR — this publishes the release and uploads to PyPI

---

## Running tests locally

```bash
pip install -e ".[occ]" pytest pytest-qt

# headless on Linux
QT_QPA_PLATFORM=offscreen pytest tests/ -v

# or with a real display
pytest tests/ -v
```

---

## Project structure

```
navicube/
├── __init__.py          exports NaviCubeOverlay, NaviCubeStyle
├── widget.py            the actual widget — no OCC/VTK imports
└── connectors/
    ├── occ.py           OCCNaviCubeSync — bridges OCC V3d_View
    └── vtk.py           VTKNaviCubeSync — bridges VTK renderer
```

The core rule: `widget.py` must never import OCC, VTK, or any renderer. Connectors are the only place renderer imports live.
