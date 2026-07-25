<p align="center">
  <img src="https://lensmith.app/logo.png" alt="Lensmith" width="120">
</p>

<h1 align="center">Lensmith</h1>

<p align="center"><em>Craft every image.</em></p>

<p align="center">
  <a href="https://github.com/fabds/lensmith-releases/releases/tag/updates"><img src="https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Ffabds%2Flensmith-releases%2Fmain%2Fmanifest.json&query=%24.latestVersion&label=version&color=F5B347" alt="Latest version"></a>
  <img src="https://img.shields.io/badge/macOS-14.6%2B-lightgrey?logo=apple" alt="macOS 14.6+">
  <img src="https://img.shields.io/badge/arch-Apple%20silicon%20%2B%20Intel-8A8F97" alt="Universal binary">
  <img src="https://img.shields.io/badge/access-invite%20only-E96034" alt="Invite only">
  <img src="https://img.shields.io/github/downloads/fabds/lensmith-releases/updates/total?label=downloads&color=444" alt="Downloads">
</p>

---

**Lensmith is a photo manager and non-destructive editor for macOS** — a modern
Lightroom alternative, with on-device AI for the tedious parts: denoise, deblur,
object removal, depth-driven bokeh, super-resolution and colorization.

This repository **is not the source code.** Lensmith is developed in a private
repo; this public one is where the app is *published*: installers, the update
feed, and the Core ML models the app downloads on demand.

