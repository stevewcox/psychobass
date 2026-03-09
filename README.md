# PsychoBass

**A psychoacoustic bass enhancement plugin for Reaper (JSFX)**

PsychoBass adds upper harmonics to bass signals so they remain audible on small speakers and earbuds — without making the instrument sound electronic or synthetic. It is designed for bass guitar, upright bass, synthesiser bass, and kick drum.

---

## The Problem It Solves

Small speakers and earphones physically cannot reproduce frequencies below ~80–120Hz. Bass guitar fundamentals typically sit at 40–100Hz, so on those devices the bass disappears entirely. The classic solution — simply boosting low frequencies — does not help because the speaker still cannot move enough air to produce them.

The psychoacoustic solution is different: if you play the 2nd, 3rd, and 4th harmonics of a note (at 160Hz, 240Hz, 320Hz for an 80Hz fundamental), the human auditory system infers the missing fundamental and perceives the bass as present even though it was never reproduced. This is the *missing fundamental* or *virtual pitch* effect, well established in psychoacoustics.

The challenge is generating those harmonics in a way that sounds like the original instrument rather than an added synthesiser. This is what PsychoBass focuses on.

---

## Features

- **Timbre-matched harmonic generation** — harmonics follow the envelope and dynamics of the original signal, so the added content sounds like part of the instrument
- **Feedback-multiplier nonlinear device (NLD)** — generates a musical harmonic series favouring 2nd and 3rd harmonics (similar to tube saturation), rather than harsh odd-only or buzzy distortion
- **Phase-cancellation-free architecture** — the harmonic path is high-passed at 1.5× the crossover frequency, ensuring no frequency overlap between the direct bass signal and the NLD output (eliminates comb-filter notches)
- **Spectral tilt control** — smoothly rolls off upper harmonics so the series decays naturally, like a real acoustic instrument, rather than sitting at equal amplitude
- **Three harmonic styles** — Warm (2nd+3rd dominant), Tight (odd harmonics), Full (broadest series)
- **4th-order Butterworth crossover** — clean separation of bass band from the rest of the signal
- **Independent Original Bass control** — reduce the fundamental to let the harmonics carry more of the perceived weight on small speakers
- **Output gain** — ±24dB / +10dB trim to compensate for level changes

---

## Installation

### Via ReaPack (recommended)

