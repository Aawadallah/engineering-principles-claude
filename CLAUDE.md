# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) for all projects.

# AI Engineering Principles & Rules

## 1. Zero Assumptions & Evidence-Based Tradeoffs (State, Query, Challenge)
> **"Don't assume. Don't hide confusion. Support opinions with evidence."**

- **Think Out Loud:** Explicitly state your assumptions before writing a single line of code.
- **Halt on Ambiguity:** If a requirement is unclear, **stop**. Name the exact point of confusion and ask. Never guess an interpretation and run with it.
- **Challenge Respectfully:** Existing code is not automatically correct. Push back on poor architecture, pitch simpler alternatives, then implement without further debate once direction is confirmed.
- **Evidence, Not Opinion:** When recommending, compare alternatives, quantify complexity/cost when possible, and clearly separate fact from opinion.
- **Measure Before Optimizing:** No optimization without a measurement — profile, benchmark, or `EXPLAIN` first, record the baseline, and report before/after on the same workload. If a "performance" change can't be measured, revert to the simpler version.

---

## 2. Radical Simplicity (YAGNI First)
> **"Write the minimum that solves the problem. Nothing speculative."**

- **Strict Minimalism:** Write the absolute minimum code required. No speculative abstractions, helper functions, or "future-proofing" flexibility that wasn't explicitly requested.
- **The Senior Test:** *"Would a principal engineer reject this in a PR review for being over-engineered?"* If yes, strip it back.
- **Eliminate, Don't Manage:** Prefer removing complexity over building machinery to manage it.

---

## 3. Surgical Precision & Minimal Diffs (Leave No Trace)
> **"Touch only what you must. The best PR changes the fewest lines necessary."**

- **Zero Collateral Damage:** Modify only the exact files and lines required. No drive-by refactoring, reformatting, or reordering unless directly broken and blocking your change.
- **Preserve Context:** Keep existing formatting, ordering, and naming intact.
- **Perfect Traceability:** Every modified line must have a direct line of sight back to the original request. Keep reviews focused and easy to verify.
- **Blast Radius Before Diff:** Before editing shared code, enumerate its consumers and classify the change as a two-way door (cheap to reverse) or a one-way door (data loss, external side effects) — one-way doors demand extra review. Impact is measured in consumers affected, not lines changed.

---

## 4. Verifiable Success (Target & Loop)
> **"Define success criteria. Loop until verified."**

- **Define the Target:** Convert vague instructions into binary, verifiable outcomes before coding.
- **Validation First:** Identify the tests, assertions, or acceptance criteria before implementing.
- **Loop Until Green:** Implement only until all validation criteria pass.
- **Right-Sized Tests:** Place each test at the lowest layer that can prove the behavior; test observable contracts, not implementation internals. Fix or delete flaky tests immediately.
- **Evidence-Bound Claims:** Never say "done", "fixed", or "passing" without running the verification and observing the output. Tag every status as verified (output observed) or unverified ("should", "expected") — never blur the two. Report partial completion as partial.

---

## 5. Idiomatic Integration (Match Context)
> **"When in Rome, code as the Romans do."**

- **Style Match:** Scan surrounding files before writing. Match naming, architecture, formatting, and project conventions.
- **No Dependency Drift:** Never introduce a new library if existing project dependencies or the standard library solve the problem cleanly.
- **Respect Existing Architecture:** Integrate naturally instead of forcing personal preferences.

---

## 6. Defensive Engineering (No Silent Failures)
> **"Fail loud, fail early."**

- Throw explicit, descriptive errors instead of silently ignoring failures.
- Validate inputs at the boundaries of newly introduced or modified functions.
- Never swallow exceptions unless explicitly converting them into another meaningful error.

---

## 7. Complete Deliverables (No Placeholders)
> **"Never write partial code."**

- Provide complete, runnable code within the requested scope.
- Never use placeholders like `// existing code`.
- Output should be immediately usable with minimal manual editing.

---

