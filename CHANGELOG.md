# Changelog

All notable changes to PsychoBass will be documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [0.4.12] - 2026-03-14

### Fixed
- **Feedback state not reset on bypass**: `fmL`/`fmR` could hold a stale value
  from a loud transient if the plugin was bypassed mid-signal, causing an audible
  artifact on re-engagement. Fixed by multiplying `fmL`/`fmR` by `bypass_gain`
  each sample — the state drains to zero as the plugin fades out (over the same
  30ms crossfade window) and rebuilds naturally from zero as it fades back in.

## [0.4.11] - 2026-03-14

### Changed
- **NLD parameters updated**: `nld_drive=1.8`, `dc=0.78`, `k=0.28`, `harm_cal=5.0`
  — empirically tuned by listening and spectrum comparison.
- **Header comments cleaned up**: removed all superseded NLD iteration history
  (v0.3.2–v0.4.10 intermediate steps). Replaced with two concise architecture
  summaries covering the signal path and NLD design rationale.

## [0.4.10] - 2026-03-14

### Fixed
- **Hard clip on feedback state distorted harmonic structure**: v0.4.9 tightened
  the safety clip to ±2.0, but with `drive=0.8` and `dc=0.29` the feedback state
  regularly exceeds ±2.0 during normal steady-state operation. The hard clip was
  engaging every cycle, saturating the recursive term and boosting high-order
  harmonics while suppressing lower ones. Replaced with a soft limiter
  `y/(1+|y|/4)` which approaches ±4 asymptotically and is smooth everywhere.

## [0.4.9] - 2026-03-14

### Changed
- **Envelope-scaled DC bias**: `dc` is now multiplied by `min(safe_env/0.1, 1.0)`,
  ramping from zero at silence to full value once the envelope exceeds -20dBFS.
  Prevents the feedback loop from generating background harmonics during silence.
- **Safety clip tightened ±4.0 → ±2.0** (superseded by soft limiter in v0.4.10).

## [0.4.8] - 2026-03-14

### Changed
- **NLD parameters tuned**: `nld_drive=0.8`, `dc=0.29`, `k=0.6` by spectrum
  comparison against reference product using a 50Hz sine.
- **Tilt shelf raised from 5× to 7× crossover freq** (560Hz at 80Hz): the 7th
  and 9th harmonics of a 50Hz source (350Hz, 450Hz) were sitting in the shelf
  rolloff and being attenuated at 5×.

## [0.4.7] - 2026-03-14

### Fixed
- **Even harmonics missing from feedback multiplier**: `y[n] = x[n]*(1+k*|y[n-1]|)`
  uses `|y[n-1]|` which is even-symmetric, generating only odd harmonics (same
  problem as the earlier `x|x|` polynomial term). Fixed by adding a DC bias to
  the input: `y[n] = (x[n]+dc)*(1+k*|y[n-1]|)`. The bias breaks symmetry so
  both even and odd harmonics are generated. DC is removed by the harmonic HP.

## [0.4.6] - 2026-03-14

### Changed
- **NLD replaced with recursive feedback multiplier** — the original MaxxBass
  architecture (patent US5930373, expired September 2019; confirmed in
  Ben-Tzur & Colloms AES 1999 and Larsen & Aarts JAES 2002).
  `y[n] = x[n] * (1 + k * |y[n-1]|)`. All previous NLD implementations
  (polynomial, tanh) were memoryless and could not produce harmonics extending
  to high frequencies. The recursive structure naturally generates a rich
  harmonic series to 10kHz+, matching the reference product's behaviour.

## [0.4.0–0.4.5] - 2026-03-14

### Changed (NLD exploration — superseded by v0.4.6)
- These versions explored asymmetric polynomial and tanh-based NLDs, addressing
  issues with missing even harmonics, clipping noise floors, and harmonic series
  cutoff. All were superseded when the original feedback multiplier architecture
  was identified from the published research and implemented in v0.4.6.

## [0.3.8–0.3.9] - 2026-03-14

### Changed
- **Harmonic LP moved to Tight mode only**: it had been applied globally based on
  complex test signal observations, but a clean 50Hz sine comparison showed the
  reference product extends harmonics clearly to ~2kHz in Full mode. Full and
  Bright are now LP-free; Tight retains the 8th-order LP at 4× freq as its
  focused low-mid character is intentional.
