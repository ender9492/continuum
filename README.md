# Continuum

**Continuum** is an open-source, privacy-first XR operating system designed to keep standalone VR and AR hardware usable long after vendor end-of-life.

Modern XR headsets are powerful, expensive devices that become unusable when companies shut down stores, abandon software, or lock hardware behind proprietary services. Continuum exists to break that cycle.

---

## What Continuum Is

Continuum is a **runtime-first XR platform** built around **OpenXR**.

- Applications talk to Continuum through OpenXR.
- Continuum owns tracking, hands, controllers, passthrough, and eye tracking via modular providers.
- Device-specific behavior lives in installable **profiles and packs**, not baked OS images.
- The base system boots generically and improves through calibration and optional modules.

Continuum is initially built on **AOSP (Android Open Source Project)** for maximum compatibility with existing standalone headsets, with a clean architecture that allows rebasing to Android XR or Linux in the future.

---

## What Continuum Is Not

- Not a vendor store or account platform  
- Not dependent on Google services or proprietary cloud APIs  
- Not a telemetry or data-collection system  
- Not a replacement for vendor firmware or factory calibration partitions  

Continuum prioritizes **longevity, ownership, and privacy** over polish or platform lock-in.

---

## Core Principles

- **User Ownership**  
  Hardware should remain usable without vendor permission.

- **Privacy by Default**  
  No Google packages, no telemetry, no sensor streaming unless the user explicitly enables it.

- **Offline-First**  
  Continuum boots and functions without an internet connection.  
  Device support is delivered via sideloadable packs.

- **Runtime-First Architecture**  
  OpenXR is the application contract.  
  The OS exists to provide hardware access, not define the XR experience.

- **Progressive Enhancement**  
  Continuum supports a wide range of devices:
  - 3DoF IMU-only
  - 6DoF visual-inertial tracking
  - Passthrough AR
  - Hand tracking
  - Controller and body tracking
  - Eye tracking (when hardware exists)

- **Read-Only Use of OEM Calibration**  
  Factory calibration is imported when available and never overwritten.  
  Continuum stores its own derived calibration in userland.

---

## Architecture (Runtime-First)

flowchart TD
    A[XR Apps / Game Engines<br/>(Unity, Unreal, Native, WebXR)]
    B[OpenXR API]
    C[Continuum Runtime]
    D[Providers<br/>(Head, Hands, Controllers, Passthrough, Eye)]
    E[Profiles & Calibration<br/>(Headset / Controller / Hand / Eye)]
    F[OS Substrate<br/>(AOSP / Android XR / Linux)]
    G[Hardware Sensors & Display<br/>(Cameras, IMU, Depth, Eye Sensors, Panels)]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G

---

## Device Support Model

Continuum uses a **GSI-like approach**:

- One core system image
- Device support delivered via installable **Continuum Packs** (APK-based)
- Packs contain:
  - headset profiles
  - controller profiles
  - hand/eye configuration
  - optional native provider modules

Users never choose “the right ROM” for their device.  
Continuum detects hardware, loads the best available profiles, and validates calibration on first boot.

---

## Project Status

Continuum is in **Phase 0: Architecture & Charter Definition**.

No public builds are available yet.

If you are interested in:
- XR preservation
- OpenXR runtimes
- Tracking and SLAM
- Hand/controller tracking
- Android systems work
- Privacy-first platforms

…contributions and discussion will be welcome as the project progresses.

---

## License

Apache 2.0
