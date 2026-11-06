# REBUILD COMMAND: "Ochre" gallery hero with a gilded oval frame wrapped by an orange display headline

Build ONE file, `index.html`, that reproduces the page below exactly. It is a full-screen off-white gallery hero. A three-line, extra-condensed serif headline in orange ("THE EYE / THAT NEVER / SLUMBERS") wraps around a tilted, gold-framed oval painting of a roaring tiger. Some words pass **behind** the frame and others **in front** of it. An orange "Private Collection" panel is pinned to the bottom-left corner. On mouse devices the frame tilts gently toward the cursor and the headline layers drift at different depths. Every number in this command is measured. Do not interpret, restyle or "improve" anything.

**Assets: Path A (hosted).** All images are already on a CDN with CORS enabled. Use the URLs in section 7 exactly as written. Do not download, re-encode, rename or replace them.

**Fastest correct path:** the complete, verified source is in APPENDIX A. Output it verbatim. Sections 0-13 explain every value so you can check your output. If anything in a section seems to conflict with the appendix, the appendix wins.

---

## 0. GLOBAL RULES (non-negotiable)

- One `index.html`, no build step, no framework, no external JS library. Plain HTML + CSS + about 95 lines of vanilla JS.
- `<html lang="en">`. All visible text, `alt`, `aria-label` and code comments are English.
- Fonts come from Google Fonts with this exact URL (one `<link>`, plus two `preconnect` links):
  `https://fonts.googleapis.com/css2?family=Great+Vibes&family=Inter:opsz,wght@14..32,400..500&family=Noto+Serif+Display:wdth,wght@62.5,500;100,300&display=swap`
- Desktop has no scroll: `html.desk, html.desk body {height:100%; overflow:hidden}` and the hero is `position:fixed; inset:0`. Tablet and phone scroll normally.
- Use the individual CSS transform properties (`translate`, `scale`, `rotate`) everywhere. Never write `transform:`, with ONE exception: `.tilt`, which nothing animates in CSS, receives an inline `transform` from the cursor-depth script (section 10.1). The entrance keyframes, the float loop, the cursor script and the type fitter rely on these properties not colliding.

### 0.1 Tokens

| Token | Value | Use |
|---|---|---|
| `--bg` | `#FAF5F0` | page background (off-white) |
| `--accent` | `#F45D14` | headline, bottom panel |
| `--ink` | `#141414` | plus icon, logo, ENQUIRE |
| `--label` | `#78736E` | side labels |
| `--para` | `rgba(255,255,255,.8)` | panel paragraph + arrow |
| `--e-out` | `cubic-bezier(.16,1,.3,1)` | frame, slide-outs, nav, painting, arrow |
| `--e-rise` | `cubic-bezier(.19,1,.22,1)` | letter and line rises |
| `--e-curtain` | `cubic-bezier(.77,0,.18,1)` | bottom panel curtain |

## 1. FLUID LAYER WITH A SCALE FACTOR (desktop)

- Design canvas: **W = 1690, H = 1190** design px. All desktop coordinates below are in design px.
- Desktop mode applies when `innerWidth >= 1024 && innerWidth / innerHeight >= 1.1`. The JS adds `desk` to `<html>`; otherwise it adds `stack` (plus `phone` when width < 600).
- `k = min(innerWidth / 1690, innerHeight / 1190)`. JS sets `--k`, `--wd = innerWidth / k px` and `--hd = innerHeight / k px` on `<html>`.
- `.in` is `position:absolute; left:0; top:0; width:var(--wd); height:var(--hd); scale:var(--k); transform-origin:0 0`. It always fills the viewport exactly, so ultrawide screens get extra off-white on the sides and are never letterboxed.
- Inside `.in`:
  - `.nav` spans the full layer width (corners stick to the viewport edges).
  - `.comp` (the 1690 × 1190 composition) sits at `left: calc(50% - 845px); top: calc((var(--hd) - 1190px) / 2)`, centered both ways.
  - `.box` is `left:0; bottom:0`, glued to the bottom-left corner.
- Recompute on `resize` and via a `ResizeObserver` on `<body>`. The type fitter does NOT need to rerun on resize because its values are in design px.

## 1.5 COORDINATE TABLE (design px; final state after the entrance)

`.comp` coordinates are relative to the 1690 × 1190 composition. `.box` coordinates are relative to the box. Nav coordinates are relative to the layer's top-left (left items), top-center (logo) and top-right (right items).

| Element | x | y | w | h | z | Notes |
|---|---|---|---|---|---|---|
| Plus button | left 59 | top 48 | 35 | 35 | 5 | two 1.5px bars, `#141414` |
| Logo "Ochre" | ink center x = 845 (layer center) | baseline 88 | about 102 ink | about 46 ink | 5 | Great Vibes 54.3px, placed by the fitter |
| ENQUIRE | right 58 | top 53 | auto | 24 | 5 | Inter 500 24/24, tracking -0.04em |
| Label "FINE OIL / PORTRAIT" | right edge 422 (`right:1268px`) | top 362 | auto | 48 | 3 | Inter 400 24/24, -0.05em, right-aligned |
| Label "SINCE / 1908" | right edge 1367 (`right:323px`) | top 362 | auto | 48 | 3 | same, right-aligned |
| Headline "THE" (behind) | ink RIGHT = 779 | baseline 404 | about 337 ink | cap 182 | 1 | fitter `right 779 404` |
| Headline "EYE" (front) | ink LEFT = 900 | baseline 404 | about 306 ink | cap 182 | 3 | fitter `left 900 404` |
| Headline "THAT" (behind) | ink RIGHT = 696 | baseline 635 | about 450 ink | cap 182 | 1 | fitter `right 696 635` |
| Headline "NEVER" (front) | ink LEFT = 970 | baseline 635 | about 558 ink | cap 182 | 3 | fitter `left 970 635` |
| Headline "SLUMBERS" (front) | ink CENTER = 845 | baseline 867 | about 886 ink | cap 182 | 3 | fitter `center 845 867` |
| Oval frame image | 590 | 269 | 549 | 611 | 2 | 1040 × 1158 source, drop shadow |
| Bottom panel `.box` | 0 (left) | bottom 0 | 707 | 238 | 4 | `#F45D14` |
| Panel painting | 20 | 36 | 236 | 166.8 | - | 900 × 636 source |
| "Private" | ink left 279 | baseline 76 | about 147 ink | cap 33 | - | Noto Serif Display 300, 45.83px |
| "Collection" | ink left 457 | baseline 109 | about 204 ink | cap 33 | - | same |
| Paragraph (3 lines) | 278 | top 127.6 (baselines 145 / 168.5 / 192) | 420 | 70.5 | - | Inter 400 15.5/23.5, -0.03em |
| Arrow | 570 | 180 | 84 | 14 | - | 1.2px stroke, `--para` |

