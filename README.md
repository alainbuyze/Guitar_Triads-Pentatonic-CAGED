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
- **Quality toggle** — minor, major, sus2, sus4, aug, dim, maj7, dom7, min7 (switches triad tones, pentatonic, and degree labels). Hover any quality button for a tooltip showing its scale-degree formula (e.g. minor → `1-b3-5`). Sus2, sus4, and min7 are proper subsets of the major/minor pentatonic scale, so they get the full pentatonic-box/CAGED-shape treatment like major and minor. Augmented, diminished, maj7, and dom7 don't map onto any standard CAGED shape or pentatonic scale, so for those the overlay is hidden and only the three voicings (still fully clickable and playable) are shown.
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

Everything is computed from music-theory first principles at render time — nothing is hard-coded per key. There are two separate layers: **how triads are found**, and **how they're matched to a CAGED shape** — each with its own major/minor-first design.

### How triads are found

**Major and minor** triads are found by brute-force search: every compact (≤4-fret span) voicing of the three chord tones on each string set.

**Every other quality is *derived* from a major or minor triad, not searched for independently.** Sus2, sus4, augmented, and the two major-rooted 7th shells (maj7, dom7) are built from the major triad; diminished and the min7 shell are built from the minor triad. Deriving means: take each already-found base triad, and re-pitch exactly the one note that needs to change (the 3rd, for sus2/sus4; the 5th, for aug/dim/the 7th shells) by the right number of semitones on the *same string* — e.g. a 4th is a major 3rd + 1 semitone, so sus4 shifts that one note up 1 fret and leaves the other two untouched. This was a deliberate fix: an independent brute-force search for these qualities could occasionally turn up an extra, spurious voicing that overlaps an already-correct neighboring one in fret-range — a "shape" no real player would ever treat as its own position, since sus/aug/dim/7th-shell chords are conventionally taught as alterations of the plain triad shapes, not their own independently-fingered set. Deriving from the clean, non-overlapping major/minor sequence guarantees the same property carries over.

### How CAGED shapes are matched

**Major, minor, sus2, and sus4** are matched against fixed shape templates — not re-derived from a generic heuristic, but encoded directly from real guitar theory. Each of the 5 shapes (E, A, D, C, G) has a per-string template giving the exact fret offset and scale degree (root/3rd/5th, root/b3/5th, root/4th/5th, or root/2nd/5th) that shape puts on each string, taken from the real open-chord fingerings (e.g. open E = `0-2-2-1-0-0`) and shifted by the same semitone rule used to derive the triads themselves. A few templates include a small, harmonically-valid extension beyond the strict open-chord form — e.g. barring the low-E string together with the A-string's root always sounds the 5th, which fills a real gap in the C-shape and A-shape templates without inventing anything not actually in the chord. Some shapes only cover 4 or 5 strings even after this (D-shape never touches the low-E or A strings, matching real D-shape fingerings), so **not every found triad has a canonical CAGED shape at all** — the shape overlay is honestly hidden for those, mostly triads voicing the "middle" scale degree (3rd, 4th, or 2nd) as the lowest note on the Low 3 (E-A-D) string set, which none of the 5 shapes are built to do. These triads still live inside a real pentatonic box, though (the separate box-number system below still finds one), so the dashed box outline and full-chord overlay still show up using that box's own range — only the shape name itself is left blank.

1. **Shape matching** checks a triad's exact (string, fret) positions against all 5 templates arithmetically — is there a single barre position where every one of the triad's notes lands exactly where that shape's template says it should? No anchor windows, no distance-based guessing.
2. **Ties**: a triad occasionally fits more than one shape (e.g. right at the boundary between two positions). These are resolved by checking whether a *sibling* triad — on a different string-set, sharing 2 of the same 3 notes — also fits that same shape; whichever candidate is corroborated this way wins, falling back to whichever barre is closest to the triad's own position if neither is corroborated.
3. **The full chord** (every string outside the selected triad) is read directly off the matched shape's template at its matched barre — the single source of truth, so the same shape always shows the same notes no matter which string-set, octave, or inversion you're looking at it from. A string whose template position would need a negative fret at this barre (too close to the nut) is simply omitted rather than guessed at.
4. Non-major shapes are labelled with their proper name (**Cm shape**, **Csus4 shape**, **Gsus2 shape**, etc.), not just the bare letter.

**Augmented, diminished, and the maj7/dom7 shells** don't have canonical CAGED shapes at all (there's no "open Caug" or "open Cdom7" chord), so they still use the original pentatonic-anchor method for their box number only: for box *N*, the anchor is the *N*-th pentatonic degree on the low-E string, and the box is every pentatonic note within a 4-fret window of that anchor. The CAGED overlay itself stays hidden for these, and their full chord is filled in by borrowing notes from any other string-set's triad sharing the same box, falling back to a nearest-chord-tone guess only where nothing else covers a string. The min7 shell is a proper subset of the minor pentatonic scale, so it also gets the box/overlay treatment this way (though not the fixed-template shape matching sus2/sus4 get, since a 3-note root-b3-b7 shell doesn't map onto a root/3rd/5th-shaped template the same way).

### A note on box numbering in major keys

Pentatonic box numbers are anchored to the **shared pentatonic shape** — i.e. the relative minor — so a major key and its relative minor use the *same* box numbers (C-major pentatonic and A-minor pentatonic are the same shapes). The **CAGED shape** label, however, follows the actual chord. In major keys these two therefore sit **one position apart** (e.g. C major's E-shape barre lives in pentatonic box 2). The app calls this out in its "Connection" panel.

## Usage

Just open `index.html` in any modern browser — that's the whole app.

## Deployment (GitHub Pages)

This repo includes a GitHub Actions workflow ([`.github/workflows/deploy.yml`](.github/workflows/static.yml)) that **auto-deploys to GitHub Pages on every push to `main`**.

To turn it on once: **Settings → Pages → Build and deployment → Source: GitHub Actions**. After the next push, the site is live at the demo link above.

## License

MIT — see [LICENSE](LICENSE).
