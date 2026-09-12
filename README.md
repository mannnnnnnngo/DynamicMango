<div align="center">

<img src="Icon/mangodynamic.png" width="140" alt="DynamicMango icon">

# 🥭 DynamicMango

**A Dynamic Island for the MacBook notch — now playing, lyrics, a live piano roll, a drop shelf — with a knob for everything.**

Made by Mingyu 🧑‍💻

<br>

![macOS](https://img.shields.io/badge/macOS-14%2B-202020?style=for-the-badge&logo=apple&logoColor=white)
![Version](https://img.shields.io/badge/version-1.0.0-7C5CFF?style=for-the-badge)
![Status](https://img.shields.io/badge/status-in%20development-F59E0B?style=for-the-badge)
![Price](https://img.shields.io/badge/price-free-2EA043?style=for-the-badge)
![Permissions](https://img.shields.io/badge/permissions%20to%20run-none-0EA5E9?style=for-the-badge)

</div>

---

> [!WARNING]
> **DynamicMango is in development.** 🚧 The notch draws itself, now playing works, the shelf accepts
> drops, and the settings window is real — but several features are built and not yet proven against
> the messy real world, and they are listed honestly in
> [🚧 Known limitations](#-known-limitations) rather than quietly.

> [!NOTE]
> **A boringNotch replacement, written clean-room.** boringNotch's behaviour was observed from the
> running app — its bundle, its settings, its network endpoints — and reimplemented. Its source was
> never read. That is a standing rule of the project, not a convenience. 🧼

---

## 📖 Contents

| | | |
| --- | --- | --- |
| [🧐 Why this exists](#-why-this-exists) | [📥 Install](#-install) | [📐 Measuring your notch](#-measuring-your-notch) |
| [⚙️ Configuration](#️-configuration) | [🔐 Permissions](#-permissions) | [🎹 The piano roll](#-the-piano-roll) |
| [🚧 Known limitations](#-known-limitations) | [🗂️ Where things live](#️-where-things-live) | [🔔 Updates](#-updates) |
| [🗑️ Uninstall](#️-uninstall) | [⚖️ Licence](#️-licence) | |

---

## 🧐 Why this exists

Two reasons, in that order: wanting my own version, and wanting **deeper customization** than
boringNotch offers — colour, transparency, layout, preferences.

boringNotch has opinions; DynamicMango has knobs. Every feature exposes its parameters in
`config.json` from the moment it is written, and a theme is data rather than code. That is a rule, not
an aspiration: nothing is hardcoded with the intention of making it configurable later.

---

## 📥 Install

Grab the `.dmg` from **[the Releases page](https://github.com/mannnnnnnngo/DynamicMango/releases)**,
open it, and drag **DynamicMango** onto Applications. It runs as a background agent — no Dock icon.
Needs macOS 14 or newer, on a MacBook with a notch.

> [!NOTE]
> DynamicMango is still in development, so there may not be a build on that page yet. 🚧

> [!IMPORTANT]
> The first time you open it, macOS blocks it — the app isn't signed with a paid Apple developer
> account. Double-click DynamicMango, press **Done** on the warning, then go to
> **&#63743; → System Settings → Privacy & Security**, scroll to the bottom, and press **Open Anyway**.
> Press **Open Anyway** once more to confirm. You only do this once. 🔓

First run writes `~/.config/dynamicmango/config.json`.

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
`appearance.widthAdjust` and `heightAdjust` nudge it without reinstalling, and
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
  at all. This is exactly what boringNotch already does; the difference is that this says so.
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
  than bodged and degrades to "no brightness HUD" rather than crashing.
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

None of it is in this repository — what you were listening to is not something a download should
carry. 🔒

---

## 🔔 Updates

DynamicMango checks [`updates/latest.json`](updates/latest.json) on this repository and tells you when
a newer version is out. It carries nothing about you, and the download is whatever is attached to the
matching release. 📡

---

## 🗑️ Uninstall

```bash
pkill -x DynamicMango
rm -rf /Applications/DynamicMango.app ~/.config/dynamicmango ~/Library/Logs/DynamicMango
```

Nothing is installed system-wide and no system settings are modified.

---

## ⚖️ Licence

DynamicMango is **free to use** but **not open source**. It may not be redistributed, modified,
resold, reverse engineered, or presented as anyone else's work. The full terms are in
[`LICENSE`](LICENSE).

DynamicMango uses `mediaremote-adapter` (BSD 3-Clause) and basic-pitch. Those are not mine and are not
covered by the above — each carries its own licence.

Copyright © 2026 Mingyu. All rights reserved.

---

<div align="center">

**Made with 🥭 by Mingyu**

🆓 Free forever · 🔒 No permissions to run · 🎛️ A knob for everything

</div>
