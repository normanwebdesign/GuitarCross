# Cross Guitar

An offline guitar tuner, song book and chord dictionary for iPhone and iPad.
No accounts, no network calls, no libraries — `index.html` is the whole app.

## The four tabs

**Tune** — chromatic tuner driven by the microphone. Auto mode finds the string
you are playing; tap a circle to lock one instead, tap it again to hear the
reference pitch. Ten tunings (standard, drop D, Eb, drop C, open G/D/E, DADGAD,
ukulele), adjustable A4 calibration.

**Songs** — your library. Three kinds of song:
- *Chords & lyrics* — paste a sheet and tap **Detect chords in text**. It reads
  both the usual web format (chords on their own line above the words) and
  ChordPro (`[G]amazing [C]grace`), and keeps the columns intact.
- *Tab* — monospace tablature, exactly as you type it.
- *Custom* — a photo of a chart, or **Create my own**: paste lyrics, then tap
  any letter to drop a chord above it.

Photo songs pinch to zoom and drag to pan, and a double tap snaps between fit
and full size — on a desktop the wheel zooms, and `+` / `-` / `0` and the arrow
keys work once the photo has focus. Tap **+ Chord**, then tap a spot on the
photo, to pin a chord there. A pin is stored as a point on the photo itself, so
it holds its place at any zoom and on any screen; pins transpose with the song,
open the usual chord diagram when tapped (change, nudge or remove them there),
and travel in a backup.

Every chord in a song is a button: tap it for the diagram, arrow through every
other way to play it, hear it strummed. Transpose with ♭/♯, set a capo, modulate
to a new key, auto-scroll hands free while you play.

**Chords** — the dictionary. Search (`C#m7`, `Bbsus4`, `F#m7b5`), or browse by
root and quality. Every chord shows its standard shape first, then alternatives
up the neck, with barre marks and finger numbers.

**Tools** — modulate any chord or a whole song, capo calculator, metronome with
tap tempo, settings (left-handed diagrams, sharps/flats, A4), and backup.

## Putting it on your phone

The app works from anywhere, but **the tuner needs https** — iOS only hands the
microphone to secure pages, so opening the file from the Files app gives you
everything except the tuner.

The five-minute version:

1. Create a GitHub repository and upload `index.html`, `sw.js`,
   `manifest.webmanifest` and the three `icon-*.png` files.
2. Settings → Pages → Build from branch → `main` / root.
3. Open the `https://<you>.github.io/<repo>/` address in **Safari** on your phone.
4. Share → **Add to Home Screen**.

It then launches full screen, with no address bar, and keeps working with no
signal. Any https host does the job — Netlify, Cloudflare Pages, your own server.

## Where your data lives

Songs and settings live in this browser profile's local storage; photos go in
IndexedDB so large images do not fill the quota. Nothing leaves the device.

Two things to know:
- Safari and the Home Screen app have **separate storage**. Add to the Home
  Screen first, then enter your songs there.
- Clearing Safari data erases the library. Tools → **Export backup** writes a
  single JSON file with every song and photo; **Import backup** restores it.

## Editing the app

`index.html` is self-contained: styles, chord engine and UI in one file. If you
change it and you are using the service worker, bump `CACHE` in `sw.js` so
phones pick up the new version instead of the cached one.

Chord shapes come from three sources, in this order: a short list of curated
open chords, movable CAGED-style templates, and a search that works out
playable voicings for anything else. All 324 root/quality combinations were
checked: correct notes only, root in the bass, no stretch over four frets.
