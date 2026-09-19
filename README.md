# junieberry.github.io

Personal homepage / CV site for Junyoung Kim, styled after
[Jon Barron's academic homepage](https://github.com/jonbarron/jonbarron.github.io).

## Structure

- `index.html` — the page itself
- `stylesheet.css` — styling (Lato font, blue/orange links, 800px centered layout)
- `data/JunyoungKim-CV.pdf` — CV, linked from the page
- `images/` — put a profile photo here (e.g. `profile.jpg`) if you want one

## Editing

Just edit `index.html` directly — it's plain HTML, no build step. Preview locally by
opening the file in a browser, or run a tiny local server:

```bash
python3 -m http.server 8000
```

then visit http://localhost:8000.

## Publishing (GitHub Pages)

This repo is named `<username>.github.io`, so GitHub Pages serves it automatically
from the `main` branch at `https://<username>.github.io/` — no extra config needed.
Just commit and push changes to `main` and the live site updates within a minute or two.
