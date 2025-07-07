# 6. User ↔ Module Communication

This section defines how userspace applications (or users directly) can interact with active modules managed by a Partenza-compliant orchestrator.

## 6.1 Direct Communication

By default, Partenza allows userspace programs to communicate directly with modules using traditional methods such as:

- Unix domain sockets (e.g. `/run/foo-log.sock`)
- Named pipes
- Standard input/output
- Files in `/dev` or `/proc`, if applicable

### Example

A logging client can write messages directly to the logger:

```sh
  echo "hello world" > /run/foo-log.sock
```

This behavior is **not forbidden** by Partenza, but **must be documented by the module**.

## 6.2 Declared Interfaces

Each module may declare a formal API or interface it supports. This allows tools, shells or desktops (e.g. GNOME) to communicate with modules without knowing implementation details.

```yaml
  interfaces:
    - logging
    - log-query
```

The orchestrator may expose a service registry so that tools can discover:

- Which module implements `log-query`
- How to reach it (socket path, HTTP port, etc.)
- What version of the interface is supported

## 6.3 Sidecars for Communication

Some modules may delegate their user interaction to a sidecar. For example:

```yaml
  name: foo-log
  sidecars:
    - foo-logctl
```

`foo-logctl` exposes a CLI or socket API on behalf of `foo-log`. It may even provide compatibility with older tools.

## 6.4 User Communication Directory

Partenza recommends using a dedicated, well-known path for communication endpoints:

- `/run/partenza/modules/<module>/`
- `/var/run/partenza/...` as legacy fallback

Inside this directory, a module may place:

- `api.sock` — main communication socket
- `status` — current state file
- `control` — optional named pipe for commands

## 6.5 Optional Orchestrator Proxy

For enhanced mediation or logging, some orchestrators may expose user requests to modules **through a proxy sidecar** (e.g. `foo-proxy`). This is optional and should not block direct communication if the user prefers it.

## 6.6 Stability & Expectations

User communication channels must:

- Be available only **after** the module declares itself `READY`
- Be removed or invalidated if the module crashes or exits
- Avoid blocking the orchestrator if they become overloaded

If necessary, the orchestrator may throttle communication or restart a misbehaving proxy.
