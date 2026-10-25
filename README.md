<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo/png/framecore-lockup-dark-1600.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo/png/framecore-lockup-light-1600.png">
    <img src="assets/logo/png/framecore-lockup-light-1600.png" alt="FrameCore" width="100%">
  </picture>
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo/tiles/tile-dark-1024.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo/tiles/tile-light-1024.png">
    <img src="assets/logo/tiles/tile-light-1024.png" alt="FrameCore" width="260">
  </picture>
</p>

<p align="center"><b>AI animation for After Effects.</b></p>

<p align="center">
  <img src="https://img.shields.io/badge/status-release%20candidate-8B5CF6?style=for-the-badge" alt="Status: Release candidate">
</p>

<p align="center">
  After Effects 2024–2026 &nbsp;·&nbsp; Windows & macOS &nbsp;·&nbsp; Free models by default
</p>

---

## Overview

FrameCore is an After Effects panel that turns a written description into a finished animation. Describe what you want, press Generate, and FrameCore builds it directly inside After Effects as real layers, keyframes, and expressions.

No second application, no exporting between tools, and no manual keyframing.

## Availability

**Release candidate, not yet published.** Version `0.9.0` is complete and in testing. There is no public download and no signed build yet. Release builds will be published on the [releases page](https://github.com/melthedevv/FrameCore/releases).

## How it works

| Step | What happens |
|---|---|
| **1. Prompt** | Describe the animation you want in plain language. |
| **2. Generate** | FrameCore selects the best available AI model and builds the animation in After Effects. |
| **3. Animate** | Keep it as an editable project, or take the finished MP4. |

## Output modes

| Mode | Result |
|---|---|
| **Build in AE** | The animation is created in your active composition as fully editable layers, keyframes, and expressions. |
| **MP4 only** | FrameCore builds the animation, renders it inside After Effects, and delivers the finished MP4. |

Both modes produce the same animation. A single toggle decides whether you keep the editable layers or the video.

## Capabilities

- **Native animation.** Everything is built inside After Effects, so the result is as editable as a project you made by hand.
- **Multi-provider AI.** Connect your own API key for Groq, OpenRouter, or NVIDIA NIM.
- **Smart model choice.** Automatic mode picks the strongest free model from the providers you connected, and moves on when one is unavailable.
- **Free by default.** Only free models are used unless you enable paid models in Settings, which prevents unexpected charges.
- **Manual selection.** Choose a specific provider and model whenever you prefer.
- **Privacy.** Your API keys stay on your machine. FrameCore has no accounts, no telemetry, and no backend.
- **Native panel experience.** It docks like any After Effects panel and includes dark and light themes.

## Supported providers

| Provider | Access |
|---|---|
| Groq | Your own API key |
| OpenRouter | Your own API key |
| NVIDIA NIM (build.nvidia.com) | Your own API key |

## Requirements

- Adobe After Effects 2024, 2025, or 2026
- Windows or macOS
- An internet connection
- An API key from at least one supported provider (free tiers are available)

## Install

FrameCore installs as an After Effects panel. Step-by-step instructions for Windows and macOS are in [`docs/INSTALL.md`](docs/INSTALL.md).

No signed build is published yet, so the current test build needs a one-time setting in After Effects to allow unsigned panels. A signed build will install normally from the [releases page](https://github.com/melthedevv/FrameCore/releases).

## Adding keys

1. Open `Window > Extensions > FrameCore`.
2. Go to **Settings**.
3. Paste a key into a provider card and press **Validate**.
4. Validate reports either the number of models the key can use or a sentence saying what to fix.

Keys are stored on this machine only:

- Windows: `%APPDATA%\FrameCore\settings.json`
- macOS: `~/Library/Application Support/FrameCore/settings.json`

**Remove** deletes one key. **Reset all settings** clears every key and preference on this machine.

## Free and paid models

FrameCore is free-first. **Allow paid models** is off by default; while it is off, only free models are used.

- **OpenRouter** marks which models are free. FrameCore uses only those while paid models are off.
- **Groq** and **NVIDIA NIM** decide free use by account, not by a label on the model. Their cards ask whether the account is on a paid plan, so FrameCore knows what that key can use.
- A paid model chosen by hand is refused with a plain explanation unless **Allow paid models** is on. It never silently swaps in a different model.

## Model selection

**Automatic** is the default. FrameCore uses only models you are allowed to use, prefers the strongest free one, and tries another provider when one is unavailable.

**Manual** lets you pick a provider and model in the runbar or in Settings. If the choice is unavailable, FrameCore says so instead of quietly changing it.

## Privacy

Your prompt and the composition settings sent with it (size, frame rate, duration, work area) go to the provider that answers. FrameCore does not send layer names, the project path, or any footage, keeps no copy, and makes no telemetry or analytics requests.

Free tiers differ, and they change:

- **Groq** states that API inputs and outputs are not used for training and are not retained by default; reliability and abuse logs can last up to 30 days.
- **OpenRouter** does not train on your data itself, but the model provider it routes to may. OpenRouter labels models that do not train.
- **NVIDIA NIM** free keys are for testing and evaluation only, not production, and NVIDIA may use content to improve its products.

The panel links each current policy. Check the policy before sending client work. More detail: [`docs/PRIVACY.md`](docs/PRIVACY.md).

## Troubleshooting

| Symptom | What to do |
|---|---|
| No FrameCore entry in `Window > Extensions` | The panel is not loaded. Reinstall it with `docs/INSTALL.md` and restart After Effects. |
| The panel opens blank | Close and reopen it. If it stays blank, reinstall and restart After Effects. |
| “No project open” / “No active composition” | Open a project and select a composition, then press Generate again. |
| A key will not validate | Check the key on the provider's site and confirm the machine is online, then read the sentence under the key field. |
| No models are available | Validate at least one key. If it still fails, choose a provider and model manually in Settings. |
| The MP4 will not render | Make sure After Effects can export an MP4 on its own, then try again. |
| A render is already running | Let the existing render finish, then try again. FrameCore never interrupts a render. |
| Settings did not persist | Check that `%APPDATA%\FrameCore` (Windows) or `~/Library/Application Support/FrameCore` (macOS) is writable. |

Every error message is a complete sentence with a next step. If one is not clear, that is a bug.

## Roadmap

- **Add-ons:** a system for building and plugging in your own extensions
- **Earlier After Effects versions:** support beyond 2024
- **UXP:** a UXP version of the panel

---

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo/png/framecore-wordmark-1200.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo/png/framecore-wordmark-light-1200.png">
    <img src="assets/logo/png/framecore-wordmark-light-1200.png" alt="FrameCore" width="420">
  </picture>
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo/png/framecore-tagline-1600.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo/png/framecore-tagline-light-1600.png">
    <img src="assets/logo/png/framecore-tagline-light-1600.png" alt="AI animation for After Effects" width="560">
  </picture>
</p>

## Credits

Designed and built by **[melthedev](https://github.com/melthedevv)**.

Bundled fonts are Orbitron and Inter under the SIL Open Font License; the licence texts are in `assets/fonts/`.

## License

FrameCore is proprietary software. Copyright © 2026 melthedev. All rights reserved.

Installing and using a copy for your own work is permitted. Copying, modifying, redistributing, or reselling it is not permitted without written permission. See [`LICENSE`](LICENSE).

The bundled Orbitron and Inter fonts remain under the SIL Open Font License.
