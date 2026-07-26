# Guitar: Triads → Pentatonic Boxes → CAGED

An interactive guitar-fretboard tool that shows how **triads**, **pentatonic boxes**, and the **CAGED system** are the same shapes viewed at different levels of detail — for any root note, in major, minor, sus2, sus4, aug, dim, maj7, dom7, or min7 (shell voicings).

It's a single self-contained `index.html` file: no build step, no dependencies, no network calls. Open it in a browser and it runs.

![Screenshot of the explorer showing A minor on the top three strings, with the 1st-inversion triad selected inside its pentatonic box and CAGED E-shape](docs/screenshot.png)

**Live demo:** https://alainbuyze.github.io/Guitar_Triads-Pentatonic-CAGED/

## What it shows

For a chosen key (any of the 12 roots × major/minor), on any of the four adjacent 3-string sets, the tool plots the three closed triad voicings (root, 1st, and 2nd inversion) and reveals, for each one:

- the **pentatonic box** it lives inside,
- the **CAGED shape** anchoring that box,
- the **full chord** — every chord tone of that shape across all six strings, traced with a dotted line (i.e. the barre chord the triad is part of).

The core idea: the **triad is the 3-note skeleton**, the **pentatonic box wraps two extra "safe" notes** around it, and the **CAGED barre chord is the full voicing** filling the same zone of the neck.

## Features

- **Root selector** — all 12 chromatic roots.
- **Quality toggle** — minor, major, sus2, sus4, aug, dim, maj7, dom7, min7 (switches triad tones, pentatonic, and degree labels). Sus2, sus4, and min7 are proper subsets of the major/minor pentatonic scale, so they get the full pentatonic-box/CAGED-shape treatment like major and minor. Augmented, diminished, maj7, and dom7 don't map onto any standard CAGED shape or pentatonic scale, so for those the overlay is hidden and only the three voicings (still fully clickable and playable) are shown.
- **Shell voicings** (maj7, dom7, min7) — stripped-down 7th chords using only the root, 3rd, and 7th (the 5th is dropped). These are the guide-tone voicings used for jazz/blues comping, still built from only 3 notes so they fit the same engine as the triads. Min7 shells happen to be a full subset of the minor pentatonic scale, so — unlike maj7/dom7 shells — they get boxes and CAGED shapes just like an ordinary minor chord.
- **String-set selector** — Top 3 (G-B-e), Middle 3 (D-G-B), A-D-G, Low 3 (E-A-D). Together these surface all five CAGED shapes.
- **Layer toggles** — pentatonic dots, CAGED shape bars, active box outline, full-chord overlay.
- **Label modes** — none, scale degrees (`1 b3 4 5 b7` / `1 2 3 5 6`), or note names.
- **Sharps/flats toggle** — display all note names (root selector, fretboard, card labels) with sharps or flats. A simple global preference rather than automatic per-key spelling — proper enharmonic spelling depends on the root's position on the circle of fifths (not the quality), which is out of scope for this toggle.
- **Click any triad** to isolate it, brighten its CAGED bar, outline its box, and draw its full chord.
- Root notes are always ringed in red.
- **Audio** — click any note dot on the fretboard to hear its pitch, hit **Play** on a triad card to strum that voicing, or **Play full chord** (shown when a triad is selected) to strum the whole barre chord. Sound can be muted with the SOUND toggle. Tones are synthesized live in the browser (Karplus-Strong plucked-string algorithm via the Web Audio API) — no audio files, no dependencies.
- **Mobile-friendly**: segmented controls (quality, labels) wrap onto extra lines instead of clipping on narrow screens, note dots have an enlarged invisible tap target for touchscreens, and the fretboard scrolls horizontally within its own frame without breaking the page layout.

## How it works

Everything is computed from music-theory first principles at render time — nothing is hard-coded per key:

1. **Pentatonic** = scale intervals above the root (`0 3 5 7 10` minor, `0 2 4 7 9` major) mapped onto standard tuning `E A D G B e`.
2. **Boxes** are built with an anchor-window method: for box *N*, the anchor is the *N*-th pentatonic degree on the low E string, and the box is every pentatonic note within a 4-fret window of that anchor. This reproduces the five standard positions and bounds each box cleanly.
3. **Triads** are found by brute-force search for compact voicings of the three chord tones on each string set, then each is assigned to whichever pentatonic box contains all three of its notes. A genuine, unclipped box always spans 3-4 frets, so any narrower candidate is discarded as a sliver left over from a box anchored off the edge of the neck (unless it's the *only* candidate, which happens right at the open position where the fretboard itself has no room to extend further). Among the remaining genuine candidates, the closest-centered one wins — this is what keeps a triad's assigned CAGED shape consistent with the same physical barre position on adjacent string sets. When a wide-interval quality (sus2, sus4, or a shell voicing) admits two equally compact arrangements sharing the same bass note, only the tighter one is kept, so each inversion shows once.

### A note on box numbering in major keys

Pentatonic box numbers are anchored to the **shared pentatonic shape** — i.e. the relative minor — so a major key and its relative minor use the *same* box numbers (C-major pentatonic and A-minor pentatonic are the same shapes). The **CAGED shape** label, however, follows the actual chord. In major keys these two therefore sit **one position apart** (e.g. C major's E-shape barre lives in pentatonic box 2). The app calls this out in its "Connection" panel.

## Usage

Just open `index.html` in any modern browser — that's the whole app.

## Deployment (GitHub Pages)

This repo includes a GitHub Actions workflow ([`.github/workflows/deploy.yml`](.github/workflows/static.yml)) that **auto-deploys to GitHub Pages on every push to `main`**.

To turn it on once: **Settings → Pages → Build and deployment → Source: GitHub Actions**. After the next push, the site is live at the demo link above.

## License

MIT — see [LICENSE](LICENSE).
