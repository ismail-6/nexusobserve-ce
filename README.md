# NexusObserve

NexusObserve is a self-hosted observability and operations platform built for financial services institutions and technology companies running transactional and custom in-house applications. It ingests OpenTelemetry telemetry, models infrastructure and service state, evaluates alert rules, and exposes a programmatic interface for AI-driven investigation.

The platform consolidates responsibilities typically split across an observability backend, an infrastructure monitoring system, and an operational control plane. It is designed for organizations that operate business-critical workloads under regulatory or latency constraints that make third-party SaaS telemetry unsuitable, and that maintain custom applications requiring deep, language-agnostic instrumentation.

---

## Architecture Overview

NexusObserve is composed of three layers:

1. **Collection layer** — A first-party agent and OpenTelemetry-compatible receivers gather telemetry and operational signals from hosts, containers, Kubernetes workloads, applications, and browsers.
2. **Server layer** — A backend service that normalizes telemetry, evaluates rules, manages incidents, exposes APIs, and serves the web console.
3. **Storage layer** — A relational store for control-plane state (users, configuration, rules, alerts, incidents, RUM metadata) and a columnar store for high-volume telemetry (metrics, logs, traces, RUM events). The recommended production deployment uses PostgreSQL paired with ClickHouse; an embedded columnar option is supported for smaller single-node installations.

All telemetry interfaces are OTLP-native, so existing OpenTelemetry SDKs and collectors interoperate without translation layers.

---

## Capabilities

### Telemetry Ingestion

- **OTLP** — Metrics, logs, and traces over HTTP and gRPC, supporting the OpenTelemetry data model including resource attributes, instrumentation scopes, exemplars, span events, and span links.
- **Prometheus scrape** — Pull-based metric collection for existing exporters and exporters embedded in applications.
- **First-party agent** — Native collection for host system metrics, processes, containers, Kubernetes pods and workloads, common databases, and log files. Supports both direct push and gateway-mediated transport.
- **Server-side log pipeline** — JSON extraction, severity normalization, regex parsing, field remapping, redaction of sensitive fields, and rule-based dropping before storage.

### Observability

- **Metrics** — Time-series exploration with filtering by resource attributes, label rewriting, and ad-hoc aggregation across cardinality dimensions.
- **Logs** — Full-text and structured search, faceted filtering, contextual surrounding-event views, and saved queries.
- **Traces** — Distributed trace inspection with span timelines, latency breakdowns, error highlighting, and cross-service navigation.
- **Service catalog and topology** — A graph of services and their dependencies derived directly from trace relationships rather than maintained as a separate registry.
- **Dashboards** — Composable dashboards with parameterized variables, multiple panel types, and shared time ranges.

### Application and Real-User Monitoring

- **Trace-derived APM** — Request rate, latency distributions (including high percentiles), error rate, and inter-service dependency metrics computed from ingested spans without requiring a separate APM agent.
- **Browser RUM** — Session capture, JavaScript error tracking, performance metric collection, and source-map–assisted stack traces.
- **Session replay** — Reconstruction of user sessions for debugging error reports and reproducing user-impacting issues.

### Alerting and Incidents

- **Rule engine** — Threshold and expression-based rules over metrics, logs, and traces, with evaluation cadence, hysteresis, and silencing.
- **Alert lifecycle** — Active alert tracking, history, snoozing, acknowledgement, and suppression windows.
- **Incident management** — Aggregation of related alerts into incidents with timeline, status, and ownership.
- **Notification channels** — Slack, Discord, email, PagerDuty, Telegram, Microsoft Teams, Google Chat, ServiceNow, generic HTTP, and webhooks. Channels can be combined into routing policies based on rule severity, labels, or service.

### Operational Modeling

NexusObserve incorporates an operational modeling layer inspired by gateway-style monitoring systems used in regulated and mission-critical environments:

- **Gateways and probes** — Distributed collection nodes that connect to managed environments and execute samplers.
- **Samplers and dataviews** — Configurable inspection units that surface tabular operational state alongside time-series telemetry.
- **Rules and active times** — Evaluated against dataview state, with time windows that scope when rules apply.
- **Commands** — Operator-initiated actions invokable from the console with audit trails.

### Programmatic Access

NexusObserve exposes a standards-based programmatic interface so external systems — including AI assistants — can perform observability operations without scraping the console:

- Query metrics with filters, aggregations, and time ranges.
- Search logs with structured filters.
- Retrieve full traces by ID and inspect span trees.
- List active alerts and acknowledge or snooze them.
- Pull host inventory and platform health.
- Identify monitoring coverage gaps.
- Assemble incident context bundles for triage.

---

## Deployment Model

NexusObserve is distributed for self-hosted deployment. Supported targets include:

- Single-host installation, suitable for evaluation, small environments, or edge deployments.
- Container-based deployment for local stacks.
- Kubernetes deployment with manifests and a Helm chart for the agent component.
- Systemd-based deployment for traditional virtual machine fleets.

