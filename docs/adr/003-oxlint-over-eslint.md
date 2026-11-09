# ADR-003: oxlint instead of ESLint

- Status: Accepted
- Date: 2026-11-09

## Context

The plan called for ESLint + Prettier. Lint runs in a pre-commit hook and in CI, so its speed directly affects every commit.

## Options

1. **ESLint.** The standard, with the largest plugin ecosystem and type-aware rules via typescript-eslint. Slower, and configuration is more involved.
2. **oxlint.** A Rust linter from the Oxc project, much faster than ESLint, with ESLint-compatible rules for the common plugins (core, TypeScript, React, import) and little configuration.
3. **Biome.** Linter and formatter in one, but it would replace Prettier as well.

## Decision

oxlint with `--deny-warnings`, alongside Prettier for formatting. Type errors are caught by `tsc` in the `typecheck` step, so oxlint's lack of type-aware rules is acceptable for now.

## Consequences

- Lint is near-instant in hooks and CI.
- Some ESLint plugins and type-aware rules aren't available. If a needed rule is missing, ESLint can be added for just those rules.
- Linting and formatting stay separate tools (oxlint + Prettier).