## 2. THE FRAME AND THE BEHIND/FRONT LAYERING (PIXEL CONTRACT, the fragile point)

This is what makes the page. The headline does not sit simply above or below the image: it **wraps** it.

- Stack order inside `.comp`: behind words `z-index:1` → `.frame` `z-index:2` → front words and labels `z-index:3`.
- **Behind the frame:** "THE" (line 1, left) and "THAT" (line 2, left). The frame's upper-left rim covers the lower right of the final "E" in THE. The last "T" of THAT is mostly hidden behind the frame's left rim, with only its left part showing.
- **In front of the frame:** "EYE" (line 1, right: the first E crosses the top rim), "NEVER" (line 2, right: the N and E sit over the painting and the right rim) and the whole of "SLUMBERS" (line 3: "UMBE" cross the bottom rim).
- The left words are anchored by their **right ink edge** and the right words by their **left ink edge**. These are the edges that touch the frame, so the overlap stays the same whatever the word widths are. "SLUMBERS" is centered on x = 845.
- Frame: `<figure class="frame">` at `left:590px; top:269px; width:549px; height:611px`. Inside it, `.float` (`inset:0`, `perspective:1400px`, carries the float loop) holds `.tilt` (`inset:0`, `will-change:transform`, driven by the cursor script). `.tilt` holds the `<img>` (100% × 100%, `filter: drop-shadow(14px 22px 26px rgba(70,35,12,.22))`), `.glint` and `.sheen`. Keep the three nested layers: `.frame` owns the entrance, `.float` owns the idle loop and `.tilt` owns the cursor, so none of them overwrites another.
- `.glint` is a gold light sweep clipped to the frame's own alpha: `mask: url(<frame URL>?mask) center/100% 100% no-repeat` (also `-webkit-mask`), `mix-blend-mode:screen`, `opacity:0` at rest. Its background is `linear-gradient(110deg, transparent 38%, rgba(255,238,200,0) 42%, rgba(255,238,200,.6) 50%, rgba(255,238,200,0) 58%, transparent 62%) 160% 0 / 300% 100% no-repeat`.
- `.sheen` is the cursor highlight, masked like `.glint` (same `?mask` URL), `mix-blend-mode:screen`, `opacity:0` at rest, `pointer-events:none`, background `radial-gradient(circle at var(--sx,50%) var(--sy,50%), rgba(255,238,200,.55), rgba(255,238,200,0) 38%)`.
- The mask URL has the `?mask` query on purpose. It gives the CORS-mode mask fetch its own cache entry, separate from the plain `<img>` fetch. Do NOT add `crossorigin` to the `<img>`: with it, the image breaks when the file is opened from disk, or when the browser cached a non-CORS copy.
- DO NOT put all words above the image. DO NOT put all words below it. DO NOT center the words of lines 1 and 2 as a single string with a normal space. Line 2 has a deliberately wide gap filled by the frame.
- DO NOT crop, mask into a circle or rotate the frame image at rest. It is already a cut-out with transparent corners, tilted in 3D.
- DO NOT add borders, outlines or extra shadows to the frame.

## 3. BRAND (PIXEL CONTRACT)

- The logo is a wordmark only (no icon), the word **Ochre** in Great Vibes 400 at 54.3px, `#141414`. On desktop it is placed by the fitter (`data-fit="center 845 88"`) at `left: calc(50% - 845px + var(--fx)); top: var(--fy)`. It is an `<a href="#" aria-label="Ochre">`.
- `<title>`: `Ochre - The eye that never slumbers`.
- Favicon (inline SVG data URI). The idea is a dab of ochre pigment with a highlight. Use exactly:
  `<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'><circle cx='16' cy='16' r='13' fill='#F45D14'/><circle cx='20.5' cy='11.5' r='4' fill='#FAF5F0'/></svg>`
- DO NOT redraw the favicon, add strokes or gradients, or replace the wordmark font.

## 4. HEADLINE (PIXEL CONTRACT)

- Every word is its own `<span class="w back|front">`, absolutely positioned at `left:var(--fx); top:var(--fy)`.
- Font: `font-family:"Noto Serif Display"; font-stretch:62.5%; font-weight:500; font-size:253.2px; line-height:1; color:#F45D14`. That gives a cap height of 182 design px.
- `scale: .82 1; transform-origin: 0 0`. The horizontal squeeze is part of the design (stem about 21px, hairline about 4px at cap 182). DO NOT remove it, and DO NOT use `transform: scaleX()`.
- Baselines: 404, 635 and 867 (line pitch about 231.5).
- `.w.back { clip-path: inset(-20% 0 -20% -40%) }`. The right edge is clipped so the word can slide out from behind the frame.
- `.w.front { clip-path: inset(-40% -10% 0 -10%) }`. The bottom edge is clipped so letters can rise from the baseline.
- Front words are split into one `<span class="l">` per letter (`display:inline-block`). Behind words hold one `<span class="wi">`.
- `<h1 class="hl" aria-label="The eye that never slumbers">` wraps the five spans, each `aria-hidden="true"`.

