# 7. Health Checking

Partenza provides a standard for runtime health monitoring of active modules to ensure system stability, responsiveness, and resilience.

## 7.1 Goals

- Detect and handle module failures
- Support user-space status tools
- Allow modules to self-report health
- Define restart and fallback strategies

## 7.2 Module Declaration

Each module may declare whether it supports health checking via a `health` section in its manifest.

Example:

```yaml
  health:
    probe: "http://localhost:8080/health"
    interval: 5s
    timeout: 1s
    restart: "on-failure"
    max_retries: 3
```

### Fields:

- `probe`: command or endpoint to invoke
- `interval`: how often the orchestrator probes the module
- `timeout`: maximum wait before marking the probe as failed
- `restart`: strategy (`never`, `on-failure`, `always`)
- `max_retries`: maximum restart attempts before escalation

## 7.3 Types of Health Probes

- **Command-based**: return code of a shell command
- **HTTP-based**: 200 response from a given endpoint
- **Socket ping**: handshake response on a Unix socket
- **Custom probes**: defined by a sidecar module

The orchestrator interprets success/failure and applies the policy.

## 7.4 Fallback and Recovery

When a module is deemed unhealthy:

- If `restart` is `on-failure`, the orchestrator attempts a clean restart
- If retries are exhausted, the orchestrator may:
  - Log the failure
  - Notify a fallback module (if defined)
  - Trigger a user alert or enter degraded mode
  - Re-evaluate dependent modules

## 7.5 Critical Modules and Bluescreen Fallback

Modules can declare themselves as **critical**. In such cases, if the orchestrator cannot recover their health:

- It may invoke a predefined `bluescreen` module
- Enter a safe shutdown state
- Halt the system gracefully

Example:

```yaml
  critical: true
  fallback_module: "foo-log-fallback"
```

## 7.6 Visibility and Reporting

Health status of all modules must be made available via the orchestrator:

- CLI: `partenza status`
- API: JSON endpoint (optional)
- Log entries on state transitions
- Machine-readable files in `/run/partenza/status/*.json`

## 7.7 Sidecar-Driven Health

A specialized sidecar may be responsible for advanced probing, e.g.:

- Cluster-aware health monitoring
- Hardware diagnostics
- Security checks

Such sidecars act as agents on behalf of the orchestrator.
