# Changelog

All notable changes to PsychoBass will be documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

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
