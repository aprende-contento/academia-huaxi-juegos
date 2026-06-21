# Academia Huaxi Juegos — CLAUDE.md

## Project overview

Educational games platform for kids learning math and Chinese Mandarin, in Spanish. Three categories: Matemáticas, Chino Mandarín, and Misceláneos. Each category has game pages that start as placeholders and get built out over time.

**GitHub:** https://github.com/aprende-contento/academia-huaxi-juegos
**Language:** Spanish (es) throughout — all UI copy, labels, descriptions, and navigation must be in Spanish.

---

## Git workflow

**Always ask before committing or pushing.** Never run `git commit` or `git push` without explicit user approval. Show a summary of changes and ask "¿listo para hacer commit y push?" first.

---

## Design system

Every page in this repo — the hub (`index.html`) and all game pages under `games/` — must use the same visual identity exactly. No exceptions. Do not introduce new colors, fonts, border-radius values, or layout patterns that are not in this spec.

### Color tokens (CSS custom properties)

```css
:root {
  --bg: #F4EFFF;               /* Light lavender — page background */
  --surface: #FFFFFF;           /* Card / panel background */
  --math: #D94F30;              /* Matemáticas accent (deep coral) */
  --math-glow: rgba(217, 79, 48, 0.15);
  --chinese: #1E9A92;           /* Chino accent (deep teal) */
  --chinese-glow: rgba(30, 154, 146, 0.15);
  --misc: #B8900A;              /* Misceláneos accent (deep gold) */
  --misc-glow: rgba(184, 144, 10, 0.15);
  --text: #1A0F3C;              /* Primary text (dark on light bg) */
  --text-muted: #6B5B95;        /* Secondary / label text */
  --font-display: 'Baloo 2', system-ui, sans-serif;
  --font-body: 'Nunito', system-ui, sans-serif;
}
```

### Typography

Always load both Google Fonts in the `<head>`:

```html
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@400;600;800&family=Nunito:wght@400;600;700&display=swap" rel="stylesheet">
```

- **Display / headings:** `Baloo 2`, weight 800. Used for page titles, game titles, section headings.
- **Body / UI:** `Nunito`, weight 400/600/700. Used for descriptions, labels, nav links, body copy.
- Never use system fonts alone or introduce any other typeface.

### Background atmosphere (required on every page)

Every page must include the animated starfield and the three ambient color orbs. Copy this HTML structure into `<body>` before `<nav>`:

```html
<div class="orbs" aria-hidden="true">
  <div class="orb orb-1"></div>
  <div class="orb orb-2"></div>
  <div class="orb orb-3"></div>
</div>
<div id="stars" aria-hidden="true"></div>
```

And this JS at the end of `<body>`:

```js
const starsEl = document.getElementById('stars');
for (let i = 0; i < 140; i++) {
  const s = document.createElement('div');
  s.className = 'star';
  const size = Math.random() * 2.5 + 0.5;
  s.style.cssText = [
    `left:${Math.random()*100}%`,
    `top:${Math.random()*100}%`,
    `width:${size}px`,
    `height:${size}px`,
    `--d:${2 + Math.random()*5}s`,
    `--o:${0.3 + Math.random()*0.65}`,
    `animation-delay:${Math.random()*7}s`
  ].join(';');
  starsEl.appendChild(s);
}
```

CSS for stars and orbs (include in every page):

```css
#stars {
  position: fixed; top: 0; left: 0;
  width: 100%; height: 100%;
  pointer-events: none; z-index: 0;
}
.star {
  position: absolute; border-radius: 50%; background: rgba(107, 91, 149, 0.45);
  animation: twinkle var(--d, 3s) ease-in-out infinite; opacity: 0;
}
@keyframes twinkle {
  0%, 100% { opacity: 0; }
  50% { opacity: var(--o, 0.7); }
}
.orbs {
  position: fixed; top: 0; left: 0;
  width: 100%; height: 100%;
  pointer-events: none; z-index: 0;
}
.orb {
  position: absolute; border-radius: 50%;
  filter: blur(90px);
  animation: drift 22s ease-in-out infinite;
  opacity: 0.14;
}
.orb-1 { width: 500px; height: 500px; background: var(--math); top: -10%; left: -8%; animation-delay: 0s; }
.orb-2 { width: 600px; height: 600px; background: var(--chinese); bottom: -15%; right: -12%; animation-delay: -8s; }
.orb-3 { width: 350px; height: 350px; background: var(--misc); top: 35%; left: 48%; animation-delay: -16s; }
@keyframes drift {
  0%, 100% { transform: translate(0, 0) scale(1); }
  33% { transform: translate(40px, -30px) scale(1.06); }
  66% { transform: translate(-25px, 35px) scale(0.94); }
}
```

