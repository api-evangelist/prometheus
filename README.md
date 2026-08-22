# Prometheus (prometheus)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
