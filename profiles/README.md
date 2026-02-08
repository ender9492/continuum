# Profiles

## Purpose
Device profiles and configuration describing how the runtime should assemble providers for a target device.

## What belongs here
- Device descriptors (what sensors exist, what inputs exist)
- Provider selection and configuration mapping
- Tunables that do not belong in code (when possible)

## Explicitly out of scope (for now)
- A full config system
- Auto-detection logic
- Per-user calibration data (that stays in userland/state, not in git)
