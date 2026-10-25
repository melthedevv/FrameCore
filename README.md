<p align="center">
  <img src="assets/banner.png" alt="FrameCore" width="100%">
</p>

<h1 align="center">FRAMECORE</h1>
<p align="center"><b>AI animation for After Effects.</b><br>From prompt to animation.</p>

<p align="center">
  After Effects 2024–2026 · Windows & macOS · Free models by default
</p>

---

> **Status: In development.** Follow the repository for updates.

## What it is

FrameCore is a panel for Adobe After Effects. Describe the animation you want, press Generate, and FrameCore builds it inside After Effects: real layers, real keyframes, real expressions.

**Prompt → Generate → Animate.**

## Two ways to get your result

| Mode | What you get |
|---|---|
| **Build in AE** | The animation is created in your active composition as fully editable layers, keyframes, and expressions. |
| **MP4 only** | FrameCore builds the animation, renders it with After Effects' own renderer, and hands you the finished MP4. |

Both modes use the same build, so the result is identical. Choose with one toggle.

## Features

**Real animation, not a render of something else.** FrameCore works natively inside After Effects. What it generates is what you would have built by hand, ready to refine.

**Multi-provider AI.** Bring your own API key for any of:
- Groq
- OpenRouter
- NVIDIA NIM (build.nvidia.com)

**Smart routing.** FrameCore picks the most powerful model available from the providers you have set up. If a provider times out or runs out of quota, it switches to the same model on another provider, then to the next best model.

**Free by default.** Only free models are used unless you turn on paid models in Settings. No accidental charges.

**Manual control.** Prefer a specific provider and model? Choose them yourself.

**Private by design.** Your keys stay on your machine. No accounts, no telemetry, no backend.

**Built to match your workflow.** Docks into After Effects like any native panel, with dark and light themes.

## Requirements

- Adobe After Effects 2024, 2025, or 2026
- Windows or macOS
- An API key from at least one supported provider (free tiers are available)

## Installation

Installation instructions arrive with the first release.

## Roadmap

- Add-ons: build your own and plug them into FrameCore
- Support for older After Effects versions
- UXP version of the panel

## Credits

Designed and built by **[melthedev](https://github.com/melthedevv)**.
