# Continuum XR

Continuum XR (CXR) is an open, runtime-first extended reality platform focused on longevity, portability, and hardware independence.

The goal of Continuum XR is not to replace existing XR ecosystems, but to preserve and extend the usable life of XR hardware by decoupling runtimes from vendor-controlled lifecycles.

## Project status

Continuum XR is in early architectural definition.

- There are no builds
- There is no ETA
- There is no supported hardware list yet

The current focus is correctness of structure and interfaces, not implementation speed.

## What this repository contains

- A skeletal runtime layout
- Architecture and design documentation
- Clearly defined non-goals to prevent premature scope creep

## Where to start reading

- [`docs/architecture.md`](docs/architecture.md) — high-level system design and philosophy
- Folder-level READMEs — explain intent and boundaries of each subsystem

## What Continuum XR is not

- A fork of Android or AOSP
- A custom XR application framework
- A vendor-specific SDK
- A promise of near-term usability

Continuum XR treats XR infrastructure as long-lived system software, not disposable product firmware.