### 4.1 Type fitter (required)

After fonts load, for every `[data-fit="<anchor> <x> <baseline>"]`:
1. Set a 2D canvas context's `font` to `${fontStyle} ${fontWeight} ${fontSize} ${fontFamily}` from the computed style. Set `ctx.fontStretch` from the computed `font-stretch` (`62.5%`→`extra-condensed`, `100%`→`normal`). Set `ctx.fontKerning = 'none'` when the element contains `.l` letter spans (inline-blocks do not kern), otherwise `'normal'`.
2. `m = measureText(text)`, with `L = actualBoundingBoxLeft`, `R = actualBoundingBoxRight` and `sx = parseFloat(computed scale) || 1`.
3. `left = anchor=='left' ? x + L*sx : anchor=='right' ? x - R*sx : x - sx*(R-L)/2`.
4. `top = baseline - ((fontSize - (fontBoundingBoxAscent + fontBoundingBoxDescent)) / 2 + fontBoundingBoxAscent)`. This is valid because `line-height:1`.
5. Write `--fx` and `--fy` (px) on the element. Run once after fonts are ready, and again on `document.fonts.ready`.

## 5. LABELS AND NAV

- Labels: `<p class="lab">` with two `.ln` line masks each. Inter 400, 24px/24px, `letter-spacing:-.05em`, `#78736E`, `text-align:right`, uppercase text as written. They are `aria-hidden` and purely decorative. The last line's baseline (about 404) aligns with headline line 1.
- ENQUIRE: Inter 500, 24px/24px, `-.04em`, `#141414`, `<a href="#">`.
- Plus: `<button aria-label="Open menu">` containing a 35 × 35 span with two 1.5px bars offset 16.75px (so each bar is centered on 17.5px).

## 6. BOTTOM PANEL (PIXEL CONTRACT)

- `<section class="box" aria-labelledby="box-title">`: background `#F45D14`, 707 × 238, flush to the left and bottom edges, no radius, no shadow.
- The painting `<img>` sits at left 20, top 36, width 236 (height auto = 166.8).
- Title `<h2 id="box-title" aria-label="Private Collection">` has two `.tw` spans ("Private", "Collection"). Font: Noto Serif Display 300, `font-stretch:100%`, 45.83px, `line-height:1`, white. Each word is positioned by the fitter (`left 279 76` and `left 457 109`). The second word steps down and to the right; this offset is the design.
- Paragraph: three explicit `.ln` lines (text in section 8), Inter 400 15.5px/23.5px, `-.03em`, `rgba(255,255,255,.8)`, box at left 278, top 127.6, width 420. Keep the space before each line end so the words never glue together when lines rewrap on phones.
- Arrow `<a aria-label="View the collection">`: inline SVG, viewBox `0 0 84 14`, paths `M0 7H83` and `M76.5 .8 83 7l-6.5 6.2`, stroke `currentColor` 1.2, fill none, color `--para`. Placed at left 570, top 180, size 84 × 14.
- DO NOT round the panel, add padding that moves the coordinates, or change the title to a single line on desktop.

## 7. ASSETS (Path A, Cloudflare R2, CORS `*`)

| File | URL | Size | Use |
|---|---|---|---|
| Oval frame (cut-out, alpha) | `https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp` | 1040 × 1158 | `.frame img` (preloaded, `fetchpriority="high"`) |
| Same file as mask | `https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp?mask` | - | `.glint` mask only |
| Panel painting (cut-out, alpha) | `https://assets.layerweb.com.br/ochre/images/ochre-tiger-portrait.webp` | 900 × 636 | `.pt img` |

Fallback: `onerror` adds `.broken` to `.frame` or `.pt`. The image becomes `visibility:hidden`, so the box keeps its size (no layout shift). A broken frame shows a soft `#e9dccb` oval instead.

## 8. LITERAL TEXTS (English, exact)

- Nav: `ENQUIRE`; logo `Ochre`.
- Labels: `FINE OIL` / `PORTRAIT`, and `SINCE` / `1908`.
- Headline: `THE` `EYE` / `THAT` `NEVER` / `SLUMBERS`.
- Panel title: `Private` / `Collection`.
- Paragraph lines:
  1. `A QUIET ROOM OF PORTRAITS WHERE THE WILD`
  2. `LOOKS BACK AT YOU, PAINTED IN OIL AND HELD IN`
  3. `HAND-CARVED FRAMES OF OLD GOLD.`
- Alts: frame `Oil painting of a roaring tiger inside a round, hand-carved gilded frame`; painting `Oil portrait of a dark tiger in profile, framed in ornate carved gold`.

## 9. RESPONSIVE (explicit architectures)

### 9.1 Desktop (`desk`): width >= 1024 and aspect >= 1.1
The fluid layer from section 1. Checked at 2560 × 1080, 1920 × 1080, 1690 × 1190, 1440 × 900 and 1024 × 768.

### 9.2 Stack (`stack`): tablet, any other size (checked at 820 × 1180)
- `--g` (side gutter) = 32px; `--k2 = (body.clientWidth - 2*g) / 1390`.
- `.in` becomes a flex column with `min-height:100svh` and padding `0 g`.
- Nav: flex row, height 72, margin-bottom 16. Plus icon 22 × 22. Logo 40px, centered with `left:0; right:0; width:max-content; margin:0 auto; top:calc(50% - .353em)`. ENQUIRE 14px/14px, -0.02em. Do not center the logo with `translate`: the entrance animation owns that property.
- `.win`: width 100%, height `700 * k2`, `margin-top:auto`. `.comp` is shown through it: `left: -150*k2; top: -200*k2; scale: k2; transform-origin: 0 0`. That is a crop of design x 150-1540 and y 200-900. The in-composition labels are hidden.
- `.labs-row`: flex, space-between, margin `18px 0 auto`. The two labels sit at 13px/14px, -0.03em; the first is left-aligned, the second right-aligned.
- `.box`: grid `38% 1fr`, gap 24, align center, margin `40px -g 0` (full bleed), padding `28px g 32px`. Title 40px; "Collection" gets `padding-left:3.88em; margin-top:-.28em`. Paragraph 13px/19px, -0.02em, margin-top 16, max-width 46ch. Arrow width 64, margin-top 12.

