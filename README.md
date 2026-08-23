# Church Guitar Lessons

A static, mobile-first website for sharing guitar-lesson materials (slides, sheet
music, and demo videos) with students. Built with plain HTML/CSS/JS and hosted on
GitHub Pages — no backend, no build step.

- **Academy:** 祂的敬拜學院
- **Class:** 木吉他基礎班
- **Church:** 龍潭聖潔會

Students open the site on their phones, browse lessons (newest first), and **open
or download** every file directly to their device.

## How it works

- `index.html` — the page. It `fetch`es `lessons.json` and renders the lessons.
  It also holds the **tuning guide** (a static `<template>` near the bottom of the
  file) — that's reference content, so it lives in the HTML rather than the JSON.
- `lessons.json` — the lesson index **and** the upcoming-events list (the only
  file you normally edit).
- `lessons/<NN>/` — one folder per lesson, holding that lesson's actual files.

The page shows one view at a time, picked from a sticky tab strip: one tab per
lesson (newest first), then a pinned **🎸 調音教學** tab for the tuning guide.

Lessons are identified and ordered by a **lesson number** (`lesson: 1, 2, 3, …`),
not by date, because lessons don't follow a fixed weekly schedule. The page always
shows the highest lesson number on top.

## Adding a new lesson

1. **Pick the next number** — one higher than the last lesson (e.g. `02`).
2. **Create the folder** `lessons/02/` and put this lesson's files in it:
   - the slides, exported as a **PDF**
   - any **images** (sheet music / appendix) — `.jpg` or `.png`
   - optionally a **demo video** — see "Video tips" below
3. **Add an entry** to the top of the `lessons` array in `lessons.json`:

   ```json
   {
     "lesson": 2,
     "date": "2026-07-15",
     "title": "和弦轉換",
     "description": "本課練習 C、G、D 之間的轉換。",
     "files": [
       { "name": "投影片", "path": "lessons/02/slides.pdf", "type": "pdf" },
       { "name": "練習譜", "path": "lessons/02/chart.jpg", "type": "image" },
       { "name": "示範影片", "path": "lessons/02/demo.mp4", "type": "video" }
     ]
   }
   ```

   - `lesson` (number, **required**) — controls ordering and matches the folder name.
   - `date` (string `YYYY-MM-DD`, optional) — shown on the card only if present.
   - `title` / `description` — Traditional Chinese; the page adds the「第 N 課」label.
   - `files[]` — each file has `name` (display name), `path`, and `type`
     (`"pdf"`, `"image"`, or `"video"`). Images show a thumbnail; videos play inline.

4. **Publish:**

   ```sh
   git add lessons.json lessons/02
   git commit -m "Add lesson 2"
   git push
   ```

   (Or just ask Claude Code to push for you.)

The page reads `type` to decide how to display each file, so the file extension's
casing doesn't matter, and Traditional Chinese filenames are fine (the page
URL-encodes them automatically).

## Upcoming events (近期活動)

The card under the site header comes from the `events` array at the top of
`lessons.json`:

```json
"events": [
  { "date": "2026-09-13", "title": "第三課", "note": "" },
  { "date": "2026-11-28", "title": "敬拜學院結業式", "note": "上午" }
]
```

- `date` (`YYYY-MM-DD`, **required**) — the weekday label (週日 / 週六 …) is
  worked out from this, so you never type it by hand.
- `title` — what shows next to the date.
- `note` (optional) — a small grey hint after the title, e.g. `"上午"`.

**Past events disappear on their own.** The page hides anything dated before
today and sorts the rest soonest-first, so the card can't go stale — you only
ever need to *add* entries. When nothing is upcoming the whole card is hidden.
(An event with a missing or malformed `date` is skipped.)

## Video tips

- Export clips as **MP4 (H.264 video + AAC audio)** so they play inline in every
  phone browser, including iOS Safari and Android Chrome.
- Keep each file under GitHub's **100 MB per-file limit** (≈720p is plenty for a
  phone). If a file is larger, `git push` will reject it — compress it first, or
  host it elsewhere (e.g. an unlisted YouTube link) and link to it instead.
- To convert a `.mov`/large video to a web-friendly 720p MP4 with `ffmpeg`:

  ```sh
  ffmpeg -i input.mov -vf "scale=-2:720" -c:v libx264 -pix_fmt yuv420p \
    -crf 23 -preset medium -c:a aac -b:a 128k -movflags +faststart output.mp4
  ```

## Local preview

GitHub Pages serves these files as-is, but to preview locally:

```sh
python3 -m http.server 8000
# then open http://localhost:8000
```

(`fetch('lessons.json')` needs to be served over HTTP, so open it through a local
server rather than double-clicking `index.html`.)

## Deployment (GitHub Pages)

Settings → Pages → Build and deployment → Source: **Deploy from a branch**,
Branch: **main** / **/(root)**. After it builds, the site is live at:

```
https://<your-username>.github.io/church-guitar-lessons/
```
