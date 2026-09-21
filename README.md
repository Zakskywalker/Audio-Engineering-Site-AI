# Acoustic to Algorithmic: Music Theory & Audio DSP Compendium

<p align="left">
  <a href="https://deepmind.google/technologies/gemini/">
    <img src="https://img.shields.io/badge/Generated%20with-Google%20Gemini%20Pro-4285F4?style=for-the-badge&logo=google&logoColor=white&labelColor=1a1a2e" alt="Generated with Google Gemini Pro" />
  </a>
</p>

> <sub>⚡ **Origin Note:** The initial architecture, interactive Web Audio DSP engine, and documentation for this project were generated with **Google Gemini Pro**.</sub>

---

A production-grade, dark-themed interactive guide and algorithmic audio reference bridging music theory across piano, synthesizers, and guitar with digital signal processing (DSP) implementations in Python and C# (.NET).

## Table of Contents

* [Overview](#overview)

* [Architecture & Feature Set](#architecture--feature-set)

* [Acoustic & Theoretical Core](#acoustic--theoretical-core)

  * [Instrument Triad Matrix](#instrument-triad-matrix)

  * [Pitch Mechanics & Enharmonics](#pitch-mechanics--enharmonics)

  * [Curriculum Spectrum](#curriculum-spectrum)

* [Digital Signal Processing & Math](#digital-signal-processing--math)

  * [Biquad Resonant Lowpass Filter](#biquad-resonant-lowpass-filter)

  * [808 Drum Synthesis Mathematics](#808-drum-synthesis-mathematics)

  * [Lookahead Limiter & Peak Detection](#lookahead-limiter--peak-detection)

  * [Non-Linear Wavefolding Function](#non-linear-wavefolding-function)

  * [Euclidean Rhythm Distribution](#euclidean-rhythm-distribution)

* [Software Implementations (Python & C#)](#software-implementations-python--c)

* [DAWs, Hardware & Production Tools](#daws-hardware--production-tools)

* [Frequency & MIDI Specification](#frequency--midi-specification)

* [Deployment & Usage](#deployment--usage)

* [Dependencies & References](#dependencies--references)

* [License](#license)

## Overview

Modern audio production requires moving effortlessly between classical chord structures, acoustic fretboards, analog voltage-controlled circuits, and software DSP code. This compendium is designed as an interactive single-page application (`index.html`) accompanied by production algorithms that handle:

1. **Interactive Synthesis:** Embedded Web Audio API oscillator bank and drum synthesis engine with real-time biquad filtering.

2. **Pedagogical Theory:** Step-by-step progress from chromatic intervals and ledger line reading to tritone substitutions, negative harmony, and polyrhythms.

3. **Engineering Pipelines:** Standalone, dependency-isolated algorithms in both **Python** and **C#** covering subtractive synthesis, drum sequencing, stem alignment/mashups, and brickwall mastering limiters.

## Architecture & Feature Set

* **Single-File Zero-Dependency UI:** Styled with Tailwind CSS, custom scrollbars, glowing status indicators, and mobile-friendly drawer navigation.

* **Client-Side Web Audio Engine:**

  * Standard waveforms: *Sine*, *Sawtooth*, *Square*, *Triangle*.

  * Dynamic Bi-quad low-pass filter slider ($200\text{ Hz} - 8000\text{ Hz}$, $Q = 2.5$).

  * Sample-free mathematical drum triggers for 808 Kick, 909 Snare, Metallic Closed Hi-Hat, and 32 Hz Sub Bass.

* **Tabbed Code Viewports:** Instant language toggling between Python (`NumPy`, `SciPy`, `Librosa`) and C# (`System.MathF`, `NAudio`, `CSCore`).

* **Interactive Copy-to-Clipboard:** Inline code blocks configured for direct extraction.

## Acoustic & Theoretical Core

### Instrument Triad Matrix

| Feature | Acoustic Piano | Synthesizer (Analog/Digital) | Electric / Acoustic Guitar | 
 | ----- | ----- | ----- | ----- | 
| **Coordinate Geometry** | Linear 1D ($A_0 \to C_8$) | Timbral / Frequency Domain | 2D Matrix (Strings $\times$ Frets) | 
| **Pitch Flexibility** | Fixed (12-TET, static strings) | Continuous glide / Portamento | Bending ($\pm 200\text{ cents}$), Vibrato | 
| **Voicing Character** | Close tertian voicings, wide spread | Single-note leads to unison supersaws | Drop-2, Drop-3, open quartal shapes | 
| **Dynamic Range** | Velocity-driven hammer force | Envelope Generators (ADSR), VCA | String attack velocity, pick angle | 

### Pitch Mechanics & Enharmonics

* **Enharmonic Spelling Rules:** Diatonic systems mandate each scale degree have a distinct letter name. For instance, in the key of $C\sharp$ Major ($C\sharp, D\sharp, E\sharp, F\sharp, G\sharp, A\sharp, B\sharp$), $E\sharp$ cannot be written as $F$ without duplicating scale degrees and breaking sheet music syntax.

* **Double Accidentals:**

  * Double Sharp ($\boldsymbol{\mathcal{x}}$ / $\sharp\sharp$): $+2\text{ semitones}$. Common in harmonic minor leading tones (e.g., $G\sharp\text{ minor} \to F\boldsymbol{\mathcal{x}}$).

  * Double Flat ($\flat\flat$): $-2\text{ semitones}$. Common in fully diminished seventh chords (e.g., $C^\circ 7 \to B\flat\flat$).

* **Microtonal & Neutral Intervals:** Blues notes bend between the minor third and major third (\~50 cents), creating non-12-TET intervals replicated via synth pitch-wheels or guitar string bending.

### Curriculum Spectrum

```
Tier 1: Atomic Foundations (Weeks 1-6)
 ├── 12 Chromatic Intervals & Half/Whole Steps
 ├── Major Scale Formula (W-W-H-W-W-W-H) & Natural/Harmonic Minor
 └── Grand Staff Reading (Treble G4, Bass F3, Tenor/Alto C4)

Tier 2: Applied Harmonic Systems (Weeks 7-14)
 ├── The 7 Diatonic Modes (Ionian, Dorian, Phrygian, Lydian, Mixolydian, Aeolian, Locrian)
 ├── Extended Four-Note Chords (Maj7, Min7, Dom7, Half-Diminished m7b5)
 └── Modulation Mechanics, Pivot Chords & Circle of Fifths

Tier 3: Advanced Modern Composition
 ├── Tritone Substitution (Exploiting identical 3rd/7th tritone axes)
 ├── Polyrhythmic Phase Timing (3:2 Hemiola, 4:3 Metric Counterpoint)
 └── Negative Harmony Polarity Mapping

```

## Digital Signal Processing & Math

### Biquad Resonant Lowpass Filter

Derived from Robert Bristow-Johnson’s Audio EQ Cookbook:

$$
\omega_0 = 2\pi \frac{f_c}{f_s}, \quad \alpha = \frac{\sin(\omega_0)}{2Q}
$$

$$
b_0 = \frac{1 - \cos(\omega_0)}{2}, \quad b_1 = 1 - \cos(\omega_0), \quad b_2 = \frac{1 - \cos(\omega_0)}{2}
$$

$$
a_0 = 1 + \alpha, \quad a_1 = -2\cos(\omega_0), \quad a_2 = 1 - \alpha
$$

Difference equation implemented per sample:

$$
y[n] = \frac{b_0}{a_0}x[n] + \frac{b_1}{a_0}x[n-1] + \frac{b_2}{a_0}x[n-2] - \frac{a_1}{a_0}y[n-1] - \frac{a_2}{a_0}y[n-2]
$$

### 808 Drum Synthesis Mathematics

A resonant T-bridge circuit oscillator kick is modeled via an exponential frequency descent coupled with an exponential decay envelope:

$$
f(t) = f_{\text{end}} + (f_{\text{start}} - f_{\text{end}}) \cdot e^{-k_f t}
$$

$$
\phi(t) = 2\pi \int_0^t f(\tau) d\tau = 2\pi \left( f_{\text{end}} t + \frac{f_{\text{start}} - f_{\text{end}}}{k_f}(1 - e^{-k_f t}) \right)
$$

$$
A(t) = e^{-k_a t}
$$

$$
s(t) = A(t) \cdot \sin(\phi(t))
$$

Where:

* $f_{\text{start}} = 150\text{ Hz}, \quad f_{\text{end}} = 42\text{ Hz}$

* Pitch decay factor $k_f \approx 25.0$

* Amplitude decay factor $k_a \approx 9.0$

### Lookahead Limiter & Peak Detection

Envelope tracking with decoupled attack and release coefficients:

$$
\alpha_{\text{att}} = \exp\left(-\frac{1}{f_s \cdot \tau_{\text{att}}}\right), \quad \alpha_{\text{rel}} = \exp\left(-\frac{1}{f_s \cdot \tau_{\text{rel}}}\right)
$$

$$
\text{env}[n] = \begin{cases}  \vert{}x[n]\vert{} + \alpha_{\text{att}} \cdot (\text{env}[n-1] - \vert{}x[n]\vert{}), & \text{if } \vert{}x[n]\vert{} > \text{env}[n-1] \\  \vert{}x[n]\vert{} + \alpha_{\text{rel}} \cdot (\text{env}[n-1] - \vert{}x[n]\vert{}), & \text{if } \vert{}x[n]\vert{} \le \text{env}[n-1]  \end{cases}
$$

Gain reduction applied with a lookahead buffer delay ($D \approx 5\text{ ms}$):

$$
g[n] = \begin{cases}  1.0, & \text{if } \text{env}[n] \le \text{Threshold} \\  \frac{\text{Threshold}}{\text{env}[n]}, & \text{if } \text{env}[n] > \text{Threshold}  \end{cases}
$$

$$
y[n] = x[n - D] \cdot g[n]
$$

### Non-Linear Wavefolding Function

West Coast / Buchla folding function wrapping overdriven waveforms back into range:

$$
f(x) = \left\vert{} \left( (x + 1) \bmod 4 \right) - 2 \right\vert{} - 1
$$

### Euclidean Rhythm Distribution

Given $k$ pulses across $n$ time subdivisions, pulses are distributed via Bjorklund’s algorithm (equivalent to the Euclidean greatest common divisor step):

$$
\text{hit}(i) = \left\lfloor \frac{i \cdot k}{n} \right\rfloor \neq \left\lfloor \frac{(i - 1) \cdot k}{n} \right\rfloor \quad \text{for } i \in \{0, 1, \dots, n-1\}
$$

* $E(3, 8) \to [1, 0, 0, 1, 0, 0, 1, 0]$ (Tresillo)

* $E(5, 8) \to [1, 0, 1, 1, 0, 1, 1, 0]$ (Cinquillo)

* $E(7, 12) \to [1, 0, 1, 0, 1, 1, 0, 1, 0, 1, 0, 1]$ (West African Bell)

## Software Implementations (Python & C#)

The repository provides four primary processing engines implemented symmetrically in both Python and C#:

1. **`SubtractiveSynth`**: Bandlimited waveform generator (Sawtooth, Square) with Direct Form I Resonant Biquad lowpass filtering.

2. **`DrumSequencer`**: Procedural procedural 808 kick, noise-burst 909 snare, highpass metallic hat generator, and 16-step grid renderer.

3. **`MashupEngine`**: Spectral tempo extraction, Phase-vocoder based time-stretching, chromatic pitch shifting, and soft-saturation ($\tanh$) stem mixing.

4. **`MasteringDSP`**: DC-offset filter, dynamic gain stage, peak detector, and brickwall lookahead peak limiter.

## DAWs, Hardware & Production Tools

* **DAWs:**

  * [FL Studio](https://www.image-line.com/fl-studio/?utm_source=gemini): High-speed step sequencing, microtonal piano roll slides, Sytrus/Harmor engines.

  * [Ableton Live](https://www.ableton.com/?utm_source=gemini): Real-time audio warping (Complex Pro), session view clip sequencing, Max for Live (M4L) visual programming.

  * [Reaper](https://www.reaper.fm/?utm_source=gemini): Lightweight digital workstation with native JSFX scriptable audio DSP.

* **Hardware Synthesizers:**

  * **Korg Minilogue / Monologue:** 100% analog VCO/VCF architecture with built-in OLED oscilloscope and microtuning support.

  * **Korg MS-20:** Semi-modular dual-filter patching architecture capable of self-oscillation.

  * **Roland TR-808 / TR-909:** Classic analog/hybrid transistor-based rhythm composers.

* **Modern Software Synths:**

  * [Vital](https://vital.audio/?utm_source=gemini): Spectral warping wavetable synthesizer.

  * [Surge XT](https://surge-synthesizer.github.io/?utm_source=gemini): Open-source hybrid synthesizer.

## Frequency & MIDI Specification

Reference tuning: $A_4 = 440.00\text{ Hz}$. Formula: $f = 440 \cdot 2^{\frac{d - 69}{12}}$

| Note | MIDI Number | Frequency ($Hz$) | Wavelength ($\lambda$ in air) | Guitar Register Reference | 
 | ----- | ----- | ----- | ----- | ----- | 
| $E_1$ | 28 | 41.20 | 832.5 cm | Low string on standard 4-string Bass | 
| $E_2$ | 40 | 82.41 | 416.2 cm | String 6 (Open) | 
| $A_2$ | 45 | 110.00 | 311.8 cm | String 5 (Open) | 
| $D_3$ | 50 | 146.83 | 233.6 cm | String 4 (Open) | 
| $G_3$ | 55 | 196.00 | 175.0 cm | String 3 (Open) | 
| $B_3$ | 59 | 246.94 | 138.9 cm | String 2 (Open) | 
| $C_4$ | 60 | 261.63 | 131.1 cm | Middle C (String 2, Fret 1) | 
| $E_4$ | 64 | 329.63 | 104.1 cm | String 1 (Open) | 
| $A_4$ | 69 | 440.00 | 77.9 cm | Concert Pitch (String 1, Fret 5) | 
| $C_5$ | 72 | 523.25 | 65.5 cm | Soprano Register (String 1, Fret 8) | 
| $E_5$ | 76 | 659.25 | 52.0 cm | String 1, Fret 12 (Octave Node) | 

## Deployment & Usage

### 1. Running the Interactive Interface

No build tools, bundlers, or Node.js runtime required. Open the HTML file in any modern evergreen browser:

```
# Clone the repository
git clone https://github.com/your-username/music-theory-audio-dsp.git
cd music-theory-audio-dsp

# Open in browser (macOS / Linux / Windows)
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows

```

Click **"Arm WebAudio"** in the top-left corner of the sidebar to enable dynamic audio playback.

### 2. Running Python DSP Scripts

Ensure Python 3.9+ is installed along with numeric signal libraries:

```
pip install numpy scipy sounddevice librosa
python dsp_synth_example.py

```

### 3. Running C# Audio Implementations

Compatible with .NET 6.0, 7.0, and 8.0:

```
dotnet new console -n AudioDSPTest
cd AudioDSPTest
dotnet add package NAudio
# Paste C# snippet into Program.cs
dotnet run

```

## Dependencies & References

### Academic Texts & Manuals

* **The Jazz Theory Book** - *Mark Levine* (Exhaustive modal and reharmonization reference).

* **Designing Sound** - *Andy Farnell* (Procedural audio synthesis and pure-math physical modeling).

* **The Computer Music Tutorial** - *Curtis Roads* (The definitive volume on digital audio algorithms).

* **The Audio EQ Cookbook** - *Robert Bristow-Johnson* (Standard biquad filter formulas).

### Libraries & Frameworks

* [Librosa](https://librosa.org/?utm_source=gemini) - Audio and music analysis in Python.

* [Sounddevice](https://python-sounddevice.readthedocs.io/?utm_source=gemini) - PortAudio bindings for Python.

* [NAudio](https://github.com/naudio/NAudio?utm_source=gemini) - Audio and MIDI library for .NET.

* [CSCore](https://github.com/filoe/cscore?utm_source=gemini) - Advanced .NET audio processing library.

* [Mido](https://mido.readthedocs.io/?utm_source=gemini) - MIDI Objects for Python.

## License

Distributed under the MIT License. See `LICENSE` for details.