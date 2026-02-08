# Runtime Lifecycle

This document defines how the Continuum XR runtime starts, composes providers, exposes status, and (eventually) hosts OpenXR.

It is intentionally implementation-agnostic.

## Runtime responsibilities

The runtime is responsible for:
- loading a device profile
- initializing and starting providers in a defined order
- exposing a unified status view across all providers
- exposing an OpenXR runtime surface (when implemented)
- failing loudly and cleanly when prerequisites are missing

The runtime is not responsible for:
- embedding tracking research directly into core logic
- hiding missing capabilities behind fake success paths
- storing calibration in git or build artifacts

## Process model (Phase 1–2 assumptions)

Initial assumption: a single userland runtime process that:
- owns provider lifecycle
- owns diagnostics/status reporting
- hosts OpenXR entrypoints (Phase 2+)

This may evolve into multiple processes later (e.g., compositor separation),
but Phase 1–2 should optimize for clarity and debuggability.

## Boot sequence (conceptual)

1. **Load profile**
   - determine target device profile (explicit selection in early phases)
   - validate profile schema
   - produce a profile summary in status output

2. **Initialize core services**
   - logging
   - monotonic timebase selection
   - configuration resolution

3. **Initialize providers**
   Providers are initialized in dependency order:

   - Timing provider first
   - Display provider early (if present)
   - Pose provider(s)
   - Input providers
   - Tracking providers (later phases)

   Each provider must publish status immediately after init, even if failed.

4. **Start providers**
   Providers are started in the same dependency order.
   Start may be a no-op for stub providers, but status must still reflect reality.

5. **Expose diagnostics**
   Runtime must expose a consistent view of:
   - selected providers per capability
   - per-provider status
   - aggregate capability status

6. **Expose OpenXR (Phase 2+)**
   OpenXR exposure should not claim capabilities that providers do not supply.

## Provider selection rules

Provider selection should be deterministic.

### In Phase 1–2
- selection is driven by the active profile
- no auto-detection is required
- no “best guess” fallbacks that hide missing configuration

### Later phases
Auto-detection can exist, but it must still:
- publish what it detected
- publish what it could not detect
- allow explicit override via profile/config

## Runtime state model

The runtime has a small set of states:

- `Starting`
- `Running`
- `Degraded`
- `Failed`
- `Stopping`

State is derived from provider states:

- `Running` if all required capabilities are `ready`
- `Degraded` if runtime can function but one or more non-critical providers are `degraded`
- `Failed` if any critical provider is `failed` or profile loading fails

Critical vs non-critical is defined by profile (not hard-coded).

## Aggregate status output

The runtime must be able to output a single status report that includes:

- runtime version/build identity (even if "dev")
- active profile name/id
- capability matrix (present/absent, selected provider, health)
- provider list with per-provider status blocks
- top-level blocking issues (flattened)

This output should be designed so tooling can consume it.

## First executable slice (what “working” means early)

The earliest meaningful “working” build of Continuum XR is not perfect tracking.

It is a runtime that can:
- start deterministically
- load a profile
- initialize stub providers
- expose a diagnostic status report
- expose OpenXR entrypoints that accurately reflect capability availability

If a capability is missing, the correct behavior is:
- report it clearly
- fail only if the profile marks it critical

## Shutdown sequence

Shutdown should be symmetrical:

1. Stop OpenXR sessions (when present)
2. Stop providers (reverse dependency order)
3. Shutdown providers
4. Flush logs and exit cleanly

Providers must tolerate repeated stop/shutdown calls without crashing.

## Logging and failure policy

- Failures are expected early. They should be explicit, not silent.
- Status should be sufficient to diagnose without reading source.
- One-line summaries in status; details in logs.

A provider failing should not crash the runtime unless:
- the profile declares that provider/capability critical, or
- the failure compromises runtime correctness (e.g. invalid timebase).

## Testing hooks (conceptual)

Even before “real” hardware work, the runtime should support:
- running with stub/mock providers
- dumping status on demand
- validating profiles without starting providers
