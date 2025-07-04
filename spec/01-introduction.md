# 1. Introduction

## Purpose and Scope

**Partenza** is a modular, specification-driven init system architecture.  
It is not an implementation, but a design: a method for structuring service orchestration, startup, and runtime system behavior in a predictable, secure, and extensible way.

The purpose of this specification is to:

- Define a minimal, pluggable foundation for boot and service orchestration.
- Separate concerns between orchestrators, modules, and extensions.
- Promote clarity, portability, and composability across Linux and Unix-like systems.
- Enable experimentation and alternative implementations without fragmentation.

## Design Philosophy

Partenza is based on the following principles:

- **Minimal core**: Only the essential logic is in the orchestrator; everything else is modular.
- **Separation of concerns**: Logging, networking, health-checking, etc., are decoupled.
- **Zero Trust**: Permissions and trust boundaries are explicitly declared.
- **Sidecar-first**: Cross-cutting features (e.g., authentication, proxying) are handled via companion modules.
- **Fail-fast, recover-fast**: Stability is achieved through health checks and structured fallback.
- **Specification first**: Code follows the spec, not the other way around.
- **Runtime introspection**: The system is observable, testable, and verifiable before and after boot.

## Target Platforms and Use Cases

Partenza is designed to support a wide range of platforms, including:

- Embedded systems (e.g. routers, IoT devices)
- General-purpose Linux distributions
- Custom or experimental userlands
- Cloud-native environments and container hosts
- Security-sensitive deployments (e.g. air-gapped or auditable systems)
- Development and academic platforms

## Relationship to Existing Init Systems

Partenza does not replace existing init systems like `systemd`, `OpenRC`, or `initWare`.  
Instead, it defines a way they could **become conformant**, or expose their functionality in a compatible manner through a module-based interface.

An orchestrator may be written from scratch or adapt an existing system by wrapping or proxying through conformant modules.  
Compatibility layers may allow legacy tools and desktops (e.g., GNOME, KDE) to continue functioning without disruption.
