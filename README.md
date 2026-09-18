# neorecorder

A voice recorder and an audio-to-MIDI converter in one HTML file. The app works offline. It has no libraries and makes no network requests.

**Version:** 1.1.2

## Functions

- **Voice mode.** This is the start mode. Record a sound. When you stop, the app saves a WAV file immediately.
- **MIDI mode.** Record a melody. When you stop, the app analyzes the sound, makes a MIDI file, and saves it immediately.
- **File upload.** In MIDI mode, select an audio file. The app analyzes the file and saves a MIDI file immediately.
- **Microphone only.** The app asks for the microphone when it starts. It does not ask for the camera.
- **Level meter.** Red bars show the level of the microphone.
- **Save again.** The button at the bottom left saves the last file again.
- **Recording timer.** A red indicator and a timer are at the top of the screen during a recording.
- **Screen lock prevention.** The screen stays on during a recording, if the browser gives this function.

## Interface

| Control | Position | Function |
|---|---|---|
| Shutter | Bottom center | Starts and stops a recording |
| Mode labels | Above the shutter | Change between Voice and MIDI. A swipe on the screen also changes the mode |
| Save again | Bottom left | Saves the last file again |
| Upload | Bottom right | Selects an audio file to convert to MIDI. It shows in MIDI mode only |

Keyboard: the space bar or the enter key operates the shutter. The left and right arrow keys change the mode.

You cannot change the mode or upload a file during a recording or an analysis.

## Files

| Mode | Name | Format |
|---|---|---|
| Voice | `AUD_20260915_142233.wav` | WAV, 16-bit PCM, mono, at the rate of the microphone |
| MIDI | `MIDI_20260915_142233_120bpm.mid` | Standard MIDI File, format 0, 480 ticks for each quarter note |
| Upload | `take-01_120bpm.mid` | Standard MIDI File, format 0, 480 ticks for each quarter note |

A recording name contains the local date and the local time. An upload name contains the name of the audio file. Each MIDI name also contains the estimated tempo.

Maximum length: 20 minutes in Voice mode, 5 minutes in MIDI mode. At the maximum, the recording stops and the app saves the file.

## Upload formats

The browser decodes the file. Thus the formats change with the browser. WAV, MP3, M4A, and AAC operate in all supported browsers. FLAC, OGG, and Opus operate in most browsers. The app mixes stereo files to mono. It analyzes the first 10 minutes of a file.

## How MIDI mode works

1. The app records the raw microphone signal, or it decodes the uploaded file. For a recording, echo cancellation, noise suppression, and automatic gain are off.
2. The app finds the pitch every 10 ms with the McLeod pitch method.
3. The app divides the pitch line into notes. A new note starts when the pitch changes for more than 0.75 semitone, when the sound stops, or when the level rises sharply after a dip. The last rule finds repeated notes of the same pitch.
4. The app finds the tuning of the full recording and corrects it. Thus a sound that is out of tune by a constant amount gives the correct notes.
5. The app estimates the tempo from the start times of the notes. Then it puts the first beat at or before the first note.
6. The app writes the notes with the tempo and a 4/4 time signature. Loud notes get a high velocity.

The note times are the times of your performance. The app does not quantize them. The tempo only sets the grid in your DAW.

### Limits

- **One note at a time.** Chords give one note or wrong notes. Voice, whistle, and single-note instruments give the best result.
- **Range:** from G1 (50 Hz) to B6 (2000 Hz).
- **Tempo** is an estimate. It can be half or double of the correct tempo. With fewer than 4 notes, or less than 1.5 seconds of notes, the tempo is 120 BPM and the message shows "(default)".
- **Glides** give a short note for each semitone that the glide crosses.
- **Background noise** decreases the accuracy. Record in a quiet room, near the microphone.

## How to use

1. Put `neorecorder-v1.1.2.html` on a web server with HTTPS, or on `localhost`.
2. Open the file in the browser.
3. Give permission to the microphone.
4. Select Voice or MIDI. Touch the shutter to record. Touch it again to stop.
5. To convert a file, select MIDI and touch the upload button.

A secure origin is necessary. Browsers refuse the microphone on plain HTTP. Chrome also permits a local `file://` page, but Safari does not. For a phone, GitHub Pages is a sufficient host.

The browser can ask for permission before it saves more than one file. Allow this to keep the automatic download.

## Microphone problems on a desktop computer

A desktop browser can have permission while another item still blocks the microphone. The app finds the cause and shows the applicable instruction. Small grey text under the instruction shows the error from the browser.

| Message | Cause | Correction |
|---|---|---|
| The operating system blocks the microphone | macOS or Windows does not permit the browser to use the microphone | macOS: System Settings > Privacy & Security > Microphone. Windows: Settings > Privacy & security > Microphone. Turn on the browser, then quit and open the browser again |
| Microphone access is off for this page | The page permission in the browser is "Block" | Click the icon at the left of the address bar and allow the microphone |
| The page is inside a frame | A preview frame opened the page without microphone permission | Download the file and open it directly |
| The microphone is not available | Another app uses the microphone | Close the other app |

If the browser refuses the raw microphone settings, the app tries again with the default settings.

## Browser support

| Browser | Voice | MIDI | Upload |
|---|---|---|---|
| Safari on iOS 15 or later | Yes | Yes | Yes |
| Chrome on Android | Yes | Yes | Yes |
| Chrome and Edge on desktop | Yes | Yes | Yes |
| Firefox | Yes | Yes | Yes |

The app records with an `AudioWorklet`. If the browser does not have it, the app uses a `ScriptProcessorNode`.

## Technical notes

- One file. No external requests, no fonts, no libraries, and no build step.
- The app uses `getUserMedia` and the Web Audio API only. The browser decoder `decodeAudioData` reads uploaded files. The FFT, the pitch detector, the tempo estimator, the WAV writer, and the MIDI writer are in the file.
- The analysis runs in small parts. Thus the screen shows the progress and stays active.
- The app writes no data to the disk of the browser and it uses no cookies.

## Changelog

### 1.1.2
- The permission screen now shows the cause of a microphone error: the operating system, the page permission, a blocking frame, or a busy device.
- The permission screen shows the error from the browser.
- If the browser refuses the raw microphone settings, the app tries again with the default settings.

### 1.1.1
- Changed the app name to neorecorder.

### 1.1.0
- Added an upload button at the bottom right in MIDI mode. It converts an audio file to MIDI and saves the file immediately.
- Removed the inactive flip button.

### 1.0.0
- First release: fork of the camera app audio-only mode, Voice mode with WAV output, MIDI mode with pitch and tempo analysis, save again button, recording timer, and mode change with labels, swipe, and arrow keys.
