# Cyberdeck — Scoped Requirements

**Version:** 0.1.0  
**Date:** 2026-07-13  
**Status:** Draft — awaiting design phase

---

## 1. Project Overview

This cyberdeck is a self-contained, portable computing device built for **writing and research**. It is not a general-purpose laptop replacement. Every design decision is optimized for long, focused writing sessions, offline research, and reading — with good battery life, minimal eye strain, and a keyboard that rewards touch-typing.

The build philosophy is: **minimal, repairable, expandable, and sourced secondhand wherever possible.**

---

## 2. Primary Use Cases

| Priority | Use Case |
|----------|----------|
| 1 | Distraction-free long-form writing (Markdown, plain text) |
| 2 | Offline research and note-taking |
| 3 | E-reading (EPUB, PDF) |
| 4 | Light terminal work (SSH, git, scripting) |

**Out of scope (v1):**
- Video playback
- Gaming
- GUI-heavy desktop applications
- Network penetration testing or security tooling
- Audio production

---

## 3. Form Factor

- **Style:** Clamshell (laptop-style) — hinged lid containing the display, base containing keyboard and internals
- **Target dimensions (approximate):** 280mm × 180mm × 35mm closed (A5 footprint)
- **Weight target:** Under 1 kg
- **Enclosure material:** 3D-printed PLA (recyclable, printable by anyone with a standard FDM printer)
- **Hinge:** Salvaged laptop hinge or off-the-shelf friction hinge (M3 mount)
- **Port placement:** Right side — USB-A ×2, USB-C (charge), 3.5mm audio; left side — microSD slot, HDMI (passthrough for external display)

---

## 4. Compute

| Attribute | Specification |
|-----------|---------------|
| SBC | Raspberry Pi 4 Model B, 2GB RAM |
| CPU | Broadcom BCM2711, 4× Cortex-A72 @ 1.8 GHz |
| Storage | 32 GB microSD (Class 10 / A1 minimum) |
| Wireless | 802.11ac Wi-Fi + Bluetooth 5.0 (built-in) |
| GPIO | 40-pin header exposed on expansion rail |
| Sourcing preference | Secondhand / used |

**Rationale:** The RPi 4 2GB has more than enough headroom for writing tools, terminal apps, and a local e-book library. Its massive community, HAT ecosystem, and secondhand availability make it the best value for this use case.

---

## 5. Display

| Attribute | Specification |
|-----------|---------------|
| Type | E-Ink / E-Paper |
| Target model | Waveshare 7.5" V2 E-Paper display (800×480) |
| Interface | SPI (connected directly to RPi GPIO) |
| Refresh rate | ~2s full refresh; partial refresh ~0.3s |
| Color | Black & white |
| Backlight | None (requires ambient light — acceptable for writing/research) |
| Power draw | ~0W standby; ~26mW during refresh |

**Rationale:** E-ink eliminates backlight eye strain for multi-hour sessions, dramatically extends battery life, and is fully sunlight-readable. The tradeoff (slow refresh, no color) is acceptable for the writing/research use case.

**Display driver:** Waveshare's open-source Python/C library for RPi. Will be wrapped in a custom display management layer.

**Known limitation:** No video, no smooth scrolling. Fast typing requires a compositor or text-buffer approach to avoid display thrashing — this will be addressed in the software design.

---

## 6. Keyboard

| Attribute | Specification |
|-----------|---------------|
| Layout | Ortholinear (grid layout, no stagger) |
| Size | 4×12 (48 keys) or 5×12 (60 keys) — TBD in design phase |
| Switch type | Mechanical, hot-swap sockets (Gateron or equivalent) |
| Switch feel | Linear or tactile — TBD based on sourcing |
| Keycaps | Blank PBT (no legends, touch-type focus) |
| Connection | USB (to RPi USB-A) or direct microcontroller integration |
| Firmware | QMK (preferred) or KMK (CircuitPython, easier to modify) |
| PCB | Off-the-shelf ortho PCB (BM40, Planck, or equivalent) — custom PCB optional in v2 |

**Rationale:** Ortholinear layout is popular in the cyberdeck community and ergonomically suited to touch-typing. Hot-swap sockets are a hard requirement for repairability — switches can be replaced without soldering. Open-source firmware (QMK/KMK) means the layout is fully programmable.

---

## 7. Power System

| Attribute | Specification |
|-----------|---------------|
| Battery cells | 4× 18650 lithium-ion (salvaged or new) |
| Configuration | 2S2P (7.4V nominal, ~8.4V fully charged) |
| Estimated capacity | ~20–26 Wh (depending on cell quality) |
| BMS | 2S BMS module with overcharge, over-discharge, and short-circuit protection |
| Charging | USB-C PD input via step-up/step-down regulator |
| Output to RPi | 5V / 3A regulated via DC-DC buck converter |
| Expected runtime | 6–10 hours at typical writing workload (~2–3W total draw) |
| Battery indicator | Simple 4-LED fuel gauge on panel (optional in v1) |

