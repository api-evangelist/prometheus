# Prometheus (prometheus)

An open-source systems monitoring and alerting toolkit originally built at SoundCloud. Prometheus collects and stores metrics as time series data and provides a powerful query language (PromQL) for analysis.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/prometheus/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/prometheus/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- Alerting
- Metrics
- Monitoring
- Observability
- Time Series

## Timestamps

- **Created:** 2024-01-01
- **Modified:** 2026-05-19

## APIs

### Prometheus HTTP API

The Prometheus HTTP API provides endpoints for executing instant and range queries using PromQL, querying metadata such as labels and series, and managing targets and rules. The API is reachable under /api/v1 on a Prometheus server and returns JSON responses. An OpenAPI specification is served at /api/v1/openapi.yaml.

- **Human URL:** [https://prometheus.io/docs/prometheus/latest/querying/api/](https://prometheus.io/docs/prometheus/latest/querying/api/)

#### Tags

- Metrics
- PromQL
- Query
- Time Series

#### Properties

- [Documentation](https://prometheus.io/docs/prometheus/latest/querying/api/)
- [OpenAPI](openapi/prometheus-http-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/prometheus-http-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-http-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [GitHub Repository](https://github.com/prometheus/prometheus)
- [Changelog](https://github.com/prometheus/prometheus/blob/main/CHANGELOG.md)
- [JSON Schema](json-schema/prometheus-metrics-schema.json) — [JSON Schema](https://json-schema.org/specification)

### Prometheus Management API

The Prometheus Management API provides administrative endpoints for managing a running Prometheus server, including configuration reloads, taking snapshots of the TSDB, cleaning tombstones, and graceful shutdown. These endpoints are disabled by default and require the --web.enable-lifecycle flag.

- **Human URL:** [https://prometheus.io/docs/prometheus/latest/management_api/](https://prometheus.io/docs/prometheus/latest/management_api/)

#### Tags

- Administration
- Management
- Monitoring

#### Properties

- [Documentation](https://prometheus.io/docs/prometheus/latest/management_api/)
- [OpenAPI](openapi/prometheus-management-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/prometheus-management-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-management-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Prometheus Pushgateway API

The Pushgateway API accepts metrics pushed from short-lived batch jobs and ephemeral processes via HTTP PUT, POST, and DELETE requests. Metrics are organized by job and optional grouping labels, and are exposed for Prometheus scraping until explicitly deleted.

- **Human URL:** [https://github.com/prometheus/pushgateway](https://github.com/prometheus/pushgateway)

#### Tags

- Batch Jobs
- Metrics
- Pushgateway

#### Properties

- [Documentation](https://github.com/prometheus/pushgateway/blob/master/README.md)
- [OpenAPI](openapi/prometheus-pushgateway-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/prometheus-pushgateway-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-pushgateway-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [GitHub Repository](https://github.com/prometheus/pushgateway)
- [Changelog](https://github.com/prometheus/pushgateway/releases)

### Prometheus Alertmanager API

The Alertmanager API v2 provides HTTP endpoints for querying alert status, managing silences and inhibitions, retrieving receiver configurations, and checking cluster status. An OpenAPI 2.0 specification is available in the Alertmanager repository.

- **Human URL:** [https://prometheus.io/docs/alerting/latest/alertmanager/](https://prometheus.io/docs/alerting/latest/alertmanager/)

#### Tags

- Alerting
- Notifications
- Silences

#### Properties

- [Documentation](https://prometheus.io/docs/alerting/latest/alertmanager/)
- [OpenAPI](openapi/prometheus-alertmanager-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/prometheus-alertmanager-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-alertmanager-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [AsyncAPI](asyncapi/prometheus-alertmanager-webhook-asyncapi.yml) — [AsyncAPI Specification](https://www.asyncapi.com/docs/reference/specification/latest)
- [GitHub Repository](https://github.com/prometheus/alertmanager)
- [Changelog](https://github.com/prometheus/alertmanager/releases)

### Prometheus Remote Write API

The Prometheus Remote Write API defines a standard protocol for sending time series data from Prometheus or compatible agents to remote storage backends via HTTP POST with Snappy-compressed protobuf payloads. The specification documents the wire format, required headers, and retry semantics for interoperable remote write implementations.

- **Human URL:** [https://prometheus.io/docs/specs/prw/remote_write_spec/](https://prometheus.io/docs/specs/prw/remote_write_spec/)

#### Tags

- Integration
- Remote Write
- Storage
- Time Series

#### Properties

- [Documentation](https://prometheus.io/docs/specs/prw/remote_write_spec/)
- [Postman Collection](collections/prometheus-alertmanager-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-alertmanager-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/prometheus-http-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-http-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/prometheus-management-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-management-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/prometheus-pushgateway-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-pushgateway-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Prometheus Client Libraries

Prometheus provides official client libraries for Go, Java/Scala, Python, Ruby, and Rust that enable application instrumentation. Libraries implement the Prometheus metric types (Counter, Gauge, Histogram, Summary) and expose metrics via an HTTP endpoint for Prometheus to scrape.

- **Human URL:** [https://prometheus.io/docs/instrumenting/clientlibs/](https://prometheus.io/docs/instrumenting/clientlibs/)

#### Tags

- Client Libraries
- Instrumentation
- Metrics
- SDK

#### Properties

- [Documentation](https://prometheus.io/docs/instrumenting/clientlibs/)
- [Reference](https://prometheus.io/docs/instrumenting/writing_clientlibs/)
- [Postman Collection](collections/prometheus-alertmanager-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-alertmanager-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/prometheus-http-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-http-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/prometheus-management-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-management-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/prometheus-pushgateway-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/prometheus-pushgateway-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [JSON-LD](json-ld/prometheus-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON Schema](json-schema/prometheus-metrics-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [Website](https://prometheus.io)
- [Documentation](https://prometheus.io/docs/)
- [Getting Started](https://prometheus.io/docs/introduction/getting_started/)
- [GitHub Organization](https://github.com/prometheus)
- [GitHub Repository](https://github.com/prometheus/prometheus)
- [Blog](https://prometheus.io/blog/)
- [Community](https://prometheus.io/community/)
- [Changelog](https://github.com/prometheus/prometheus/releases)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/prometheus)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
