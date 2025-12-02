# Technical Plan & Requirements (WaveCave)

## Summary

THE TUB is a self-contained system that interfaces cleanly with the WaveCave’s existing six-channel audio infrastructure. I bring the sculptural object, control hardware, sensing, computer, and interface; the WaveCave provides the room, the existing Lynx Aurora and Genelec speakers, and basic power and ladder access.

No projectors will be moved or repointed.

## Physical Components

- **The Tub**
  - Rectangular prism constructed from welded 1/2" frosted acrylic panels.
  - Internal brace blocks and optional top-face trusses for robustness.
  - Sized approximately 6' long, 3' wide, and ~3' high (tunable to room).
  - Edges sanded, corners slightly rounded to avoid sharp points.

- **Control Pedestals**
  - 3–4 low metal pedestals between entrance and tub.
  - Each pedestal hosts Allen-Bradley selector switches and/or momentary buttons.
  - Cabling is visible but strain-relieved and either taped or routed through cable ramps.

- **Electronics**
  - Laptop (e.g., MacBook Pro) running the real-time audio patch.
  - Audio interface (e.g., Focusrite 4i4) for local input/output management.
  - LOM BasicUcho (or similar) contact microphone attached firmly to the tub body.
  - Optional small network switch or USB hub if needed.

## Audio & Control I/O

**Inputs:**

- Contact microphone on the tub (permanently patched).
- Up to two combo-inputs for instruments / microphones.
- One stereo input for phones / laptops (minijack or USB-C via adapter).

**Outputs:**

- Multichannel output from my interface into the WaveCave’s Lynx Aurora, feeding six Genelec 8030 ceiling speakers in the default configuration.

Routing details will be coordinated with the WaveCave TA, but the baseline assumption is:

- My interface → line outputs → Lynx Aurora inputs 1–6 → existing speaker grid.

**Control:**

- Allen-Bradley 3-position selector switches (e.g., 800T series) wired to a microcontroller or MIDI/OSC interface reporting:
  - `spatial_form` (tight / orbit / fragment)
  - `persona` (shadow / partner / adversary)
  - `memory` (amnesiac / short / long)
  - `timbre` (acoustic / hybrid / synthetic)
- Momentary buttons for `jolt` and `clear` impulses.

## Power & On/Off

- Single dedicated power strip for all tub electronics, clearly labeled and accessible in the control room.
- Simple printed instructions posted next to the WaveCave computer explaining:
  - How to power the tub system on.
  - How to mute or fully disable audio quickly (for the piano tuner, other events, emergencies).

## What I Provide

- Tub structure and reinforcements.
- Pedestals, switches, microcontroller, and wiring harness.
- Laptop, audio interface, contact microphone, and all patch cables.
- Any additional small speakers or sub (if added) and their stands.
- Cable management materials (tape, ramps as needed).
- Install tools specific to the tub (clamps, acrylic polish, etc.).

## What WaveCave Provides

- Gallery space and access for a full Sunday–Saturday week.
- Existing six Genelec ceiling speakers and Lynx Aurora interface.
- Mac Studio (optional; I can either run the patch there or from my laptop).
- Access to the default 8-channel XLR snake and control room.

The technical footprint is modest: the work primarily consumes six output channels, several inputs, one outlet circuit, and a reasonable portion of the floor area while leaving default projection hardware untouched.
