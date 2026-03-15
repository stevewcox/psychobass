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

- **Recursive feedback multiplier NLD** — `y[n] = (x[n] + dc) × (1 + k × |y[n-1]|)`, the original MaxxBass architecture (patent expired 2019). Generates a full even+odd harmonic series extending to high frequencies; DC bias controls even/odd balance; soft limiter and envelope-scaled bias prevent distortion artefacts
- **Bandpass-isolated NLD drive** — a narrow bandpass filter (±1 octave around the crossover) isolates the fundamental before the NLD, preventing intermodulation from complex signals (bass guitar chords, double stops) and keeping the generated harmonic series clean
- **Timbre-matched harmonic generation** — the NLD is driven at a normalised level then re-scaled by the signal envelope, so harmonics track the instrument's dynamics naturally rather than sitting at a fixed level
- **Perceptually bounded harmonic LP** — an 8th-order low-pass at 4× the crossover frequency (-48dB/oct) removes content above the perceptually active range for the missing fundamental effect (~100–500Hz), matching the rolloff shape of the reference product
- **Correct output crossover architecture** — mids and highs above the crossover always pass through at full level; only the sub-bass below the crossover is scaled by the Original Bass control, matching the behaviour expected from a psychoacoustic bass enhancer
- **Sub-bass bleed floor** — a 15% minimum is applied to the Original Bass path so the sub-bass is never completely removed, matching the reference product's behaviour at minimum Original Bass
- **Three harmonic styles** — Full (smooth, natural harmonic decay), Tight (LP filtered, focused low-mids), Bright (half-wave rectified blend, extended harmonic series)
- **4th-order Butterworth crossover** — clean separation of the bass band used for the output mix
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
| **Original Bass** | 0–100% | 50% | Level of the original sub-bass in the output. Reducing this lets the harmonics carry more of the perceived bass on small speakers. A 15% minimum is always retained. |
| **Frequency** | 40–200 Hz | 80 Hz | Crossover point. Set just above the fundamental of your lowest note. Bass guitar: 80–100Hz. Kick drum: 60–80Hz. |
| **Intensity** | 0–100% | 50% | Controls how hard the NLD is driven, changing the *character* of the harmonics. Low values give a sparse, subtle series; high values give a richer, more saturated series with stronger high-order harmonics. |
| **Harmonics Mix** | 0–100% | 50% | Blend level of the generated harmonics in the output. Unlike Intensity, this is a simple level control — it does not change harmonic character. |
| **Output Gain** | -24 to +10 dB | 0 dB | Output trim. Applied to the wet signal only — has no effect when bypassed. |
| **Harmonic Tilt** | 0–8 dB/oct | 4 dB/oct | Depth of the high-shelf cut on the harmonic path. Higher values roll off the upper series more steeply. 0 = flat. |
| **Harmonic Style** | — | Full | **Full**: natural harmonic decay, perceptually bounded LP at 4× crossover freq — best for bass guitar and upright bass. **Tight**: LP filtered at 4.5× crossover, focused low-mids — suits kick drum. **Bright**: half-wave rectified blend, extended harmonic series with no upper LP — use sparingly on synth bass. |
| **Bypass** | Bypassed / Active | Active | Engages or disengages the plugin with a ~30ms dry/wet crossfade to prevent clicks. Can be automated via right-click → "Add automation lane for Bypass". When bypassed, the signal passes through unprocessed at unity gain. |

---

## Using PsychoBass

PsychoBass is designed to be set up in a specific order. Working through the controls from top to bottom — rather than adjusting everything at once — makes it much easier to dial in a good result.

### 1. Set the Original Bass level

Start by deciding how much of the original sub-bass you want to retain. On a full-range system, leave **Original Bass** near 100%. On a mix intended mainly for small speakers, reducing it lets the harmonics carry more of the perceived bass weight — the fundamental that small speakers cannot reproduce anyway can be pulled back without losing the sense of bass.

### 2. Set the crossover frequency

