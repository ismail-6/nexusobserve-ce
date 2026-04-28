# NexusObserve

NexusObserve is a self-hosted observability and operations platform. It ingests OpenTelemetry telemetry, models infrastructure and service state, evaluates alert rules, and exposes a programmatic interface for AI-driven investigation.

The platform consolidates responsibilities typically split across an observability backend, an infrastructure monitoring system, and an operational control plane. It is designed for teams that prefer to run their own data plane rather than ship telemetry to a third-party SaaS.

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

## Use Cases

NexusObserve is intended for:

- **Engineering teams** seeking unified metrics, logs, traces, APM, and RUM under one console without per-signal vendor sprawl.
- **Platform and SRE teams** running diverse fleets of hosts, containers, and Kubernetes workloads who need monitoring coverage intelligence and operational control.
- **Regulated environments** where data residency, deterministic cost, and self-hosting are non-negotiable.
- **Organizations adopting AI-assisted operations** that need a programmatic surface for agents to investigate incidents and answer operational questions.

---

## Status

Active development. Suitable for evaluation and internal deployments. Not yet positioned as a drop-in replacement for established commercial observability or operations platforms. Interfaces, schemas, and configuration formats may evolve.

---

## License

All rights reserved. See [LICENSE](LICENSE).

---

## Contact

For access, evaluation, or partnership inquiries, contact the maintainer.