## 8. Terse Communication (Show, Don't Tell)
> **"Cut the fluff. Code over conversation."**

- Skip unnecessary introductions.
- Explain architectural reasoning instead of basic language syntax.
- Keep responses concise and technically focused.

---

## 9. Grounded APIs (No Hallucinations)
> **"Don't invent methods. Stick to verified APIs."**

- Only use APIs that exist.
- Respect dependency versions.
- If uncertain, stop and ask instead of guessing.

---

## 10. Secure by Default (Protect the Core)
> **"Assume hostile inputs. Never leak secrets."**

- Never hardcode credentials.
- Always use parameterized queries.
- Validate external input.
- Sanitize filesystem, shell, SQL, and HTML interactions.

---

# Engineering Principles

## 11. Architecture Before Implementation
> **"Design first. Code second."**

For any non-trivial task:

1. Describe the architecture.
2. Explain why it fits.
3. Identify tradeoffs.
4. Then implement.

---

## 12. Infrastructure Before Code
> **"Verify the platform before changing the application."**

Before modifying application logic, verify:

- IAM
- Service Accounts
- Environment Variables
- Networking
- Dataset Locations
- Runtime Configuration
- API Enablement
- Deployment Configuration

Do not assume application code is the source of the issue.

---

## 13. Root Cause Over Symptom
> **"Fix the disease, not the symptom."**

Before implementing a fix:

- Explain the root cause and why the issue occurred.
- Ensure the solution eliminates the underlying problem rather than masking symptoms.

---

## 14. Production & Operational Excellence
> **"Optimize for production, not demos. Every feature eventually fails."**

- Assume every solution reaches production. Avoid demo shortcuts and temporary implementations.
- Design for deployment, rollback, and failure scenarios.
- Build in structured logging, metrics, timeouts, retries, idempotency, correlation IDs, and graceful degradation.
- Treat every cache as a contract: explicit TTL/eviction policy, a stated staleness tolerance, and a read path that works correctly from a cold or flushed cache.
- Decouple deploy from release: gate risky behavior behind flags that default off, ramp exposure gradually, provide a kill switch, and delete stale flags promptly after rollout.
- If production debugging would be difficult, the implementation is incomplete.

---

## 15. Cloud Cost Awareness
> **"Performance matters. Cost is a feature."**

- Optimize BigQuery queries to minimize bytes scanned; prefer partition pruning and clustering.
- Consider Cloud Run, Pub/Sub, GCS, networking, and storage costs in every design.
- If multiple solutions satisfy the requirements, recommend the one with the lowest operational cost.

---

## 16. Native Cloud Preference
> **"Prefer managed services over custom infrastructure."**

When applicable, prefer:

- BigQuery over Spark
- Cloud Scheduler over cron
- Pub/Sub or Eventarc over polling
- Cloud Storage lifecycle rules over cleanup scripts

Only introduce custom infrastructure when native services cannot satisfy the requirements.

---

## 17. SQL Engineering Standards
> **"SQL is production code."**

- Never use `SELECT *` unless explicitly required.
- Push filters as early as possible.
- Use explicit aliases and consistent join formatting.
- Prefer readable CTEs when they don't negatively impact performance.
- Explain expensive operations when appropriate.

---

## 18. Data Engineering Principles
> **"Move computation to the data."**

- Prefer BigQuery-native transformations; avoid unnecessary ETL stages.
- Design incremental pipelines before full reloads.
- Keep pipelines idempotent and eliminate unnecessary data duplication.

---

## 19. Documentation Synchronization
> **"Code and documentation evolve together."**

Whenever behavior changes:

- Update documentation, examples, and configuration references.
- Update comments that become inaccurate.
- Never leave stale documentation.

---

## 20. Principal Engineer Mindset (Enterprise Maintainability)
> **"Think in systems, not tickets. Assume someone else owns this next year."**

Every solution should consider reliability, scalability, security, cost, operability, maintainability, observability, and long-term platform health.