The control plane and telemetry stores can be co-located on a single host or scaled as separate services as ingest volume grows.

---

## Security and Operational Posture

- **Authentication** — Console and API access are authenticated; service-to-service calls support token-based auth.
- **Data residency** — All telemetry and operational state remain within the operator's infrastructure.
- **Cost model** — Resource consumption is bounded by deployed infrastructure, not per-event or per-host vendor pricing.
- **Sensitive data handling** — The log pipeline supports redaction and dropping rules to prevent persistence of regulated content.

---

## Workload Focus

NexusObserve is built around the operational requirements of financial services institutions and technology companies running transactional and custom in-house applications. These workloads share a common profile: business-critical latency budgets, regulated data handling, deep custom instrumentation, and operational practices that pre-date modern OpenTelemetry tooling.

### Financial Services

Banks, trading firms, payment processors, exchanges, asset managers, and fintech platforms operate transactional systems where every request carries measurable business impact. NexusObserve targets these environments with:

- **End-to-end transaction tracing** — Distributed traces span order capture, risk checks, matching, settlement, and downstream messaging, so a single transaction can be reconstructed across every service it touched.
- **High-percentile latency tracking** — Latency distributions are computed at p50, p95, p99, p99.9, and p99.99, matching the tail-latency budgets that govern execution and settlement workflows.
- **Regulatory-grade data handling** — Self-hosting keeps regulated content (PII, PCI, MNPI, transaction data) inside the institution's perimeter. The log pipeline supports redaction and rule-based dropping before persistence.
- **Audit trails** — Operator commands, alert acknowledgements, and incident state changes are recorded with attribution.
- **Gateway-style operational modeling** — Gateways, probes, samplers, dataviews, and commands match the operational model used in trading floors and middle-office monitoring estates, so existing operational practices carry over.
- **Deterministic cost** — Resource consumption is bounded by deployed infrastructure rather than per-event vendor pricing, making the platform viable for high-throughput trading and payment estates where event volume scales nonlinearly.

### Technology Companies

SaaS platforms, internal product engineering teams, and infrastructure organizations running custom backends benefit from:

- **Language-agnostic instrumentation** — OTLP-native ingestion works with every officially supported OpenTelemetry SDK and any language with a community SDK or manual exporter. Custom services are instrumented identically to off-the-shelf components.
- **Service ownership signals** — The service catalog and topology graph make ownership and dependency relationships explicit without requiring a parallel registry.
- **Custom metric exposition** — Application-specific metrics (queue depth, business KPIs, internal SLOs) can be emitted via OTLP or Prometheus and treated as first-class telemetry alongside system metrics.
- **RUM for product engineering** — Browser session capture, error tracking, and replay support frontend and product teams investigating user-impacting regressions.
- **AI-assisted investigation** — The programmatic interface lets coding assistants and ops agents pull production context directly into developer workflows.

### Transactional Application Monitoring

For applications where each request represents a discrete business transaction — orders, payments, trades, bookings, claims, settlements — NexusObserve provides:

- **Span-level transaction reconstruction** — A trace ID corresponds to a complete business transaction, with span attributes carrying transaction identifiers, customer or counterparty context, and business outcome.
- **Error attribution** — Span events and exception records preserve the exact stage where a transaction failed, including upstream and downstream service context.
- **Volume and outcome rules** — Alert rules can fire on transaction volume drops, error-rate spikes, or outcome distributions in addition to traditional system metrics.
- **Forensic context** — RUM session replay, log retention, and trace storage together support post-incident reconstruction without requiring a separate forensic pipeline.

### Custom Application Monitoring

In-house applications — bespoke trading systems, internal platforms, regulatory reporting engines, batch processors, settlement engines — typically lack the ready-made integrations that off-the-shelf observability tools rely on. NexusObserve treats custom applications as the primary case rather than the exception:

- **OTLP-first** — No proprietary agent or SDK is required; instrumentation is added through standard OpenTelemetry libraries.
- **Custom field-level enrichment** — Resource attributes and span attributes carry application-specific dimensions (account ID, desk, region, environment, version, tenant) used for filtering, aggregation, and rule evaluation.
- **Process and sampler probes** — Where direct instrumentation is impractical (legacy binaries, vendor processes, batch jobs), agent-driven probes and samplers surface state into the same telemetry pipeline.
- **Server-side log normalization** — Unstructured or partially structured logs from legacy systems are normalized server-side, so bespoke log formats become queryable without modifying the source application.

---

## Status

Active development. Suitable for evaluation and internal deployments. Not yet positioned as a drop-in replacement for established commercial observability or operations platforms. Interfaces, schemas, and configuration formats may evolve.

---

## License

All rights reserved. See [LICENSE](LICENSE).

---

## Contact

For access, evaluation, or partnership inquiries, contact the maintainer.
