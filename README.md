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
- **Click/touch and hold** to produce sound. Release to stop.

On touchscreens this becomes a touch surface: press and drag your finger to play.

A custom cursor with a glow ring follows your movement, and an optional particle trail visualises your path. A frequency/note readout appears at the top of the surface while playing.

This mode is designed to mirror the gestural, spatial nature of a real theremin — small hand movements translate directly into continuous pitch and volume changes.

### Gesture Mode (Webcam)

The closest experience to playing a real theremin: you play it by moving your hands in the air, with the same two-hand technique as the physical instrument. Click the **Gesture** mode button, then **Enable Camera**. Hand tracking runs entirely in the browser (MediaPipe HandLandmarker, GPU-accelerated) — no video ever leaves your machine.

#### How to play it

| Gesture | Effect |
|---|---|
| **Right hand up / down** | Pitch — higher hand = higher note, continuous and step-free |
| **Right hand wobble** | Natural vibrato — your own hand oscillation modulates the pitch |
| **Right hand thumb–index pinch** | Fine tuning, ±1 semitone (pinched = flat, spread = sharp) |
| **Left hand height** | Volume — raised = loud, resting low = silent |
| **Left fist closed** | Mute — for articulating phrases and separating notes |
| **Right hand leaves the frame** | The theremin falls silent |

A mirrored live preview shows a skeleton overlay of both tracked hands (green = pitch hand, amber = volume hand), a volume meter, and a frequency/note readout. Recording, presets, effects, and scale quantization all work exactly as in the other modes.

#### Calibration

Like a real theremin, the playing fields should match *your* body and reach, not the camera frame. Once the camera is running, click **Calibrate** (top-right) and follow the four prompts:

1. Hold your **right hand** at your **highest** comfortable note.
2. Hold your **right hand** at your **lowest** note.
3. Raise your **left hand** — full volume.
4. Rest your **left hand** low — silence (your virtual "volume plate").

The full pitch and volume ranges are then mapped between those positions, so every note is always where your muscle memory expects it — the single most important factor in real theremin technique. Calibration is saved locally and reused next session; recalibrate whenever you move the camera or change where you sit.

#### How it mimics a real theremin

A real theremin senses hand position capacitively: the pitch antenna produces a continuous tone that rises as your hand approaches, and the volume loop attenuates the sound as your other hand nears it. There are no buttons, keys, or contacts — pitch is a smooth analog continuum, vibrato comes from oscillating the pitch hand, phrasing and articulation are shaped entirely by the volume hand, and players make fine pitch corrections with finger and wrist movements ("aerial fingering") while the arm holds position. Gesture mode reproduces each of these behaviours:

- **Continuous pitch field** — hand height maps logarithmically across the configured frequency range (equal distance per octave, like a well-tuned antenna), with no steps unless you deliberately enable scale quantization.
- **Proximity volume** — volume follows left-hand height above a calibrated rest plane (the virtual equivalent of the volume loop), so swells, crescendos, and note separation are shaped with the volume hand exactly as on the real instrument. A closed fist is a practical addition: an instant mute for crisp articulation.
- **Natural vibrato** — the synthetic LFO vibrato is disabled in gesture mode. Instead, a detector watches for deliberate ~5–7 Hz oscillation of your pitch hand and opens the tracking filter to let it through to the oscillator — your vibrato, at your speed and depth, like the real thing.
- **Aerial fingering** — the distance between your right thumb and index fingertips applies a ±1 semitone trim, allowing small intonation corrections without moving your arm.
- **No glitch-silence** — a ~200 ms grace period holds the note through brief tracking dropouts (e.g. when your hands cross), because a real instrument never drops out.

#### How it works, technically

- **Tracking** — MediaPipe `HandLandmarker` (21 landmarks per hand, 2 hands, GPU delegate) runs on each video frame. Handedness labels identify the pitch vs. volume hand, with a frame-position fallback when the classifier is unsure.
- **Signal conditioning** — pitch and volume pass through One Euro filters: adaptive smoothers that reject sensor jitter when your hand is still but introduce almost no lag during fast movement. The vibrato gate measures the envelope of the fast pitch component and raises the filter's cutoff only while deliberate oscillation is present, so jitter stays filtered at rest while real vibrato passes.
- **Sound** — the conditioned signals drive the same oscillator, effects chain, recorder, and quantization settings as the other modes, using smooth exponential parameter ramps (the portamento setting controls glide feel).

