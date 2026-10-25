# Portfolio — Yiqun Zhang

Personal homepage built as a single HTML file. No build tools needed — just open `index.html` in a browser.

## How to use

1. **Open locally**: `open portfolio/index.html` (or double-click the file)
2. **Or serve it**: `python3 -m http.server 8000` from this folder, then visit <http://localhost:8000>
3. **Videos** live in `media/` and are linked relative to `index.html`, so keep the whole `media/` folder together with the page.
4. **Photo & CV** — the hero avatar uses `photo.jpg` and the contact section links to `resume.docx`.

## Projects gallery

The Projects section works like a YouTube channel page:

- **Thumbnail grid** — every project shows a 16:9 thumbnail, duration badge, and short description. Nothing downloads until a visitor clicks.
- **Lightbox player** — clicking a thumbnail opens a modal with the full video, a longer description, and tags. Close it with the ✕ button, by clicking the backdrop, or with `Esc`.
- **Category filters** — All / XR & VR / Games / WebGPU / VFX.

The gallery is generated from the `PROJECTS` array in the `<script>` block at the bottom of `index.html`. Video sources, thumbnails, titles, descriptions, tags, and filters all come from that array.

## Folder layout

```
portfolio/
├── index.html              # the whole site
├── media/
│   ├── video/              # web-optimized MP4s used by the page
│   └── thumbs/             # 1280×720 thumbnail images
├── photo.jpg               # hero avatar
├── resume.docx             # downloadable CV
├── video_intro.txt         # source notes the project descriptions came from
└── *.mp4 / *.mov           # original source recordings (not loaded by the page)
```

The original recordings are kept in the project root untouched. The page only loads the compressed copies in `media/video/`, which keeps a page visit to a few hundred KB of thumbnails instead of hundreds of MB of video.

## Add or replace a project video

1. **Encode a web-friendly MP4** (H.264 + AAC, max 1600 px wide, fast-start for instant playback):

   ```bash
   ffmpeg -i "My Recording.mov" \
     -vf "scale='min(1600,iw)':-2" \
     -c:v libx264 -preset medium -crf 23 -pix_fmt yuv420p \
     -c:a aac -b:a 128k -movflags +faststart \
     media/video/my-project.mp4
   ```

2. **Generate a 16:9 thumbnail** (change `-ss` to a good moment in the video):

   ```bash
   ffmpeg -ss 5 -i "My Recording.mov" -frames:v 1 \
     -vf "scale=1280:720:force_original_aspect_ratio=increase,crop=1280:720" \
     -q:v 3 media/thumbs/my-project.jpg
   ```

3. **Add an entry** to the `PROJECTS` array in `index.html`:

   ```js
   {
     slug: 'my-project',
     title: 'My Project',
     category: 'Games',              // XR, Games, WebGPU, or VFX
     engine: 'Unity · C#',
     duration: '1:20',
     thumb: './media/thumbs/my-project.jpg',
     video: './media/video/my-project.mp4',
     meta: 'Unity · C# · 1:20 — one-line highlight',
     desc: 'Longer description shown in the lightbox.',
     tags: ['Unity', 'C#', 'Physics']
   }
   ```

Remove a project by deleting its object. Filter counts update automatically.

## Customization guide

| What to change | Where in file |
|---|---|
| Name / tagline | Search for `Yiqun Zhang` and `computational data science` |
| Contact info | Search for phone numbers and email |
| Projects / videos | `PROJECTS` array in the `<script>` block |
| Project categories | `CATEGORIES` array (keep `key` in sync with each project's `category`) |
| Color scheme | CSS variables in `:root` at the top of `<style>` |
| Photo | `src="./photo.jpg"` in `.hero-avatar` |
| CV download | `href="./resume.docx"` in the contact section |

## Structure

- **Hero** — name, tagline, quick stats
- **About** — bio + 4 stat cards
- **Research** — NTHU PSViT research + Gangjutong internship (timeline)
- **Projects** — 8 video demos in a filterable thumbnail gallery with a modal player
- **Skills** — 4 category grid
- **Awards** — 3 scholarship/competition cards
- **Contact** — email + phone links + CV download

## Tech

Single-file HTML + CSS + vanilla JS. Zero dependencies (only Google Fonts for Inter + JetBrains Mono). Fully responsive. Dark theme. Uses the native `<dialog>` element for the video lightbox.
