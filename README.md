# Falco (falco)
Falco is a cloud-native runtime security tool that detects unexpected application behavior and alerts on threats at runtime using eBPF. It is a CNCF graduated project that continuously monitors Linux kernel syscalls and compares them against configurable security rules to detect intrusions, privilege escalation, and other suspicious behaviors.

**URL:** [Visit APIs.json URL](https://falco.org)

## Scope

- **Type:** Index 
- **Position:** Consuming 
- **Access:** 3rd-Party 

## Tags:

 - Security, Runtime Security, Cloud Native, eBPF, Threat Detection

## Timestamps

- **Created:** 2025-01-01 
- **Modified:** 2026-03-18 

## APIs

### Falco HTTP API
REST API served by the Falco web server providing health checks, version information, and rules management endpoints for the Falco runtime security engine.

**Human URL:** [https://falco.org/docs/](https://falco.org/docs/)


#### Tags:

 - Security, Runtime Security, Health Check

#### Properties

- [Documentation](https://falco.org/docs/)
- [Reference](https://falco.org/docs/reference/)
- [OpenAPI](openapi/falco-openapi.yml)
- [JSONSchema](json-schema/falco-alert-output.json)
- [JSONSchema](json-schema/falco-rules.json)
- [GitHubRepository](https://github.com/falcosecurity/falco)

### Falco Plugin API
The Falco Plugin API provides a C ABI interface for developing plugins that extend Falco with new event sources and field extractors. Plugins are shared libraries that implement the plugin API and can be loaded at runtime to add support for new data sources such as cloud audit logs, container activity, and custom event streams.

**Human URL:** [https://falco.org/docs/reference/plugins/plugin-api-reference/](https://falco.org/docs/reference/plugins/plugin-api-reference/)


#### Tags:

 - Plugin, Developer Tools, Event Sources

#### Properties

- [Documentation](https://falco.org/docs/developer-guide/)
- [Reference](https://falco.org/docs/reference/plugins/plugin-api-reference/)
- [GitHubRepository](https://github.com/falcosecurity/plugin-sdk-go)

### Falco gRPC API
The Falco gRPC API provided a streaming interface for consuming Falco alert outputs and querying version information from a running Falco instance. The embedded gRPC server and gRPC Output have been deprecated in Falco 0.43.0 and will be removed in a future release.

**Human URL:** [https://falco.org/docs/developer-guide/grpc/](https://falco.org/docs/developer-guide/grpc/)


#### Tags:

 - gRPC, Security, Deprecated

#### Properties

- [Documentation](https://falco.org/docs/developer-guide/grpc/)
- [Deprecation Notice](https://falco.org/blog/falco-0-43-0/)
- [GitHubRepository](https://github.com/falcosecurity/falco)

## Common Properties

- [Website](https://falco.org)
- [Documentation](https://falco.org/docs/)
- [Blog](https://falco.org/blog/)
- [Community](https://falco.org/community/)
- [Getting Started](https://falco.org/docs/getting-started/)
- [Change Log](https://falco.org/docs/reference/changelog/)
- [GitHub Organization](https://github.com/falcosecurity)
- [GitHubRepository](https://github.com/falcosecurity/falco)
- [JSON-LD](json-ld/falco-context.jsonld)

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
