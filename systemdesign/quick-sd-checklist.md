# ⚙️ Scalable Feature Checklist (Quick Pass)

> Use this before merging any feature that affects data, APIs, or infra. Treat each section as **Go/No-Go gates**.

## 1) Problem & Blast Radius
- [ ] **Clear intent:** Single-sentence “why” and success metric (e.g., +X% conversion, -Y% latency).
- [ ] **Scope:** Which services, queues, jobs, schemas, and tenants are touched?
- [ ] **Read/write surface:** Reads from ___; writes to ___; external calls to ___.

## 2) Multi-Tenancy & Data Boundaries
- [ ] **Tenant isolation:** All reads/writes filter by `tenant_id` (or partition key). No cross-tenant joins.
- [ ] **Indexes per access path:** Covering index includes `tenant_id` + query predicates.
- [ ] **Data ownership:** Source of truth documented; no duplicate writes without reconciliation plan.

## 3) API & Contract Safety
- [ ] **Backward compatible:** No breaking changes; versioned routes or additive fields only.
- [ ] **Idempotency:** Safe retries (idempotency key or deterministic upserts).
- [ ] **Pagination & limits:** Cursor/offset + max page size; server-enforced rate limits.
- [ ] **Timeouts & retries:** Client/server timeouts set; exponential backoff; **no** infinite retries.
- [ ] **Validation:** Strong input validation + explicit error codes.

## 4) Workload & Throughput
- [ ] **Traffic model:** Peak RPS/QPS estimate with 3× headroom.
- [ ] **Hot path budget:** P50/P95/P99 latency targets (e.g., 50/150/400 ms).
- [ ] **Batch vs. real-time:** Long tasks offloaded to async job/queue with DLQ + retry policy.

## 5) Storage, Queries & Caching
- [ ] **Query plan reviewed:** `EXPLAIN`/`SET SHOWPLAN` checked for scans; avoid N+1.
- [ ] **Migrations are online:** Zero-downtime pattern (add-backfill-switch-drop).
- [ ] **Cardinality/size:** Estimated row growth, row size, and partition/archival rules.
- [ ] **Cache strategy:** What/where cached (Redis/CDN/in-proc), TTLs, and **invalidation triggers**.
- [ ] **Cache keys include tenant:** Avoid cross-tenant leakage.

## 6) Resilience & Failure Modes
- [ ] **Dependency map:** What happens if X is slow/down? (graceful degradation path defined)
- [ ] **Circuit breakers:** Configured for external calls; fallback defined.
- [ ] **Bulkheads:** Resource limits per tenant/job to prevent noisy neighbor issues.
- [ ] **Exactly-once illusions avoided:** Accept at-least-once semantics with idempotent processing.

## 7) Security, Privacy, Compliance
- [ ] **AuthZ:** Role/permission checks on every new/changed route.
- [ ] **PII handling:** Data classification updated; encryption at rest/in flight verified.
- [ ] **Secrets:** No secrets in code/logs; rotate & pull from KMS/KeyVault.
- [ ] **Tenant data export/delete:** Flows still work (GDPR/CCPA readiness).

## 8) Observability & SLOs
- [ ] **Structured logs:** Correlation/trace IDs, tenant_id, request_id on every log line.
- [ ] **Metrics:** New counters/gauges/histograms for RPS, latency, errors, queue age.
- [ ] **Traces:** Spans around external calls, DB queries, queue steps.
- [ ] **Dashboards/alerts:** P95 latency, error rate, saturation; paging thresholds set.
- [ ] **Feature flag metrics:** Adoption and rollback impact visible.

## 9) Delivery, Flags & Rollback
- [ ] **Feature-flagged:** Dark launch + per-tenant enablement; kill-switch in place.
- [ ] **Safe deploy:** Gradual rollout (e.g., 1% → 5% → 25% → 100%); health checks gate progression.
- [ ] **Rollback plan:** DB changes reversible; toggling flag returns system to stable state.
- [ ] **Runbook updated:** “How to disable/recover” steps documented.

## 10) Cost & Capacity
- [ ] **Cost deltas:** Expected DB/egress/cache/job cost change; stay within budget.
- [ ] **Autoscaling:** HPA/queue concurrency limits set; cold-start impact acceptable.
- [ ] **Storage growth:** Forecast + retention/archival policy defined.

## 11) DX & Ownership
- [ ] **Docs:** README/service ADR updated; API examples and error codes included.
- [ ] **On-call aware:** Alert routes tagged to owning team; escalation path verified.
- [ ] **Tests:** Unit + integration + load/smoke; golden path & failure mode tests pass.

## Quick Perf Budget (copy for PR)
- **Target P50/P95/P99:** ___ / ___ / ___ ms  
- **Peak RPS (est/3×):** ___ / ___  
- **Max payload size:** ___ KB  
- **DB query count per request:** ≤ ___ (hot path)  
- **Cache hit goal:** ≥ ___%  

## ADR Stub (if needed)
**Decision:** ___  
**Context:** ___  
**Options considered:** ___  
**Decision drivers:** ___  
**Consequences (trade-offs):** ___

### Final Go/No-Go
- [ ] All boxes above checked (or explicit, reviewed exceptions).
- [ ] Observability + rollback verified in a staging canary.
- [ ] Owner signed off: ___  Date: ___