### 9.3 Phone (`stack phone`, width < 600; checked at 375 × 812)
Same as 9.2 with `g` = 16, logo 34px, box grid of one column (gap 20, padding-top 24), painting `width:min(78%,300px)`, title 34px.

## 10. ENTRANCE TIMELINE AND LOOPS

Every animated element has class `.a` with `animation-fill-mode:both; animation-play-state:paused; animation-delay:var(--d)`. `html.go .a` sets `running`. `go` is added in the `requestAnimationFrame` after the fitter runs, once fonts load (max 1.5s wait; each `document.fonts.load()` has its own `.catch`).

| t (s) | Element | Keyframes | Duration / easing |
|---|---|---|---|
| 0.10 | `.frame` | `clip-path: circle(0% at 50% 50%)`, `scale 1.28`, `rotate -12deg`, `opacity 0`, `blur(12px)` → 45% `opacity 1` → `circle(100%)`, `scale 1`, `rotate 0`, `blur(0)` | 1.6s `--e-out` |
| 0.65 / 0.75 | `.wi` THE / THAT | `translate: 105% 0 → 0` (emerges from behind the frame through the right-edge clip) | 1.4s `--e-out` |
| 0.90 + 0.045·i | EYE letters | `translate: 0 105% → 0` | 1.1s `--e-rise` |
| 1.00 + 0.045·i | NEVER letters | same | same |
| 1.10 + 0.045·i | SLUMBERS letters | same | same |
| 1.35 | `.glint` | background-position `160% → -60%`, opacity 0 → 1 (15%) → 1 (85%) → 0 | 1.2s `cubic-bezier(.45,0,.25,1)` |
| 1.45 | `.box` | `clip-path: inset(100% 0 0 0) → inset(0)` | 1.0s `--e-curtain` |
| 1.50 / 1.58 / 1.66 | plus / logo / ENQUIRE (class `.drop`) | `opacity 0, translate 0 -14px → 1, 0` | 0.9s `--e-out` |
| 1.60, 1.68 / 1.64, 1.72 | label lines L / R | rise `translate: 0 105% → 0` inside `.ln` (overflow clip) | 0.9s `--e-rise` |
| 1.95 | `.pt` painting | `opacity 0, scale 1.12, translate 0 18px → 1, 1, 0` | 1.1s `--e-out` |
| 2.05 / 2.15 | Private / Collection | rise inside `.tw` | 0.9s `--e-rise` |
| 2.25 / 2.33 / 2.41 | paragraph lines | rise inside `.ln` | 0.9s `--e-rise` |
| 2.60 | arrow | `scale: 0 1 → 1 1`, origin left | 0.8s `--e-out` |
| 2.00 → ∞ | `.float` | `translate 0 0, rotate 0 ↔ translate 0 -8px, rotate .8deg` | 7s ease-in-out, infinite alternate |

Total entrance: about 3.4s. The frame is the hero of the sequence. The behind-words slide outward from the frame as if they had been hiding behind it, and the front words rise letter by letter over it.

### 10.1 Cursor depth (after the entrance)

Runs only when `matchMedia('(hover: hover) and (pointer: fine)')` matches AND reduced motion is off. It starts 2900 ms after the fonts-ready moment (`setTimeout` set right where `go` is added), so the cursor never fights the entrance.

- Pointer, normalised in composition design px: `X = clamp(((clientX - comp.left) / s - 845) / 845)`, `Y = clamp(((clientY - comp.top) / s - 574) / 600)`, with `s = comp.getBoundingClientRect().width / 1690` and clamp to [-1, 1]. When the pointer leaves the document (`pointerleave` on `<html>`) or the window blurs, the targets become 0.
- Smoothing per animation frame: `x += (target - x) * .08`, `y += (target - y) * .08`, `on += ((inside ? 1 : 0) - on) * .06`. Skip the frame when the summed difference is below `.0005` (idle costs nothing).
- `.tilt`: `transform: translate3d(X*10px, Y*6px, 0) rotateY(X*7deg) rotateX(-Y*6deg)`.
- `.sheen`: `opacity = on * .8`, `--sx = 50 + X*28 %`, `--sy = 50 + Y*28 %`.
- Headline words (`translate` on each `.w`): behind words depth -1, front words depth 2, so the offset is `(X*8*depth px, Y*5*depth px)`. Behind words drift against the pointer and front words go with it, twice as far, which makes the wrap around the frame read in 3D.
- Side labels in `.comp` (`translate` on `.lab`): depth .625, giving `(X*5px, Y*3.1px)`.
- Maximum travel at a corner: frame 10 × 6 px and 7° / 6°; front words 16 × 10 px; behind words 8 × 5 px.
- DO NOT increase these amounts, add spring overshoot, move the nav or the panel, or enable this on touch devices.

## 11. ACCESSIBILITY

- `@media (prefers-reduced-motion: reduce) { .a, .go .a { animation: none !important } }`: everything shows in its final state, the float loop is off and the cursor depth never starts. The glint stays invisible (`opacity:0` at rest).
- One `<h1>` and one `<h2>` with `aria-label`s carrying the readable sentence; the split visual spans are `aria-hidden`.
- The menu button and the arrow link have `aria-label`s; the images have descriptive `alt`s.

## 12. GLOBAL "DO NOT" LIST

