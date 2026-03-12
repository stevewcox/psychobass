# PsychoBass

**A psychoacoustic bass enhancement plugin for Reaper (JSFX)**

PsychoBass adds upper harmonics to bass signals so they remain audible on small speakers and earbuds — without making the instrument sound electronic or synthetic. It is designed for bass guitar, upright bass, synthesiser bass, and kick drum.

**Currently Pre-Release**

The sound and levels of the plugin may differ between versions, please be aware if updating and revisting previous installations. At version 1.0.0, the output will remain consistent at the same settings and this pre-release warning will be removed. 
---

## The Problem It Solves

Small speakers and earphones physically cannot reproduce frequencies below ~80–120Hz. Bass guitar fundamentals typically sit at 40–100Hz, so on those devices the bass disappears entirely. The classic solution — simply boosting low frequencies — does not help because the speaker still cannot move enough air to produce them.

The psychoacoustic solution is different: if you play the 2nd, 3rd, and 4th harmonics of a note (at 160Hz, 240Hz, 320Hz for an 80Hz fundamental), the human auditory system infers the missing fundamental and perceives the bass as present even though it was never reproduced. This is the *missing fundamental* or *virtual pitch* effect, well established in psychoacoustics.

The challenge is generating those harmonics in a way that sounds like the original instrument rather than an added synthesiser. This is what PsychoBass focuses on.

---

## Features

- **Timbre-matched harmonic generation** — the NLD is driven at a normalised level then re-scaled by the signal envelope, so harmonics track the instrument's dynamics naturally rather than sitting at a fixed level
- **Feedback-multiplier nonlinear device (NLD)** — generates a rich harmonic series with strong 2nd and 3rd harmonics, similar to asymmetric tube saturation, avoiding the harsh character of simple clipping or rectification
- **Phase-cancellation-free architecture** — the harmonic path is high-passed at 1.5× the crossover frequency, ensuring no frequency overlap between the direct bass signal and the NLD output (eliminates comb-filter notches)
- **Two-shelf spectral envelope** — a low-shelf boost lifts the 2nd–4th harmonic peak region, while a high-shelf cut rolls off the upper series smoothly, matching the harmonic decay shape of a natural instrument
- **Three harmonic styles** — Full (smooth decay to ~2kHz), Tight (focused low-mids, drops off ~600Hz), Bright (extended series to 10kHz+)
- **4th-order Butterworth crossover** — clean separation of the bass band from the rest of the signal
- **Independent Original Bass control** — reduce the fundamental to let the harmonics carry more of the perceived weight on small speakers
- **Output gain** — -24dB to +10dB trim
- **Automatable bypass** — click-free dry/wet crossfade bypass that can be automated on any track

---

## Installation

### Via ReaPack (recommended)