**Rationale:** 18650 cells are the most standardized rechargeable lithium format. Salvaged cells from old laptop battery packs are common and cheap. A BMS is a hard safety requirement — no exposed lithium without protection circuitry.

---

## 8. Connectivity & Expansion

### Built-in (v1)
- Wi-Fi 802.11ac (RPi 4 built-in)
- Bluetooth 5.0 (RPi 4 built-in)
- USB-A ×2 (external ports, from RPi USB 3.0)
- USB-C ×1 (charge input only)
- HDMI ×1 (micro-HDMI passthrough to external display)
- 3.5mm audio jack (RPi 4 built-in)
- microSD slot (RPi 4 built-in)

### Expansion (v2 / modular)
- GPIO header exposed on interior rail — compatible with standard RPi HATs
- Space reserved for: GPS HAT, LoRa HAT, USB SDR dongle tray, extra USB hub
- NATO/MOLLE-style mounting rail on exterior (optional aesthetic/utility feature)

---

## 9. Software Stack

| Layer | Choice |
|-------|--------|
| OS | Raspberry Pi OS Lite (64-bit, headless base) |
| Display server | Framebuffer-only (no X11/Wayland in v1) |
| Editor | `neovim` (primary writing tool) |
| Notes | `joplin-cli` or plain Markdown in `git` repo |
| E-reader | `fbreader` (framebuffer) or custom EPUB renderer |
| Browser | `w3m` or `lynx` (terminal-based, low bandwidth) |
| File sync | `syncthing` (p2p sync with phone/desktop, no cloud required) |
| E-ink driver | Waveshare library + custom Python wrapper |
| Shell | `zsh` with minimal config |
| Boot | ~15s to usable shell target |

**OS rationale:** No desktop environment keeps RAM usage below 200MB, leaving headroom for editors and local document storage. The e-ink display renders best via framebuffer apps or a minimal compositor — this is designed in, not bolted on.

---

## 10. Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| Boot to usable state | < 20 seconds |
| Idle power draw | < 1.5W |
| Active write power draw | < 3W |
| Battery runtime (writing) | ≥ 4 hours (target 6–8 hours) |
| Weight | < 1 kg |
| Closed dimensions | ~280 × 180 × 35 mm |
| Drop resistance | Survives 1m drop onto concrete (PLA enclosure + foam lining) |
| Operating temperature | 0°C – 45°C |
| Repairability | Any single component replaceable with standard tools (screwdrivers only) |
| Enclosure reproduction | Fully 3D-printable on a standard 220×220mm print bed |

---

## 11. Budget Breakdown (Target)

| Component | Target Cost | Sourcing Strategy |
|-----------|-------------|-------------------|
| Raspberry Pi 4 2GB | $30–40 | Secondhand (eBay, r/hardwareswap) |
| Waveshare 7.5" E-Ink display | $45–55 | New (Waveshare official or Amazon) |
| Ortho keyboard PCB + microcontroller | $20–30 | AliExpress (BM40, RP2040 Pro Micro) |
| Mechanical switches ×48 | $10–20 | Secondhand or AliExpress bulk |
| Blank PBT keycaps (ortho) | $12–18 | AliExpress |
| 4× 18650 cells | $8–15 | Salvaged from old laptop battery |
| 2S BMS module | $4–8 | AliExpress |
| 5V buck converter | $3–6 | AliExpress |
| 32GB microSD | $5–10 | Secondhand or new |
| PLA filament (~400g) | $8–12 | Any brand |
| Hinge (salvaged or off-shelf) | $0–8 | Salvaged laptop or AliExpress |
| USB hub, wiring, connectors | $8–15 | AliExpress / local electronics store |
| Screws, standoffs, misc hardware | $5–10 | Local hardware store |
| **Total (low estimate)** | **$158** | |
| **Total (high estimate)** | **$247** | |

**Note:** To stay under $200, prioritize: salvaged RPi, salvaged 18650 cells, cheapest ortho PCB option. The display is the single largest fixed cost and should be bought new for reliability.

---

## 12. Open Questions (to resolve in Design phase)

- [ ] 4×12 (48 key) vs 5×12 (60 key) keyboard — depends on case dimensions
- [ ] Exact enclosure hinge mechanism and print orientation
- [ ] Whether to use QMK or KMK for keyboard firmware
- [ ] E-ink partial vs full refresh strategy in the text editor
- [ ] Whether to run a minimal framebuffer compositor (fbdev + `fbv` / `directfb`) or go full framebuffer raw
- [ ] GPIO expansion layout inside the chassis
- [ ] Whether to include a hardware power button with clean shutdown logic

---

## 13. Success Criteria

The v1 build is considered complete when:

1. The device boots to a usable neovim writing session in under 20 seconds
2. Battery lasts at least 4 hours of continuous writing
3. Text is readable on the e-ink display without eye strain in normal indoor lighting
4. Every component can be removed and replaced with a screwdriver only
5. The enclosure can be reprinted from the provided STL files on a standard FDM printer
6. Wi-Fi syncs documents to a second device via Syncthing
7. The total parts cost is documented and confirmed ≤ $200 (or clearly justified if over)