- DO NOT change the colors, fonts, sizes, coordinates or texts.
- DO NOT use `transform:` (use `translate` / `scale` / `rotate`); the only exception is the inline transform on `.tilt`.
- DO NOT add the two extra framed paintings some gallery mockups show at the top and bottom-right. This page has only the oval frame and the panel painting.
- DO NOT add `crossorigin` to the `<img>` tags, and DO NOT drop the `?mask` query.
- DO NOT replace the images with placeholders, gradients or other images.
- DO NOT center the logo or anything else with `translate` (the entrance owns it).
- DO NOT add scroll (on desktop), hover effects other than the cursor depth of 10.1, a custom cursor or any library.

## 13. SELF-CHECK (run before answering)

1. At 1690 × 1190: the frame box is 549 × 611 at (590, 269); the panel is 707 × 238 at the bottom-left; ENQUIRE's right edge is 58px from the viewport edge.
2. Headline cap height = 182 px at k = 1; baselines at 404 / 635 / 867.
3. THE and THAT render UNDER the frame; EYE, NEVER and SLUMBERS render OVER it. Check that the last T of THAT is mostly hidden and that "UMBE" crosses the frame's bottom rim.
4. At 2560 × 1080 the page fills the width (no side bands); the nav corners and the panel touch the viewport edges.
5. At 820 × 1180 and 375 × 812 the stacked layout shows: nav, composition, label row, full-bleed orange panel. No horizontal scroll.
6. The four font faces load (`document.fonts.check` true for Noto Serif Display 500 and 300, Inter 400/500, Great Vibes). Both images load with status 200, the `?mask` request returns 200, and the console has no 404s or CORS errors.
7. The entrance plays once in about 3.4s; then the frame floats. With a mouse, after about 2.9s, moving to the top-right corner tilts the frame toward it, lights its upper-right rim and shifts EYE / NEVER / SLUMBERS up-right while THE / THAT shift down-left; leaving the window eases everything back to rest.
8. With reduced motion everything is visible immediately and nothing follows the cursor.

---