- Optimize for readability, simplicity, discoverability, and debuggability.
- Avoid clever implementations that increase long-term maintenance cost.
- Make tradeoffs explicit; optimize for systems that remain understandable years after implementation.

---

# Collaboration & Process

## 21. Git Hygiene & Reviewable Changes
> **"The commit history is a product. Optimize the change for the reviewer, not the writer."**

- **Atomic Commits:** One logical change per commit that compiles and passes tests on its own; never bundle a refactor with a behavior change.
- **Meaningful Messages:** Imperative subjects (≤50 chars) that state intent; add a body only when the why isn't obvious from the diff.
- **Never Commit:** Secrets, credentials, generated artifacts, build outputs, or personal editor config. Review the full staged diff before every commit — no blind `git add -A`.
- **Branch Discipline:** Short-lived, descriptively named, issue-linked branches off mainline. Rebase local work freely; never rewrite history on shared branches.
- **Reviewable PRs:** Small and single-purpose. Description states intent, approach chosen and alternatives rejected, testing evidence, known risks, and rollback plan. Self-review the diff line-by-line before requesting review.
- **Close the Loop:** Address every review comment explicitly — apply it, or reply with evidence for why not. Verify feedback technically before implementing; never apply suggestions performatively.
- **AI Assistants:** Never commit, push, or tag without explicit human instruction — stage and present the diff first.

---

## 22. Human-in-the-Loop AI Pairing (Scope Fidelity)
> **"Propose, pause, proceed — irreversible actions require a human yes."**

- **Approval Gates:** All irreversible or externally visible actions (commit, push, deploy, migration, deletion, sending messages) require explicit human approval — show the exact diff or command first.
- **Checkpointed Phases:** Plan → approve → implement → verify. Stop at each boundary on consequential work; don't run end-to-end unsupervised.
- **No Silent Expansion:** Problems discovered mid-task are flagged as separate proposals, never silently folded into the current change.
- **No Silent Shrinkage:** No stubbing, skipping edge cases, or downgrading requirements to finish faster. If a requirement proves infeasible, stop and renegotiate.
- **Delta Report:** End every task with requested vs delivered — anything deferred, dropped, or added, with reasons.
- **Resumable Workspace:** Leave no half-applied edits, uncommitted mystery state, or orphaned branches at any stop.

---

## 23. Verification Integrity (Never Game the Green)
> **"Make the code pass the test — never make the test pass the code."**

- **Fix the Code, Not the Check:** Modify or delete a test only when it is demonstrably wrong, and state that justification explicitly.
- **No Softening:** Never weaken assertions, widen tolerances, add skips, or mark tests flaky to reach a passing state.
- **No Cheating:** Never hardcode expected outputs, detect test environments, or special-case test inputs in production code.
- **Honest Red:** A red check is information. If you can't make it pass honestly, report it red — a truthful failure beats a fabricated success.
- **Prove Tests Can Fail:** Watch a new test fail (or mutate the code) before trusting its pass.

---

## 24. Durable Context (Assume Amnesia)
> **"If it isn't written down, the next session never knew it."**

- **Durable Artifacts:** Record decisions, constraints, and rejected alternatives in commit messages, ADRs, tickets, or project docs — never only in chat or an AI session transcript.
- **Traceable Work:** Link branches, commits, and PRs to their work items so any line of code traces to its motivating requirement.
- **Re-Derive, Don't Recall:** Start each session from the repository, CI, and tooling state — not from remembered conversation. Re-verify prior assumptions after any session break, compaction, or handoff.
- **Supersede Explicitly:** Before reversing an existing decision, find and read its original record; supersede it explicitly rather than silently contradicting it.
- **Self-Describing Handoffs:** Leave interrupted work documented: what changed, what remains, how to verify — so any session (human or AI) can resume cold.

---

# Data & Systems at Scale

## 25. Data Minimization & Privacy by Default
> **"Collect nothing you can't justify; access nothing you don't need."**

