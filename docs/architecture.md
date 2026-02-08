# Continuum XR Architecture

This document describes Continuum XR (CXR) at a system-design level: what it is, what it is not, and how the repository is intended to evolve.

## Design stance: runtime-first, not OS-first

Continuum XR is built as a **runtime-first** platform.

- **Runtime-first** means: start by defining the XR runtime boundaries, interfaces, and observability in userland, then grow downward into device integration.
- **OS-first** means: start by forking/modifying large portions of AOSP/Android system layers, then attempt to integrate XR later.

CXR is runtime-first because:
- It is easier to keep interfaces honest.
- It reduces early coupling to one vendor device stack.
- It enables incremental progress (stubs → diagnostics → minimal runtime → providers → capability growth).

CXR may eventually ship as part of an OS image, but its architecture is designed to remain legible and modular even when embedded.

## What OpenXR does (and does not) do in CXR

OpenXR is the application-facing contract layer. In Continuum XR terms:

### OpenXR does
- Defines the **API surface** that XR apps expect.
- Defines **objects and lifecycles** (instance/system/session/spaces/actions).
- Defines extension mechanisms that CXR can support over time.

### OpenXR does not
- Provide a compositor implementation.
- Provide tracking algorithms or SLAM.
- Provide device drivers.
- Define how sensors are accessed or fused.
- Define how calibration is stored or managed.

CXR’s job is to provide an OpenXR runtime that is:
- portable across devices,
- honest about capability availability,
- and designed for long-lived hardware support.

## Providers: the core abstraction in Continuum XR

A **provider** is a module that supplies a capability to the runtime.

Providers are how CXR avoids prematurely hard-coding “the way tracking works” or “the way input works.”

Examples of provider types (conceptual):
- **Pose** (head pose source, time-sync, prediction inputs)
- **Tracking** (baseline external, inside-out, hybrid)
- **Input** (controllers, hands, buttons, haptics)
- **Display** (views, timing, display characteristics)
- **Timing** (monotonic clock, frame pacing, sensor timestamps)

A provider is expected to answer three things cleanly:
1. **What capability do you provide?**
2. **What is your health/status right now?**
3. **What is your configuration and requirements?**

In early phases, providers may be **stubs** that only report:
- “not implemented”
- “hardware not present”
- “blocked by missing permission”
- “dependency missing”

That is still valuable because it keeps the runtime structurally truthful.

## Why calibration lives in userland

Calibration is not a build artifact. It is not source code. It changes per:
- user,
- environment,
- device wear,
- sensor drift,
- mounting geometry,
- play space.

So calibration belongs in userland state (device-local storage), not in git.

Continuum XR should treat calibration as:
- discoverable (the runtime can report what calibration is missing),
- inspectable (diagnostics can show what was last known),
- replaceable (clear/reset/recalibrate),
- but not “baked into” code or repo configuration.

## Phase sequencing: observability before perfection

CXR intentionally avoids starting with “perfect tracking.”

The first executable slice should be able to:
- boot (eventually, on a target platform),
- expose an OpenXR runtime surface (even if minimal),
- report capability status,
- report sensor presence/health,
- and fail loudly/cleanly when something is missing.

Tracking quality comes after:
- the contracts are stable,
- timebases are trustworthy,
- status reporting exists,
- and the runtime can express partial capability honestly.

## Repository mapping

This repo is structured to match the architecture:

- `runtime/`  
  The runtime core and OpenXR-facing integration points. Orchestration and diagnostics live here.

- `providers/`  
  Provider interfaces and implementations (eventually). Early phases may only contain contracts and stubs.

- `profiles/`  
  Device profiles describing which providers should be assembled for a target device, plus configuration that should not live in code.

- `tools/`  
  Developer utilities: validation, diagnostics, repo hygiene, and helper scripts.

- `docs/`  
  Architecture, terminology, and decision rationale.

## Non-goals (explicit)

Continuum XR is not:
- a “new app framework” for XR applications,
- a promise of near-term end-user usability,
- a vendor SDK clone,
- a place to commit proprietary blobs of uncertain provenance,
- a tracking research playground without contracts and diagnostics.

## Terminology

- **CXR / Continuum XR**: the platform and repository.
- **Runtime**: the userland system that exposes OpenXR and orchestrates providers.
- **Provider**: a capability module that feeds the runtime.
- **Profile**: a configuration describing how a target device composes providers.
- **Observability**: the ability to report what exists, what works, and what is missing.

## Next architecture documents (planned)

- Provider contract definitions (plain-language first)
- Runtime lifecycle model (processes, threads, responsibilities)
- Capability/status reporting schema (what “healthy” means)
- Roadmap phases tied to measurable deliverables
