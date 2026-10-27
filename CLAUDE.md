# CLAUDE.md

## Project Structure

Excalidraw is a **monorepo** with a clear separation between the core library and the application:

- **`packages/excalidraw/`** - Main React component library published to npm as `@excalidraw/excalidraw`
- **`excalidraw-app/`** - Full-featured web application (excalidraw.com) that uses the library
- **`packages/`** - Core packages: `@excalidraw/common`, `@excalidraw/element`, `@excalidraw/math`, `@excalidraw/utils`
- **`examples/`** - Integration examples (NextJS, browser script)

## Development Workflow

1. **Package Development**: Work in `packages/*` for editor features
2. **App Development**: Work in `excalidraw-app/` for app-specific features
3. **Testing**: Always run `yarn test:update` before committing
4. **Type Safety**: Use `yarn test:typecheck` to verify TypeScript

## Development Commands

```bash
yarn test:typecheck  # TypeScript type checking
yarn test:update     # Run all tests (with snapshot updates)
yarn fix             # Auto-fix formatting and linting issues
```

## Architecture Notes

### Package System

- Uses Yarn workspaces for monorepo management
- Internal packages use path aliases (see `vitest.config.mts`)
- Build system uses esbuild for packages, Vite for the app
- TypeScript throughout with strict configuration

---

## Personal fork (read first)

This is a personal fork. See `FORK.md` for the divergence log; **every change to an upstream file must be recorded there**. Everything above this line is upstream's and should not be edited, to keep `scripts/sync-upstream.fish` conflict-free.

- Language: answer in Portuguese; code, commits and comments in English.
- Environment: CachyOS, KDE Wayland, fish shell. No bash-only syntax in scripts meant for the user; no `sudo` / `pacman -S` without asking first.
- Branches: `master` mirrors `upstream/master` (never commit there); all work goes on `custom`. Remote `upstream` = excalidraw/excalidraw; `origin` = my fork (may not exist yet).
- Commits: small, conventional commits. Run typecheck + lint + tests first. Never commit secrets (`.env*` with keys; only `.env.example`).

### Commands

```bash
yarn install --frozen-lockfile   # yarn 1.22 via corepack (shim in ~/.local/bin)
yarn start                       # dev server (vite, default port 3001; picks next free one)
yarn test:typecheck              # tsc
yarn test:code                   # eslint --max-warnings=0
yarn test:other                  # prettier --list-different (css/scss/json/md/html/yml)
yarn test:app --watch=false      # vitest, single run (~105s)
yarn fix                         # prettier --write + eslint --fix
yarn build                       # production build of excalidraw-app
```

### Where things live

- Editor core: `packages/excalidraw/components/App.tsx` (huge class component), actions in `packages/excalidraw/actions/`, data/IO in `packages/excalidraw/data/`.
- App shell (excalidraw.com specifics): `excalidraw-app/` — `App.tsx`, `collab/` (socket.io), `data/firebase.ts`, `sentry.ts`, `components/AI.tsx`.
- AI seam: `excalidraw-app/components/AI.tsx` wires `<TTDDialog onTextSubmit>` and `<DiagramToCodePlugin generate>` to `VITE_APP_AI_BACKEND` over SSE (`packages/excalidraw/components/TTDDialog/utils/TTDStreamFetch.ts`).
- Env: `.env.development` / `.env.production` (committed upstream defaults); override locally with `.env.development.local` / `.env.production.local`.
