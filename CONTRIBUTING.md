# Contributing to PsychoBass

Thank you for your interest in contributing. Here are some ways you can help.

## Bug Reports

Please open a GitHub issue and include:

- Reaper version
- Sample rate and buffer size
- The instrument / source material you were processing
- What you expected to hear vs what you heard
- If possible, a spectrum analyser screenshot showing the problem

## Suggested Improvements

Before opening a PR for a significant change, please open an issue first to discuss it. This avoids duplicated effort and ensures the change fits the project's goals.

## Areas Where Contributions Are Especially Welcome

- **Better default values** for specific instrument types, based on measured results
- **Spectrum analysis** comparing output to reference psychoacoustic bass tools
- **Alternative NLD topologies** — if you have a design that sounds more natural, please share the research or reasoning behind it
- **Documentation improvements** — clearer explanations, translated READMEs, diagrams

## Code Style

The plugin is written in JSFX (EEL2). Please keep the same conventions as the existing code:

- Comment each processing stage clearly
- Explain the *why* of DSP decisions, not just the what
- Keep filter coefficient calculations in `@block` (not `@sample`)
- Test at both 44.1kHz and 48kHz before submitting

## Licence

By contributing, you agree that your contributions will be licensed under the same MIT licence as the project.

Steve Cox 2026