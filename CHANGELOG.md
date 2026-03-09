# Changelog

All notable changes to PsychoBass will be documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.0.1] - 2026

### Changed

- Dates and author references

## [1.0.0] - 2026

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
