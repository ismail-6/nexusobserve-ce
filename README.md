# NexusObserve CE

NexusObserve CE is a self-hosted operational intelligence platform that brings observability, infrastructure monitoring, real-time operations, alerting, service topology, and AI-agent access to live telemetry into a single control plane.

It combines OpenTelemetry-native ingestion with operational modeling concepts familiar to teams running mission-critical systems, and exposes that context to AI assistants so they can investigate incidents, query telemetry, and assemble context without humans bouncing between dashboards.

## What It Does

- **Telemetry ingestion** — Metrics, logs, and traces over OTLP (HTTP and gRPC), Prometheus scrape, and a first-party agent for hosts, processes, containers, and Kubernetes workloads.
- **Observability surfaces** — Dashboards, log search, metric exploration, distributed tracing, service catalog, and topology maps derived from trace relationships.
- **APM and RUM** — Trace-derived service request, latency, error, and dependency metrics, plus browser real-user monitoring with sessions, errors, and session replay.
- **Alerting and incidents** — Rules, alert history, snoozing, incident workflows, and notification routing across Slack, Discord, email, PagerDuty, Telegram, Microsoft Teams, Google Chat, ServiceNow, HTTP, and generic webhooks.
- **Operational control** — Gateway, probe, sampler, dataview, rule, and command primitives for real-time operations on production estates.
- **AI-native investigation** — A standards-based interface that lets coding and ops agents query metrics, search logs, pull traces, review active alerts, and gather incident context directly.

## Why NexusObserve

NexusObserve is intended as a self-hosted alternative to the patchwork of tools teams typically stitch together — observability backends, infrastructure monitors, log intelligence platforms, and operational control planes — with predictable cost, local control, and AI-first investigation built in from day one.

It is designed to combine:

- OTLP-native metrics, logs, traces, and RUM.
- First-party agents that understand hosts, processes, containers, Kubernetes, databases, and service topology.
- Operational modeling through gateways, probes, entities, samplers, dataviews, rules, active times, and commands.
- Monitoring coverage intelligence that shows what exists, what is monitored, and what is missing.
- AI-native investigation through evidence-linked explanations and incident context APIs.

## Status

NexusObserve CE is under active development. Treat it as ready for controlled dogfooding rather than a drop-in replacement for established commercial observability or operations platforms.

## License

All rights reserved. See [LICENSE](LICENSE).

## Contact

For access, partnerships, or evaluation requests, please reach out to the maintainer.
