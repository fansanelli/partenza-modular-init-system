# 5. Inter-Module Communication

Modules within a Partenza-based system can communicate with each other using standard, well-defined interfaces. Communication is intentionally decoupled to allow for modularity, isolation, and flexibility.

## 5.1 Interfaces

Each module **declares the interfaces it provides and consumes**. These interfaces are uniquely named and versioned.

Example:

Module `foo-net` provides:
- interface: `network/basic`, version `1.0`

Module `foo-dns` requires:
- `network/basic`, version `>=1.0`

The orchestrator ensures that dependencies are resolved before starting a module.

## 5.2 Communication Channels

Communication between modules can happen via:

- **Unix domain sockets**
- **Named pipes**
- **HTTP/HTTPS over loopback**
- **Shared memory (advanced use cases)**
- **DBus (if supported)**

The channel type is implementation-defined and negotiable between orchestrator and module, but the interface must declare how it exposes its functionality.

## 5.3 Interface Contracts

An interface is a formal specification describing:

- **Methods or endpoints** exposed
- **Expected arguments and response structure**
- **Behavior and lifecycle expectations**
- **Optional timeouts and retry policies**

Interfaces should be published as separate documentation files or schemas.

## 5.4 Runtime Discovery

The orchestrator exposes a **service registry API** to modules, so that they can:

- Discover available services (matching required interfaces)
- Query endpoint details (address, method, protocol)
- Optionally subscribe to lifecycle events (start/stop/failure)

This avoids hardcoded paths or ports and ensures late binding of services.

## 5.5 Sidecar Mediation

Sidecars may act as **communication proxies**, enforcing policies like:

- Authentication (via handshake or token)
- Rate limiting
- Logging or tracing
- Version negotiation

A sidecar is simply another module launched in parallel and listed in the `sidecars` field. The orchestrator ensures it is running before the main module.

## 5.6 Permissions and Isolation

All inter-module communication must honor the defined permissions. If a module is not permitted to talk to another (e.g., lacks capability or interface permission), the orchestrator MUST deny or isolate the interaction.

In secure systems, message integrity or origin can be enforced using:
- Signatures
- Token exchange
- TLS certificates (in local HTTPS)
