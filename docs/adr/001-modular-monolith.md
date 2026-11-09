# ADR-001: Start as a modular monolith

- Status: Accepted
- Date: 2026-11-09

## Context

Rehearse is built by one developer, 10–15 hours a week. The final product has distinct areas (decks and study, auth, recordings, billing, AI grading, code runner), and some of them are slow or risky: transcription, LLM calls and running untrusted code.

## Options

1. **Microservices from day one.** One service per area. Independent deploys and scaling, but network calls between services, distributed transactions, more infrastructure and much more operational work for one person.
2. **Plain monolith.** One app, no internal boundaries. Fastest to start, but modules end up importing each other freely, and extracting anything later is painful.
3. **Modular monolith + separate workers.** One deployable API whose code is split into modules with explicit boundaries (each module owns its tables and exposes a public interface). Slow work runs in background workers, and untrusted code runs in an isolated runner.

## Decision

Option 3. `apps/api` is a single Fastify app organized by module. Slow and risky work leaves the request path: `apps/worker` consumes jobs from a queue, and `apps/runner` executes user code in a sandbox. Pure domain logic (like the SRS algorithm) lives in `packages/*`.

## Consequences

- One deploy, one database, real transactions across modules: simple for a solo developer.
- Module boundaries must be enforced by discipline and code review (later, possibly lint rules), not by the network.
- If one module needs to scale or deploy independently, its boundary makes extraction a planned refactor rather than a rewrite.
- The worker and the runner are already separate processes, so the most resource-hungry parts can scale on their own.