[ReaPack](https://reapack.com) is Reaper's package manager and handles installation and future updates automatically.

1. In Reaper, go to **Extensions → ReaPack → Import a repository**
2. Paste this URL:
   ```
   https://github.com/stevewcox/psychobass/raw/main/index.xml
   ```
3. Click **OK**, then **Extensions → ReaPack → Browse packages**
4. Find **PsychoBass** and click **Install**
5. Restart Reaper when prompted

To receive future updates automatically, run **Extensions → ReaPack → Synchronize packages**.

### Manual installation

1. Download `Effects/PsychoBass.jsfx` from this repository
2. Copy it to your Reaper JSFX folder:
   - **Windows:** `%APPDATA%\REAPER\Effects\`
   - **macOS:** `~/Library/Application Support/REAPER/Effects/`
   - **Linux:** `~/.config/REAPER/Effects/`
3. In Reaper, open the FX chain on a track, click **Add**, and search for `PsychoBass`

No compilation or dependencies required.

---

## Controls

| Slider | Range | Default | Description |
|---|---|---|---|
| **Frequency** | 40–200 Hz | 80 Hz | Crossover point. Set just above the fundamental of your lowest note. Bass guitar: 80–100Hz. Kick drum: 60–80Hz. |
| **Intensity** | 0–100% | 50% | How strongly harmonics are generated. Start around 40–60% and increase to taste. |
| **Original Bass** | 0–100% | 70% | Level of the original sub-bass in the output. Reducing this lets the harmonics carry more of the perceived bass on small speakers. |
| **Harmonics Mix** | 0–100% | 50% | Blend of the generated harmonics into the output. |
| **Output Gain** | -24 to +10 dB | 0 dB | Output trim. The default -6dB compensates for the level increase from added harmonics. |
| **Harmonic Tilt** | 0–6 dB/oct | 4 dB/oct | How steeply the upper harmonics roll off. Higher values give a more natural, instrument-like decay of the harmonic series. 0 = flat (all harmonics equal level). |
| **Harmonic Style** | — | Warm | **Warm**: 2nd and 3rd harmonic dominant — suits bass guitar and upright bass. **Tight**: odd harmonics — punchy and modern. **Full**: broadest harmonic series — suits synth bass and kick. |

---

## Suggested Starting Points

**Bass guitar (finger style)**
Frequency: 85Hz · Intensity: 45% · Original Bass: 75% · Harmonics Mix: 55% · Tilt: 4dB · Style: Warm

**Bass guitar (pick / aggressive)**
Frequency: 90Hz · Intensity: 55% · Original Bass: 65% · Harmonics Mix: 60% · Tilt: 3dB · Style: Tight

**Upright / acoustic bass**
Frequency: 80Hz · Intensity: 35% · Original Bass: 80% · Harmonics Mix: 45% · Tilt: 5dB · Style: Warm

**Synth bass**
Frequency: 70Hz · Intensity: 60% · Original Bass: 60% · Harmonics Mix: 65% · Tilt: 2dB · Style: Full

**Kick drum**
Frequency: 65Hz · Intensity: 50% · Original Bass: 70% · Harmonics Mix: 50% · Tilt: 3dB · Style: Tight

---

## How It Works

The signal path has eight stages:

```
Input
  │
  ├─── 4th-order HP crossover ──────────────────────────────────┐
  │                                                              │
  └─── 4th-order LP crossover                                   │
         │                                                       │
         ├── Envelope follower                                   │
         │                                                       │
         └── Feedback-multiplier NLD                             │
               │                                                 │
               ├── Harmonic style blend                          │
               │                                                 │
               ├── Timbre-matched compander                      │
               │   (envelope-referenced gain, attack/release)    │
               │                                                 │
               ├── 4th-order HP at 1.5× crossover freq          │
               │   (removes overlap with fundamental)            │
               │                                                 │
               └── Spectral tilt (high-shelf cut)                │
                     │                                           │
                     └────── Mix ──────────────────────────────┘
                                │
                           Output gain
                                │
                             Output
```

**Timbre matching** is the most important element for natural sound. Rather than adding harmonics at a fixed level, the plugin continuously measures the envelope of the bass band and adjusts the harmonic amplitude to follow it. This means attack transients, sustain, and decay all retain the character of the original instrument. Without this, heavily processed bass takes on a synthetic quality.

**Phase cancellation suppression** addresses a common problem in naive implementations: when the NLD output contains energy at the same frequencies as the direct signal, any phase difference between them causes comb-filter notches — visible as regular dips in the spectrum and audible as a hollow, electronic character. By high-passing the harmonic output at 1.5× the crossover frequency, the two paths occupy entirely separate frequency ranges and cannot interfere.

**Spectral tilt** shapes the harmonic series so it decays with frequency, matching the behaviour of real plucked and bowed strings. A flat harmonic series (equal amplitude at every harmonic) looks stepped on a spectrum analyser and sounds unnatural; a tilted series is perceptually smoother and blends with the original instrument more convincingly.

---

## Technical Reference

The approach is based on the following published research:

- Ben-Tzur, D. & Colloms, M. (1999). *MaxxBass psychoacoustic bass enhancement*. AES 107th Convention.
- Larsen, E. & Aarts, R.M. (2002). *Reproducing low-pitched signals through small loudspeakers*. JAES, 50(3).
- Mu, B., Gan, W.-S. & Tan, E.-L. (2013). *Timbre matching of inharmonic signals for virtual bass system*. ICASSP.
- Oo, Z.Z. & Gan, W.-S. *Analysis of nonlinear devices for virtual bass systems.*

The nonlinear device is a feedback-multiplier design: `y = x · (1 + k·|x|)` followed by exponential soft-clipping. This produces a musically weighted harmonic series with strong 2nd and 3rd harmonics, analogous to asymmetric tube saturation.

---

## Licence

MIT License. See [LICENSE](LICENSE) for full text.

This plugin is an independent implementation based on published academic research. It is not affiliated with, endorsed by, or derived from any commercial product.

---

## Contributing

Bug reports, suggestions, and pull requests are welcome. If you have measured results comparing the output spectrum against reference material, or have found better default values for specific instrument types, please open an issue or PR.

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md).
