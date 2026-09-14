# CLAUDE.md

Always respond in English.

This is a senior-level complete Electron application.

## Web Search

When performing web searches, you MUST use the `fresh` CLI tool. Never use other search methods.

### Fresh CLI Usage

```bash
# Search the web
fresh search "your search query"

# Fetch content from a specific URL
fresh fetch <url>
```

### Examples

```bash
# Search for React documentation
fresh search "React documentation 2026"

# Get content from a specific page
fresh fetch https://react.dev/docs
```

Available commands:
- `fresh auth` - Authentication commands
- `fresh search [options]` - Search the web using Exa.ai
- `fresh fetch [options] <url>` - Fetch and extract content from a URL

## Documentation Structure

- Internal documentation: `docs/internal/`
- Learnings: `docs/learnings/`
- Plans: `docs/plans/`
- Reports: `docs/reports/`

## CI/CD Philosophy

Each workflow file performs exactly **one action** (lint, typecheck, build, etc.).
This is intentional: when a CI run fails, agents can immediately identify which
workflow failed without parsing combined output. It improves debuggability for
both human and AI agents reviewing failure output.

## Tech Stack

**Year**: 2026 (current date)

### apps/web (TanStack Router SPA — no SSR)
| Tech | Version |
|------|---------|
| React | 19.2.4 |
| Tailwind CSS | 4.2.1 |
| TanStack Router | 1.167.4 |
| TanStack React Router DevTools | 1.166.9 |
| TanStack DevTools Vite | 0.7.0 |
| Vite | 7.3.1 |
| TypeScript | 6.0.3 |
| oRPC Client | 1.14.3 |
| Zod | 4.4.3 |
| i18next | 26.2.0 |
| Radix UI | 1.4.3 |
| Lucide React | 1.16.0 |
| Recharts | 3.8.0 |
| shadcn | 4.7.0 |
| Sonner | 2.0.7 |
| date-fns | 4.1.0 |

### apps/desktop (Electron Desktop App)
| Tech | Version |
|------|---------|
| Electron | 35.0.0 |
| electron-vite | 5.0.0 |
| electron-builder | 26.8.1 |
| TypeScript | 6.0.3 |
| ESLint | 10.4.0 |
| oRPC Client | 1.14.3 |
| oRPC Server | 1.14.3 |
| Zod | 4.4.3 |

### packages/api (oRPC Server Router)
| Tech | Version |
|------|---------|
| oRPC Server | 1.14.3 |
| Zod | 4.4.3 |
| drizzle-orm | 0.45.2 |
| @electron-template/db | workspace:* |

### packages/db (Drizzle ORM Database)
| Tech | Version |
|------|---------|
| Drizzle ORM | 0.45.2 |
| better-sqlite3 | 12.10.0 |
| drizzle-kit | 0.31.10 |

### packages/sdk (Shared SDK — type-only)
| Tech | Version |
|------|---------|
| @electron-template/api | workspace:* |

### packages/ui (shadcn components package)
| Tech | Version |
|------|---------|
| shadcn | 4.7.0 |
| Radix UI | 1.4.3 |
| Tailwind CSS | 4.2.1 |
| Lucide React | 1.16.0 |
| class-variance-authority | 0.7.1 |

### Build & CI
| Tech | Version |
|------|---------|
| pnpm | 9 (CI) |
| Vite | 7.3.1 |
| TypeScript | 6.0.3 |
| ESLint | 10.4.0 |
| Vitest | 3.2.4 |

## Network Configuration

The desktop app renderer dev server and IPC communication use `127.0.0.1` (not `localhost`).
This is intentional: some networks block localhost resolution, causing ERR_CONNECTION_TIMED_OUT.
Using the explicit IP avoids this issue.

## Issue Labels

GitHub issues use a structured label taxonomy:

### Type (color-coded)
- `type: bug` - Bug fix (red)
- `type: feature` - New feature (blue)
- `type: refactor` - Code refactoring (purple)
- `type: docs` - Documentation changes (gray)
- `type: security` - Security fix (black)

### Status (gray to green gradient)
- `status: triage` - Not yet reviewed by Tech Lead
- `status: needs-info` - Ticket incomplete, needs more info
- `status: ready` - Validated by Tech Lead, ready to pick up
- `status: in-progress` - Currently being worked on
- `status: in-review` - In code review
- `status: blocked` - Blocked by dependency or decision