The **Frequency** control determines which part of the signal is treated as "bass" and sent to the harmonic generator. Set it just above the fundamental of the lowest note your instrument plays.

For most bass guitar, 80–90Hz is a good starting point. For kick drum, try 60–70Hz. If set too high, the plugin will start generating harmonics from mid-range content and the result will sound cluttered. If set too low, the fundamental may not trigger the harmonic generator consistently.

### 3. Shape the harmonic character with Intensity

Raise **Intensity** until you can clearly hear the harmonics being added. On a small speaker or earbud, this is the point where the bass starts to become audible as a pitched sound rather than a vague low-frequency presence.

Unlike a simple level control, Intensity changes the *character* of the harmonics by controlling how hard the NLD is driven. At low settings the harmonic series is sparse and subtle (mainly 2nd and 3rd harmonics). At higher settings the series becomes richer and more saturated, with stronger high-order content. Going too high makes the result sound electronic — if the harmonics feel synthetic or harsh, back Intensity off.

### 4. Choose a harmonic style

**Full** is the default and suits most sources — it produces a smooth harmonic series that decays naturally above ~2kHz, similar to the character of a well-saturated instrument. **Tight** rolls off sharply above ~600Hz, which works well for kick drum or when you want the enhancement to stay focused in the low-mids without adding upper harmonic brightness. **Bright** extends the series all the way to 10kHz+ and is best used sparingly on synth bass where that additional presence is part of the intended sound.

### 5. Shape the harmonic decay with Harmonic Tilt

Once you have harmonics you like, **Harmonic Tilt** controls how steeply the upper harmonics roll off. At 0 the harmonic series is relatively flat; higher values increasingly favour the lower harmonics (2nd, 3rd, 4th) over the upper ones. This is useful for making the enhancement blend more naturally — most acoustic instruments have a harmonic series that decays with frequency, so a moderate tilt of 3–5dB/oct tends to sound most natural. If the harmonics feel bright or abrasive on playback, increase the tilt before reducing Intensity.

### 6. Set the blend level with Harmonics Mix

