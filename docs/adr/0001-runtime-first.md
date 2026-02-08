# ADR 0001: Runtime-first architecture

## Context
Continuum XR needs to support long-lived XR hardware across devices without early lock-in to one vendor or OS fork.
Starting OS-first (deep AOSP fork) increases coupling, scope, and time-to-observability.

## Decision
Continuum XR is runtime-first:
- define the runtime boundaries, contracts, provider model, and observability in userland first
- grow downward into device integration incrementally

## Consequences
- Early phases prioritize status/diagnostics and truthful stubs.
- Device-specific work is expressed as providers + profiles, not hard-coded runtime logic.
- OS integration can happen later, but the runtime architecture stays legible and modular.
