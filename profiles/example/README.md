# Example Profile (Stub)

This is a minimal example device profile for Continuum XR.

It is intentionally stub-only:
- it selects providers that do not implement real hardware access
- it exists to validate profile loading, selection logic, and status output shape

This profile should remain safe to run on any machine because it does not touch device nodes.

## Files

- `profile.json` — example profile definition

## Notes

If you add additional example profiles, keep them:
- explicit about whether they are stubs or real device targets
- free of calibration data
- free of proprietary blobs
