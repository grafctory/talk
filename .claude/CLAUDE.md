> [!CAUTION]
>
> - **ONLY** modify code when explicitly requested or clearly required.
> - **NEVER** make unsolicited changes or revert **unrelated** user edits.
> - **ALWAYS** monitor IDE diagnostics when working on a file

> [!CAUTION]
> Activating and following skill guidance is **MANDATORY** for every task. Before starting any work, identify and
> activate all relevant skills. Skill instructions are binding and override default behaviours. When in doubt about
> whether skill guidance is current, relevant skills MUST be reloaded.

# Overview

`@grafctory/talk` is a standalone monorepo collecting the conversational AI packages of Grafctory, each sitting
directly under `packages/` (for example `packages/talk/`).

# References

- [@grafctory/core](https://github.com/grafctory/core) - Core utilities and shared types
- [@grafctory/flow](https://github.com/grafctory/flow) - Composable async iterable processing
- [@grafctory/tape](https://github.com/grafctory/tape) - Simplified facade for the LogTape logging framework

# NPM Scripts

- **`npm run clean`** - Remove dependencies and build artefacts
- **`npm run prime`** - Install dependencies from the lockfile
- **`npm run setup`** - Install dependencies and link sibling `@grafctory/*` repositories
- **`npm run build`** - Compile sources and generate docs
- **`npm run check`** - Run the test suite
- **`npm run proof`** - Build and serve docs

> [!CAUTION]
> **`prime` and `setup` are not interchangeable.** Run `prime` when finalising a public release: `@grafctory/*` imports
> resolve to the published releases recorded in the lockfile. Run `setup` for local development against unpublished
> sibling branches: imports resolve to the working copies in the neighbouring repositories.

# Package Layout

The root `package.json` `workspaces` glob (`packages/*`) covers the framework packages, each in its own directory
immediately under `packages/` (for example `packages/talk`).

# Shared Utilities

Reach for `@grafctory/core` before writing a helper: its `strings`, `numbers`, `arrays` and `values` entry points
already cover text tidying, escaping, splitting and templating alongside the common collection and value operations. A
hand-rolled equivalent duplicates tested code and drifts from it, missing the edge cases the shared one handles.

Keep a local helper only where the shared one genuinely doesn't fit, and record in its doc comment what the difference
is, so the next reader doesn't take it for an oversight.

# Testing

The root `vitest.config.ts` resolves imports of symlinked `@grafctory/*` packages, both workspace packages and siblings
linked by `npm run setup`, to the TypeScript source their `@grafctory/source` export entries declare, so tests run
against `src/` without a prior build. Packages installed from the registry keep resolving to their published `dist`.
An export entry lacking the condition falls back to `dist` as well, and tests then see that package as last built.

> [!CAUTION]
> **NEVER** enable `@grafctory/source` through vitest `resolve.conditions`. Published manifests carry the condition
> while their packages ship no `src/`, and Vite does not fall back to `default` on a missing target, so a global
> condition breaks every package installed from the registry that declares it, as happens under `npm ci` in CI.

# Version Management

All workspace packages share the root `package.json` version. Beyond the `version` fields the release flow already
cascades, update the internal `@grafctory/talk*` dependency ranges in every `packages/**/package.json` to match.

When adding, removing, or renaming packages, update the package table in the root `README.md` Usage section to match.
