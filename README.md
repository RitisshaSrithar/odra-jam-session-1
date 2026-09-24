# 🎸 Run Club Jam — Digital Setlist

A mobile-first digital jam book for **RUN CLUB JAM · OMR × ECR**.
Pamphlet → QR scan → landing page → setlist → song → sing along → next song.

**Stack:** React + Vite + React Router + plain CSS. No backend, no login, no database.
I skipped Tailwind on purpose: one hand-written stylesheet keeps the visual identity (grain, waveforms, pamphlet card) easy to tweak in a single place.

---

## Quick start

Requires **Node.js 18+** (20+ recommended).

```bash
npm install
npm run dev        # opens on http://localhost:5173 (also on your Wi-Fi IP, so you can test on your phone)
npm run build      # production build → dist/
npm run preview    # preview the production build
```

**Test on your phone:** run `npm run dev`, then open the `Network:` URL it prints (e.g. `http://192.168.1.5:5173`) on a phone connected to the same Wi-Fi.

---

## ✏️ Editing content — only 2 files

| What | File |
|---|---|
| Event name, date, time, location, tagline, About copy, QR copy, Instagram, finale text, **Now Playing** | `src/config.js` |
| Songs: order, titles, artists, keys, lyrics, chords, crowd cues | `src/data/songs.js` |

### Songs (`src/data/songs.js`)
- **Order = array order.** Move a song object up or down to reorder it. The 01, 02, 03 numbers and prev/next links update automatically.
- **`id`** is a permanent unique number used in the URL (`/song/3`). Don't reuse ids.
- Fields: `title, artist, language, key, duration, category, introNotes, crowdMoment, lyrics, endingNotes, notes`. Leave any field as `''` to hide it.

Lyrics are an array of sections:

```js
lyrics: [
  { type: 'verse', label: 'Verse 1', lines: [
      'A plain line',
      { chords: 'G        C', text: 'A line with chords above it' },
  ]},
  { type: 'chorus', lines: ['...'] },            // choruses get "🎤 SING THIS PART" automatically
  { type: 'bridge', singAlong: true, lines: [] }, // force the tag on any section
  { type: 'cue', lines: ['👏 Clap on 2 & 4'] },  // highlighted crowd instruction
]
```

Section types: `intro, verse, pre-chorus, chorus, bridge, instrumental, outro, cue`.
Song 7 ("Run Club Warm-Up Chant") is an original demo showing every feature. Delete it when you're ready.

> ⚠️ **Lyrics & copyright:** the setlist songs ship with `[LYRICS GO HERE]` placeholders. Only paste in lyrics you have the rights to use (your own, public domain, or licensed). Song titles and artist names are fine.

### Now Playing
In `src/config.js`:
```js
export const nowPlaying = { currentSongId: 3 };   // id of the live song, or null to hide
```
Change it and redeploy. On Vercel or Netlify, a push to GitHub redeploys in about 30 seconds. The live song gets a pulsing ● NOW PLAYING badge on the setlist, the home page and its song page, plus a "Jump" button on the setlist.

### Instagram
Replace `'[INSTAGRAM LINK]'` with a full URL. Until you do, the footer shows a disabled placeholder instead of a broken link.

---

## 🗺️ Routes

| Route | Page |
|---|---|
| `/` | Landing page: hero, Now Playing teaser, QR CTA, About, Event info |
| `/setlist` | The Setlist (performance order) |
| `/song/:id` | Song page with lyrics, reading tools, sticky prev/next |
| `/about` | About Run Club + event info |
| `/finale` | "You made it through the set 🎸" (after the last song) |
| anything else | Friendly 404 |

---

## 📱 QR destination

**The printed QR code should point to the deployed root URL**, e.g. `https://runclubjam.vercel.app/`.

- Point it at `/`, not at a specific song. The setlist can change without reprinting.
- Deploy **first**, open the URL on your phone, *then* generate the QR code with any QR generator.
- Use a domain you'll keep. If the URL changes, every printed pamphlet breaks.
- Print it at least **2.5 cm × 2.5 cm** with a white quiet-zone border, and test-scan the printed proof with both an iPhone and an Android phone.

---

## 🚀 Deploy (free)

**Vercel** (easiest): push to GitHub → vercel.com → *Add New Project* → import the repo → Deploy. `vercel.json` is already included so deep links like `/song/3` work on refresh.

**Netlify:** build command `npm run build`, publish directory `dist`. `public/_redirects` handles deep links.

**Any static host:** upload the contents of `dist/`, and configure all paths to fall back to `index.html`.

---

## ✅ Built-in details

- Mobile-first, tested at 320 / 360 / 375 / 414 px and desktop, with no horizontal scroll
- Lyric reader: 4 text sizes (A− / A+), chord toggle, narrow line length, high contrast. Both settings are remembered on the device.
- Sticky song header with progress bar, and a bottom prev/next bar in the thumb zone with a `03 / 07` counter
- Arrow keys ← → switch songs on a keyboard
- Respects `prefers-reduced-motion` (all animation off), safe-area insets for notched iPhones
- Skip link, focus outlines, ARIA labels, semantic lists
- Lightweight: ~90 KB gzipped JS, CSS-only animations, no images

## 📁 Structure

```
src/
  config.js            ← event copy + Now Playing
  data/songs.js        ← the setlist
  lib/                 ← setlist helpers, page titles, safe localStorage
  components/          ← TopBar, Footer, SongCard, Lyrics, QRCTA, Waveform, …
  pages/               ← Home, Setlist, Song, About, Finale, NotFound
  styles/global.css    ← theme tokens at the top (colours, fonts, radius)
```
