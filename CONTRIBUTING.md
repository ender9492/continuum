# Contributing to Continuum XR

Continuum XR is in early architectural definition.
The priority is correctness of structure, contracts, and observability—not feature velocity.

## What to contribute right now
- Documentation improvements and clarifications
- Contract/interface proposals (plain language first)
- Profile format proposals and validation rules
- Tooling specs for diagnostics output
- Small repo hygiene improvements

## What NOT to contribute right now
- Large tracking implementations
- Large rendering/compositor implementations
- Vendor blobs or code with unclear provenance
- “Guesswork” features that imply maturity the project does not yet have

## Standards
- Keep changes minimal and reviewable.
- Prefer explicitness over cleverness.
- If you change contracts, update docs first.
- Don’t add calibration data to the repo.

## Communication
If proposing a major architectural change, add or update an ADR in `docs/adr/`.
