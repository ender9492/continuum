# Runtime

## Purpose
The Continuum XR runtime layer: the core userland system that will expose OpenXR and orchestrate providers.

## What belongs here
- Runtime core (process model, lifecycle)
- OpenXR entrypoints and runtime integration glue
- Diagnostics and status reporting surfaces
- Minimal “shell” / service scaffolding (when it exists)

## Explicitly out of scope (for now)
- Tracking algorithms
- Rendering/compositor implementation
- Device-specific drivers
- Any full application framework beyond what OpenXR requires