### Logo assets

Two logo files live in the repo root:
- `school-logo.jpg` — the school seal + "华西书院 / ACADEMIA HUA XI IDIOMA CHINO" wordmark. White background. Render directly without any wrapper in both nav and footer — the light page background makes it legible.
- `wx-logo.png` — WeChat QR code (black on white). Always mount inside a white rounded container. Label: "Contáctanos en WeChat".

When referencing logos from a game page, use a relative path: `../../../school-logo.jpg` (3 levels up from `games/categoria/juego/`). From `index.html` in the root, use `school-logo.jpg` directly.

### Navigation (required on every page)

Sticky nav, height 64px, blurred light lavender background (slightly darker than the page body). The logo links back to the hub (`/` or `../../..` depending on depth).

```html
<nav>
  <a href="/" class="nav-logo">
    <span class="chinese-char">华西</span>
    Academia <span class="name-gold">Huaxi</span>
  </a>
  <ul class="nav-links">
    <li><a href="/#matematicas" class="math">Matemáticas</a></li>
    <li><a href="/#chino" class="chinese">Chino</a></li>
    <li><a href="/#miscelaneos" class="misc">Misceláneos</a></li>
  </ul>
</nav>
```

The nav logo is the `school-logo.jpg` image, not text. Use:

```html
<a href="{root}" class="nav-logo-img">
  <img src="{root}school-logo.jpg" alt="Academia Hua Xi Idioma Chino">
</a>
```

Nav CSS:

```css
.nav-logo-img {
  display: flex; align-items: center;
  text-decoration: none;
  transition: opacity 0.2s;
}
.nav-logo-img:hover { opacity: 0.85; }
.nav-logo-img img { height: 32px; width: auto; display: block; }

nav {
  position: sticky; top: 0; z-index: 100;
  padding: 0 2rem;
  background: rgba(220, 210, 245, 0.95);
  backdrop-filter: blur(14px);
  border-bottom: 1px solid rgba(107, 91, 149, 0.15);
  display: flex; align-items: center; justify-content: space-between;
  height: 64px;
}
.nav-logo {
  font-family: var(--font-display); font-weight: 800; font-size: 1.15rem;
  color: var(--text); text-decoration: none;
  display: flex; align-items: center; gap: 0.6rem;
}
.nav-logo .chinese-char { color: var(--chinese); font-size: 1.3rem; }
.nav-logo .name-gold { color: var(--misc); }
.nav-links { display: flex; gap: 0.2rem; list-style: none; }
.nav-links a {
  color: var(--text-muted); text-decoration: none; font-weight: 700;
  font-size: 0.875rem; padding: 0.4rem 0.8rem; border-radius: 8px;
  transition: color 0.2s, background 0.2s;
}
.nav-links a:hover { background: rgba(107, 91, 149, 0.08); }
.nav-links a.math:hover { color: var(--math); }
.nav-links a.chinese:hover { color: var(--chinese); }
.nav-links a.misc:hover { color: var(--misc); }
@media (max-width: 640px) {
  nav { padding: 0 1rem; }
  .nav-links { display: none; }
}
```

### Footer (required on every page)

The footer always contains the school logo and the WeChat QR code side by side, then a bottom line with the tagline.

```html
<footer>
  <div class="footer-top">
    <div class="footer-school-logo">
      <img src="{root}school-logo.jpg" alt="Academia Hua Xi Idioma Chino">
    </div>
    <div class="footer-wx-block">
      <span class="footer-wx-label">Contáctanos en WeChat</span>
      <div class="footer-wx-qr">
        <img src="{root}wx-logo.png" alt="Código QR de WeChat">
      </div>
    </div>
  </div>
  <div class="footer-bottom">
    <p>Academia Huaxi Juegos &mdash; Hecho para aprender jugando</p>
  </div>
</footer>
```

Footer CSS:

