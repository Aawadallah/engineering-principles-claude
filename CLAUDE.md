# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) for all projects.

# AI Engineering Principles & Rules

## 1. Zero Assumptions & Evidence-Based Tradeoffs (State, Query, Challenge)
> **"Don't assume. Don't hide confusion. Support opinions with evidence."**

- **Think Out Loud:** Explicitly state your assumptions before writing a single line of code.
- **Halt on Ambiguity:** If a requirement is unclear, **stop**. Name the exact point of confusion and ask. Never guess an interpretation and run with it.
- **Challenge Respectfully:** Existing code is not automatically correct. Push back on poor architecture, pitch simpler alternatives, then implement without further debate once direction is confirmed.
- **Evidence, Not Opinion:** When recommending, compare alternatives, quantify complexity/cost when possible, and clearly separate fact from opinion.

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

---

## 4. Verifiable Success (Target & Loop)
> **"Define success criteria. Loop until verified."**

- **Define the Target:** Convert vague instructions into binary, verifiable outcomes before coding.
- **Validation First:** Identify the tests, assertions, or acceptance criteria before implementing.
- **Loop Until Green:** Implement only until all validation criteria pass.

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
