# Observability Stack

All three pillars (logs, metrics, traces) are shipped and integrated in Grafana.

## Structured Logging (Loki)

```
All hermes-* containers (stdout/stderr)
        │
        ▼
    Promtail  ──push──►  Loki  ──query──►  Grafana
    (docker_sd_configs)   (:3100)           (Explore → Loki)
```

- **Format**: JSON in production (`APP_LOGGING__FORMAT=json`), pretty in dev
- **Fields per log line**: `method`, `uri`, `request_id`, `trace_id`, `status`, `latency_ms`, `service`, `level`, `target`
- **Labels extracted by Promtail**: `service` (from container name), `container`, `stream`, `level`, `target`
- **Retention**: 7 days
- **Config**: `hermes-be/infra/loki/loki-config.yml`, `hermes-be/infra/promtail/promtail-config.yml`

## Metrics (Prometheus)

- **Scrape interval**: 15 seconds
- **Metrics per service**:
  - `http_requests_total` — counter, labeled by `method`, `path`, `status`
  - `http_request_duration_seconds` — histogram with custom buckets
- **Endpoint**: `GET /metrics` on each service (mounted outside trace layer to avoid fake spans)
- **Traefik metrics**: Built-in Prometheus exporter, scraped alongside services
- **Config**: `hermes-be/infra/prometheus/prometheus.yml`

## Distributed Tracing (OpenTelemetry → Tempo)

- **Protocol**: W3C `traceparent` header propagation
- **Export**: OTLP/gRPC on port 4317 to Tempo
- **Activation**: Set `APP_LOGGING__OTLP_ENDPOINT=http://hermes-tempo:4317` — when unset, noop propagator
- **Config**: `hermes-be/infra/tempo/tempo.yaml`

### Trace Propagation

1. **Inbound HTTP** (`HermesMakeSpan`): Extracts `traceparent`, opens span, records `trace_id`
2. **Inbound gRPC** (`RequestIdScopeService`): Same, reading from gRPC metadata
3. **Outbound gRPC** (`RequestIdInterceptor`): Injects active span's `traceparent` into metadata
4. **Tempo export**: `tracing_opentelemetry` layer forwards spans to Tempo

## Grafana Dashboards

- **URL**: `http://localhost:3000` (admin/admin)
- **Datasources**: Prometheus, Loki, Tempo (pinned UIDs for stable cross-references)
- **Dashboard**: `hermes-overview.json` — HTTP latency (p50/p95), 5xx rate, log volume, recent errors
- **Cross-linking**:
  - Loki → Tempo: `trace_id` in JSON logs → clickable link to trace flame graph
  - Tempo → Loki: `tracesToLogsV2` → jump from span to related logs
- **Config**: `hermes-be/infra/grafana/provisioning/`

## Health Checks

Every active service exposes:
- `GET /health/live` — liveness probe
- `GET /health/ready` — readiness probe
