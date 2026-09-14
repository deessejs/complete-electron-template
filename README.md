# Complete Electron Template

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Latest Release](https://img.shields.io/github/v/release/deessejs/complete-electron-template)](https://github.com/deessejs/complete-electron-template/releases)

**Production-ready desktop app starter.** Electron 35 · React 19 · TanStack Router · oRPC · Drizzle · Tailwind v4 · Ship a desktop app in minutes.

A senior-grade Electron monorepo: type-safe IPC over MessagePort, a factory-pattern database with auto-migrations, and a strict CI matrix where every workflow does exactly one thing. Two deployable surfaces (`apps/desktop` and `apps/web`) share four packages (`api`, `db`, `sdk`, `ui`) on a single pnpm workspace.

## What's Included

| Layer | What you get | Why it matters |
|-------|--------------|----------------|
| Desktop shell | Electron 35, electron-vite, electron-builder, frameless window with hidden title bar | One command boots dev, one command ships a signed-capable installer. |
| Renderer | React 19.2, TanStack Router (file-based SPA, no SSR), TanStack Query 5, Tailwind v4 (CSS-first) | Modern React with type-safe routing, server-state caching, and a theme system that's two CSS variables away. |
| IPC | oRPC over MessagePort — typed end-to-end contract, no `contextBridge` API surface | One source of truth for renderer ↔ main calls; types flow from server to client, no manual DTOs. |
| Database | Drizzle ORM + better-sqlite3 (WAL), factory `initDatabase()`, auto-migrations at boot | Testable, parallelizable, no global singleton; migrations apply before the window opens. |
| Type safety | TypeScript ^6, zod ^4 validation at every boundary, shared `@electron-template/sdk` | Contract drift is a compile error, not a runtime crash. |
| UI primitives | shadcn/ui (added via `pnpm ui:add`), Radix UI, lucide-react, sonner, recharts, date-fns | Bring only what you need; everything lives in `packages/ui`. |
| i18n | i18next + react-i18next + browser language detection | Render-time locale switching, no async boot delay. |
| Testing | Vitest 4, real SQLite fixtures in `packages/db` | Tests run with the production ORM, not an in-memory mock. |
| Tooling | ESLint 10, Prettier, electron-rebuild for native modules | Lint and format are per-workspace, so failures localize. |
| CI | 19 GitHub Actions workflows — one action per workflow, parallel matrix | When a pipeline fails, you know exactly which check failed. |

## Why This Template

- **Monorepo over polyrepo.** Shared types, single install, atomic refactors. The renderer, the IPC server, and the database schema all change in one PR.
- **MessagePort IPC over `contextBridge`.** Same security model (contextIsolation + sandbox + CSP), better performance, no exposed API surface to leak.
- **Factory database over singleton.** `initDatabase({ dataPath, backup })` returns a fresh handle. No module-level mutable state means tests can run in parallel without `SQLITE_BUSY` traps.
- **`127.0.0.1`, not `localhost`.** Some corporate networks and DNS setups don't resolve `localhost` consistently. Using the literal IP sidesteps `ERR_CONNECTION_TIMED_OUT`.
- **Auto-migrations at boot.** No separate `db:migrate` step to forget in deploy. `runMigrations(handle.db)` runs as part of app startup.
- **One workflow per action.** 19 separate `.github/workflows/*.yml` files. Each runs on its own filter path; failures are easy to attribute.
- **oRPC AppRouter contract.** A single `AppRouter` type in `packages/api` defines every server procedure. The renderer imports the inferred client type — drift is a compile error.

## Quick Start

**Prerequisites**

- Node.js ≥ **22.13.0** (`engines.node` enforced in root `package.json`)
- pnpm **9.15+** (`corepack enable` if not installed)
- Windows 10/11 for current build target — other platforms are a config line away (see Deployment)

**Install and run**

```bash
# 1. Clone
git clone https://github.com/deessejs/complete-electron-template.git
cd complete-electron-template

# 2. Install (postinstall rebuilds native modules + builds all packages)
pnpm install

# 3. Start the desktop app in dev mode (boots main + preload + renderer)
pnpm dev:desktop
```

The window opens against the Vite dev server at `http://127.0.0.1:5173`. Hot-reload works in main, preload, and renderer simultaneously.

**Web-only dev**

```bash
pnpm --filter web dev   # standalone SPA on http://127.0.0.1:3456
```

Useful when iterating on UI without the Electron shell.

## Available Commands

### Root

| Command | Purpose |
|---------|---------|
| `pnpm dev:desktop` | Boot Electron (main + preload + renderer) in dev mode |
| `pnpm build:web` | Build the web renderer into `apps/web/dist` |
| `pnpm build:desktop` | Build the Electron app and copy renderer into `out/renderer` |
| `pnpm setup` | One-time project scaffolding (`scripts/setup.ts`) |
| `pnpm clean` | Remove all build artifacts |
| `pnpm reset:db` | Wipe the local SQLite database |
| `pnpm outdated` | List outdated dependencies across the workspace |
| `pnpm outdated:summary` | Pretty-printed version summary |
| `pnpm outdated:strict` | Fail if any dependency is behind |
| `pnpm typecheck:scripts` | Type-check root scripts (`tsconfig.scripts.json`) |
| `pnpm ui:add` | Add a shadcn component into `packages/ui` |

### `apps/desktop`

| Command | Purpose |
|---------|---------|
| `pnpm --filter desktop dev` | `electron-vite dev` |
| `pnpm --filter desktop build` | `electron-vite build` + copy `apps/web/dist` into `out/renderer` |
| `pnpm --filter desktop preview` | `electron-vite preview` |
| `pnpm --filter desktop release` | Build + copy renderer + run `electron-builder --win` |
| `pnpm --filter desktop lint` | ESLint the desktop app |
| `pnpm --filter desktop typecheck` | `tsc --noEmit` |
| `pnpm --filter desktop rebuild:native` | Rebuild `better-sqlite3` for the bundled Node ABI |

### `apps/web`

| Command | Purpose |
|---------|---------|
| `pnpm --filter web dev` | Vite dev server on port 3456 |
| `pnpm --filter web build` | Production renderer build |
| `pnpm --filter web preview` | Serve the production build locally |
| `pnpm --filter web test` | Vitest run |
| `pnpm --filter web lint` | ESLint |
| `pnpm --filter web format` | Prettier write |
| `pnpm --filter web typecheck` | `tsc --noEmit` |

### `packages/db`

| Command | Purpose |
|---------|---------|
| `pnpm --filter @electron-template/db clean` | Remove `packages/db/dist` |
| `pnpm --filter @electron-template/db build` | Compile + copy Drizzle migrations to `dist/drizzle` |
| `pnpm --filter @electron-template/db db:generate` | Diff schema → write SQL migration |
| `pnpm --filter @electron-template/db db:migrate` | Apply pending migrations |
| `pnpm --filter @electron-template/db db:push` | Push schema directly (dev only — never in prod) |
| `pnpm --filter @electron-template/db db:studio` | Open Drizzle Studio in the browser |
| `pnpm --filter @electron-template/db test` | Vitest with real SQLite fixtures |

### `packages/api`, `packages/sdk`, `packages/ui`

Each exposes `build`, `typecheck`, `lint`, and (where applicable) `test`. The `packages/sdk` `prepare` hook runs `build` automatically — the renderer can import its types as soon as `pnpm install` finishes.

## Environment Variables

**None.** This template requires no environment variables to run. The SQLite database file is created automatically at `<userData>/data/database.sqlite` (Windows: `%APPDATA%/Complete Electron Template/data/database.sqlite`). To override the path, see Customization.

If you later add a remote service or secret, add a Zod-validated loader in `packages/api` and document it here — don't reach for `process.env` directly.

## Project Structure

```
complete-electron-template/
├── apps/
│   ├── desktop/                       # Electron shell
│   │   ├── src/
│   │   │   ├── main/                  # Main process: app lifecycle, IPC server, DB init
│   │   │   ├── preload/               # MessagePort forwarder (no contextBridge)
│   │   │   └── renderer/              # (loaded from apps/web via electron-vite root)
│   │   ├── electron-builder.json
│   │   └── electron.vite.config.ts
│   └── web/                           # Renderer source (React SPA)
│       ├── src/
│       │   ├── routes/                # TanStack Router file-based routes
│       │   ├── components/            # App-level components
│       │   ├── lib/                   # oRPC client, query setup, utilities
│       │   └── hooks/
│       └── vite.config.ts
├── packages/
│   ├── api/                           # oRPC AppRouter contract + handlers
│   ├── db/                            # Drizzle schema + factory + migrations
│   ├── sdk/                           # Shared types and client helpers
│   └── ui/                            # Reusable UI primitives (shadcn source)
├── scripts/                           # Root-level TS scripts
│   ├── setup.ts
│   ├── clean.ts
│   ├── reset-db.ts
│   └── outdated.ts
├── .github/workflows/                  # 19 CI workflows
├── CLAUDE.md                          # Dev guidelines for AI agents
├── LICENSE
└── README.md
```

## Deployment

**Currently ships Windows x64.** Other platforms are a config edit away — see the end of this section.

### Local release build

```bash
# Build the desktop app and produce a Windows directory artifact
pnpm --filter desktop release
```

Outputs land in `apps/desktop/release/`:

- `Complete Electron Template-1.0.0.exe` — portable executable
- `win-unpacked/` — unpacked application directory (run `Complete Electron Template.exe` inside)

The `electron-builder.json` target is currently `dir` with `arch: x64`. Switch to NSIS for a proper installer, or add `mac` / `linux` targets for cross-platform builds.

### Adding macOS and Linux

Edit `apps/desktop/electron-builder.json` to add targets:

```json
{
  "mac": { "target": "dmg", "category": "public.app-category.developer-tools" },
  "linux": { "target": "AppImage", "category": "Development" }
}
```

Then run the corresponding `electron-builder` flag on the host OS that matches the target. Universal macOS binaries use `--mac universal`. Code signing and notarization are not configured by default — set them up before your first public release.

### GitHub release flow

```
dev  →  staging  →  main
```

Promotion is managed by the release-manager process. To cut a release:

```bash
git checkout main
git pull
git tag v1.2.3
git push origin v1.2.3
```

The `v*.*.*` tag triggers `.github/workflows/release-desktop.yml`, which builds the Windows artifacts and uploads them to the GitHub release.

**What ships:** `out/main/`, `out/preload/`, `out/renderer/` (copied from `apps/web/dist` at build time), `packages/db/dist/drizzle/` (SQL migrations).

**What doesn't ship yet:** macOS `.dmg`, Linux `AppImage`, auto-update channel (`electron-updater`), code signing.

## Customization

| What | Where | Default | When to change |
|------|-------|---------|----------------|
| `appId` | `apps/desktop/electron-builder.json` | `com.electron-template.app` | Before your first release — use your reverse-DNS bundle ID. Changing it after install is breaking (Windows treats it as a different app). |
| `productName` | `apps/desktop/electron-builder.json` | `Complete Electron Template` | Display name in the OS taskbar / dock / window list. |
| `ALLOWED_ORIGIN` | `apps/desktop/src/preload/index.ts` | `http://127.0.0.1:5173` | Only if you change the renderer dev port. |
| Database path | `apps/desktop/src/main/index.ts` | `<userData>/data` | Multi-user installs, portable mode, or CI fixtures. |
| Renderer dev port | `apps/desktop/electron.vite.config.ts` (`server.port`) | `5173` | Only if 5173 is taken on your machine. |
| Build target | `apps/desktop/electron-builder.json` (`win.target`, add `mac`, `linux`) | Windows `dir` x64 | Switch to NSIS for an installer; add macOS / Linux for cross-platform. |
| shadcn components | `pnpm ui:add` | none by default | Add only what you need — they live in `packages/ui/src/components/`. |
| CI matrix | `.github/workflows/` | 19 workflows | Add a new workflow per concern; never combine concerns in one file. |

## Architecture Notes

**MessagePort IPC.** The preload is a forwarder, not an API surface. It listens for a `postMessage('start-orpc-client', port)` from the renderer, then transfers the port to the main process via `'start-orpc-server'`. Main hosts the oRPC server on that port. The renderer talks to a typed oRPC client over MessagePort; types flow from `@electron-template/sdk`. There is no `contextBridge.exposeInMainWorld`.

**Factory database, no env vars.** `initDatabase({ dataPath, backup })` returns a fresh `{ sqlite, db }` handle. No module-level mutable state, no `getDb()` singleton. Migrations run automatically via `runMigrations(handle.db)` at boot. Drizzle pragmas: `journal_mode = WAL`, `foreign_keys = ON`, `synchronous = NORMAL`, `busy_timeout = 5000`.

**`127.0.0.1`, not `localhost`.** The renderer dev server, preload origin check, and CSP `connect-src` all bind or expect `127.0.0.1`. Some networks don't resolve `localhost` consistently — the literal IP avoids the timeout.

**oRPC AppRouter contract.** A single `AppRouter` type in `packages/api` defines every server procedure. The renderer imports the inferred client type once, so contract drift is a compile error. Zod schemas validate every input at the boundary.

**One workflow per action.** 19 separate workflows: `build-{api,db,desktop,sdk,web}`, `lint-{api,db,desktop,sdk}` + `lint.yml` (web), `typecheck-{api,db,desktop,sdk}` + `typecheck.yml` (web), `test-{api,db,web}`, `release-desktop.yml`. Each runs on its own filter path; failures are easy to attribute and the matrix runs in parallel.

**Vite version note.** `apps/web` is on Vite `^8.0.0` (Rolldown bundler). Some legacy Vite plugins may not be compatible yet — the path aliases in `electron.vite.config.ts` exist partly to work around Rolldown's stricter `exports` resolution.

**CSP.** A Content-Security-Policy is installed via `webRequest.onHeadersReceived`. Production blocks inline scripts; dev allows `unsafe-inline` + `ws://127.0.0.1:5173` for Vite HMR. This is the compensating control for `sandbox: false` on the BrowserWindow.

## Contributing

This is a template, not a product — contributions should keep it boring infrastructure, not opinionated features.

- Fork the repo, branch off `dev`.
- Use conventional commits (`feat(electron): …`, `fix(db): …`, `docs(readme): …`).
- PRs target `dev`. The `dev → staging → main` promotion flow runs separately.
- Open an issue before proposing larger changes. Bug reports with reproduction steps are always welcome.

## License

MIT — see [LICENSE](./LICENSE).