```css
footer {
  position: relative; z-index: 1;
  padding: 3.5rem 2rem 3rem; text-align: center;
  color: var(--text-muted); font-size: 0.85rem;
  border-top: 1px solid rgba(255,255,255,0.05);
  margin-top: 2rem;
}
.footer-top { display: flex; align-items: flex-start; justify-content: center; gap: 4rem; flex-wrap: wrap; margin-bottom: 2.5rem; }
.footer-school-logo { display: inline-block; }
.footer-school-logo img { height: 48px; width: auto; display: block; }
.footer-wx-block { display: flex; flex-direction: column; align-items: center; gap: 0.6rem; }
.footer-wx-label { font-size: 0.7rem; font-weight: 700; letter-spacing: 0.14em; text-transform: uppercase; color: var(--text-muted); }
.footer-wx-qr { background: white; border-radius: 10px; padding: 8px; display: inline-block; }
.footer-wx-qr img { width: 96px; height: 96px; display: block; }
.footer-bottom { border-top: 1px solid rgba(255,255,255,0.05); padding-top: 1.5rem; }
footer a { color: var(--chinese); text-decoration: none; font-weight: 600; }
footer a:hover { text-decoration: underline; }
```

### Game page layout

When a game is ready to be built (replacing a placeholder), use this page structure:

```
nav
main (position: relative; z-index: 1)
  .game-container  ← game-specific content goes here
footer
```

The game content area should use `var(--surface)` for panels/cards, category accent color for interactive elements, and keep `var(--bg)` as the page background (the starfield shows through).

### Placeholder game pages

Pages not yet built show a centered holding screen with:
- Large emoji (4rem)
- Game title in the category accent color (Baloo 2, 800, 2.5rem)
- One-line description in `var(--text-muted)`
- A "← Volver al inicio" link styled with the category accent color

The "← Volver al inicio" `href` must be a correct relative path back to `index.html` (e.g., `../../..` from `games/categoria/juego/`).

### Base CSS reset (every page)

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body {
  background: var(--bg); color: var(--text);
  font-family: var(--font-body); overflow-x: hidden; line-height: 1.6;
}
main { position: relative; z-index: 1; }
:focus-visible { outline: 2px solid var(--misc); outline-offset: 3px; border-radius: 6px; }
@media (prefers-reduced-motion: reduce) {
  .orb, .star { animation: none; }
  * { transition: none !important; }
}
```

---

## File structure

```
/
├── index.html                          ← Hub page (all three categories)
├── CLAUDE.md                           ← This file
└── games/
    ├── matematicas/
    │   ├── suma-rapida/index.html      ⚡ Suma Rápida
    │   ├── tablas/index.html           ✖️ Tablas al Vuelo
    │   ├── geometria/index.html        🔷 Formas y Figuras
    │   └── fracciones/index.html       🍕 Fracciones Locas
    ├── chino/
    │   ├── caracteres/index.html       🖊️ Trazos Básicos
    │   ├── tonos/index.html            🎵 Los 4 Tonos
    │   ├── numeros/index.html          🔢 一二三… Contar
    │   └── vocabulario/index.html      📖 Palabras HSK 1
    └── misc/
        ├── memoria/index.html          🃏 Juego de Memoria
        ├── rompecabezas/index.html     🧩 Rompecabezas
        ├── trivia/index.html           🏆 Trivia Academia
        └── crucigrama/index.html       📝 Crucigrama
```

New games go inside the appropriate category folder as `games/{categoria}/{slug}/index.html`.

---

## Category color mapping

| Category | CSS var | Hex | Usage |
|---|---|---|---|
| Matemáticas | `--math` | `#D94F30` | Accent, glow, tags, interactive elements |
| Chino Mandarín | `--chinese` | `#1E9A92` | Accent, glow, tags, interactive elements |
| Misceláneos | `--misc` | `#B8900A` | Accent, glow, tags, interactive elements |

---

## What NOT to do

- Do not use any color outside the token system above.
- Do not use any font other than Baloo 2 and Nunito.
- Do not use dark backgrounds — the page background is the light lavender `var(--bg)` (#F4EFFF).
- Do not omit the starfield or ambient orbs from any page.
- Do not omit the nav or footer from any page.
- Do not write UI copy in English — everything is in Spanish.
- Do not add a "Ver en GitHub" link (or any GitHub link) to the footer. Remove it if already present.
- Do not commit or push without asking the user first.