### Priority (yellow to red gradient)
- `p0: critical` - Critical, stop everything and fix now
- `p1: high` - Required for next release
- `p2: medium` - Normal priority
- `p3: low` - Nice to have

### Effort (optional, for Tech Lead)
- `effort: xs` - Few minutes
- `effort: s` - Half a day
- `effort: m` - 1-2 days
- `effort: l` - A week or more (needs breakdown)

## Branching Strategy

Development workflow:
- `dev` - All development PRs merge here. This is the main development branch.
- `staging` - Pre-release testing branch.
- `main` - Production-ready code. Only releases merge here.

The flow `dev → staging → main` is managed by the **release manager**.

## Project Structure

```
complete-electron-template/
├── apps/
│   ├── desktop/                    # Electron desktop app
│   │   ├── electron.vite.config.ts
│   │   ├── electron-builder.json
│   │   ├── eslint.config.js
│   │   ├── package.json
│   │   ├── release/                # Release artifacts
│   │   ├── src/
│   │   │   ├── main/index.ts       # Main process entry
│   │   │   └── preload/index.ts    # Preload (MessagePort forwarder only)
│   │   └── tsconfig.json
│   │
│   └── web/                        # TanStack Router SPA (no SSR)
│       ├── index.html
│       ├── src/
│       │   ├── components/        # Local components (LanguageSwitcher, etc.)
│       │   ├── i18n/              # i18next setup + locales/
│       │   ├── lib/                # Utilities (orpc.ts)
│       │   ├── routes/             # TanStack Router file-based routes
│       │   ├── main.tsx            # CSR entry (createRoot + RouterProvider)
│       │   ├── router.tsx          # Router config
│       │   ├── routeTree.gen.ts    # Auto-generated route tree
│       │   └── styles.css          # Entry CSS (@source + globals.css)
│       ├── eslint.config.mjs
│       ├── package.json
│       ├── vite.config.ts
│       └── tsconfig.json
│
├── packages/
│   ├── api/                        # oRPC server router
│   │   ├── src/
│   │   │   ├── index.ts           # Exports router + types
│   │   │   └── routes/             # oRPC procedures (system/, users/)
│   │   ├── drizzle.config.ts
│   │   ├── eslint.config.js
│   │   ├── package.json
│   │   └── tsconfig.json
│   │
│   ├── db/                         # Drizzle ORM database layer
│   │   ├── src/
│   │   │   ├── client.ts          # initDatabase(), closeSqlite() — factory, no globals
│   │   │   ├── migrator.ts        # runMigrations()
│   │   │   ├── schema/            # Table definitions + $inferSelect/$inferInsert
│   │   │   └── index.ts           # Public surface
│   │   ├── drizzle/               # Generated SQL migrations (committed)
│   │   ├── drizzle.config.ts
│   │   ├── eslint.config.js
│   │   ├── package.json
│   │   └── tsconfig.json
│   │
│   ├── sdk/                        # Type-only contract for renderer
│   │   ├── src/
│   │   │   ├── index.ts           # SDK exports
│   │   │   └── router.ts          # AppRouter type re-export
│   │   ├── eslint.config.js
│   │   ├── package.json
│   │   └── tsconfig.json
│   │
│   └── ui/                        # shadcn components package
│       ├── src/
│       │   ├── components/        # shadcn components (button, input, card, etc.)
│       │   ├── hooks/
│       │   ├── lib/               # utils.ts, cn()
│       │   ├── styles/            # globals.css (@source + theme variables)
│       │   └── index.ts           # Public surface
│       ├── components.json
│       ├── eslint.config.js
│       ├── package.json
│       └── tsconfig.json
│
├── docs/
│   ├── internal/                  # ADRs (security, ipc-contract, ssr-decision)
│   ├── learnings/                 # Library notes + agent docs
│   ├── plans/                      # Migration + upgrade plans
│   └── reports/                    # Feasibility reports
│
├── .github/
│   └── workflows/                 # CI workflows (17 total)
│       ├── build-*.yml             # Build workflows
│       ├── lint-*.yml              # Lint workflows
│       ├── typecheck-*.yml         # Typecheck workflows
│       ├── test-*.yml              # Test workflows
│       ├── release-desktop.yml     # Desktop release
│       └── test-*.yml              # Integration tests
│
├── package.json                    # Root package.json
├── pnpm-workspace.yaml             # pnpm workspaces config
└── CLAUDE.md                       # This file
```

