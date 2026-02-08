# Profiles (Device Composition)

Profiles define how Continuum XR (CXR) composes providers for a target device.

A profile answers:
- what capabilities are required vs optional
- which providers should be selected for each capability
- what configuration each provider receives

Profiles exist to keep the runtime deterministic and honest.
Early phases should avoid auto-detection and “best guess” behavior.

## Principles

- Profiles are declarative, not code.
- Profiles must be human-reviewable.
- Profiles should be stable over time.
- Profiles must not contain per-user calibration data.
- Profiles must make “required” vs “optional” explicit.

## Format (conceptual)

The profile format is conceptual and may be represented as JSON/YAML/TOML later.
This doc defines the required fields and intent.

## Required fields

- `profile_id`  
  Stable identifier string (e.g. `meta.quest3.stub`)

- `display_name`  
  Human-readable name.

- `device`  
  Metadata describing the target (vendor/model/platform notes).

- `capabilities`  
  Map of capability key → capability config block.

- `providers`  
  List of provider declarations (id, type, config).

## Capability config block

Each capability must declare:

- `required` (bool)  
  If true, runtime cannot be `running` without it.

- `provider` (string | null)  
  Provider ID selected for this capability.

- `notes` (optional)  
  Human-only text.

Example (conceptual):

- `pose.head`:
  - required: true
  - provider: pose.head.stub

## Provider declaration

Each provider entry must declare:

- `provider_id`  
- `provider_type`  
- `capabilities` (list)
- `config` (map, may be empty)

Providers may be stubs early on, but must still be listed explicitly.

## Dependency declaration (optional)

Providers may declare:

- `depends_on` (list of capability keys or provider ids)

The runtime should validate dependencies during profile load and report issues if unmet.

## Validation rules (Phase 2 target)

On profile load, the runtime should validate:

- required top-level fields exist
- capability keys are known (or marked experimental)
- every selected provider exists in `providers`
- provider types match intended capability categories (best-effort check)
- duplicate provider ids are not allowed
- required capabilities are listed explicitly
- config values are serializable

If validation fails, runtime status should include:
- `PROFILE_INVALID` (blocker)
- field-level hints where possible

## What profiles must NOT include

- user calibration values
- personally identifying data
- secret keys or tokens
- proprietary blobs
- “magic” paths that only work on one developer machine

## Evolution policy

Profiles may evolve, but breaking changes should be rare.
Prefer additive changes and deprecation notes.