#### Realistic limitations

- **Latency** — a real theremin responds essentially instantly. Camera-based control adds capture + inference delay (typically 50–100 ms depending on your camera and machine), comparable to a large-monitor or wireless setup. Fast passages feel less crisp than the real instrument.
- **Frame rate** — tracking runs at your camera's frame rate (usually 30 fps). Pitch is interpolated smoothly between frames, but extremely fast hand motions are inherently sampled, not continuous.
- **No true depth** — a single webcam sees a 2D projection. Moving your hand toward/away from the camera only weakly affects the reading (via foreshortening), so the pitch field is vertical rather than the real instrument's hand-to-antenna distance. Leaning your body shifts the field — recalibrate if you move.
- **Lighting and camera quality matter** — dim rooms, backlighting, motion blur, and noisy low-end webcams degrade tracking. Even, frontal lighting and a 720p+ camera give the best results.
- **Occlusion** — when one hand blocks the other from the camera's view, or both overlap closely, tracking can swap or drop hands momentarily. The grace period and handedness fallback smooth this over, but keep your hands separated in the frame for best results.
- **Vibrato is detected, not magic** — very small or very slow vibrato may be partially smoothed out; very large wobbles may read as pitch movement. The sweet spot is a relaxed ~5–7 Hz oscillation.
- **Intonation is still on you** — there are no frets in the air. Use the note readout (and optionally scale quantization in Settings) while building muscle memory.
- **First use needs internet** — the hand-tracking model (~10 MB) is fetched from a CDN the first time you enable gesture mode. Everything else in the app is fully self-contained.

If your browser refuses camera access from a `file://` URL, serve the folder locally instead (e.g. `python3 -m http.server`) and open it via `localhost`.

### Keyboard Mode

A two-octave piano-style keyboard rendered on screen. Notes can be triggered with mouse clicks on the keys or with the computer keyboard:

| Row | Keys | Notes |
|---|---|---|
| Home row | `A S D F G H J K L ;` | White keys (C through E, two octaves) |
| Upper row | `W E T Y U O P` | Black keys (sharps/flats) |

- **Z** / **X** — Shift octave down / up.
- Octave buttons (2–6) in the top bar select the base octave directly.
- A volume slider below the keyboard controls output level.

The active note and its frequency are displayed above the keyboard. On touchscreens the keys respond to touch.

---

## Mobile Support

Æther detects phones and tablets (via the coarse-pointer media query and touch-point count) and adapts its layout automatically:

- **Compact top bar** — mode buttons collapse to icons, the logo shrinks to its mark, and the timer hides on very narrow screens (the pulsing record button still signals recording).
- **Touch playing** — the mouse surface and on-screen keyboard respond to touch via pointer events; slider thumbs are enlarged for fingers.
- **Overlay panels** — Settings and Tracks slide over the playing surface instead of squeezing it.
- **Gesture mode** works in both portrait and landscape: the calibration prompts, volume meter, frequency readout, and camera controls reflow for small screens, and the front (selfie) camera is preferred so it faces you while you play. In landscape the top bar and scope shrink to preserve vertical space.
- **Safe areas & dynamic viewport** — the layout respects iPhone notches/home indicators (`viewport-fit=cover` + `env(safe-area-inset-*)`) and uses `100dvh` so the app fits as mobile browser chrome collapses.

Everything else — recording, presets, effects, calibration — works identically to desktop.

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
| `Tab` | Cycle mouse / keyboard / gesture mode |
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
- **Hand tracking** — Gesture mode uses MediaPipe's `HandLandmarker` (`@mediapipe/tasks-vision`), lazy-loaded from a CDN on first use. All inference runs locally in the browser via WebAssembly/GPU; camera frames are never uploaded anywhere.
- **No other external dependencies** — The only other external resources are the Google Fonts stylesheet for Inter and JetBrains Mono and the hand-tracking assets above. Outside gesture mode, the app works fully offline once the fonts are cached.

---

## Browser Support

Tested on current versions of Chrome, Firefox, Safari, and Edge. Requires Web Audio API and `MediaRecorder` support (all modern browsers).

---

## License

MIT