- **Classify First:** Label every field you touch (public / internal / PII / sensitive) before storing, copying, or transmitting it.
- **Minimum Fields:** Collect, query, and propagate only what the feature strictly requires — never fetch-and-forward whole records "just in case."
- **Least Privilege:** Scope every service account, token, and database role to the minimum data the code actually reads.
- **Clean Logs:** Never log PII, credentials, tokens, session IDs, or full request/response bodies. Redact at a single enforced boundary — including exception messages, stack traces, URLs, and third-party telemetry. Temporary "print the payload" debugging is prohibited, not discouraged.
- **Production Data Stays in Production:** Real customer data never appears in tests, fixtures, seed scripts, examples, or AI-assistant prompts. Use synthetic, obviously fake data (reserved domains, invalid ID ranges).
- **Every Copy Is a Liability:** Each new copy of personal data (cache, export, analytics table) requires justification, a defined retention/TTL, and a real delete path that cascades to replicas, caches, indexes, and derived tables.

---

## 26. Design for the Second Instance (Concurrency, Time & Delivery)
> **"Somewhere, another copy of your code is executing the same line right now."**

- **Races:** Treat every check-then-act on shared state as a race; replace with an atomic operation, conditional write, or unique constraint.
- **Datastore Invariants:** Enforce invariants with unique indexes, constraints, and transactions — never solely in application logic.
- **No Local Truth:** Never hold source-of-truth state in process memory; it dies with the instance and diverges across replicas.
- **Guarded Jobs:** Scheduled and startup jobs need distributed locks or leader election — otherwise every replica fires them.
- **Assume Duplicates:** Messages, webhooks, and retries deliver at-least-once. Key every external side effect (charge, email, insert) on an idempotency or dedup key; make every consumer safe to replay.
- **No Naked Dual-Writes:** Never write to a database plus a queue/API without an outbox, transaction, or reconciliation path — one of the writes will eventually fail.
- **Time Is Distributed Too:** Store and compute in UTC; convert only at the display edge with explicit zone identifiers. Use monotonic clocks for elapsed time. Never infer cross-machine event order from timestamps; declare timezone and DST behavior for every schedule.

---

## 27. Contracts Outlive Code (Compatible Evolution & Reversible Migrations)
> **"Once published, an interface is a promise — evolve it, never break it."**

- **Unseen Consumers:** Treat every exposed surface — API, schema, event, config key, file format — as consumed by clients you cannot see or redeploy.
- **Additive Only:** Add fields, endpoints, and columns; never rename, retype, or repurpose an existing one in place. Never silently change the meaning of a field, enum value, or status code — introduce a new one.
- **Deprecate Before Remove:** Version or dual-publish, warn, and delete only after consumers have verifiably migrated.
- **Expand → Migrate → Contract:** Split every breaking schema/data change into three independently deployable, reversible steps. Keep code compatible with both shapes during rollout — a deploying fleet is always mixed-version.
- **Destructive Step Last:** Never drop, delete, or rename in the same release that stops depending on the old shape; wait at least one verified release.
- **Rollback First:** Write and test the rollback path (or documented forward-fix) before executing the forward migration. Run backfills as resumable, separately triggered jobs — never inline in deploy or startup.

---

## 28. Design for Realistic Load
> **"Code that only works on the dev fixture doesn't work."**

- **State the Scale:** Declare expected row counts, QPS, payload sizes, concurrency, and cardinality before choosing an approach.
- **Reject Pass-Small-Fail-Large:** No N+1 queries, unbounded in-memory collections, or O(n²) loops over user-sized data.
- **Bound Everything:** Paginate, stream, or batch any collection that can grow without bound; never assume "it fits in memory."
- **Production-Shaped Validation:** Test against realistic data volumes and concurrent access, not single-user happy paths.
- **Numeric Budgets:** Performance requirements need numbers (p95/p99, not averages). Treat a budget breach like a failing test — never renegotiate the number silently.
- **Know the Next Bottleneck:** Identify what breaks first at 10x current load and note it, even if you don't fix it now.
