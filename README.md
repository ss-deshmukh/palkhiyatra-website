# Palkhi Yatra website

Source for **[www.palkhiyatra.org](https://www.palkhiyatra.org)**, the website of Palkhi Yatra, a non-profit that serves food to Warkaris (pilgrims) during the Pandharpur Wari.

It is a static site: plain HTML, CSS and JavaScript with no build step. It was originally made with the Mobirise site builder and is now edited by hand.

## Pages

| File | Page |
| --- | --- |
| `index.html` | Home: hero video, impact stats, Recent Events |
| `work.html` | Our Work |
| `about.html` | About Us: story, team |
| `gallery.html` | Gallery: photos by year, videos, artifacts |
| `contact.html` | Support: donation and contact |
| `page5.html` | Terms & Conditions |

## Run it locally

From the repository root:

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

## Deployment

GitHub Pages publishes the `main` branch automatically. The custom domain is set in `CNAME`. Changes go live a minute or two after you push to `main`.

## Styling

- `assets/mobirise/css/mbr-additional.css`: the original Mobirise styles, one block per section (`.cid-…`).
- `assets/theme/css/style.css`: base theme styles, plus the hero background video rules at the end.
- `assets/theme/css/modern.css`: the modern UI layer (rounded cards and images, pill buttons, frosted navbar, hover effects). It is loaded last on every page and changes only appearance, not layout. Remove its `<link>` to see the original look.

## After each year's event

### 1. Update the numbers

| What | Where |
| --- | --- |
| Pilgrims served, years of service, years since founding (2018) | `index.html`: "Making a Difference" list and the I M P A C T tiles |
| Money raised (INR and approx. USD) | `index.html`: I M P A C T tiles |
| "Our Story" totals and "For the past N years" | `about.html` |
| Event year that donations are for, and the USD value of the donation cap | `contact.html` |

### 2. Add the year's photos

Put web-sized photos in `assets/images/<year>/` (about 1600px on the long side, a few hundred KB each). Do not commit original phone photos, videos or zip files: GitHub rejects files over 100 MB. `.gitignore` already excludes zips in the year folders.

Resize a photo (this also strips location data and other metadata) with:

```sh
ffmpeg -i original.jpg -map_metadata -1 \
  -vf "scale=w=1600:h=1600:force_original_aspect_ratio=decrease" \
  -q:v 4 assets/images/2027/py-2027-01.jpg
```

### 3. Add a gallery year

In `gallery.html`:

1. Copy an existing grid such as `<div class="row mbr-gallery mbr-masonry" … id="gallery2026">`, rename its id to `gallery<year>`, point the images at the new files, and number `data-slide-to` from 0.
2. Add `style="display: none;"` to the previous newest grid.
3. Put the new year at the front of the `years` list in the script at the bottom of the page.
4. Change the default heading under `id="galleryTitle"` to the new year.

The lightbox rebuilds itself from whichever year is showing, so it needs no editing. Links like `gallery.html#gallery2026` open that year directly.

### 4. Add a Recent Events card

In `index.html`, copy a card in the "Recent Events" section, then change the year, the photo and the link (`gallery.html#gallery<year>`).

### 5. Hero background video (optional)

The home page hero plays `assets/images/2026/py-2026-bg-landscape.mp4` on wide screens and `py-2026-bg-portrait.mp4` on phones, with `py-2026-bg-poster.jpg` as the still image shown before it plays. To replace it, make a short (about 15 s), silent, compressed version of the new clip:

```sh
# Wide version, 16:9 crop
ffmpeg -t 16 -i clip.mp4 -an -map_metadata -1 \
  -vf "crop=iw:iw*9/16:0:(ih-iw*9/16)*0.4,scale=1280:720,fps=24,format=yuv420p" \
  -c:v libx264 -preset slow -crf 30 -movflags +faststart py-<year>-bg-landscape.mp4

# Phone version (for a portrait clip)
ffmpeg -t 16 -i clip.mp4 -an -map_metadata -1 \
  -vf "scale=720:1280,fps=24,format=yuv420p" \
  -c:v libx264 -preset slow -crf 30 -movflags +faststart py-<year>-bg-portrait.mp4
```

Then update the `<video>` sources and `poster` at the top of `index.html`.

## License

The website code is MIT-licensed. Photos, videos, the logo and the written content are © Palkhi Yatra, all rights reserved. Bundled libraries keep their own licenses. See [LICENSE](LICENSE) for details.
