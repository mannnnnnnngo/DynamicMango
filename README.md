<div align="center">

<img src="mangodynamic.png" width="140" alt="DynamicMango icon">

# 🥭 DynamicMango

**A Dynamic Island for the MacBook notch — now playing, lyrics, a live piano roll, a drop shelf — with a knob for everything.**

Made by Mingyu 🧑‍💻

<br>

![macOS](https://img.shields.io/badge/macOS-14%2B-202020?style=for-the-badge&logo=apple&logoColor=white)
![Swift](https://img.shields.io/badge/Swift-SwiftUI-FA7343?style=for-the-badge&logo=swift&logoColor=white)
![Version](https://img.shields.io/badge/version-1.1.0-7C5CFF?style=for-the-badge)
![Status](https://img.shields.io/badge/status-in%20development-F59E0B?style=for-the-badge)
![Price](https://img.shields.io/badge/price-free-2EA043?style=for-the-badge)
![Permissions](https://img.shields.io/badge/permissions%20to%20run-none-0EA5E9?style=for-the-badge)

</div>

---

> [!WARNING]
> **DynamicMango is in development.** 🚧 The notch draws itself, now playing works, the shelf accepts
> drops, and the settings window is real — but several features are built and not yet proven against
> the messy real world, and they are listed honestly in
> [🚧 Known limitations](#-known-limitations) rather than quietly. `checklist.json` is the
> source of truth: **49 of 77 items done, 9 in progress, 18 to go** as of its last update.

> [!NOTE]
> **Written clean-room.** Where another notch app already did something, its behaviour was observed
> from the running app — its bundle, its settings, its network endpoints — and written again from
> scratch. No other project's source was ever read. That rule is recorded in `checklist.json`
> under `ground_rules` so it never gets treated as a shortcut when something turns out to be hard. 🧼

---

## 📖 Contents

| | | |
| --- | --- | --- |
| [🧐 Why this exists](#-why-this-exists) | [📥 Install](#-install) | [📐 Measuring your notch](#-measuring-your-notch) |
| [⚙️ Configuration](#️-configuration) | [🔐 Permissions](#-permissions) | [🎹 The piano roll](#-the-piano-roll) |
| [🚧 Known limitations](#-known-limitations) | [🗂️ Where things live](#️-where-things-live) | [🧱 Source layout](#-source-layout) |
| [🕶️ Privacy](#️-privacy) | [🆕 What's new in 1.1.0](#-whats-new-in-110) | [🗑️ Uninstall](#️-uninstall) |
| [⚖️ Licence](#️-licence) | | |

---

## 🧐 Why this exists

Two reasons, in that order: wanting my own, and wanting **deep customization** of it — colour,
transparency, layout, preferences.

Every feature exposes its parameters in `config.json` from the moment it is written, and a theme is
data rather than code. That is a rule, not an aspiration: nothing is hardcoded with the intention of
making it configurable later.

---

## 📥 Install

Download **`dynamicmango-1.1.0.dmg`** from the
[latest release](https://github.com/mannnnnnnngo/DynamicMango/releases/latest), drag the mango onto
Applications, then **right-click DynamicMango → Open** the first time. That step matters — the app
isn't signed with a paid Apple developer account, and right-click → Open is Apple's own way past
the warning. You only do it once.

### Building it yourself

```bash
./make_signing_cert.sh   # once
./build_app.sh           # builds and installs to /Applications
./package.sh             # builds and wraps it in dist/dynamicmango-<version>.dmg
```

Installs **DynamicMango.app** into `/Applications` and runs it as a background agent.

> [!IMPORTANT]
> Run `make_signing_cert.sh` first and it genuinely matters here. Ad-hoc signatures get a new code hash
> on every build, and macOS treats a changed hash as a different app — so every rebuild would drop your
> Calendar, Camera and audio permissions and re-prompt. A stable self-signed identity fixes that
> permanently. 🔏

Unlike [MangoBar](https://github.com/mannnnnnnngo/Mangobar), this is a **real compile**: Swift has no
thin-shim option, so editing source means rebuilding. `./build_app.sh` takes a few seconds. `VERSION`
is the single source of truth for the version number, and the build refuses to run if
`Core/Version.swift` has drifted from it.

For development without installing:

```bash
./run.sh
```

First run writes `~/.config/dynamicmango/config.json` and shows the six-step tour.

---

## 📐 Measuring your notch

```bash
/Applications/DynamicMango.app/Contents/MacOS/DynamicMango --print-geometry
```

```
screen        : 1710.0 x 1107.0 at (0.0, 0.0)  scale 2.0x
hardware notch: true
safeArea.top  : 33.0
aux strips    : left 763.0  right 762.0
notch rect    : 185.0 x 33.0 at (763.0, 1074.0)
notch pixels  : 370.0 x 66.0
```

The notch rect is derived entirely from public API — `safeAreaInsets.top` for the height, and the width
of the two auxiliary menu-bar strips either side for the width. If the panel is ever a hair misaligned,
`appearance.widthAdjust` and `heightAdjust` nudge it without recompiling, and
`debug.showGeometryOverlay` strokes the computed rect in magenta so you can *see* the error instead of
guessing at it. 🔍

---

## ⚙️ Configuration

Everything lives in `~/.config/dynamicmango/config.json` and applies within about a second — no
restart. The first run writes every key, including the ones set to their defaults, so the whole surface
is discoverable without reading docs.

| Key | Meaning |
|---|---|
| `appearance.theme` | `mango` or `neutral` |
| `appearance.opacity` | 0.0–1.0 for the closed notch fill |
| `appearance.bottomCornerRadius` | Lower corner radius, points (13 matches the hardware) |
| `appearance.topCornerRadius` | Concave fillets into the menu bar; 0 while closed |
| `appearance.widthAdjust` / `heightAdjust` | Nudge the measured notch rect, points |
| `behavior.hideFromScreenRecording` | Keep the panel out of screenshots and recordings |
| `behavior.showMenuBarIcon` | Menu bar item — also your escape hatch |
| `shelf.enabled` | Accept files dragged onto the notch. Off means a drag passes straight over it |
| `shelf.maxItems` | Oldest items fall off past this. There is no unlimited |
| `shelf.copyFilesIn` | Copy the bytes into Application Support instead of remembering the path |
| `shelf.pruneMissing` | Forget items whose file has moved or gone, checked when the panel opens |
| `shelf.tileSize` | Tile size in points |
| `shelf.showTab` | Show the shelf tab. Off still accepts drops, it just isn't browsable |
| `display.builtInOnly` | Only draw on the built-in notched display |
| `display.pinnedScreenUUID` | Pin to one screen by UUID; `null` means built-in |
| `debug.showGeometryOverlay` | Stroke the computed notch rect in magenta |
| `debug.fileLogging` | Mirror logs to `~/Library/Logs/DynamicMango/` |

> [!TIP]
> A malformed edit will not break the app or reset your settings: a bad value falls back to that key's
> default and logs a warning, and invalid JSON leaves the file untouched so you can fix your typo. 🛟

---

## 🔐 Permissions

The notch panel needs **none**. Notably it needs neither Accessibility nor Screen Recording, which is a
real advantage over MangoBar and is protected as a design constraint.

| Feature | Permission | Without it |
|---|---|---|
| 📅 Calendar widget | Calendar | No events |
| 📷 Camera mirror | Camera | No mirror. Off by default, and the session is torn down whenever the mirror is hidden, so the green indicator never lights while idle |
| 🎚️ Audio visualizer | None observed on macOS 26 — a per-process tap prompted for nothing | Falls back to playback-driven synthetic bars |
| 🎹 Piano roll | The same tap as the visualizer; no additional permission | No roll |

Nothing is requested until you use the feature that needs it.

---

## 🕶️ Privacy

**Nothing leaves your Mac.** There is no account, no analytics, and no server — there is nowhere
for anything to go.

- The visualiser and the piano roll tap the audio your Mac is already playing. What the tap hears
  becomes bars and notes in memory and is then discarded: never recorded, never written to disk,
  never uploaded.
- What is playing, the lyrics read out of the player you already have open, and anything you put
  on the shelf are all read here and stay here.
- Your settings are one JSON file in your own home folder.

The only request DynamicMango makes by itself is reading one small text file on GitHub to find out
whether a newer version exists. It sends nothing about you or this Mac, and Settings → Updates
switches even that off. The same words are in the app, in Settings → **Privacy**.

---

## 🆕 What's new in 1.1.0

| | |
|---|---|
| 🗂️ **Sidebar settings** | Nine panes grouped into Notch / What it shows / App, each with a line saying what it is for. The seven segments it replaced had room for one word each and nowhere to grow. |
| 🚀 **Open at login** | Settings → General. |
| ❓ **A tutorial** | Six steps, shown on first launch and replayable from the menu bar item. |
| 🕶️ **A Privacy pane** | What the audio tap does, in the app rather than only in this file. |
| 🔽 **Updates** | Unchanged, but now a pane with a name instead of the eighth segment. |
| 💿 **A proper installer** | The disk image opens the same drag-to-Applications window every Mango app uses. |

---

## 🎹 The piano roll

The notch can draw the notes of what is playing. Two things can feed it:

- **A delivered score** from [Mango MIDI](https://github.com/mannnnnnnngo/MangoMIDI), dropped into
  `~/Library/Application Support/DynamicMango/Scores`. This is the cheapest the roll ever runs: no tap,
  no classifier, no inference.
- **Its own live transcription**, from a per-process audio tap, when there is no delivered score.

A delivered score **replaces** the live transcription rather than joining it. A delivered score is the
end of a search that rendered candidates back to audio and kept the best over the whole piece; a live
pass is one two-second window judged once and never revised. Merging them drew the live pass's wrong
notes on top of a transcription that had already rejected them — permanently, because the cache never
forgets. Better evidence has to win outright.

---

## 🚧 Known limitations

Written as they are hit, not at the end. 📝

- **Now-playing depends on an entitled Perl shim.** Apple restricted the private MediaRemote framework
  in macOS 15.4, so reading "what's playing" requires `mediaremote-adapter` (BSD 3-Clause) loaded
  inside `/usr/bin/perl`. It works on 26.2 — verified — but it is the project's largest external
  dependency risk, and there is a documented fallback for the day Apple closes it.
- **Lyrics require the internet.** Spotify has no lyrics API; their lyrics are licensed from Musixmatch
  and absent from the Web API. Lyrics come from lrclib.net, which means the title, artist, album and
  duration of what you are playing leave your machine — no identifiers, no account. Results are cached
  on disk so a repeat play is offline. Set `lyrics.enabled` to `false` and there is no network traffic
  at all. Any notch app that shows you lyrics is doing this; the difference is that this one says so.
- **The piano roll has not been seen rendering in the panel.** Every stage under it is verified — the
  model loads and compiles at runtime, `--probe-transcribe --self-test` recovers synthetic notes at the
  right pitch and time, and `--probe-transcribe` transcribes real audio from a live tap — but the view
  itself has only been exercised through those probes. The gate's thresholds are also untuned against
  real tracks.
- **The visualizer's real spectrum path has not been seen against music yet.** The bars are fed by an
  actual per-process audio tap and a vDSP FFT, and the maths is verified — `--probe-spectrum
  --self-test` checks tone placement, amplitude scaling and silence against generated signals, with no
  audio device involved. What is unproven is the whole chain running against a real player. If the tap
  yields nothing the bars fall back to the synthetic ones and the reason is logged, so the failure is
  quiet by design; check `--probe-spectrum` if they look fake.
- **No brightness peek.** Volume and charge use public, event-driven APIs. Brightness has no public
  read API on Apple Silicon internal displays — it needs a private framework — so it is deferred rather
  than bodged, isolated behind one file, and degrades to "no brightness HUD" rather than crashing.
- **System HUD suppression is off by default.** It works by suspending `OSDUIHelper`, for which there
  is no API. Recovery is proven (restore on quit, heal at next launch, a menu item), but suppression
  itself is untested end to end, because `OSDUIHelper` only exists once you press a volume key.
- **External displays draw nothing.** An external monitor has no physical cutout, so anything drawn
  there would read as a black tab rather than a notch.

---

## 🗂️ Where things live

| Where | What |
|---|---|
| `~/.config/dynamicmango/config.json` | ⚙️ Every setting, live-reloaded |
| `~/Library/Application Support/DynamicMango/Scores` | 🎼 Scores delivered by Mango MIDI |
| `~/Library/Logs/DynamicMango/` | 📜 Logs, when `debug.fileLogging` is on |

None of it is in this repository, and `Scores/` is in `.gitignore` — what you were listening to is not
something a clone should carry. 🔒

---

## 🧱 Source layout

Layered, so any one piece can be replaced without touching the rest. This is a hard rule, not a
guideline — the same layering as MangoBar:

```
Sources/DynamicMango/
  main.swift            entry point, diagnostic flags
  App/                  AppDelegate - wiring only, no logic
  Core/                 logging, models, state machines.  NO platform imports.
  Config/               defaults + JSON store with live reload
  MacOS/                ALL platform API lives here: AppKit, IOKit, CoreAudio,
                        private frameworks. Nothing else may import them.
  Feeds/                where content comes from: now playing, calendar, battery, audio taps
  Actions/              what user interactions do
  UI/                   views + theme
    Widgets/            one file per widget + a registry
```

**The rules:**

1. **`Core/` imports nothing platform-specific.** If it needs a screen or a process, it's in the wrong
   layer.
2. **Platform API is quarantined in `MacOS/`,** one file per concern. This matters more than usual
   here: several features use private frameworks (brightness) or fragile entitled paths (now playing),
   and when Apple breaks them the blast radius must be one file with a documented fallback.
3. **`UI/` never touches a platform API directly** and never reads a setting from anywhere but
   `Config/`.
4. **Every feature registers its defaults in `Config/` as it is written.** Never hardcode a value
   intending to make it configurable later — customization depth is the reason this project exists.
5. **Adding a widget is:** write the module in `UI/Widgets/`, add one line to the registry, name it in
   config. If it takes more than that, the registry is wrong.
6. **Restyling touches `UI/Theme.swift` only.**

> [!IMPORTANT]
> **A window intercepts every click inside its frame, no matter what it draws there.** Drawing nothing
> does not make a region click-through, and neither does a clear background. `ARCHITECTURE.md` has the
> full account of how the panel stays out of the way of everything it overlaps. 🖱️

| 📄 File | Purpose |
| --- | --- |
| `MacOS/NotchGeometry.swift` | Measuring the hardware notch from public API alone |
| `MacOS/NotchPanel.swift` | The borderless window, and its mouse transparency |
| `Core/MangoScores.swift` | Reading what Mango MIDI delivers |
| `Feeds/NowPlayingFeed.swift` | What's playing, through the entitled adapter |
| `MacOS/ProcessTap.swift` | The per-process audio tap the visualizer and roll share |
| `UI/Theme.swift` | Every colour and metric. Restyling touches this file only |
| `checklist.json` | What is done, what isn't, and the ground rules |
| `ARCHITECTURE.md` | The layering, and the reasons behind the awkward parts |

---

## 🗑️ Uninstall

```bash
pkill -x DynamicMango
rm -rf /Applications/DynamicMango.app ~/.config/dynamicmango ~/Library/Logs/DynamicMango
```

Then delete this folder. Nothing is installed system-wide and no system settings are modified.

---

## ⚖️ Licence

DynamicMango is **free to use** but **not open source**. The source is published here to be read, not
reused: it may not be redistributed, resold, built upon, or presented as anyone else's work. The full
terms are in [`LICENSE`](LICENSE).

`third_party/` is not mine and is not covered by that — `mediaremote-adapter` (BSD 3-Clause) and
basic-pitch each carry their own licence beside them.

Copyright © 2026 Mingyu. All rights reserved.

---

<div align="center">

**Made with 🥭 by Mingyu**

🚧 In development · 🔒 No permissions needed to run · 🎹 Fed by [Mango MIDI](https://github.com/mannnnnnnngo/MangoMIDI)

Part of [🥭 MangoApps](https://github.com/mannnnnnnngo/MangoApps)

</div>
