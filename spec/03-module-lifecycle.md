# 3. Module Lifecycle and Boot Flow

This section describes the lifecycle of a module and how the orchestrator initializes, supervises, and shuts down the system.

## 3.1 Boot Flow Overview

The orchestrator follows a deterministic startup sequence:

1. **Cold Start / PID 1 (if applicable):**
   - Initializes as the root process.
   - Starts minimal platform services (e.g., fallback logger, process reaper).
   - Scans configuration files and validates consistency.
   - If cache is valid, loads the compiled boot plan.
   - Otherwise, regenerates the plan and writes it to disk.

2. **Preflight Check (optional):**
   - Verifies the integrity and compatibility of all declared modules.
   - Checks that required executables and permissions are in place.
   - Can be triggered manually when configuration changes are made.

3. **Module Initialization:**
   - Modules are initialized in dependency order.
   - Each module may start with its declared sidecars.
   - A handshake is initiated, exchanging secrets to establish trust.
   - Health check endpoints are monitored to confirm readiness.

4. **System Ready:**
   - Once all required modules are healthy and reported "ready", the system is considered operational.
   - Non-critical or delayed-start modules may continue launching in the background.

## 3.2 Module Lifecycle States

A module transitions through several states:

- `pending`: Not yet initialized.
- `initializing`: Handshake and setup in progress.
- `ready`: Operational and healthy.
- `degraded`: Running but with limited functionality.
- `unhealthy`: Failed health check but still running.
- `crashed`: Process exited unexpectedly.
- `restarting`: Restart policy applied.
- `disabled`: Manually disabled or configuration skipped.

## 3.3 Failure Recovery

Each module may define a restart policy:

- `always`: Restart on any failure.
- `on-crash`: Restart only if exited abnormally.
- `never`: Do not restart.

If restart attempts fail repeatedly, escalation policy applies:

- Alert operator or log to emergency channel.
- Trigger fallback service (e.g., fallback logger, fallback network).
- Enter degraded mode.
- Invoke `bluescreen` or `system recovery` module as a last resort.

## 3.4 Shutdown Sequence

On system shutdown:

1. Orchestrator signals modules in reverse dependency order.
2. Graceful shutdown timeout is enforced.
3. Remaining modules are force-killed if unresponsive.
4. Final fallback logger and emergency systems are shut down last.

## 3.5 Deferred and Lazy Modules

Modules can be tagged as:

- `deferred`: Launched after critical modules are ready.
- `on-demand`: Spawned only when requested by another module or the user.

This allows optimization for embedded or fast-boot environments.

## 3.6 Parallelism and Scheduling

The orchestrator may run modules in parallel where no dependencies conflict.  
Each launch can include:

- Timeout enforcement
- CPU/memory constraints
- Priority weighting
- Log isolation via sidecar or sandboxing

## 3.7 Zombie Reaping

If running as PID 1, the orchestrator reaps any child processes that exit.  
A lightweight process monitor ensures that orphaned PIDs do not accumulate.

---
