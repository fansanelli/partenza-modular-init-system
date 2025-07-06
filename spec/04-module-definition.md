# 4. Module Definition

Each module is described using a YAML file. This file tells the orchestrator how to launch, supervise, and interact with the module.

## 4.1 Basic Structure

```yaml
id: foo-log  
version: "1.0.0"  
entrypoint: /usr/bin/foo-log  

description: "Basic logging service"  

provides:  
  - interface: log/basic  
    version: "1.0"  

requires:  
  - foo-network  

sidecars:  
  - foo-handshake  

permissions:  
  run_as: root  
  capabilities:  
    - CAP_SYSLOG  

restart: on-failure  

healthcheck:  
  type: http  
  path: /health  
  interval: 5s  
```

## 4.2 Field Descriptions

### id  
Unique identifier of the module.

### version  
Semantic version of the module.

### entrypoint  
Absolute path to the binary to execute.

### description  
Human-readable explanation of the module's role.

### provides  
List of interfaces implemented by the module.  
Each item includes an interface name and a version.

### requires  
List of module IDs that must be running before this module starts.

### sidecars  
Optional list of additional modules that must be started alongside this one.  
Used for auxiliary functions (e.g., handshake, proxy).

### permissions  
Defines execution context. Includes `run_as` (user or UID), and optional `capabilities` required.

### restart  
Restart policy in case the process crashes.  
Options: `never`, `on-failure`, `always`.

### healthcheck  
Describes how to check module liveness.  
Includes a `type` (e.g., `http`, `command`, `none`), `path`, and `interval`.