> **Private beta.** Builds here need an **invite code** to run. Without one the
> app stops at the invite screen. Codes are issued by the maintainer — see
> [lensmith.app](https://lensmith.app/).

## Install

1. **Download the DMG** — from [lensmith.app](https://lensmith.app/) (always
   resolves to the current build) or from the
   [`updates` release](https://github.com/fabds/lensmith-releases/releases/tag/updates).
2. **Open it and drag Lensmith into Applications.**
3. **Get past Gatekeeper on first launch.** Lensmith is distributed outside the
   App Store and is not notarized yet, so macOS warns that it "can't verify" the
   app. Open **System Settings ▸ Privacy & Security**, scroll to **Security**,
   and click **Open Anyway**. The
   [installation guide](https://lensmith.app/install.html) walks through it with
   screenshots, including the Terminal fallback:
   ```sh
   xattr -dr com.apple.quarantine /Applications/Lensmith.app
   ```
4. **Enter your invite code.** It is remembered per Mac — you'll only type it once.

### Requirements

| | |
|---|---|
| **macOS** | 14.6 or later |
| **Architecture** | Universal — Apple silicon and Intel |
| **Disk** | ~130 MB for the app, plus up to ~385 MB if you use every AI model |
| **Network** | Only to download the app, its updates and the AI models |

## Updates

Lensmith updates itself through [Sparkle](https://sparkle-project.org). It
checks [`appcast.xml`](appcast.xml) in this repository once a day, and
**Help ▸ Check for Updates…** asks immediately. Every build is signed with an
EdDSA key whose public half ships inside the app, so an update that wasn't
published here is refused.

The app also reads [`manifest.json`](manifest.json) at launch. That file is the
release gate: it names the current version, the oldest one still allowed, and —
if a build ever turns out to be harmful — can retire it outright. During a beta
this is what makes it possible to stop a bad build from doing damage on someone
else's photo library. If your build falls below `minimumSupportedVersion`, the
app asks you to update before continuing.

## The AI models

The heavy Core ML models are **not bundled**: shipping all six would triple the
download for people who never use them. Each one is fetched on first use from
the [`models-v1` release](https://github.com/fabds/lensmith-releases/releases/tag/models-v1)
into `~/Library/Application Support/Lensmith/Models`, and you can delete that
folder any time — the app re-downloads what it needs.

Everything runs **on your Mac**. No photo is ever uploaded for processing.

| Asset | Powers | Upstream project | License | Download |
|---|---|---|---|---|
| `LensmithDenoiser.aar` | AI Denoise | [NAFNet](https://github.com/megvii-research/NAFNet) (SIDD) | MIT | 51 MB |
| `LensmithDeblurrer.aar` | AI Deblur | [NAFNet](https://github.com/megvii-research/NAFNet) (GoPro) | MIT | 30 MB |
| `LensmithUpscaler.aar` | Super Resolution (2× export) | [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN) x2plus | BSD-3-Clause | 29 MB |
| `DepthAnythingV2SmallF16.aar` | Depth tool — bokeh + relight | [Depth Anything V2 Small](https://huggingface.co/apple/coreml-depth-anything-v2-small) | Apache-2.0 | 43 MB |
| `LensmithInpainter.aar` | Content-aware Remove | [big-LaMa](https://github.com/advimman/lama) | Apache-2.0 | 91 MB |
| `LensmithColorizer.aar` | AI Colorize | [DDColor](https://github.com/piddnad/DDColor) (tiny) | Apache-2.0 | 97 MB |

Subject, sky and skin masking use [SAM 2.1 Tiny](https://huggingface.co/apple/coreml-sam2.1-tiny)
(Apache-2.0) and Apple's Vision framework, both bundled — nothing to download.

## What's in this repository

| Path | What it is |
|---|---|
| [`manifest.json`](manifest.json) | Release gate read by every running copy: `latestVersion`, `minimumSupportedVersion`, `killSwitch`, `message`, `downloadURL`, and the SHA-256 hashes of valid invite codes |
| [`appcast.xml`](appcast.xml) | Sparkle update feed — signed enclosures for each build |
| Release **`updates`** | `Lensmith-<version>.dmg` (first install) and `Lensmith-<version>.zip` (what Sparkle installs) |
| Release **`models-v1`** | The six Core ML models, as AppleArchive `.aar` |

Invite codes appear here only as **SHA-256 hashes** — the manifest is public, so
the codes themselves never are. Revoking a code means removing its hash: the
next launch locks that install out.

## Privacy

Your photos, catalogs and edits stay on your Mac. There is no cloud, no account,
and no sync. Every AI feature runs locally through Core ML.

What the app does send:

- **Update and gate checks** — plain requests for `manifest.json` and
  `appcast.xml`.
- **A beta heartbeat** — once at admission and then at most once a day: a random
  per-Mac identifier (generated locally, not a hardware ID), the SHA-256 of your
  invite code, the app version and the macOS version. It answers "how many
  testers are running which build", nothing else.
- **Problem reports you write** — only when you submit one. Diagnostics are
  optional and shown to you in full, verbatim, before anything is sent.

## Found a bug? Something feel wrong?

Use **Help ▸ Report a Problem…** inside the app. It carries the context that
makes a report actionable (build, macOS version, optional diagnostics), lets you
attach a screenshot, and gives you a ticket you can follow under
**Help ▸ My Reports** — the maintainer replies in the same thread.

Rough edges, confusing wording and slow moments are worth reporting too, not
just crashes. That is what a beta is for.

## License

Lensmith itself is proprietary software, distributed for private beta testing —
no license to redistribute or modify is granted. The third-party components
inside it keep their own licenses, reproduced in full under
**Help ▸ Open Source Licenses**:

[NAFNet](https://github.com/megvii-research/NAFNet) (MIT) ·
[SAM 2.1](https://huggingface.co/apple/coreml-sam2.1-tiny) (Apache-2.0) ·
[Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN) (BSD-3-Clause) ·
[Depth Anything V2](https://huggingface.co/apple/coreml-depth-anything-v2-small) (Apache-2.0) ·
[LaMa](https://github.com/advimman/lama) (Apache-2.0) ·
[DDColor](https://github.com/piddnad/DDColor) (Apache-2.0) ·
[GRDB.swift](https://github.com/groue/GRDB.swift) (MIT) ·
[Sparkle](https://sparkle-project.org) (MIT)

---

<p align="center">
  <a href="https://lensmith.app/">lensmith.app</a> ·
  <a href="https://lensmith.app/install.html">Installation guide</a> ·
  <a href="https://github.com/fabds/lensmith-releases/releases">All releases</a>
</p>
