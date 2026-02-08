# Continuum XR Roadmap

This roadmap describes intended phases of work for Continuum XR (CXR).
It is not a commitment to timelines, features, or delivery dates.

Progress is expected to be incremental and may pause or reorder as constraints are discovered.

## Phase 1 — Runtime skeleton & diagnostics (current)

- Repository structure and documentation
- Runtime-first architectural definition
- Provider abstraction model
- Diagnostics and observability planning
- No executable runtime yet

## Phase 2 — Minimal runtime surface

- Runtime process scaffolding
- OpenXR runtime exposure (minimal / stubbed)
- Capability and provider status reporting
- No real tracking or rendering yet

## Phase 3 — Head pose + compositor baseline

- Head pose provider (baseline, minimal)
- Display timing and view configuration
- Minimal compositor path sufficient for OpenXR conformance testing
- Still limited device support

## Phase 4 — Tracking providers (baseline)

- Inside-out tracking provider(s)
- Controller and/or hand input providers
- Calibration workflows (userland only)
- Expanded diagnostics

## Phase X — Extensions and nice-to-haves

- Advanced tracking quality improvements
- Additional input modalities
- Performance tuning
- Platform-specific optimizations
- Developer tooling improvements
