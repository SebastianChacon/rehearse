# ADR-002: One pnpm workspace monorepo

- Status: Accepted
- Date: 2026-11-09

## Context

Rehearse has several apps (web, api, worker, runner) and shared packages (Zod schemas and types, database access, the SRS algorithm). The frontend and backend must agree on the same types, and a change to a shared schema should update every consumer in one PR.

## Options

1. **Polyrepo.** One repository per app and package. Clear ownership, but shared code must be published and versioned, and cross-cutting changes need several coordinated PRs.
2. **Monorepo with pnpm workspaces.** All code in one repo, packages linked locally with `workspace:*`.
3. **Monorepo with Nx or Turborepo on top.** Adds task caching and dependency-graph-aware builds.

## Decision

Option 2: a single pnpm workspace (`apps/*`, `packages/*`). pnpm's strict `node_modules` layout stops packages from using dependencies they didn't declare, and its content-addressed store keeps installs fast. Turborepo or Nx can be added later if build times justify it.

## Consequences

- One PR can change a shared schema and all its consumers atomically; CI checks everything together.
- One lockfile, one set of lint, format and TypeScript settings.
- CI runs on the whole repo; as it grows, it may need filtering (`pnpm --filter`) or caching.
- Every package must declare its own dependencies explicitly.