[ReaPack](https://reapack.com) is Reaper's package manager and handles installation and future updates automatically.

1. In Reaper, go to **Extensions → ReaPack → Import a repository**
2. Paste this URL:
   ```
   https://github.com/stevewcox/SteveCoxJSFX/raw/main/index.xml
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
| **Intensity** | 0–100% | 60% | How strongly harmonics are generated. Start around 50–65% and increase to taste. |
| **Original Bass** | 0–100% | 70% | Level of the original sub-bass in the output. Reducing this lets the harmonics carry more of the perceived bass on small speakers. |
| **Harmonics Mix** | 0–100% | 65% | Blend of the generated harmonics into the output. |
| **Output Gain** | -24 to +10 dB | 0 dB | Output trim. Applied to the wet signal only — has no effect when bypassed. |
| **Harmonic Tilt** | 0–8 dB/oct | 4 dB/oct | Depth of the high-shelf cut on the harmonic path. Higher values roll off the upper series more steeply. 0 = flat. |
| **Harmonic Style** | — | Full | **Full**: smooth decay to ~2kHz, closest to character of a well-known alternative — best for bass guitar and upright bass. **Tight**: LP filtered, drops off ~600Hz, focused low-mids — suits kick drum. **Bright**: raw NLD output, extends to 10kHz+, use sparingly. |
| **Bypass** | Bypassed / Active | Active | Engages or disengages the plugin with a ~30ms dry/wet crossfade to prevent clicks. Can be automated via right-click → "Add automation lane for Bypass". When bypassed, the signal passes through unprocessed at unity gain. |

---

## Using PsychoBass

PsychoBass is designed to be set up in a specific order. Working through the controls from top to bottom — rather than adjusting everything at once — makes it much easier to dial in a good result.

### 1. Set the crossover frequency

The **Frequency** control determines which part of the signal is treated as "bass" and sent to the harmonic generator. Set it just above the fundamental of the lowest note your instrument plays — low enough that the fundamental itself stays in the bass band, high enough that you're not feeding mid-range content into the NLD.

For most bass guitar, 80–90Hz is a good starting point. For kick drum, try 60–70Hz. If you set it too high, the plugin will start generating harmonics from mid-range content and the result will sound cluttered. If you set it too low, the fundamental may not trigger the harmonic generator consistently.

### 2. Engage the harmonics with Intensity

With **Original Bass** and **Harmonics Mix** both at 100%, raise **Intensity** until you can clearly hear the harmonics being added. On a small speaker or earbud, this is the point where the bass starts to become audible as a pitched sound rather than a vague low-frequency presence.

Intensity controls how hard the nonlinear device is driven, which affects both the level and the character of the harmonics. At low settings the harmonic series is sparse and subtle; at higher settings it becomes richer and more saturated. Most sources sit well somewhere in the 45–70% range. Going too high makes the result sound electronic rather than musical — if the harmonics feel synthetic or harsh, back Intensity off before reaching for any other control.

### 3. Choose a harmonic style

**Full** is the default and suits most sources — it produces a smooth harmonic series that decays naturally above ~2kHz, similar to the character of a well-saturated instrument. **Tight** rolls off sharply above ~600Hz, which works well for kick drum or when you want the enhancement to stay focused in the low-mids without adding upper harmonic brightness. **Bright** extends the series all the way to 10kHz+ and is best used sparingly on synth bass where that additional presence is part of the intended sound.

### 4. Shape the harmonic decay with Harmonic Tilt

Once you have harmonics you like, **Harmonic Tilt** controls how steeply the upper harmonics roll off. At 0 the harmonic series is relatively flat; higher values increasingly favour the lower harmonics (2nd, 3rd, 4th) over the upper ones. This is useful for making the enhancement blend more naturally — most acoustic instruments have a harmonic series that decays with frequency, so a moderate tilt of 3–5dB/oct tends to sound most natural. If the harmonics feel bright or abrasive on playback, increase the tilt before reducing Intensity.

### 5. Balance the output with Harmonics Mix and Original Bass

These two controls determine the final blend of the three signal components: the high-frequency content above the crossover (always passed through untouched), the original bass fundamental, and the generated harmonics.

**Harmonics Mix** sets how much of the processed harmonic signal appears in the output. Unlike Intensity, this is a simple level fader applied after all processing — it does not change the character of the harmonics, only how prominent they are. Use it to blend the harmonics naturally into the track.

**Original Bass** sets the level of the original sub-bass fundamental. On a full-range system you will typically want this at or near 100% so the low end remains intact. On a mix intended for small speakers you may find it useful to reduce it — the harmonics carry the perceived bass weight, and pulling back the fundamental that small speakers cannot reproduce anyway can actually make the bass feel tighter and more present on those devices.

A good workflow is to set Harmonics Mix first on the target playback device (phone speaker, earbuds) until the bass feels present, then check back on a full-range system and adjust Original Bass to make sure the low end still has the right weight there.

### 6. Trim the output level

**Output Gain** is a final level trim applied to the wet signal. Use it to compensate for any overall level change the plugin introduces. It has no effect when the plugin is bypassed, so it is useful for gain-matched A/B comparisons — set it so the bypassed and active signals feel roughly equal in loudness, then the difference you hear is purely the character of the enhancement rather than a level change.

---

## Suggested Starting Points

**Bass guitar (finger style)**
Frequency: 85Hz · Intensity: 55% · Original Bass: 75% · Harmonics Mix: 60% · Tilt: 4dB · Style: Full

**Bass guitar (pick / aggressive)**
Frequency: 90Hz · Intensity: 60% · Original Bass: 65% · Harmonics Mix: 65% · Tilt: 3dB · Style: Full

**Upright / acoustic bass**
Frequency: 80Hz · Intensity: 45% · Original Bass: 80% · Harmonics Mix: 50% · Tilt: 5dB · Style: Full

**Synth bass**
Frequency: 70Hz · Intensity: 60% · Original Bass: 60% · Harmonics Mix: 65% · Tilt: 2dB · Style: Bright

**Kick drum**
Frequency: 65Hz · Intensity: 50% · Original Bass: 70% · Harmonics Mix: 50% · Tilt: 4dB · Style: Tight

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
         ├── Normalise to NLD drive level                        │
         │   (envelope-referenced pre-gain)                      │
         │                                                       │
         └── Feedback-multiplier NLD                             │
               │                                                 │
               ├── Harmonic style blend                          │
               │                                                 │
               ├── Re-scale by envelope (timbre matching)        │
               │   smoothed by attack/release compander          │
               │                                                 │
               ├── 2nd-order HP at 1.5× crossover freq          │
               │   (removes overlap with fundamental)            │
               │                                                 │
               └── Two-shelf spectral envelope                   │
                   (low-shelf boost + high-shelf tilt cut)       │
                     │                                           │
                     └────── Mix ──────────────────────────────┘
                                │
                           Output gain
                                │
                        Bypass crossfade
                                │
                             Output
```

**Normalised NLD drive** is the key to consistent harmonic generation at any input level. The bass band signal is scaled to a fixed drive level before the nonlinearity, so the NLD always generates a full harmonic series regardless of whether the input is loud or quiet. The envelope is then multiplied back in after the NLD, restoring correct dynamics. This is what gives the plugin its compressed, saturated character at moderate settings.

**Timbre matching** ensures the harmonics track the instrument's natural dynamics. The re-scaling compander uses separate attack and release times so it follows transients without pumping, preserving the instrument's articulation even at high Intensity settings.

**Phase cancellation suppression** addresses a common problem in naive implementations: when the NLD output contains energy at the same frequencies as the direct signal, any phase difference causes comb-filter notches — visible as regular dips in the spectrum and audible as a hollow, electronic character. By high-passing the harmonic output at 1.5× the crossover frequency, the two paths occupy entirely separate frequency ranges and cannot interfere.

**Two-shelf spectral envelope** shapes the harmonic series to match the decay curve of a natural instrument. A low-shelf boost lifts the 2nd–4th harmonic region (the perceptual "body" of the bass on small speakers), and a high-shelf cut rolls off the upper series at a rate set by the Harmonic Tilt slider. Together these produce a smooth downward envelope peaking at the 3rd–4th harmonic, rather than a flat or stepped series.

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

The documentation and portions of the codebase were written with the assistance of [Claude AI](https://claude.ai) by Anthropic.

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md).