## ADHD Reader Mode

Output is not just brief. It is shaped so an ADHD brain can act on it.

### Persistence

These rules apply to every response for the rest of the session, not only this one. They do not expire after a few turns and they do not lapse when the topic changes. If unsure whether they still apply, they do.

Turn them off only when the reader says "stop adhd mode" or "normal mode". Confirm in one line, then return to the default style.

### What ADHD changes about reading

Five facts drive every rule below:

1. Working memory is small. Anything not on screen is forgotten. Do not ask the reader to "keep in mind X."
2. Knowing the answer is not doing the answer. The friction between "got it" and "done it" is where work dies.
3. Starting is the hardest step. The first action must be obvious, small, and doable now.
4. Time estimates feel uniform. "A bit of work" and "a few hours" register the same. Vague estimates fail.
5. Dopamine is scarce. Visible progress matters. Buried wins do not register.

### Rules

1. **Lead with the next action.** First line is something the reader can do. Not context. Not a plan. If the answer is a command, path, or snippet, it goes first. Prose comes after, if at all.
2. **Number multi-step tasks.** Each step is one bounded action. No step contains "and then" twice. Use the fewest steps that still work. Cut any step the reader does not need. A short path finished beats a complete path abandoned.
3. **End with one concrete next action.** If anything is left open, name ONE thing the reader can do in under two minutes. Even "open the file" counts.
4. **Suppress tangents.** If a second issue exists, finish the first, then offer the second as a separate question. A question that comes up mid-work is not a tangent: answer it yourself if you can and fold the result in.
5. **Restate state every turn.** The reader cannot hold "we are on step 3 of 5" between messages. Restate it. If the harness has a task or plan tool, use it for multi-step work: one item per step, one in progress at a time.
6. **Give specific time estimates.** Ballpark in concrete units. "About 15 minutes if tests already cover this. An afternoon if not."
7. **Make completed work visible.** Show what now works, in concrete terms. Do not bury wins in a recap.
8. **Matter-of-fact tone for errors.** Never use "Uh oh," "Oh no," or "There seems to be a problem." State cause and fix.
9. **Cap lists to 5 items.** Group related items and rank the most relevant first. Keep the visible working set small. When more items are relevant, retain them internally and display them only when the user asks or when they become the next items to address. Never omit relevant items when completeness matters.
10. **No preamble, no recap, no closing pleasantries.** Forbidden openers: "Great question," "Let me...", "I'll...", "Sure!", "Looking at your...". Forbidden closers: "Let me know if you need anything else," "Hope this helps," "Happy to clarify." Start with the answer. End when the answer is done.

### When to break the rules

Override the defaults when:

- User asks to "explain" or "walk me through." Explain fully. Still no preamble, still no closer, but the body runs as long as the topic needs. Add headers so the reader can skim back.
- Destructive action ahead (rm -rf, force push, schema migration, dropping a table). Confirm before acting. Safety wins over brevity.
- Debug spiral. If the last three turns have been "still broken," stop iterating on code. Name the assumption that might be wrong. Ask one diagnostic question.
- Real ambiguity in the request. One short clarifying question beats guessing and rewriting.
- A rule fights the task. When a rule would delete the answer itself, the task wins; the shape stays. Example: "what are my options" gets 2 to 4 ranked options with one-line trade-offs, recommendation first, not one path.
- A rule fights the harness. Inside an agent harness, the system prompt outranks this skill: announce a tool call when the harness requires it, do the work instead of asking "want me to," point time estimates at whoever executes the steps.

### Pre-send check

Before sending, delete:

- The first sentence if it announces what is about to be done.
- The last sentence if it asks "anything else?" or recaps what just happened.
- Any "by the way" sidebar.
- Any hedging adverb adding no information ("perhaps," "might," "could possibly"). Keep a hedge that carries real uncertainty.
- Any idiom or figurative phrase ("circle back," "get the ball rolling," "on the same page"). Replace with the literal action.

Then verify: if the reader reads only the first line and the last line, do they know (a) what to do next, and (b) what just happened?