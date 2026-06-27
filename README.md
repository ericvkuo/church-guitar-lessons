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
- `lessons.json` — the lesson index (the only file you normally edit).
- `lessons/<NN>/` — one folder per lesson, holding that lesson's actual files.

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
