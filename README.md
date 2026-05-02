# Prometheus (prometheus)

An open-source systems monitoring and alerting toolkit originally built at SoundCloud. Prometheus collects and stores metrics as time series data and provides a powerful query language (PromQL) for analysis.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/prometheus/refs/heads/main/apis.yml)

## Tags

- Alerting, Metrics, Monitoring, Observability, Time Series

## Timestamps

- **Created:** 2024-01-01
- **Modified:** 2026-04-28

## APIs

### Prometheus HTTP API
The Prometheus HTTP API provides endpoints for executing instant and range queries using PromQL, querying metadata such as labels and series, and managing targets and rules. The API is reachable under /api/v1 on a Prometheus server and returns JSON responses.

**Human URL:** [https://prometheus.io/docs/prometheus/latest/querying/api/](https://prometheus.io/docs/prometheus/latest/querying/api/)

#### Tags
- Metrics, PromQL, Query, Time Series

#### Properties
- [Documentation](https://prometheus.io/docs/prometheus/latest/querying/api/)
- [OpenAPI](openapi/prometheus-http-api-openapi.yml)
- [GitHubRepository](https://github.com/prometheus/prometheus)
- [Change Log](https://github.com/prometheus/prometheus/blob/main/CHANGELOG.md)
- [JSONSchema](json-schema/prometheus-metrics-schema.json)

### Prometheus Management API
Administrative endpoints for managing a running Prometheus server, including configuration reloads, taking snapshots of the TSDB, cleaning tombstones, and graceful shutdown. Disabled by default; requires the --web.enable-lifecycle flag.

**Human URL:** [https://prometheus.io/docs/prometheus/latest/management_api/](https://prometheus.io/docs/prometheus/latest/management_api/)

#### Tags
- Administration, Management, Monitoring

#### Properties
- [Documentation](https://prometheus.io/docs/prometheus/latest/management_api/)
- [OpenAPI](openapi/prometheus-management-api-openapi.yml)

### Prometheus Pushgateway API
The Pushgateway API accepts metrics pushed from short-lived batch jobs and ephemeral processes via HTTP PUT, POST, and DELETE requests. Metrics are organized by job and optional grouping labels, and exposed for Prometheus scraping until explicitly deleted.

**Human URL:** [https://github.com/prometheus/pushgateway](https://github.com/prometheus/pushgateway)

#### Tags
- Batch Jobs, Metrics, Pushgateway

#### Properties
- [Documentation](https://github.com/prometheus/pushgateway/blob/master/README.md)
- [OpenAPI](openapi/prometheus-pushgateway-api-openapi.yml)
- [GitHubRepository](https://github.com/prometheus/pushgateway)
- [Change Log](https://github.com/prometheus/pushgateway/releases)

### Prometheus Alertmanager API
The Alertmanager API v2 provides HTTP endpoints for querying alert status, managing silences and inhibitions, retrieving receiver configurations, and checking cluster status.

**Human URL:** [https://prometheus.io/docs/alerting/latest/alertmanager/](https://prometheus.io/docs/alerting/latest/alertmanager/)

#### Tags
- Alerting, Notifications, Silences

#### Properties
- [Documentation](https://prometheus.io/docs/alerting/latest/alertmanager/)
- [OpenAPI](openapi/prometheus-alertmanager-api-openapi.yml)
- [AsyncAPI](asyncapi/prometheus-alertmanager-webhook-asyncapi.yml)
- [GitHubRepository](https://github.com/prometheus/alertmanager)
- [Change Log](https://github.com/prometheus/alertmanager/releases)

### Prometheus Remote Write API
Defines a standard protocol for sending time series data from Prometheus or compatible agents to remote storage backends via HTTP POST with Snappy-compressed protobuf payloads.

**Human URL:** [https://prometheus.io/docs/specs/prw/remote_write_spec/](https://prometheus.io/docs/specs/prw/remote_write_spec/)

#### Tags
- Integration, Remote Write, Storage, Time Series

#### Properties
- [Documentation](https://prometheus.io/docs/specs/prw/remote_write_spec/)

### Prometheus Client Libraries
Official client libraries for Go, Java/Scala, Python, Ruby, and Rust that enable application instrumentation. Implement Counter, Gauge, Histogram, and Summary metric types and expose metrics via an HTTP endpoint for Prometheus to scrape.

**Human URL:** [https://prometheus.io/docs/instrumenting/clientlibs/](https://prometheus.io/docs/instrumenting/clientlibs/)

#### Tags
- Client Libraries, Instrumentation, Metrics, SDK

#### Properties
- [Documentation](https://prometheus.io/docs/instrumenting/clientlibs/)
- [Reference](https://prometheus.io/docs/instrumenting/writing_clientlibs/)

## Common Properties

- [JSON-LD](json-ld/prometheus-context.jsonld)
- [JSONSchema](json-schema/prometheus-metrics-schema.json)
- [Website](https://prometheus.io)
- [Documentation](https://prometheus.io/docs/)
- [Getting Started](https://prometheus.io/docs/introduction/getting_started/)
- [GitHub Organization](https://github.com/prometheus)
- [GitHubRepository](https://github.com/prometheus/prometheus)
- [Blog](https://prometheus.io/blog/)
- [Community](https://prometheus.io/community/)
- [Change Log](https://github.com/prometheus/prometheus/releases)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/prometheus)

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
