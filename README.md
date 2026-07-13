# Cyberdeck — Modular Writing & Research Deck

A mini, modular, economical, and sustainable DIY portable computer built for distraction-free writing and offline research.

## Project Status

| Phase | Status |
|-------|--------|
| Requirements | ✅ Complete |
| Design | 🔲 Not started |
| Bill of Materials | 🔲 Not started |
| Hardware Assembly | 🔲 Not started |
| Software / OS Config | 🔲 Not started |
| Testing | 🔲 Not started |
| Documentation | 🟡 In progress |

## Quick Summary

| Attribute | Decision |
|-----------|----------|
| Form factor | Clamshell (laptop-style) |
| Compute | Raspberry Pi 4 (2GB) |
| Display | Waveshare 7.5" E-Ink (SPI) |
| Keyboard | Ortholinear mechanical (hot-swap) |
| Battery | 4× 18650 cells + BMS |
| Target budget | $100–$200 USD (secondhand-first) |
| OS | Raspberry Pi OS Lite (headless-friendly) |

## Repository Structure

```
├── docs/
│   ├── requirements.md     # Scoped requirements (start here)
│   ├── design/             # Architecture and design decisions
│   ├── bom/                # Bill of materials and sourcing notes
│   └── testing/            # Test plans and results
├── hardware/
│   ├── cad/                # 3D-printable enclosure files (FreeCAD / STL)
│   └── pcb/                # Keyboard PCB design if custom
├── software/
│   └── setup/              # OS configuration, install scripts, dotfiles
└── firmware/               # Keyboard firmware (QMK/KMK)
```

## Philosophy

- **Modular** — every subsystem (compute, display, keyboard, battery) is independently replaceable
- **Economical** — source secondhand first; no component over $60
- **Sustainable** — recycled/salvaged parts preferred; 3D-printed enclosure in recyclable PLA
- **Documented** — every decision recorded so anyone can replicate or fork the build

## Getting Started

Read [`docs/requirements.md`](docs/requirements.md) for the full scope, then follow the design and BOM documents as they are completed.
