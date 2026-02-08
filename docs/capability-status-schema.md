# Capability & Status Schema

This document defines a normalized, tool-friendly schema for Continuum XR (CXR) status output.

The goal is:
- consistent observability across devices and providers
- machine-readable diagnostics
- stable fields that evolve conservatively

This schema is conceptual and can be represented as JSON, protobuf, or another structured format later.

## Design principles

- Status must be **truthful** (no fake “ready”).
- Status must be **actionable** (clear blockers + hints).
- Status must be **stable** (fields change slowly).
- Status must be **hierarchical** (runtime → capabilities → providers → issues).
- Human text exists, but tools must not depend on freeform parsing.

## Top-level runtime status

### Required fields

- `runtime_id`  
  Stable identifier for the runtime (e.g. `continuum-xr`)

- `runtime_version`  
  Semver or dev string (e.g. `0.0.0-dev`)

- `build_id`  
  Optional but recommended. Git commit hash or build stamp.

- `timestamp`  
  Wall clock time if available (ISO 8601), otherwise omitted.

- `monotonic_timebase`  
  String describing the timebase used for internal timestamps (e.g. `CLOCK_MONOTONIC`).

- `state`  
  One of: `starting`, `running`, `degraded`, `failed`, `stopping`

- `active_profile`  
  Profile identifier + optional metadata.

- `capabilities`  
  Map of capability key → capability status block.

- `providers`  
  List of provider status blocks.

- `blocking_issues`  
  Flattened list of issues that prevent the runtime from reaching `running`.

- `warnings`  
  Flattened list of non-blocking issues.

## Capability status block

A capability represents a runtime feature that may be present or absent.

### Required fields

- `key`  
  Stable capability key (see list below).

- `required`  
  Boolean. True if the active profile requires this capability for “running”.

- `available`  
  Boolean. True if the capability is currently available.

- `health`  
  One of: `ok`, `degraded`, `error`, `unknown`

- `selected_provider_id`  
  Provider ID currently responsible for this capability (or null).

- `message`  
  One-line summary.

- `blocking_issues`  
  Issues that prevent this capability from being available.

- `warnings`  
  Non-blocking issues for this capability.

### Capability keys (initial set)

These are intentionally generic and can be extended later:

- `timing`
- `display`
- `pose.head`
- `tracking.world`
- `input.controllers`
- `input.hands`
- `haptics`

## Provider status block

Providers are listed even if not selected (for diagnostics).

### Required fields

- `provider_id`  
  Stable identifier string.

- `provider_type`  
  One of: `timing`, `display`, `pose`, `tracking`, `input`, `haptics`, `other`

- `capabilities`  
  List of capability keys this provider claims to supply.

- `state`  
  One of: `uninitialized`, `initializing`, `ready`, `degraded`, `failed`, `stopped`

- `health`  
  One of: `ok`, `degraded`, `error`

- `since`  
  Monotonic timestamp/tick for when the current state began.

- `message`  
  One-line summary.

- `blocking_issues`  
  List of issues preventing `ready`.

- `warnings`  
  List of non-blocking issues.

### Optional fields (recommended)

- `dependencies`  
  List of provider IDs or capability keys required.

- `config_used`  
  Map of config keys → value summary (redacted if sensitive).

- `config_ignored`  
  List of config keys present but unused.

- `metrics`  
  Lightweight numeric metrics (e.g. sample rate, dropped frames) when available.

## Issue format (shared)

Issues are designed to be actionable and stable.

### Required fields

- `code`  
  Stable string code.

- `summary`  
  Short human text.

### Optional fields

- `detail`  
  Slightly longer text (still not a paragraph).

- `hint`  
  Suggested remediation.

- `requires`  
  Dependency key (permission, device node, library, etc.).

- `scope`  
  One of: `runtime`, `capability`, `provider`.

- `severity`  
  One of: `blocker`, `warning`.

### Recommended issue codes (initial)

- `NOT_IMPLEMENTED`
- `MISSING_PERMISSION`
- `NO_DEVICE_NODE`
- `DEPENDENCY_UNAVAILABLE`
- `INVALID_CONFIG`
- `PROFILE_NOT_FOUND`
- `PROFILE_INVALID`
- `TIMEBASE_UNAVAILABLE`
- `TIMEBASE_UNSYNCED`
- `PROVIDER_INIT_FAILED`
- `PROVIDER_START_FAILED`

## Minimal output requirement (Phase 2 target)

A minimal status dump should include at least:
- runtime state
- active profile id
- a capability matrix for the initial key set
- provider list (even if stubs)
- flattened blocking issues

Even if everything is unimplemented, the status output is still considered useful if it is truthful and consistent.

## Compatibility rules

- Fields may be added without breaking older tooling.
- Fields must not be removed without a deprecation cycle.
- Issue codes should not be repurposed to mean something else.
- Capability keys should remain stable once introduced.
