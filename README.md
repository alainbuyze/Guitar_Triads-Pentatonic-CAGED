# Fretboard CAGED Explorer

An interactive guitar-fretboard tool that shows how **triads**, **pentatonic boxes**, and the **CAGED system** are the same shapes viewed at different levels of detail — for any root note, major or minor.

It's a single self-contained `index.html` file: no build step, no dependencies, no network calls. Open it in a browser and it runs.

## What it shows

For a chosen key (any of the 12 roots × major/minor), on any of the four adjacent 3-string sets, the tool plots the three closed triad voicings (root, 1st, and 2nd inversion) and reveals, for each one:

- the **pentatonic box** it lives inside,
- the **CAGED shape** anchoring that box (fixed mapping: `E = Box 1, D = Box 2, C = Box 3, A = Box 4, G = Box 5`),
- the **full chord** — every chord tone of that shape across all six strings, traced with a dotted line (i.e. the barre chord the triad is part of).

The core idea: the **triad is the 3-note skeleton**, the **pentatonic box wraps two extra "safe" notes** around it, and the **CAGED barre chord is the full voicing** filling the same zone of the neck.

## Features

- **Root selector** — all 12 chromatic roots.
- **Quality toggle** — minor or major (switches triad tones, pentatonic, and degree labels).
- **String-set selector** — Top 3 (G-B-e), Middle 3 (D-G-B), A-D-G, Low 3 (E-A-D). Together these surface all five CAGED shapes.
- **Layer toggles** — pentatonic dots, CAGED shape bars, active box outline, full-chord overlay.
- **Label modes** — none, scale degrees (`1 b3 4 5 b7` / `1 2 3 5 6`), or note names.
- **Click any triad** to isolate it, brighten its CAGED bar, outline its box, and draw its full chord.
- Root notes are always ringed in red.

## How it works

Everything is computed from music-theory first principles at render time — nothing is hard-coded per key:

1. **Pentatonic** = scale intervals above the root (`0 3 5 7 10` minor, `0 2 4 7 9` major) mapped onto standard tuning `E A D G B e`.
2. **Boxes** are built with an anchor-window method: for box *N*, the anchor is the *N*-th pentatonic degree on the low E string, and the box is every pentatonic note within a 4-fret window of that anchor. This reproduces the five standard positions and bounds each box cleanly.
3. **Triads** are found by brute-force search for compact voicings of the three chord tones on each string set, then each is assigned to the pentatonic box that contains all three of its notes (closest-centered box on boundaries).

## Usage

Just open `index.html` in any modern browser.

### Publish with GitHub Pages

Push this repo to GitHub, then in **Settings → Pages** set the source to the `main` branch (root). Your explorer will be live at `https://<your-username>.github.io/<repo-name>/`.

## License

MIT — see [LICENSE](LICENSE).