- **Original Bass default reduced 70% → 50%**: at 100% the sub-bass fundamental
  was too prominent relative to the reference product on the test signal.
- Various `harm_cal` and `nld_drive` calibration adjustments from clean sine
  comparison (intermediate values not individually recorded).

## [0.3.7] - 2026-03-14

### Reverted
- Attempted to strengthen the 90Hz harmonic by lowering the bandpass HP from
  0.5× to 0.3× freq and raising `k3`. This was based on the incorrect assumption
  that 90Hz needed to be NLD-generated. The test signal (saw at 30Hz + triangle
  at 60Hz) already contains 90Hz as the 3rd harmonic of the saw — it passes
  through the output HP crossover without NLD involvement. Lowering the bandpass
  HP also re-introduced IMD between the 30Hz and 60Hz components. Both changes
  reverted; bandpass HP remains at 0.5×.

## [0.3.6] - 2026-03-13

### Changed
- **NLD x² coefficient raised 0.8 → 1.4**: spectrum comparison showed the 2nd
  harmonic consistently ~7dB below the reference product. The `x²` term in the
  asymmetric polynomial NLD is responsible for 2nd harmonic generation; increasing
  its coefficient directly raises the 2nd harmonic relative to the odd-order series
  without affecting the 3rd and 5th harmonics from the `x|x|` term.
- **Harmonic LP upgraded to 8th-order at 4× freq**: previous 6th-order at 5×
  (400Hz cutoff) was giving only -12dB at 500Hz — not steep enough to match the
  reference product's rolloff. 8th-order at 4× (320Hz at 80Hz) gives -48dB/oct:
  at 640Hz content is ~-48dB, near noise floor by ~600Hz. Uses filter slots 22–29.

## [0.3.5] - 2026-03-13

### Changed
- **Harmonic LP upgraded to 6th-order at 5× freq**: 4th-order at 6× (400Hz cutoff)
  still too gentle (-12dB at 500Hz). 6th-order at 5× (400Hz) gives -36dB/oct.
  Uses filter slots 22–27.
- **harm_cal bumped 3.0 → 3.5**: spectrum comparison showed low-order harmonics
  still ~4dB under the reference product.

## [0.3.4] - 2026-03-13

### Changed
- **Harmonic LP upgraded to 4th-order at 6× freq** (~480Hz at 80Hz, -24dB/oct):
  2nd-order at 10× was too gentle. Uses filter slots 22–25.

## [0.3.3] - 2026-03-13

### Added
- **Harmonic LP at 10× crossover freq (2nd-order)**: removes high-order harmonics
  above the perceptually useful range for the missing fundamental effect (~100–1000Hz).
  Harmonics above this add noise and brightness without psychoacoustic benefit.
  Uses filter slots 22–23.

### Changed
- **harm_cal bumped 2.5 → 3.0**: low-order harmonics still ~3dB under reference.

## [0.3.2] - 2026-03-13

### Fixed
- **NLD generated only odd harmonics**: the previous symmetric NLD `x·(1+k|x|)`
  is an odd function and produces only the 3rd, 5th, 7th... harmonics. The 2nd
  harmonic (and all even-order harmonics) was missing entirely. Replaced with an
  asymmetric polynomial `x + k₂x² + k₃x|x|`: the `x²` even-function term
  generates the 2nd harmonic (DC component blocked by the downstream HP), and
  `x|x|` generates the 3rd and 5th. This matches the even+odd character visible
  in spectrum analysis of the reference product.