## APPENDIX A: complete `index.html` (output verbatim)

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Ochre - The eye that never slumbers</title>
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'%3E%3Ccircle cx='16' cy='16' r='13' fill='%23F45D14'/%3E%3Ccircle cx='20.5' cy='11.5' r='4' fill='%23FAF5F0'/%3E%3C/svg%3E">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Great+Vibes&family=Inter:opsz,wght@14..32,400..500&family=Noto+Serif+Display:wdth,wght@62.5,500;100,300&display=swap" rel="stylesheet">
<link rel="preload" as="image" href="https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp">
<style>
  /* ---------- tokens ---------- */
  :root{
    --bg:#FAF5F0; --accent:#F45D14; --ink:#141414; --label:#78736E;
    --para:rgba(255,255,255,.8);
    --e-out:cubic-bezier(.16,1,.3,1); --e-rise:cubic-bezier(.19,1,.22,1); --e-curtain:cubic-bezier(.77,0,.18,1);
    --k:1; --k2:.25;
  }
  *,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
  html,body{background:var(--bg)}
  body{font-family:"Inter",system-ui,sans-serif;color:var(--ink);-webkit-font-smoothing:antialiased;overflow-x:hidden}
  a{color:inherit;text-decoration:none}
  button{font:inherit;color:inherit;background:none;border:0;cursor:pointer}
  img{display:block}

  /* ---------- shared pieces ---------- */
  .plus{position:relative;display:block;width:35px;height:35px}
  .plus::before,.plus::after{content:"";position:absolute;background:var(--ink)}
  .plus::before{left:0;top:16.75px;width:35px;height:1.5px}
  .plus::after{left:16.75px;top:0;width:1.5px;height:35px}
  .logo{font-family:"Great Vibes",cursive;font-weight:400;font-size:54.3px;line-height:1;color:var(--ink);white-space:nowrap}
  .cta{font-weight:500;font-size:24px;line-height:24px;letter-spacing:-.04em;color:var(--ink)}
  .lab{font-weight:400;font-size:24px;line-height:24px;letter-spacing:-.05em;color:var(--label);text-align:right;white-space:nowrap}

  /* composition (design px, 1690 x 1190) */
  .comp{position:absolute;width:1690px;height:1190px;pointer-events:none}
  .hl{position:absolute;inset:0;font-weight:inherit}
  .w{position:absolute;left:var(--fx,0);top:var(--fy,0);display:block;white-space:nowrap;
     font-family:"Noto Serif Display",serif;font-stretch:62.5%;font-weight:500;font-size:253.2px;line-height:1;
     color:var(--accent);scale:.82 1;transform-origin:0 0}
  .w.back{z-index:1;clip-path:inset(-20% 0 -20% -40%)}
  .w.front{z-index:3;clip-path:inset(-40% -10% 0 -10%)}
  .w .wi,.w .l{display:inline-block}
  .frame{position:absolute;left:590px;top:269px;width:549px;height:611px;z-index:2}
  .float{position:absolute;inset:0}
  .float img{width:100%;height:100%;filter:drop-shadow(14px 22px 26px rgba(70,35,12,.22))}
  .glint{position:absolute;inset:0;opacity:0;mix-blend-mode:screen;
     background:linear-gradient(110deg,transparent 38%,rgba(255,238,200,0) 42%,rgba(255,238,200,.6) 50%,rgba(255,238,200,0) 58%,transparent 62%) 160% 0/300% 100% no-repeat;
     -webkit-mask:url("https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp?mask") center/100% 100% no-repeat;
             mask:url("https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp?mask") center/100% 100% no-repeat}
  /* cursor depth layers (driven by JS, never animated by CSS) */
  .float{perspective:1400px}
  .tilt{position:absolute;inset:0;will-change:transform}
  .sheen{position:absolute;inset:0;opacity:0;mix-blend-mode:screen;pointer-events:none;
     background:radial-gradient(circle at var(--sx,50%) var(--sy,50%),rgba(255,238,200,.55),rgba(255,238,200,0) 38%);
     -webkit-mask:url("https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp?mask") center/100% 100% no-repeat;
             mask:url("https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp?mask") center/100% 100% no-repeat}
  .frame.broken img{visibility:hidden}
  .frame.broken .float{border-radius:50%;background:radial-gradient(closest-side,#e9dccb 92%,transparent 93%)}
  .comp .lab{position:absolute;top:362px;z-index:3}
  .lab-l{right:1268px}
  .lab-r{right:323px}
  .ln{display:block;overflow:clip}
  .ln>span{display:block}

  /* bottom box */
  .box{background:var(--accent);color:#fff}
  .pt img{width:100%;height:auto}
  .pt.broken img{visibility:hidden}
  .ttl{font-family:"Noto Serif Display",serif;font-weight:300;font-stretch:100%;font-size:45.83px;line-height:1;color:#fff;white-space:nowrap}
  .ttl .tw{display:block;overflow:clip}
  .ttl .tw>span{display:block}
  .para{font-weight:400;font-size:15.5px;line-height:23.5px;letter-spacing:-.03em;color:var(--para)}
  .arrow{display:block;width:84px;height:14px;color:var(--para);transform-origin:0 50%}

  /* ---------- DESKTOP: fluid layer scaled by k ---------- */
  html.desk,html.desk body{height:100%;overflow:hidden}
  .desk .hero{position:fixed;inset:0;overflow:hidden}
  .desk .in{position:absolute;left:0;top:0;width:var(--wd);height:var(--hd);scale:var(--k);transform-origin:0 0}
  .desk .nav{position:absolute;left:0;top:0;width:100%;height:130px;z-index:5}
  .desk .nav .plus-btn{position:absolute;left:59px;top:48px}
  .desk .nav .logo{position:absolute;left:calc(50% - 845px + var(--fx,0px));top:var(--fy,0)}
  .desk .nav .cta{position:absolute;right:58px;top:53px}
  .desk .win{display:contents}
  .desk .comp{left:calc(50% - 845px);top:calc((var(--hd) - 1190px) / 2)}
  .desk .labs-row{display:none}
  .desk .box{position:absolute;left:0;bottom:0;width:707px;height:238px;z-index:4}
  .desk .pt{position:absolute;left:20px;top:36px;width:236px}
  .desk .ttl .tw{position:absolute;left:var(--fx,0);top:var(--fy,0)}
  .desk .para{position:absolute;left:278px;top:127.6px;width:420px}
  .desk .arrow{position:absolute;left:570px;top:180px}

  /* ---------- STACK: tablet + phone, page flows ---------- */
  .stack .in{position:relative;display:flex;flex-direction:column;min-height:100vh;min-height:100svh;padding:0 var(--g) 0}
  .stack .nav{position:relative;display:flex;align-items:center;justify-content:space-between;height:72px;flex:none;margin-bottom:16px}
  .stack .plus{width:22px;height:22px}
  .stack .plus::before{top:10.25px;width:22px}
  .stack .plus::after{left:10.25px;height:22px}
  .stack .nav .logo{position:absolute;left:0;right:0;width:max-content;margin:0 auto;top:calc(50% - .353em);font-size:40px}
  .stack.phone .nav .logo{font-size:34px}
  .stack .nav .cta{font-size:14px;line-height:14px;letter-spacing:-.02em}
  .stack .win{position:relative;width:100%;height:calc(700px * var(--k2));margin-top:auto;flex:none}
  .stack .comp{left:calc(-150px * var(--k2));top:calc(-200px * var(--k2));scale:var(--k2);transform-origin:0 0}
  .stack .comp .lab{display:none}
  .stack .labs-row{display:flex;justify-content:space-between;margin:18px 0 auto}
  .stack .labs-row .lab{font-size:13px;line-height:14px;letter-spacing:-.03em}
  .stack .labs-row .lab:first-child{text-align:left}
  .stack .box{position:relative;display:grid;grid-template-columns:38% 1fr;gap:24px;align-items:center;
     margin:40px calc(var(--g) * -1) 0;padding:28px var(--g) 32px}
  .stack .ttl{font-size:40px}
  .stack .ttl .tw+.tw{padding-left:3.88em;margin-top:-.28em}
  .stack .para{font-size:13px;line-height:19px;letter-spacing:-.02em;margin-top:16px;max-width:46ch}
  .stack .arrow{width:64px;margin-top:12px}
  .stack.phone .box{grid-template-columns:1fr;gap:20px;padding-top:24px}
  .stack.phone .pt{width:min(78%,300px)}
  .stack.phone .ttl{font-size:34px}

  /* ---------- entrance (paused until html.go) ---------- */
  .a{animation-duration:1s;animation-fill-mode:both;animation-play-state:paused;animation-delay:var(--d,0s)}
  .go .a{animation-play-state:running}
  .frame.a{animation-name:frameIn;animation-duration:1.6s;animation-timing-function:var(--e-out)}
  .float.a{animation:float 7s ease-in-out var(--d) infinite alternate both;animation-play-state:paused}
  .go .float.a{animation-play-state:running}
  .glint.a{animation-name:glint;animation-duration:1.2s;animation-timing-function:cubic-bezier(.45,0,.25,1)}
  .wi.a{animation-name:slideOut;animation-duration:1.4s;animation-timing-function:var(--e-out)}
  .l.a{animation-name:rise;animation-duration:1.1s;animation-timing-function:var(--e-rise)}
  .ln>span.a,.tw>span.a{animation-name:rise;animation-duration:.9s;animation-timing-function:var(--e-rise)}
  .drop.a{animation-name:drop;animation-duration:.9s;animation-timing-function:var(--e-out)}
  .box.a{animation-name:curtain;animation-duration:1s;animation-timing-function:var(--e-curtain)}
  .pt.a{animation-name:paint;animation-duration:1.1s;animation-timing-function:var(--e-out)}
  .arrow.a{animation-name:draw;animation-duration:.8s;animation-timing-function:var(--e-out)}
  @keyframes frameIn{
    from{clip-path:circle(0% at 50% 50%);scale:1.28;rotate:-12deg;opacity:0;filter:blur(12px)}
    45%{opacity:1}
    to{clip-path:circle(100% at 50% 50%);scale:1;rotate:0deg;opacity:1;filter:blur(0)}}
  @keyframes float{from{translate:0 0;rotate:0deg}to{translate:0 -8px;rotate:.8deg}}
  @keyframes glint{from{background-position:160% 0;opacity:0}15%{opacity:1}85%{opacity:1}to{background-position:-60% 0;opacity:0}}
  @keyframes slideOut{from{translate:105% 0}to{translate:0 0}}
  @keyframes rise{from{translate:0 105%}to{translate:0 0}}
  @keyframes drop{from{opacity:0;translate:0 -14px}to{opacity:1;translate:0 0}}
  @keyframes curtain{from{clip-path:inset(100% 0 0 0)}to{clip-path:inset(0 0 0 0)}}
  @keyframes paint{from{opacity:0;scale:1.12;translate:0 18px}to{opacity:1;scale:1;translate:0 0}}
  @keyframes draw{from{scale:0 1}to{scale:1 1}}
  @media (prefers-reduced-motion:reduce){
    .a,.go .a{animation:none!important}
  }
</style>
</head>
<body>
<main class="hero">
  <div class="in">
    <header class="nav">
      <button class="plus-btn drop a" style="--d:1.5s" aria-label="Open menu"><span class="plus"></span></button>
      <a class="logo drop a" style="--d:1.58s" href="#" aria-label="Ochre" data-fit="center 845 88">Ochre</a>
      <a class="cta drop a" style="--d:1.66s" href="#">ENQUIRE</a>
    </header>

    <div class="win">
      <div class="comp">
        <p class="lab lab-l" aria-hidden="true"><span class="ln"><span class="a" style="--d:1.6s">FINE OIL</span></span><span class="ln"><span class="a" style="--d:1.68s">PORTRAIT</span></span></p>
        <p class="lab lab-r" aria-hidden="true"><span class="ln"><span class="a" style="--d:1.64s">SINCE</span></span><span class="ln"><span class="a" style="--d:1.72s">1908</span></span></p>

        <h1 class="hl" aria-label="The eye that never slumbers">
          <span class="w back" aria-hidden="true" data-fit="right 779 404"><span class="wi a" style="--d:.65s">THE</span></span>
          <span class="w back" aria-hidden="true" data-fit="right 696 635"><span class="wi a" style="--d:.75s">THAT</span></span>
          <span class="w front" aria-hidden="true" data-fit="left 900 404"><span class="l a" style="--d:.9s">E</span><span class="l a" style="--d:.945s">Y</span><span class="l a" style="--d:.99s">E</span></span>
          <span class="w front" aria-hidden="true" data-fit="left 970 635"><span class="l a" style="--d:1s">N</span><span class="l a" style="--d:1.045s">E</span><span class="l a" style="--d:1.09s">V</span><span class="l a" style="--d:1.135s">E</span><span class="l a" style="--d:1.18s">R</span></span>
          <span class="w front" aria-hidden="true" data-fit="center 845 867"><span class="l a" style="--d:1.1s">S</span><span class="l a" style="--d:1.145s">L</span><span class="l a" style="--d:1.19s">U</span><span class="l a" style="--d:1.235s">M</span><span class="l a" style="--d:1.28s">B</span><span class="l a" style="--d:1.325s">E</span><span class="l a" style="--d:1.37s">R</span><span class="l a" style="--d:1.415s">S</span></span>
        </h1>

        <figure class="frame a" style="--d:.1s">
          <div class="float a" style="--d:2s">
            <div class="tilt">
              <img src="https://assets.layerweb.com.br/ochre/images/ochre-frame-tiger.webp" width="1040" height="1158"
                   alt="Oil painting of a roaring tiger inside a round, hand-carved gilded frame" fetchpriority="high"
                   onerror="this.closest('.frame').classList.add('broken')">
              <div class="glint a" style="--d:1.35s"></div>
              <div class="sheen"></div>
            </div>
          </div>
        </figure>
      </div>
    </div>

    <div class="labs-row" aria-hidden="true">
      <p class="lab">FINE OIL<br>PORTRAIT</p>
      <p class="lab">SINCE<br>1908</p>
    </div>

    <section class="box a" style="--d:1.45s" aria-labelledby="box-title">
      <div class="pt a" style="--d:1.95s">
        <img src="https://assets.layerweb.com.br/ochre/images/ochre-tiger-portrait.webp" width="900" height="636"
             alt="Oil portrait of a dark tiger in profile, framed in ornate carved gold" loading="eager"
             onerror="this.parentNode.classList.add('broken')">
      </div>
      <div class="txt">
        <h2 class="ttl" id="box-title" aria-label="Private Collection">
          <span class="tw" aria-hidden="true" data-fit="left 279 76"><span class="a" style="--d:2.05s">Private</span></span>
          <span class="tw" aria-hidden="true" data-fit="left 457 109"><span class="a" style="--d:2.15s">Collection</span></span>
        </h2>
        <p class="para">
          <span class="ln"><span class="a" style="--d:2.25s">A QUIET ROOM OF PORTRAITS WHERE THE WILD </span></span>
          <span class="ln"><span class="a" style="--d:2.33s">LOOKS BACK AT YOU, PAINTED IN OIL AND HELD IN </span></span>
          <span class="ln"><span class="a" style="--d:2.41s">HAND-CARVED FRAMES OF OLD GOLD.</span></span>
        </p>
        <a class="arrow a" style="--d:2.6s" href="#" aria-label="View the collection">
          <svg viewBox="0 0 84 14" width="100%" height="100%" fill="none" stroke="currentColor" stroke-width="1.2" aria-hidden="true">
            <path d="M0 7H83"/><path d="M76.5 .8 83 7l-6.5 6.2"/>
          </svg>
        </a>
      </div>
    </section>
  </div>
</main>

<script>
(() => {
  const W = 1690, H = 1190, CW = 1390;
  const root = document.documentElement;

  /* ---- layout mode + scale factors ---- */
  let lastW = 0;
  function layout() {
    const vw = innerWidth, vh = innerHeight;
    lastW = vw;
    const desk = vw >= 1024 && vw / vh >= 1.1;
    root.classList.toggle('desk', desk);
    root.classList.toggle('stack', !desk);
    root.classList.toggle('phone', !desk && vw < 600);
    if (desk) {
      const k = Math.min(vw / W, vh / H);
      root.style.setProperty('--k', k);
      root.style.setProperty('--wd', vw / k + 'px');
      root.style.setProperty('--hd', vh / k + 'px');
    } else {
      const g = vw < 600 ? 16 : 32;
      root.style.setProperty('--g', g + 'px');
      root.style.setProperty('--k2', (document.body.clientWidth - 2 * g) / CW);
    }
  }
  layout();
  addEventListener('resize', layout);
  new ResizeObserver(() => { if (innerWidth !== lastW) layout(); }).observe(document.body);

  /* ---- type fitter: places each [data-fit] by its ink box (design px) ---- */
  const STRETCH = { '62.5%': 'extra-condensed', '75%': 'condensed', '87.5%': 'semi-condensed', '100%': 'normal' };
  function fit() {
    const c = document.createElement('canvas').getContext('2d');
    document.querySelectorAll('[data-fit]').forEach(el => {
      const [anchor, xs, bs] = el.dataset.fit.split(' ');
      const x = +xs, base = +bs;
      const cs = getComputedStyle(el);
      const sx = parseFloat(cs.scale) || 1;
      c.font = `${cs.fontStyle} ${cs.fontWeight} ${cs.fontSize} ${cs.fontFamily}`;
      if ('fontStretch' in c) c.fontStretch = STRETCH[cs.fontStretch] || 'normal';
      if ('fontKerning' in c) c.fontKerning = el.querySelector('.l') ? 'none' : 'normal';
      const m = c.measureText(el.textContent.trim());
      const L = m.actualBoundingBoxLeft, R = m.actualBoundingBoxRight;
      const left = anchor === 'left' ? x + L * sx : anchor === 'right' ? x - R * sx : x - sx * (R - L) / 2;
      const fs = parseFloat(cs.fontSize);
      const top = base - ((fs - (m.fontBoundingBoxAscent + m.fontBoundingBoxDescent)) / 2 + m.fontBoundingBoxAscent);
      el.style.setProperty('--fx', left + 'px');
      el.style.setProperty('--fy', top + 'px');
    });
  }

  /* ---- cursor depth: the frame tilts toward the pointer, headline layers drift (fine pointers only) ---- */
  let depthLive = false;
  if (matchMedia('(hover: hover) and (pointer: fine)').matches && !matchMedia('(prefers-reduced-motion: reduce)').matches) {
    const comp = document.querySelector('.comp');
    const tilt = document.querySelector('.tilt');
    const sheen = document.querySelector('.sheen');
    // depth: behind words drift against the pointer, front words with it (twice as far), labels a little
    const layers = [...document.querySelectorAll('.w')].map(el => ({ el, d: el.classList.contains('back') ? -1 : 2 }))
      .concat([...document.querySelectorAll('.comp .lab')].map(el => ({ el, d: .625 })));
    const ptr = { x: 0, y: 0, inside: false }, cur = { x: 0, y: 0, on: 0 };
    const clamp = v => Math.max(-1, Math.min(1, v));
    addEventListener('pointermove', e => {
      const r = comp.getBoundingClientRect(), s = r.width / W;
      ptr.x = clamp(((e.clientX - r.left) / s - 845) / 845);
      ptr.y = clamp(((e.clientY - r.top) / s - 574) / 600);
      ptr.inside = true;
    });
    root.addEventListener('pointerleave', () => { ptr.inside = false; });
    addEventListener('blur', () => { ptr.inside = false; });
    const tick = () => {
      requestAnimationFrame(tick);
      if (!depthLive) return;
      const tx = ptr.inside ? ptr.x : 0, ty = ptr.inside ? ptr.y : 0, to = ptr.inside ? 1 : 0;
      if (Math.abs(tx - cur.x) + Math.abs(ty - cur.y) + Math.abs(to - cur.on) < .0005) return;
      cur.x += (tx - cur.x) * .08;
      cur.y += (ty - cur.y) * .08;
      cur.on += (to - cur.on) * .06;
      const X = cur.x, Y = cur.y;
      tilt.style.transform = `translate3d(${X * 10}px,${Y * 6}px,0) rotateY(${X * 7}deg) rotateX(${-Y * 6}deg)`;
      sheen.style.opacity = (cur.on * .8).toFixed(3);
      sheen.style.setProperty('--sx', 50 + X * 28 + '%');
      sheen.style.setProperty('--sy', 50 + Y * 28 + '%');
      layers.forEach(l => { l.el.style.translate = `${X * 8 * l.d}px ${Y * 5 * l.d}px`; });
    };
    requestAnimationFrame(tick);
  }

  /* ---- start after fonts (max 1.5 s) ---- */
  const fonts = ['500 253px "Noto Serif Display"', '300 46px "Noto Serif Display"', '400 54px "Great Vibes"', '400 24px "Inter"', '500 24px "Inter"']
    .map(f => document.fonts.load(f).catch(() => null));
  Promise.race([Promise.all(fonts), new Promise(r => setTimeout(r, 1500))]).then(() => {
    fit();
    requestAnimationFrame(() => root.classList.add('go'));
    setTimeout(() => { depthLive = true; }, 2900); // the cursor takes over once the entrance has settled
  });
  document.fonts.ready.then(fit);
})();
</script>
</body>
</html>
```
