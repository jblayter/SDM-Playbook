# System Design Playbook

Use this as a talk track and self‑prompt list. It scales from high‑level product design down to service/component details.

## 1) Clarify Requirements

* **Users & personas**: Who, core journeys, SLAs per persona.
* **Functional**: Must‑haves vs nice‑to‑haves; out of scope.
* **Non‑functional**: Latency (P50/P95/P99), availability target (e.g., 99.9%), durability, throughput, freshness, cost.
* **Success metrics**: e.g., p95 latency < 300ms, 99.9% availability, $X/mo infra.
* **Constraints**: Regs (PII/PHI/PCI), data residency, on‑prem/cloud, languages.

### Prompts
* "Who are primary users and their top 3 actions?"
* "What are the strictest SLOs? Any hard constraints?"

## 2) Use Cases & APIs

* **Core flows**: enumerate primary read/write operations.
* **External integrations**: third‑party APIs, webhooks, auth models (OAuth/JWT/SAML).
* **API shape**: REST/GraphQL/gRPC; idempotency; pagination; rate limits.

### Prompts
* "List CRUD operations and their frequency."
* "What must be idempotent? What requires strong guarantees?"

## 3) Back‑of‑the‑Envelope (Capacity Planning)

* **Traffic**: DAU/MAU, RPS/QPS (read vs write), fan‑out.
* **Data volume**: objects/day, average object size, total retained.
* **Growth & peaks**: diurnal patterns, events, 10× scenario.

### Quick math template
* Requests/sec = users × actions/user × (1/sec)
* Storage/day = events/day × bytes/event

## 4) Data Model & Storage

* **Entities & relationships**: ERD sketch; access patterns → indexes.
* **Store choice**: SQL vs NoSQL vs time‑series vs blob store. Justify by access patterns, consistency, and scale.
* **Partitioning**: keys, hot‑key mitigation, regional sharding.
* **Retention & lifecycle**: TTL, archiving, GDPR deletion.

### Prompts
* "What queries must be fast? Which fields index?"
* "What is the partition key and why?"

## 5) High‑Level Architecture

* **Client → Edge**: CDN, WAF, TLS, mobile/web.
* **Gateway/Ingress**: API gateway, authn/z, throttling.
* **Services**: stateless compute, stateful dependencies.
* **Async**: queues, streams, schedulers, outbox pattern.
* **Data**: primary DB, caches, search, analytics/ETL lake.

### Prompts
* "Which paths are synchronous vs asynchronous?"
* "Where do we apply backpressure?"

## 6) Performance & Caching

* **Layers**: CDN, edge cache, service cache (Redis/Memcached), client cache.
* **Strategies**: TTL, write‑through, write‑back, cache‑aside; invalidation signals.
* **Hot paths**: precompute/denormalize; use read replicas.

### Prompts
* "What's the p95 path? Can we serve from cache?"

## 7) Scaling & Partitioning

* **Horizontal scale**: stateless services, autoscaling.
* **LB strategies**: L7 routing, sticky vs non‑sticky.
* **DB scaling**: sharding, replicas, CQRS, read/write split.
* **Workload isolation**: noisy‑neighbor controls; multi‑tenant boundaries.

### Prompts
* "Where do we shard and by what key?"
* "How do we handle hot partitions?"

## 8) Consistency, Transactions, and Freshness

* **Guarantees**: strong vs eventual; monotonic reads; read‑your‑writes.
* **Transactions**: single‑writer, saga/compensation, idempotency keys.
* **Ordering**: per‑key ordering via partition key.
* **Staleness**: acceptable lag for replicas/caches.

### Prompts
* "What must be strictly consistent?"
* "How do we design compensating actions?"

## 9) Reliability & Availability

* **Targets**: SLOs, error budgets.
* **Redundancy**: multi‑AZ/region, quorum, failover plans.
* **Resilience patterns**: retries w/ jitter, timeouts, circuit breakers, bulkheads, hedged requests, idempotency.
* **Disaster recovery**: RPO/RTO, snapshots, runbooks, chaos tests.

### Prompts
* "What fails if region X is down? What is our RPO/RTO?"

## 10) Security & Privacy

