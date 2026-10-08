# LXGW WenKai webfonts

Vendored from `lxgw-wenkai-webfont@1.7.0`:
https://github.com/chawyehsu/lxgw-wenkai-webfont

Only the proportional Regular (400) and Bold (700) CSS and their WOFF2
subsets are included. Each face uses `font-display: swap` and
`unicode-range` so browsers download subsets needed for displayed text.
Keep the CSS beside the `files/` directory to preserve relative URLs.

The webfont package is licensed under MIT (`LICENSE`); the font is
licensed under SIL Open Font License 1.1 (`OFL.txt`). `VERSION` records
the upstream font version.

Font selection: `assets/css/extended/fonts.css`.
Stylesheet loading: `layouts/_partials/extend_head.html`.
