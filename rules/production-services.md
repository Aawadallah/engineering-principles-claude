---
paths: "**/*.java,**/*.kt,**/*.groovy,**/*.scala,**/*.py,**/*.go,**/*.ts,**/*.tsx,**/*.js,**/*.sh,**/*.sql,**/*.sqlx,**/*.tf,**/*.gradle,**/Dockerfile,**/*.proto,**/*.avsc,**/*.graphql,**/openapi*.yaml,**/openapi*.yml,**/openapi*.json"
---

# Production Services

Operability, Concurrency, and Load apply to long-lived deployed services, jobs, and pipelines per `~/.claude/CLAUDE.md` §0 — not to scripts, notebooks, one-off queries, or prototypes. Contracts & Migrations applies to any published interface.
Apply these to what the task adds or materially changes (new services, jobs, endpoints, integrations, data flows); for that code they are requirements, not speculation — fold them into the acceptance criteria up front. Do not retrofit them into code the task doesn't otherwise change; report gaps as separate proposals.

## Operability
> **"Optimize for production, not demos. Every feature eventually fails."**

- Design for deployment, rollback, and failure. Build in structured logging, metrics, timeouts, retries, idempotency, correlation IDs, and graceful degradation.
- Weigh reliability, scalability, security, cost, operability, maintainability, observability, and long-term platform health in every design; optimize for readability, discoverability, and debuggability.
- If production debugging would be difficult, the implementation is incomplete.
- Every cache is a contract: explicit TTL/eviction, a stated staleness tolerance, and a read path that is correct from a cold or flushed cache.
- Decouple deploy from release: if the repo has a feature-flag mechanism, put risky behavior (user-visible changes, new data writes, new external calls) behind a flag that defaults off, ramp gradually, keep a kill switch, and delete stale flags after rollout. If it has none, gate the risky behavior behind a minimal default-off config/env toggle as its kill switch, say so, and propose a real flag mechanism separately — don't build a framework inside the task.

## Concurrency, Time & Delivery
> **"Somewhere, another copy of your code is executing the same line right now."**

- Every check-then-act on shared state is a race: use an atomic operation, conditional write, or unique constraint.
- Enforce invariants in the datastore (unique indexes, constraints, transactions), never solely in application logic. BigQuery does not enforce PK/FK/unique: use MERGE-based dedup, idempotent keys, and assertion queries.
- Never hold source-of-truth state in process memory.
- Jobs triggered per replica (startup hooks, in-process schedulers) need a distributed lock or leader election. Single-trigger managed schedulers (Cloud Scheduler) need idempotency instead.
- Assume at-least-once delivery: key every external side effect (charge, email, insert) on an idempotency or dedup key; make every consumer safe to replay.
- No naked dual-writes (database + queue/API): use an outbox, transaction, or reconciliation path.
- Time handling (UTC, monotonic clocks, schedules): `~/.claude/CLAUDE.md` §6.

## Contracts & Migrations
> **"Once published, an interface is a promise — evolve it, never break it."**

Scope: published interfaces — external APIs, library public APIs, CLI flags, persisted schemas, events, shared config keys, file formats — whether or not the code is a deployed service (prototypes excepted). Internal code may be removed after enumerating consumers (`~/.claude/CLAUDE.md` §4).

- Treat every published surface as consumed by clients you cannot see or redeploy.
- Additive only: add fields, endpoints, columns. Never rename, retype, repurpose, or silently change the meaning of a field, enum value, or status code — introduce a new one.
- Deprecate before remove: version or dual-publish, warn, delete only after consumers verifiably migrated.
- Expand → migrate → contract, each step independently deployable and reversible. Code stays compatible with both shapes during rollout; a deploying fleet is mixed-version.
- Destructive step last: never drop/delete/rename in the release that stops depending on the old shape; wait at least one verified release.
- Write and test the rollback path (or documented forward-fix) before the forward migration. Backfills are resumable, separately triggered jobs — never inline in deploy or startup.

## Load
> **"Code that only works on the dev fixture doesn't work."**

- State the scale up front: row counts, QPS, payload sizes, concurrency, cardinality.
- Paginate, stream, or batch anything that can grow without bound; never assume it fits in memory. (N+1 / O(n²) rule: `~/.claude/CLAUDE.md` §4.)
- For new or changed hot paths, or anything with a stated budget, validate against realistic volumes and concurrent access (synthetic data), not single-user happy paths. Other changes follow lowest-layer testing (`~/.claude/CLAUDE.md` §5).
- Performance budgets are p95/p99 numbers, not averages. A breach is a failing test; never renegotiate the number silently.
- Note what breaks first at 10x current load, even if not fixing it now.
