# Æther — Theremin DAW

A browser-based digital audio workstation built exclusively for the theremin. Æther synthesises theremin sound in real time using the Web Audio API and provides two distinct playing interfaces — a freeform mouse/trackpad surface and a keyboard layout — along with recording, WAV export, and a full suite of sound-shaping controls.

No installation, no dependencies. Open the HTML file in any modern browser and play.

---

## Quick Start

1. Open `theremin-daw.html` in Chrome, Firefox, Safari, or Edge.
2. Click anywhere on the playing surface to start producing sound (the browser requires a user gesture before audio can begin).
3. Record your performance, save it as a `.wav` file, and iterate.

---

## Playing Modes

### Mouse / Trackpad Mode

The default interface. A blank surface where cursor position controls the theremin:

- **X-axis (horizontal)** — Pitch. Left is low, right is high.
- **Y-axis (vertical)** — Volume. Top is quiet, bottom is loud.
- **Click and hold** to produce sound. Release to stop.

A custom cursor with a glow ring follows your movement, and an optional particle trail visualises your path. A frequency/note readout appears at the top of the surface while playing.

This mode is designed to mirror the gestural, spatial nature of a real theremin — small hand movements translate directly into continuous pitch and volume changes.

### Keyboard Mode

A two-octave piano-style keyboard rendered on screen. Notes can be triggered with mouse clicks on the keys or with the computer keyboard:

| Row | Keys | Notes |
|---|---|---|
| Home row | `A S D F G H J K L ;` | White keys (C through E, two octaves) |
| Upper row | `W E T Y U O P` | Black keys (sharps/flats) |

- **Z** / **X** — Shift octave down / up.
- Octave buttons (2–6) in the top bar select the base octave directly.
- A volume slider below the keyboard controls output level.

The active note and its frequency are displayed above the keyboard.

---

## Recording and Export

1. Press **R** or click the record button (red circle) to begin recording.
2. Play the theremin using either mode. A running timer shows elapsed time.
3. Press **Space** or click the stop button to end the recording.
4. The track appears in the **Tracks** panel (open it with the list icon in the top-right corner).

Each recorded track provides three actions:

- **Play** — Listen back through the browser. Click again to stop playback.
- **Save** — Downloads the track as a standard 16-bit PCM `.wav` file.
- **Delete** — Removes the track from the session.

Recordings capture the full output chain including all effects (reverb, delay, chorus).

---

## Settings

Open the settings panel with the gear icon in the top-right corner. All changes apply in real time.

### Presets

Six built-in presets configure the oscillator, effects, and portamento in one click:

| Preset | Character |
|---|---|
| **Classic** | Clean sine wave, light vibrato |
| **Warm** | Triangle wave, deeper vibrato, moderate reverb |
| **Bright** | Sawtooth wave, fast vibrato, tight response |
| **Dark** | Triangle wave, heavy reverb, delay, slow glide |
| **Alien** | Square wave, extreme vibrato, long delay, chorus |
| **Ethereal** | Sine wave, deep reverb, long delay, lush chorus |

### Oscillator

- **Waveform** — Sine, triangle, sawtooth, or square. Determines the tonal character.
- **Frequency Range Min / Max** — Sets the pitch boundaries of the playing surface (mouse mode). Default is 65–2000 Hz.

### Vibrato

- **Rate** — Speed of the vibrato oscillation (0–20 Hz).
- **Depth** — How far the pitch wobbles from centre (0–30).

### Portamento

- **Glide Speed** — Time it takes for pitch to slide from one note to the next (0–0.3 seconds). Higher values produce the smooth, swooping transitions characteristic of theremin playing.

### Effects

- **Reverb** — Wet/dry mix for a convolution reverb (0–100%).
- **Delay Time** — Echo delay in milliseconds (0–1000 ms).
- **Delay Feedback** — How much of the delayed signal feeds back into itself (0–90%).
- **Chorus Depth** — Adds a modulated doubling effect for width (0–100%).

### Master

- **Master Volume** — Overall output level (0–100%).
- **Quantize to Scale** — Snaps pitch to the nearest note in a chosen scale: None (free pitch), Chromatic, Major, Natural Minor, Pentatonic, Blues, or Whole Tone.
- **Root Note** — Sets the tonal centre for scale quantization (C through B).
- **Mouse Trail** — Toggles the particle trail effect in mouse mode.
- **Visualizer** — Display mode for the scope at the bottom: Waveform, Frequency spectrum, or Both.

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| `R` | Toggle recording on/off |
| `Space` | Stop recording and all playback |
| `Tab` | Switch between mouse and keyboard mode |
| `?` | Toggle help overlay |
| `Z` | Octave down (keyboard mode) |
| `X` | Octave up (keyboard mode) |
| `A`–`;` | Play white keys (keyboard mode) |
| `W E T Y U O P` | Play black keys (keyboard mode) |

---

## Technical Details

- **Single file** — All HTML, CSS, and JavaScript in one self-contained file. No build step, no server required.
- **Web Audio API** — Oscillator synthesis, LFO vibrato, convolution reverb (procedurally generated impulse response), delay line with feedback, and chorus via modulated delay.
- **Recording pipeline** — `MediaRecorder` captures the output as WebM/Opus, which is then decoded to a raw `AudioBuffer` and encoded to 16-bit PCM WAV entirely in the browser.
- **No external dependencies** — The only external resource is the Google Fonts stylesheet for Inter and JetBrains Mono. The app works fully offline once the fonts are cached.

---

## Browser Support

Tested on current versions of Chrome, Firefox, Safari, and Edge. Requires Web Audio API and `MediaRecorder` support (all modern browsers).

---

## License

MIT
