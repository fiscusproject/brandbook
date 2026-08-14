# CLAUDE.md — Fiscus brandbook

Public brand-asset repository for **Fiscus** (open-source, self-hosted fiscalization
service). Three documents split the roles: `README.md` is the human-facing brandbook
(story, palette, typography, usage rules), `TRADEMARK.md` is the binding usage policy,
and `../fiscus-context.md` — one level **above** this repo, not tracked in it — is the
full project context. Read that context doc before making any brand-level decision;
naming and logo-concept alternatives were researched and decided there. **One project,
one mark: never propose or generate alternative logo concepts** — the rejected list in
the context doc is closed.

## How the SVGs relate (the critical maintenance fact)

`logo/fiscus-mark.svg` (hand-written, 240×240) is the master. Every other SVG embeds a
full **inline copy** of the mark's geometry — scaled/positioned with a `transform`, each
with its own `clipPath` id (`w`, `f`, `ll`, `rl`, …). There is no `<use>` reference and
no build step. Consequence: **any edit to the master's geometry must be manually
propagated to all seven other SVGs**, then every PNG re-rendered.

Two derivatives diverge from the master **on purpose** — do not "fix" them:

- `logo/favicon.svg` drops the thin inner ring and thickens the remaining rings
  (field r=92; rings r=100/w=14 and r=112/w=10, vs. the master's r=91/w=4,
  r=99.5/w=13, r=110/w=8) so the seal survives at 16–32 px.
- `monochrome-mark/fiscus-mark-mono.svg` rebuilds the weave as a black/white `mask` so
  the whole mark renders in a single color (set via `style="color:…"`/`currentColor`).

Also intentional: the center vertical band is amber `#c38f24`, darker than the other
strands — the "golden thread" (see README). It is a feature, not an inconsistency.

## Regenerating PNGs

PNGs are committed derived artifacts. Never edit a PNG; re-render from its SVG and
commit both together. Use `rsvg-convert` (transparent background by default, correct
rasterization) — **not** plain ImageMagick on SVGs (fills white, worse renderer):

```bash
rsvg-convert -w 500  logo/fiscus-mark.svg           -o logo/fiscus-avatar-500-transparent.png
rsvg-convert -w 144  logo/fiscus-mark.svg           -o logo/fiscus-mark-144.png   # email signature, 72px @2x
rsvg-convert -w 1812 lockup/fiscus-lockup-light.svg -o lockup/fiscus-lockup-light-3x.png   # ×3 of 604×140; same for -dark
rsvg-convert banner/fiscus-readme-1280x320-light.svg -o banner/fiscus-readme-1280x320-light.png  # native size; same for -dark, social
rsvg-convert -w 16 logo/favicon.svg -o logo/favicon-16.png   # and -w 32
magick logo/favicon-16.png logo/favicon-32.png logo/favicon.ico
```

## Rules

- **File paths are an API.** Once public, assets get hotlinked via
  `raw.githubusercontent.com` from other repos and the docs site. Never rename or move
  an existing file; add new ones. Naming: `fiscus-<asset>[-<variant>][-<WxH|Nx>].<ext>`;
  `-3x` = 3× the SVG's nominal size; light/dark variants always ship as a pair.
- **Colors:** the five hexes in README's Palette table are canonical and exhaustive.
  New assets use exactly those values — no new colors, no recoloring.
- **Licensing:** do **not** add an open-source LICENSE file to this repo. The assets are
  deliberately *not* OSS-licensed; `TRADEMARK.md` governs all use. The project's code
  licenses (AGPLv3 server / Apache-2.0 SDKs) apply to the software repos only.
- Keep SVGs hand-written and readable (no editor metadata, no minification passes);
  keep `clipPath`/`mask` ids unique per file — they collide when SVGs are inlined into
  the same HTML page.
