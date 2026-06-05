# Falco (falco)

Falco is a cloud-native runtime security tool that detects unexpected application behavior and alerts on threats at runtime using eBPF. It is a CNCF graduated project that continuously monitors Linux kernel syscalls and compares them against configurable security rules to detect intrusions, privilege escalation, and other suspicious behaviors.

**APIs.json:** [https://falco.org](https://falco.org)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags

- Cloud Native
- eBPF
- Runtime Security
- Security
- Threat Detection

## Timestamps

- **Created:** 2025-01-01
- **Modified:** 2026-05-19

## APIs

### Falco HTTP API

REST API served by the Falco web server providing health checks, version information, and rules management endpoints for the Falco runtime security engine.

- **Human URL:** [https://falco.org/docs/](https://falco.org/docs/)

#### Tags

- Health Check
- Runtime Security
- Security

#### Properties

- [Documentation](https://falco.org/docs/)
- [Reference](https://falco.org/docs/reference/)
- [OpenAPI](openapi/falco-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/falco.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/falco.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [JSON Schema](json-schema/falco-alert-output.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/falco-rules.json) — [JSON Schema](https://json-schema.org/specification)
- [GitHub Repository](https://github.com/falcosecurity/falco)

### Falco Plugin API

The Falco Plugin API provides a C ABI interface for developing plugins that extend Falco with new event sources and field extractors. Plugins are shared libraries that implement the plugin API and can be loaded at runtime to add support for new data sources such as cloud audit logs, container activity, and custom event streams.

- **Human URL:** [https://falco.org/docs/reference/plugins/plugin-api-reference/](https://falco.org/docs/reference/plugins/plugin-api-reference/)

#### Tags

- Developer Tools
- Event Sources
- Plugin

#### Properties

- [Documentation](https://falco.org/docs/developer-guide/)
- [Reference](https://falco.org/docs/reference/plugins/plugin-api-reference/)
- [GitHub Repository](https://github.com/falcosecurity/plugin-sdk-go)
- [Postman Collection](collections/falco.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/falco.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Falco gRPC API

The Falco gRPC API provided a streaming interface for consuming Falco alert outputs and querying version information from a running Falco instance. The embedded gRPC server and gRPC Output have been deprecated in Falco 0.43.0 and will be removed in a future release.

- **Human URL:** [https://falco.org/docs/developer-guide/grpc/](https://falco.org/docs/developer-guide/grpc/)

#### Tags

- Deprecated
- gRPC
- Security

#### Properties

- [Documentation](https://falco.org/docs/developer-guide/grpc/)
- [Deprecation  Notice](https://falco.org/blog/falco-0-43-0/)
- [GitHub Repository](https://github.com/falcosecurity/falco)
- [Postman Collection](collections/falco.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/falco.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/falco-security-oss)
- [Website](https://falco.org)
- [Documentation](https://falco.org/docs/)
- [Blog](https://falco.org/blog/)
- [Community](https://falco.org/community/)
- [Getting Started](https://falco.org/docs/getting-started/)
- [Changelog](https://falco.org/docs/reference/changelog/)
- [GitHub Organization](https://github.com/falcosecurity)
- [GitHub Repository](https://github.com/falcosecurity/falco)
- [JSON-LD](json-ld/falco-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