- **Harmonic HP was cutting the 2nd harmonic**: set at 2× crossover freq (160Hz
  at 80Hz), it was attenuating the 2nd harmonic of complex signals (e.g. the
  60Hz component's 2nd harmonic at 120Hz). Moved to 1× crossover freq (80Hz) so
  the 2nd harmonic at 120Hz passes cleanly while the processed fundamental at
  60Hz is still removed from the harmonic path.

### Changed
- **NLD drive level reduced 0.5 → 0.35**: softer saturation concentrates energy
  in the 2nd and 3rd harmonics rather than spreading into higher-order harmonics.
- **harm_cal calibration constant added (×2.5)**: internal gain applied after the
  NLD to close the ~10dB level gap identified from spectrum comparison. Replaces
  previous ad-hoc level matching.
- **Sub-bass bleed floor of 15%**: `orig_bass_eff = max(orig_bass, 0.15)` — even
  at 0% Original Bass a small amount of sub-bass is retained, matching the
  reference product's behaviour which does not completely cut the sub at minimum.
- **Spectral tilt shelf lowered from 3× to 2× crossover freq**: starts rolling
  off upper harmonics earlier, concentrating the generated series in the 2nd–4th
  harmonic range and giving a faster decay shape.

## [0.3.1] - 2026-03-13

### Fixed
- **orig_bass was scaling the full dry signal**: in v0.3.0, orig_bass multiplied
  the entire input signal, causing the original harmonics of the source (60Hz,
  120Hz etc.) to overlap and interact with the generated harmonics. The correct
  behaviour (matching the reference product) is that orig_bass controls only the
  sub-bass below the crossover frequency, not the full signal.
- **Restored output crossover**: a separate 4th-order LP/HP crossover now splits
  the signal for the output mix. The HP output (mids/highs above the crossover)
  always passes through at full level. The LP output (sub-bass below the crossover)
  is scaled by orig_bass. Generated harmonics are added on top via harm_mix.
  The detection bandpass is unchanged and still used only to drive the NLD.
- **orig_bass default restored to 70%**.

## [0.3.0] - 2026-03-13

### Changed (major architectural revision — complex signal behaviour)

- **Bandpass detection path**: the NLD is now fed from a narrow bandpass filter
  (~±1 octave around the crossover frequency) rather than a simple LP crossover.
  On complex signals (bass guitar, kick drum) the LP was passing multiple harmonics
  into the NLD, which generated intermodulation products between them — visible as
  an irregular, notched harmonic series and audible as an electronic, cluttered
  character. The bandpass isolates the fundamental so the NLD generates a clean
  series from a single pitched source regardless of signal complexity.

- **Dry signal passthrough**: the original signal now passes through completely
  untouched. Generated harmonics are added on top (additively), rather than
  being mixed with a crossover-split bass band. This preserves the full-range
  character of the source.

- **Compander removed**: the harmonic compander was suppressing the effect of
  the Intensity slider by tracking and re-scaling the envelope regardless of the
  slider position. Intensity is now a clean linear gain applied directly to the
  NLD output, giving the expected full range of control (~35dB swing from 0–100%).

- **Spectral tilt simplified**: the two-shelf cascade (low-shelf boost + high-shelf
  cut) is replaced by a single high-shelf cut above 3× crossover. Spectrum
  analysis of the alternative product on complex signals shows a simple rolloff,
  not a boost/cut pair. The low-shelf boost was adding colouration not present
  in the reference.

- **Harmonic HP moved to 2× crossover** (was 1.5×): better clears the fundamental
  from the harmonic path and reduces the chance of the NLD output reinforcing or
  cancelling the fundamental in the dry signal.

- **Original Bass default changed to 100%** (was 70%): now correctly reflects that
  the dry signal passes through at full level by default, with harmonics added
  on top.

## [0.2.9] - 2026-03-12

### Fixed
- **Output Gain applied during bypass**: `out_gain` was multiplied after the
  dry/wet crossfade, so it scaled the dry signal too. Moved inside the wet
  path so the bypassed signal always passes at unity gain.

## [0.2.8] - 2026-03-12

### Added
- **Bypass slider** (slider8, default: Active): an automatable in-plugin bypass
  with a ~30ms dry/wet crossfade, giving click-free enable/disable that works
  reliably regardless of Reaper's host bypass mechanism. Set to **Bypassed** to
  pass the signal through unprocessed; set to **Active** to engage the plugin.
  Can be automated on any track for dynamic bypass during a project.

## [0.2.7] - 2026-03-12

### Fixed
- **Loud thump on unmute / after silence**: the envelope follower was decaying
  to near-zero during silence, causing the pre-gain normalisation to amplify
  the first incoming samples by up to 5000x before the soft-clip could limit
  them. Fixed by:
  1. Raising the `safe_env` floor from `0.0001` to `0.01` (-40dBFS), limiting
     maximum pre-gain to 50x rather than 5000x
  2. Adding a 200ms hold stage to the envelope follower so it does not begin
     releasing until signal has been absent for 200ms — prevents the floor
     being reached during normal gaps between notes
  3. Initialising the compander state to the floor value rather than `1.0`,
     preventing a gain spike on first audio after plugin load or silence
- **Click on plugin enable/disable**: implemented soft bypass via Reaper's
  `bypass` variable with a ~10ms crossfade (superseded in v0.2.8 by the
  dedicated Bypass slider, which is more reliable across Reaper configurations)

## [0.2.6] - 2026-03-11

### Fixed
- **Pandoc added to reapack-index** to fix RTF warnings

## [0.2.5] - 2026-03-10

### Changed
- **Minor Changes** to comments and descriptions

## [0.2.4] - 2026-03-10

### Fixed
- **Harmonic Style labels corrected** based on spectrum analysis of all three
  modes. Labels now reflect actual measured behaviour:
  - Style 1: **Full** (smooth decay to ~2kHz, best alternative product match) — was "Warm"
  - Style 2: **Tight** (LP filtered, drops off ~600Hz, focused low-mids) — was "Full"
  - Style 3: **Bright** (raw NLD, extends to 10kHz+) — was "Tight"

## [0.2.3] - 2026-03-10

### Fixed
- **Warm and Tight processing paths swapped again**: spectrum analysis confirmed
  the rectified blend (extended series) matches the desired Warm character and
  the LP-filtered path (shorter, focused series) matches Tight. Paths now
  correctly assigned to their labels.

## [0.2.2] - 2026-03-10

### Fixed
- **Warm and Tight modes were behaviourally swapped**: the asymmetric blend
  term added after the Warm LP filter was re-introducing high harmonics,
  making Warm sound extended and Tight sound rounded — the opposite of their
  labels. Warm now applies the LP filter only, with nothing added back
  afterwards. Tight uses a clean 50/50 NLD/rectified blend with no LP.

## [0.2.1] - 2026-03-10

### Changed
- **Output Gain default changed to 0dB** (was -6dB)
- **Warm mode now spectrally distinct from Tight**: Warm applies a 2nd-order
  low-pass at 4.5× the crossover frequency before blending, physically
  suppressing the 5th harmonic and above so the 2nd and 3rd harmonics are
  clearly dominant. Previously Warm and Tight sounded nearly identical.
- Warm mode LP filter slot added (indices 14,15 in biquad memory)

## [0.2.0] - 2026-03-10

### Changed (informed by spectrum analysis against alternative product reference)

- **NLD pre-gain**: signal is now normalised to a consistent drive level before the nonlinearity, so harmonics activate properly at typical mix levels (-18 to -6 dBFS). Previously the NLD was too lightly driven at moderate input levels.
- **Compander redesign**: the compander no longer normalises harmonics *down* to match the envelope level (which was suppressing output by ~15-18dB). It now acts as a smoothed timbre-follower — harmonics are scaled *up* by the envelope after the NLD, preserving dynamic character without killing level.
- **Spectral shaping replaced**: the single high-shelf tilt is replaced by a two-shelf cascade: a gentle +3dB low-shelf boost in the 2nd–4th harmonic peak region, followed by a high-shelf cut above 6× crossover. This matches the alternative's harmonic envelope shape (peaks at ~4th harmonic, then smooth -9dB/oct decay).
- **Harmonic HP reduced to 2nd order** (was 4th): gentler slope preserves more energy in the 2nd harmonic which sits just above the cutoff frequency.
- **All styles use the same NLD base**: spectrum analysis showed Full mode was closest to the alternative product in output level. All three styles now start from the same NLD and blend in character differences after it.
- **Default values updated**: Intensity 60% (was 50%), Harmonics Mix 65% (was 50%), Harmonic Style defaults to Warm, Tilt range extended to 8dB/oct.
- **NLD coefficient raised**: feedback multiplier k increased from 0.7 to 1.2 for richer harmonic series.

## [0.1.0] - 2026-03-09

### Initial release

- Feedback-multiplier NLD with exponential soft-clip
- 4th-order Butterworth crossover (LP + HP)
- Timbre-matched envelope compander (10ms attack / 150ms release)
- Harmonic high-pass at 1.5× crossover frequency (eliminates phase-cancellation comb notches)
- Spectral tilt high-shelf cut on harmonic path (0–6 dB/oct)
- Three harmonic styles: Warm, Tight, Full
- Output gain range: -24dB to +10dB
- Stereo processing throughout
- All filter coefficients recalculated on parameter change only (efficient @block update)
