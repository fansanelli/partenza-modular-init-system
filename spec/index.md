# Partenza Specification - Table of Contents

## 1. Introduction
- Purpose and Scope
- Design Philosophy
- Target Platforms and Use Cases
- Relationship to Existing Init Systems

## 2. Core Concepts
- The Role of the Orchestrator (PID 1 and not)
- Units and Services
- Modules and Providers
- Sidecars and Extensions
- Zero Trust and Permissions Model

## 3. Boot Process
- System Startup Sequence
- Boot Plan and Caching
- Health Checking and Recovery
- Fallback and Panic Modules

## 4. Module Architecture
- Module Lifecycle (Init, Handshake, Live, Shutdown)
- Service Declaration (YAML Format)
- Permissions & Capabilities Declaration
- Dependency Management
- Versioning and Compatibility

## 5. Orchestrator Responsibilities
- Module Discovery and Validation
- Configuration Check (Preflight)
- Health Monitoring
- Restart Strategies and Limits
- Handling Resource Overload / DOS

## 6. API Contracts
- Orchestrator ↔ Module API
- User ↔ Module API
- Module ↔ Module Communication
- Declaring and Using Sidecars
- Interface Stability Guarantees

## 7. Security Model
- Rootless Operation
- Identity & Authentication (Handshake Secret Exchange)
- Trust Boundaries
- Handling Malicious or Faulty Modules

## 8. Extension Registry
- What Is an Extension?
- Core vs Optional Extensions
- Declaring and Registering a New Extension
- Extension Compatibility Matrix

## 9. Conformance
- Defining a Conformant Orchestrator
- Verifying Module Compliance
- The `partenza-conformance` Tool
- API Compatibility Declaration

## 10. Implementation Guidelines
- Suggestions for Developers
- Performance on Embedded vs. Desktop vs. Cloud
- Portability Considerations
- Language & Runtime Agnostic Design

## 11. Reference Scenarios
- Booting with Minimal Modules
- Using an External Script Instead of the Orchestrator
- Switching Providers at Runtime
- Example Logging with Fallback and Sidecar

## 12. Appendices
- Glossary of Terms
- YAML Field Reference
- Error Codes and Recovery Flags
- Related Work and Inspirations
