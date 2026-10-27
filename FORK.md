# FORK.md — divergences from upstream

Personal fork of [excalidraw/excalidraw](https://github.com/excalidraw/excalidraw). Every change that diverges from upstream is recorded here so that `scripts/sync-upstream.fish` (Phase 6) and future rebases stay cheap.

## Branch model

| Branch | Purpose |
| --- | --- |
| `master` | Mirror of `upstream/master`. Never commit here. (Upstream's default branch is `master`, not `main`.) |
| `custom` | All of my work. Rebased/merged on top of `master`. |

## Remotes

| Remote | URL | Notes |
| --- | --- | --- |
| `upstream` | https://github.com/excalidraw/excalidraw.git | read-only source of truth |
| `origin` | _not configured yet_ | add after creating the fork on GitHub (see below) |

Creating the fork was blocked by the Claude Code permission classifier in the first session, so it has to be done by the owner:

```fish
gh repo fork excalidraw/excalidraw --remote=false --clone=false
git remote add origin https://github.com/<user>/excalidraw.git
git push -u origin custom
```

## Policy

- New code goes into **own modules** (`packages/ai-server/`, `excalidraw-app/local/`, `excalidraw-app/ai/`, `desktop/`, ...) whenever possible.
- Patches to upstream files must be **minimal** (flag checks, one-line hooks) and listed in the table below with file, reason and how to re-apply.
- Behaviour toggles are controlled by `VITE_APP_*` env flags, defaults live in `.env.development.local` / `.env.production.local` (not committed secrets).

## Divergence log

| # | Phase | Type | Files | What / why |
| --- | --- | --- | --- | --- |
| 0 | 0 | docs | `FORK.md`, `CLAUDE.md` | Fork bookkeeping and project guide for Claude Code. |

### Upstream files patched (keep this list as short as possible)

- `CLAUDE.md` — appended a "Personal fork" section at the end (upstream text untouched). On conflict, keep upstream's part and re-append ours.

### Own modules (no conflict risk)

_none yet_

## Baseline (before any change)

See the "Baseline" section at the bottom of this file once Phase 0 checks finish.

## Baseline (upstream `14bb42ed`, Node v24.21.0, yarn 1.22.22)

Measured on a clean install before any change:

| Check | Result |
| --- | --- |
| `yarn test:typecheck` | pass (~16s) |
| `yarn test:code` (eslint) | pass, 0 warnings (~31s) |
| `yarn test:other` (prettier) | pass |
| `yarn test:app` | 149 files, 2632 passed / 47 skipped / 1 todo, 0 failed (~105s) |
| dev server | opens; port 3001 was busy so Vite used 3002 |

Known noise (not failures): `DEP0169 url.parse()` warning, a jsx-ast-utils `MetaProperty` message from eslint, and `Error JSON parsing firebase config` printed by `excalidraw-app/tests/firebase.test.ts` (test env has no Firebase config).