* **AuthN/Z**: OAuth/OIDC, short‑lived tokens, scopes, least privilege.
* **Data protection**: encryption in transit/at rest, key mgmt, secrets.
* **Privacy**: PII/PHI/PCI boundaries, tokenization, differential access.
* **Abuse prevention**: rate limits, bot detection, WAF, audit logging.

### Prompts
* "What data classes exist and how are they isolated?"

## 11) Observability & Operations

* **Metrics**: RED/USE, SLI/SLO dashboards; high‑cardinality cautions.
* **Logs/Traces**: structured logs, trace context propagation.
* **Alarming**: symptom‑based alerts; paging policy.
* **Runbooks & on‑call**: escalation, feature flags, kill‑switches.

### Prompts
* "Which symptoms page us? What's in the runbook?"

## 12) Cost & Efficiency

* **Drivers**: egress, storage tiers, hot vs cold paths, over‑provisioning.
* **Levers**: reserved/spot instances, compression, TTLs, batching.
* **Guardrails**: budgets, cost dashboards, per‑tenant limits.

### Prompts
* "Where are the top 3 cost sinks and how can we cap them?"

## 13) Evolution & Rollouts

* **Versioning**: API & schema; backward compatibility.
* **Migrations**: dual‑write/read, backfills, online schema change.
* **Deployments**: canary, blue‑green, feature flags, staged rollouts.
* **Roadmap**: V1, V2+, de‑risking sequence, technical debt log.

### Prompts
* "What's our safe rollout for V1? What can we defer?"

## 14) Testing Strategy

* Deterministic units; contract tests for APIs.
* Soak/load tests; fault injection (latency/packet loss).
* Replay from prod logs in staging; shadow traffic.

## 15) Failure & Edge Cases

* Partial outage behavior; degraded mode (read‑only, stale reads).
* Duplicate events, retries, idempotency; poison messages.
* Large fan‑out, thundering herd; cold starts; clock skew.

## 16) Trade‑offs & Decision Log (keep visible)

* Problem → Options → Chosen → Why → Risks → Mitigations.  
* Use [Architecture Decision Record](../communication-templates/06-architecture-decision-record.md) template for formal documentation.

## 17) Close Strong (2‑minute wrap)

* Restate requirements, show the final diagram path.
* Reiterate SLOs achieved, scaling plan, and biggest risks w/ mitigations.

## Level‑Specific Drilldowns

### A) Service Level
* Interface (proto/OpenAPI), idempotency, pagination.
* Read vs write path sequence diagrams.
* Cache keys & invalidation strategy.
* Retry matrix (client ↔ service ↔ DB).

### B) Data Layer
* Table/collection schemas with key choices and critical indexes.
* Shard key, secondary indexes, compound keys.
* Consistency level per operation; replica routing; TTLs.

### C) API Endpoint (Micro‑level)
* Request/response shape; status codes; error taxonomy.
* Rate limit numbers; timeouts; retries; dedupe keys.

## Handy Reference (Back‑of‑napkin)

* **Latency ballpark**: L1 cache ~0.5ns; RAM ~100ns; SSD ~100µs; DC RTT ~0.5–1ms; cross‑region ~50–150ms.
* **Throughput**: Single core ~1–3G ops/s simple ops; Redis ~100k–1M ops/s/node (pattern‑dependent).
* **Storage**: JSON row often 0.5–2 KB; image 100–500 KB thumbnail; event 0.1–1 KB.

*(Numbers are order‑of‑magnitude guides; justify with assumptions.)*

## Red Flags to Avoid

* Premature tech choices without access patterns.
* Ignoring P95/P99; designing only for averages.
* Single region/zone; no backpressure.
* No plan for migrations or cache invalidation.

## Reusable Phrases (Interview‑friendly)

* "Given the read‑heavy profile, I'll introduce a cache‑aside layer with TTL and explicit invalidation on writes."
* "We'll shard by tenant_id to localize hot keys and enable per‑tenant throttling."
* "I'm choosing eventual consistency for the feed but strong consistency for payments."
* "Our DR target is RPO 5 min via incremental snapshots and RTO 30 min with automated failover."

## Optional: Phased Rollout Template

* **Phase 1 (V1)**: single region, read replicas, simple cache, basic metrics.
* **Phase 2**: async pipelines, queues, better indexing, canary deploys.
* **Phase 3**: multi‑region active‑passive/active‑active, advanced cost controls, chaos engineering.
