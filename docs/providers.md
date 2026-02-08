# Providers (Contracts)

This document defines the provider model in Continuum XR (CXR) in plain language.
It is intentionally implementation-agnostic.

## Provider definition

A **provider** is a module that supplies a capability to the Continuum runtime.

A provider must be able to answer three questions cleanly:
1. What capability do you provide?
2. What is your health/status right now?
3. What configuration do you require and expose?

Providers are allowed to be stubs early on.
A stub provider is still useful if it reports status and requirements accurately.

## Provider invariants

All providers must:
- be explicit about which capability they provide
- support initialization + teardown
- publish a structured status report
- accept configuration (even if ignored by a stub)
- never lie about capability availability

Providers should:
- avoid global state
- avoid hard dependencies on a single device unless clearly scoped (device-specific providers are fine; hidden coupling is not)

## Capability model

Capabilities are discrete features the runtime may expose to OpenXR and/or diagnostics.

Examples (conceptual):
- timing
- display
- pose (head)
- input (controllers)
- input (hands)
- tracking (inside-out)
- haptics

The runtime should treat each capability as:
- present/absent
- healthy/degraded/failing
- configured/unconfigured

## Lifecycle contract

### States
A provider transitions through these states:

- `Uninitialized`
- `Initializing`
- `Ready`
- `Degraded`
- `Failed`
- `Stopped`

### Required methods (conceptual)
Every provider should support these operations:

- `init(config)`  
  Acquire resources, validate prerequisites, publish initial status.

- `start()`  
  Begin producing data (polling, callbacks, threads, etc.).

- `stop()`  
  Stop producing data. Release time-sensitive resources.

- `shutdown()`  
  Release all resources. Return to a safe state.

Providers must be able to fail during `init()` or `start()` and still produce a useful status report.

## Status contract (non-negotiable)

A provider’s status is the runtime’s primary interface for observability.

### Status fields (minimum)
Every provider status report must include:

- `provider_id`  
  Stable string identifier (e.g. `pose.head.qcom_imu_stub`)

- `provider_type`  
  One of: `timing`, `display`, `pose`, `tracking`, `input`, `haptics`, `other`

- `capabilities`  
  List of capability keys the provider claims to supply.

- `state`  
  One of: `uninitialized`, `initializing`, `ready`, `degraded`, `failed`, `stopped`

- `health`  
  One of: `ok`, `degraded`, `error`

- `since`  
  Timestamp or monotonic tick for when the current state began.

- `message`  
  One-line human readable summary for logs (no paragraphs).

- `blocking_issues`  
  List of hard blockers preventing `ready` (empty if none).

- `warnings`  
  List of non-blocking issues (empty if none).

### Blocking issues format
Blocking issues should be actionable and machine-readable:

- `code` (stable string)
- `summary` (short)
- `detail` (optional)
- `hint` (optional: suggested remediation)
- `requires` (optional: dependency key)

Example codes:
- `MISSING_PERMISSION`
- `NO_DEVICE_NODE`
- `DEPENDENCY_UNAVAILABLE`
- `NOT_IMPLEMENTED`
- `TIMEBASE_UNSYNCED`

## Configuration contract

Providers accept a config object at `init(config)`.

Config must be:
- explicit (no magic defaults that hide requirements)
- serializable (so profiles can provide it)
- stable (avoid breaking config shapes casually)

Providers must report:
- which config keys were used
- which config keys were ignored/unknown (warnings, not failure)

## Data contract guidelines

Providers that produce streams of data should:
- time-stamp data using a defined timebase
- report the timebase used (e.g. monotonic clock source)
- prefer immutable sample structs/records
- document units (meters, radians, etc.)

Early stubs may return empty samples but must still:
- report that the stream is unavailable
- report why

## Provider composition

The runtime may select:
- exactly one provider for a capability, or
- a chain where one provider depends on another (e.g. pose uses timing)

If a provider depends on others, it must declare those dependencies as part of status/config.

## Versioning policy

Provider contracts change slowly.

If contracts must change:
- update this document first
- keep old fields working when feasible
- add new fields in a backwards-compatible way
