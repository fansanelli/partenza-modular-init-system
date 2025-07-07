# 8. Configuration Validation and Compatibility

This section outlines how Partenza-compatible orchestrators should verify, validate, and simulate configuration files before attempting a real boot.

## 8.1 Goals

- Detect misconfigured modules before they are started
- Prevent runtime errors caused by unresolved dependencies or invalid declarations
- Enable tooling to simulate a boot plan
- Ensure interface/API compatibility between orchestrator and modules

## 8.2 Preflight Checks

Whenever configuration files are added or modified, the orchestrator SHOULD:

- Parse and validate the YAML syntax
- Resolve all `depends_on` fields
- Ensure all declared interfaces are either implemented locally or marked as optional
- Detect invalid or obsolete module declarations
- Check for circular dependencies (see §8.4)

This can be done proactively at edit time (via a CLI tool) or lazily at boot.

## 8.3 Cached Boot Plans

The orchestrator MAY store a boot plan (i.e., a resolved dependency graph and startup order) in a local cache (e.g. `.partenza-plan.md`) to improve startup performance.

When nothing has changed, this plan can be reused.

## 8.4 Loop Detection

To avoid deadlocks, Partenza requires that all dependency graphs be acyclic.

If a loop is detected during validation, the orchestrator MUST:

- Abort the boot plan
- Emit a clear error
- Suggest candidate modules responsible

## 8.5 Simulated Dry Run

A dry run tool SHOULD be provided (e.g., `partenza-dryrun`) that:

- Parses all active configuration files
- Validates schemas and dependencies
- Prints a simulated boot plan
- Returns a non-zero code if problems are found

## 8.6 API Compatibility

Each module must declare the version of the interface it implements:

```yaml
  interfaces:
    - network@1.0
    - dhcp@2.1
```

Orchestrators must declare their own supported interface versions:

```yaml
  orchestrator:
    interfaces:
      - logging@1.0
      - healthcheck@1.2
```

Validation tools must ensure that:

- Required interfaces are matched by compatible providers
- Interface versions follow semver rules:
  - Minor version upgrades are backward compatible
  - Major changes may break consumers

## 8.7 Ghost Modules

Sometimes a module may be uninstalled, but its YAML file remains due to permissions or packaging issues.

During validation, such modules should be flagged as:

- `ghost`: no binary present
- `invalid`: entry point or interface mismatch

Orchestrators SHOULD ignore ghost modules at runtime, but warn the user.