**Harmonics Mix** is a simple level fader applied after all NLD processing — it does not change the character of the harmonics (that is Intensity's job), only how prominent they are in the output. Use it to blend the generated harmonics naturally into the track at the character already set by Intensity.

A good workflow is to set Harmonics Mix on the target playback device (phone speaker, earbuds) until the bass feels present, then check back on a full-range system to confirm the overall balance.

### 7. Trim the output level

**Output Gain** is a final level trim applied to the wet signal. Use it to compensate for any overall level change the plugin introduces. It has no effect when the plugin is bypassed, so it is useful for gain-matched A/B comparisons — set it so the bypassed and active signals feel roughly equal in loudness, then the difference you hear is purely the character of the enhancement rather than a level change.

---

## Suggested Starting Points

**Bass guitar (finger style)**
Original Bass: 75% · Frequency: 85Hz · Intensity: 55% · Harmonics Mix: 55% · Tilt: 4dB · Style: Full

**Bass guitar (pick / aggressive)**
Original Bass: 65% · Frequency: 90Hz · Intensity: 60% · Harmonics Mix: 60% · Tilt: 3dB · Style: Full

**Upright / acoustic bass**
Original Bass: 80% · Frequency: 80Hz · Intensity: 45% · Harmonics Mix: 45% · Tilt: 5dB · Style: Full

**Synth bass**
Original Bass: 60% · Frequency: 70Hz · Intensity: 60% · Harmonics Mix: 60% · Tilt: 2dB · Style: Bright

**Kick drum**
Original Bass: 70% · Frequency: 65Hz · Intensity: 50% · Harmonics Mix: 50% · Tilt: 4dB · Style: Tight

---

## How It Works

The signal path has three parallel branches that are summed at the output:

```
Input
  │
  ├─── 4th-order HP crossover ──────────────────────────────────────────┐
  │    (mids/highs above crossover freq, always full level)             │
  │                                                                      │
  ├─── 4th-order LP crossover ── × Original Bass (min 15%) ────────────┤
  │    (sub-bass below crossover freq)                                  │
  │                                                                      │
  └─── Bandpass (4th-order LP at 2×freq + 4th-order HP at 0.5×freq)   │
         │  (isolates fundamental ±1 octave — NLD drive only)           │
         │                                                               │
         ├── Envelope follower (10ms attack / 150ms release / 200ms hold)│
         │                                                               │
         ├── Normalise to NLD drive level                                │
         │                                                               │
         └── Recursive feedback multiplier NLD                           │
               y[n] = (x[n] + dc) × (1 + k × |y[n-1]|)                │
               Soft limiter on feedback state                            │
               │                                                         │
               ├── Harmonic style blend (Full / Tight / Bright)         │
               │                                                         │
               ├── Re-scale by envelope × Intensity                     │
               │                                                         │
               ├── 2nd-order HP at 1× crossover freq                    │
               │   (removes processed fundamental from harmonic path)    │
               │                                                         │
               └── High-shelf spectral tilt at 7× crossover             │
                     │                                                   │
                     └── × Harmonics Mix                                 │
                               │                                         │
                               └──────────── Sum ───────────────────────┘
                                               │
                                          Output Gain
                                               │
                                        Bypass crossfade
                                               │
                                            Output
```

**Bandpass-isolated NLD** is the key to consistent behaviour on complex signals. Rather than feeding a low-pass crossover into the NLD (which would pass multiple harmonics of a chord and generate intermodulation products between them), a narrow bandpass (±1 octave around the crossover frequency) isolates the bass fundamental before the nonlinearity. The NLD then generates harmonics from a single pitched source, keeping the output clean regardless of signal complexity.

**Recursive feedback multiplier NLD** is based on the original MaxxBass architecture (Ben-Tzur & Colloms AES 1999, patent US5930373 expired September 2019). The formula `y[n] = (x[n] + dc) × (1 + k × |y[n-1]|)` is a one-sample recursive loop where each output sample depends on the previous one. This naturally generates a rich harmonic series extending to high frequencies — something memoryless nonlinearities cannot replicate, as they always cut off where their Taylor series saturates. The DC bias `dc` breaks the even symmetry of `|y[n-1]|` so both even and odd harmonics are present; the bias is envelope-scaled to prevent background tones during silence, and the DC itself is removed by the downstream HP filter. A soft limiter on the feedback state prevents runaway on transients. The feedback state is also decayed to zero during bypass so no stale state is present on re-engagement.

**Normalised NLD drive** ensures the NLD always generates a full harmonic series regardless of input level. The bandpass signal is scaled to a fixed drive level before the nonlinearity, then the envelope is multiplied back in after, restoring correct dynamics.

**Correct crossover architecture** separates the output mix into three components: mids/highs (always full level), sub-bass (scaled by Original Bass, with a 15% floor), and generated harmonics (scaled by Harmonics Mix). The Original Bass control affects only the sub-fundamental content below the crossover — the mid and high content passes through regardless.

---

## Technical Reference

The approach is based on the following published research:

- Ben-Tzur, D. & Colloms, M. (1999). *MaxxBass psychoacoustic bass enhancement*. AES 107th Convention.
- Larsen, E. & Aarts, R.M. (2002). *Reproducing low-pitched signals through small loudspeakers*. JAES, 50(3).
- Mu, B., Gan, W.-S. & Tan, E.-L. (2013). *Timbre matching of inharmonic signals for virtual bass system*. ICASSP.
- Oo, Z.Z. & Gan, W.-S. *Analysis of nonlinear devices for virtual bass systems.*

The nonlinear device is a recursive feedback multiplier: `y[n] = (x[n] + dc) × (1 + k × |y[n-1]|)`. The DC bias generates both even and odd harmonics; the recursive structure extends the harmonic series naturally to high frequencies. Parameters are empirically tuned by spectrum comparison against the reference product.

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
