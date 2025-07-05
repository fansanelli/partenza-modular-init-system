# 2. Core Concepts

This section defines the foundational components of the Partenza architecture and how they interact.

## 2.1 The Orchestrator

The **Orchestrator** is the central coordinator of the system. Its responsibilities include:

- Bootstrapping the system (especially when PID 1).
- Parsing configuration plans or cached startup manifests.
- Starting and supervising modules.
- Enforcing dependencies and recovery strategies.
- Monitoring health and state across all modules.
- Exposing a system-level API for status and control.

An orchestrator may vary in complexity but must conform to the Partenza specification to be considered compliant.

### Running as PID 1

If the orchestrator is PID 1, it has responsibility over:

- Reaping zombie processes.
- Handling shutdown/reboot signals.
- Acting as the ultimate fallback for crash handling.

However, an orchestrator **can also run as a non-root process**, with limited responsibilities, e.g. in containers or user sessions.

## 2.2 Modules

A **Module** is a standalone unit of functionality. It may handle:

- Logging
- Network configuration
- Time synchronization
- Process supervision
- Health checks
- User services
- Any feature not strictly required in the orchestrator

Modules declare their:

- Capabilities
- Requirements (e.g., network access, user privileges)
- Dependencies on other modules
- Sidecars (optional companions)
- API version and compatibility

Modules are usually written as daemons or long-lived processes.

## 2.3 Providers

A **Provider** is an implementation of a specific capability.  
Multiple providers can exist for the same capability (e.g., multiple DNS resolvers), and the orchestrator may select one based on policy or priority.

## 2.4 Sidecars

**Sidecars** are auxiliary modules that extend or secure other modules.  
Examples include:

- A handshake verifier that validates secrets between orchestrator and service
- A proxy module that rate-limits or exposes HTTP interfaces
- A metrics sidecar that gathers and forwards telemetry

Sidecars are explicitly declared in the configuration and are bound to the lifecycle of their target module.

## 2.5 Contracts and API Versions

Each module communicates with the orchestrator via a contract defined by the spec.  
This includes:

- A handshake protocol
- Health check endpoint
- Capability declaration
- Configuration validation interface

Modules must declare their **Partenza API version** (e.g., `1.0`, `1.1`) and whether they are backward or forward compatible.

## 2.6 Plans and Manifests

A **Plan** is the orchestrator’s full understanding of the system configuration:

- What modules should run
- In what order and under which conditions
- Which dependencies exist
- What fallback strategies to apply

It is compiled at boot or cached between boots for performance.  
Users can trigger a dry-run or validation phase to check consistency without booting.

## 2.7 Health Checking

All modules should expose a minimal health interface (e.g., a heartbeat or status query).  
The orchestrator uses this to:

- Detect crashes or hangs
- Restart or escalate as needed
- Track state transitions (initializing, ready, degraded, etc.)

Failing modules may be recovered, shut down gracefully, or trigger a full system failover.

## 2.8 Fallbacks and Safety

Fallback mechanisms are a critical part of Partenza:

- A **fallback logger** ensures visibility even if advanced logging fails.
- A **bluescreen** module may trigger a safe shutdown or panic in critical cases.
- Broken dependencies result in degraded state, not silent failure.

Systems must remain observable, even in partial failure.
