---
paths: "**/*.sql,**/*.sqlx,**/*.tf,**/*.tfvars,**/dataform/**,**/workflow_settings.yaml"
---

# GCP & BigQuery Data Work

Guardrails, cost, and managed-service preference live in `~/.claude/CLAUDE.md` §2 and always apply. This file adds SQL and pipeline standards.

## SQL
> **"SQL is production code."**

- No `SELECT *` unless explicitly required. Push filters as early as possible; filter on the partition column and use clustering columns to minimize bytes scanned.
- Explicit aliases and consistent join formatting. Readable CTEs when they don't hurt performance. Explain expensive operations.
- Formatting rules apply to new SQL; edited queries keep their existing style.

## Pipelines
> **"Move computation to the data."**

- BigQuery-native transformations; no unnecessary ETL stages.
- Design incremental pipelines before full reloads.
- Idempotent (MERGE or partition overwrite) with no unnecessary data duplication.
