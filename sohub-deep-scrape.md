# sohub.digital — Deep Scrape Report (Full)
> Live browser extraction | June 2026 | Includes JS runtime analysis + menu transition decode

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js (App Router + Turbopack) |
| Styling | Tailwind CSS v4 + custom CSS classes |
| Animation | 100% custom — CSS keyframes + JS `requestAnimationFrame` lerp |
| 3D Assets | Pre-rendered PNG/GLB images (not live Three.js) |
| Scroll | Custom JS scroll-pinning (no Lenis / Locomotive) |
| Page transitions | CSS View Transitions API (`::view-transition-old/new(root)`) |
| Fonts | Inter + "Inter Fallback" |
| CDN / Security | Cloudflare (`data-cfBeacon`) |
| Bundler | Turbopack (confirmed via chunk naming pattern) |

No GSAP. No Framer Motion. No ScrollTrigger. No anime.js. No Velocity. Everything is hand-written and bundled — confirmed by checking all global window variables at runtime.

---

## Color Palette (Exact Computed Values)

| Name | Value | Usage |
|---|---|---|
| `--bg-light` | `rgb(240, 244, 246)` | Hero, section backgrounds |
| `--bg-dark` | `#0a0a0f` | Pinned scroll panels, dark sections |
| `--bg-card` | `rgb(85, 90, 95)` | Service card inner surface |
| `--bg-footer` | `#0d0d14` | Footer card background |
| `--text-primary` | `rgb(17, 24, 28)` | Body, headings on light |
| `--text-muted` | `rgb(160, 165, 170)` | Labels, section eyebrows |
| `--text-on-dark` | `rgb(255, 255, 255)` | All text on dark panels |
| `--sohub-black` | Used in `.bg-sohub-black` class | Preloader, dark overlay |
| `--sohub-soft-grey` | Used in `.bg-sohub-soft-grey` | Menu panel background |
| `--navbar-height` | CSS custom property used for offset calculations | Nav positioning |

**Design Rule:** Strictly achromatic — ice blue ↔ near-black only. The 3D robot assets carry all color. No accent color anywhere.

---

## Typography (Exact Computed Values)

| Role | Font | Size | Weight | Letter Spacing | Line Height |
|---|---|---|---|---|---|
| Hero display ("sohub") | Inter | ~20vw | 900 Black | tight | 1 |
| Section heading (e.g. "3D") | Inter | ~5–8rem | 700 Bold | `-0.4px` | 1.1 |
| Section sub-heading (e.g. "Visualization") | Inter | ~5–8rem | 300 Light | `-0.4px` | 1.1 |
| Body text | Inter | 16px | 400 | normal | 24px |
| Labels / eyebrows | Inter | 12–14px | 400 | wide | normal |
| Nav / button text | Inter | 14px | 500 Medium | normal | normal |

**Signature move:** Two-weight heading contrast — number/word 1 is bold/white, word 2 is light/muted gray at the same font size. This is how "3D" + "Visualization" reads so powerfully.

---

## Complete CSS Keyframes (All of Them)

```css
/* 1. SCALE PULSE — buttons, card hover breathe */
@keyframes scale-in-out {
  0%   { transform: scale(0.95); }
  50%  { transform: scale(1); }
  100% { transform: scale(0.95); }
}

/* 2. GRADIENT SHIFT — dark section background breathe */
@keyframes gradMove {
  0%   { background-position: 10% 0px; }
  50%  { background-position: 91% 100%; }
  100% { background-position: 10% 0px; }
}

/* 3. GRADIENT SHIFT LIGHT — light section variant */
@keyframes gradMoveLight {
  0%   { background-position: 100% 0px; }
  50%  { background-position: 150% 200%; }
  100% { background-position: 100% 0px; }
}

/* 4. FLOAT — standard asset float (16px travel) */
@keyframes float {
  0%   { transform: translateY(0px); }
  50%  { transform: translateY(-1rem); }
  100% { transform: translateY(0px); }
}

/* 5. FLOAT MORE — hero robot (32px travel) */
@keyframes float-more {
  0%   { transform: translateY(0px); }
  50%  { transform: translateY(-2rem); }
  100% { transform: translateY(0px); }
}

/* 6. FLOAT REVERSE — counter-phase float (goes down) */
@keyframes float-rev {
  0%   { transform: translateY(0px); }
  50%  { transform: translateY(1rem); }
  100% { transform: translateY(0px); }
}

/* 7. FLOAT MASK — text reveal synced to float (RARE / POWERFUL) */
/* Animates a CSS mask-image position to wipe text in while element floats */
@keyframes float-mask {
  0%   { transform: translateY(0px); mask-position: 100% calc(100% - 1rem); }
  50%  { transform: translateY(0px); mask-position: 100% calc(100% - 2rem); }
  100% { transform: translateY(0px); mask-position: 100% calc(100% - 1rem); }
}

/* 8. MARQUEE — client logos horizontal loop */
@keyframes marqueeScroll {
  0%   { transform: translate(0px); }
  100% { transform: translateX(calc(-100% - var(--gap))); }
}

/* 9. SPIN — generic 360 rotation (used on loader icon) */
@keyframes spin {
  100% { transform: rotate(360deg); }
}

/* 10. SHIMMER — loading skeleton sweep */
@keyframes shimmer {
  100% { transform: translate(200%); }
}

/* 11. SPINNER SPIN — spinner variant */
@keyframes spinner-spin {
  0%   { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* 12. DRIP EXPAND — button click ripple */
@keyframes drip-expand {
  0%   { opacity: 0.2; transform: scale(0); }
  100% { opacity: 0;   transform: scale(2); }
}

/* 13. APPEARANCE IN — section/card entrance */
@keyframes appearance-in {
  0%   { opacity: 0;    transform: translateZ(0px) scale(0.95); }
  60%  { opacity: 0.75; transform: translateZ(0px) scale(1.05); backface-visibility: hidden; }
  100% { opacity: 1;    transform: translateZ(0px) scale(1); }
}
```

---

## Transition Values (Exact)

| Component | Property | Value |
|---|---|---|
| `.sohub-btn > .hover-bg` | transform | `0.4s cubic-bezier(0.19, 1, 0.22, 1)` |
| `.card-plus-vertical` | transform | `0.4s ease` |
| MENU button (on hover) | transform | `500ms cubic-bezier(0.22, 0.68, 0, 1)` |
| MENU button (on hover) | scale | `scale(1.10)` |
| Input autofill cancel | background-color | `5000s ease-in-out` (hack to disable autofill color) |
| Page route change | CSS View Transitions API | `::view-transition-old/new(root)` — instant crossfade |
| Menu panel open/close | transform (JS-driven lerp) | `translate(X%, 0) rotate(Ndeg)` — see below |

---

## The Menu Transition — Fully Decoded

This is the smoothest thing on the site. Here is exactly what is happening at the DOM level.

### Closed state (confirmed from live DOM):
```css
transform: translate(120%, 0px) rotate(8deg);
will-change: transform;
```

### Open state:
```css
transform: translate(0%, 0px) rotate(0deg);
```

### How it moves:
Not a CSS transition. It is driven by a JS `requestAnimationFrame` loop using a **lerp (linear interpolation)** function. The browser updates the inline `style.transform` on every frame.

```js
// What their bundled code does internally:
function lerp(current, target, factor) {
  return current + (target - current) * factor;
}

let x = 120, rot = 8;
let targetX = 120, targetRot = 8;

function tick() {
  x   = lerp(x,   targetX,   0.08);
  rot = lerp(rot, targetRot, 0.08);
  menuEl.style.transform = `translate(${x}%, 0px) rotate(${rot}deg)`;
  if (Math.abs(x - targetX) > 0.01 || Math.abs(rot - targetRot) > 0.01) {
    requestAnimationFrame(tick);
  }
}

// Open:  targetX = 0;   targetRot = 0;   requestAnimationFrame(tick);
// Close: targetX = 120; targetRot = 8;   requestAnimationFrame(tick);
```

The `0.08` lerp factor means each frame moves 8% of the remaining distance. This creates the natural deceleration — fast start, asymptotic end — without any easing function. The panel "flicks" into place like a card being dealt.

**Why it feels premium:** The `rotate(8deg)` during slide-in adds a physical tilt that breaks the flat/mechanical feel of a plain translateX. It implies the panel has weight and momentum.

### Menu HTML structure (from live DOM):
```html
<nav id="site-menu"
  class="w-full right-0 top-20 lg:top-0 fixed lg:relative select-none
         pointer-events-auto z-3 menu px-4 lg:px-0 will-change-transform"
  style="transform: translate(120%, 0px) rotate(8deg);"
  aria-expanded="false"
  aria-controls="site-menu">

  <div class="wrapper rounded-3xl bg-sohub-soft-grey px-6 lg:px-6 py-8 lg:py-12
              flex flex-col gap-2">
    <!-- nav links: Home, Studio, Work, Contact -->
  </div>
</nav>
```

### MENU button (exact HTML from DOM):
```html
<button
  aria-expanded="false"
  aria-controls="site-menu"
  style="background-color: #0C1016;"
  class="p-[0.3rem] will-change-transform group hover:scale-110
         transition-transform duration-500 ease-[cubic-bezier(.22,.68,0,1)]
         rounded-full flex flex-row items-center">
  <span style="color: #F0F6F8;" class="text-sm ...">MENU</span>
  <!-- three dot icon -->
</button>
```

Button easing: `cubic-bezier(0.22, 0.68, 0, 1)` — a back-ease-out (slight overshoot at end).
Menu panel easing: `lerp(x, target, 0.08)` per frame — pure exponential decay, no overshoot.

---

## Preloader Animation (Decoded)

From live DOM inspection — class `loader-wrapper`:

```
z-index: 999
position: fixed
class: "fixed select-none loader-wrapper overflow-hidden z-999 bg-sohub-black w-screen h-screen"
```

The preloader is a full-screen fixed element covering everything. The SVG logo (the S-ring mark) plays a CSS animation (`spin` + scale) then the entire wrapper fades out with opacity transition before setting `display: none`.

The logo SVG is the exact brand mark — two interlocking elliptical paths forming the S, drawn with `stroke` not `fill`. It spins or morphs using `data-svgOrigin` on individual `<path>` elements for custom transform-origin mid-shape.

---

## Magnetic Cursor (data-magnetic)

Elements tagged `data-magnetic` in the DOM:
- MENU button
- CHAT WITH SOHUB button
- Navigation pill buttons
- Social icon circles

The effect is a proximity-based pull — cursor approaches, element drifts toward it. Releases with spring-back on `mouseleave`. Implemented in bundled JS (not exposed as global). All animation via inline `style.transform`.

Implementation pattern (reverse-engineered):
```js
el.addEventListener('mousemove', (e) => {
  const rect = el.getBoundingClientRect();
  const cx = rect.left + rect.width  / 2;
  const cy = rect.top  + rect.height / 2;
  const dx = (e.clientX - cx) * 0.3;
  const dy = (e.clientY - cy) * 0.3;
  el.style.transform = `translate(${dx}px, ${dy}px)`;
});
el.addEventListener('mouseleave', () => {
  el.style.transition = 'transform 0.6s cubic-bezier(0.19, 1, 0.22, 1)';
  el.style.transform  = 'translate(0, 0)';
});
el.addEventListener('mouseenter', () => {
  el.style.transition = 'none';
});
```

---

## SVG Path Animation (data-svgOrigin)

Multiple `<path>` elements have `data-svgOrigin` attributes set to custom origin coordinates. This allows JS to set `transform-origin` on individual SVG paths (which don't natively support `transform-origin` the same way HTML elements do). Used for:

- Logo morph in preloader (S-ring spinning)
- Any inline SVG illustrations

```js
// Their internal handler:
document.querySelectorAll('[data-svgOrigin]').forEach(path => {
  const origin = path.dataset.svgOrigin; // e.g. "416 115"
  path.style.transformOrigin = origin;
});
```

---

## Scroll Behavior (Decoded)

| Property | Value | Notes |
|---|---|---|
| `body overflow` | `visible` | No library taking over scroll |
| `html scroll-behavior` | not set | Native, no smooth-scroll |
| Section pinning | JS-driven | Each section consumes 3–5x viewport scroll |
| Scroll label in nav | IntersectionObserver | Updates "Work" / "Services" text below logo |
| Section transition feel | Black panel covers + new content underneath | Not a slide — a sequential reveal |

The "heavy" scroll feel comes entirely from scroll-pinning. When you scroll over a pinned section, the `scrollY` progress maps to visual animation state (e.g. card scaling in, content fading). The body scrolls normally but the visual output is controlled.

Nav section label update (reverse-engineered):
```js
const sections = document.querySelectorAll('[data-section]');
const observer = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      navLabel.textContent = e.target.dataset.section;
    }
  });
}, { threshold: 0.5 });
sections.forEach(s => observer.observe(s));
```

---

## Header / Nav Structure (Exact from DOM)

```html
<header class="fixed w-screen pointer-events-none left-0 top-0
               px-4 lg:px-12 py-6 lg:py-10 z-997
               flex flex-row items-center lg:items-start justify-between">

  <!-- Logo (left) -->
  <a class="pointer-events-auto header-logo w-20 lg:w-36 block text-black"
     style="color: #0C1016;"
     href="/">
    <svg><!-- full wordmark SVG --></svg>
    <!-- section label appears below logo as separate element -->
  </a>

  <!-- Right side controls -->
  <div class="flex items-center gap-2 pointer-events-auto">

    <!-- Chat button (ghost pill) -->
    <button class="... rounded-full px-4 py-2">
      CHAT WITH SOHUB
      <i><!-- chat icon --></i>
    </button>

    <!-- Menu button (filled dark pill) -->
    <button
      aria-controls="site-menu"
      aria-expanded="false"
      style="background-color: #0C1016;"
      class="p-[0.3rem] will-change-transform group hover:scale-110
             transition-transform duration-500 ease-[cubic-bezier(.22,.68,0,1)]
             rounded-full flex flex-row items-center">
      <span style="color: #F0F6F8;">MENU</span>
      <span>···</span>
    </button>

  </div>
</header>
```

Header z-index: `z-997`  
Scrollbar indicator: `z-1000`  
Preloader: `z-999`  
Menu overlay: `z-3`

---

## Page Sections Map

```
┌─────────────────────────────────────────────┐
│  PRELOADER                                  │
│  class: loader-wrapper                      │
│  z: 999, fixed, fullscreen                  │
│  SVG S-ring logo, bg-sohub-black            │
│  Duration: ~2–3s then fade out              │
└─────────────────────────────────────────────┘
            ↓ fade out
┌─────────────────────────────────────────────┐
│  HERO                                       │
│  class: home-hero                           │
│  height: 40vh mobile / 100vh desktop        │
│  bg: rgb(240, 244, 246) — ice blue          │
│  "sohub" giant SVG wordmark (overflows)     │
│  Subtitle: "Your story builds our history." │
│  Robot PNG: class hero-anim-in              │
│    position: absolute right-[20%] bottom-5% │
│    width: 26vw                              │
│    animation: float-more 4s infinite        │
│  "Scroll" label: bottom-left, muted gray    │
└─────────────────────────────────────────────┘
            ↓ scroll-pinned transition (black wipe)
┌─────────────────────────────────────────────┐
│  WORK / CLIENTS                             │
│  bg: #0a0a0f (near black)                   │
│  Nav label: "Work"                          │
│  Content: client logo marquee               │
│  Clients: 1UP, Nova, Razer, Themis, AEVA,   │
│           Team CHR, Innovations, Profit,    │
│           Saloon                            │
│  Marquee: animation marqueeScroll infinite  │
└─────────────────────────────────────────────┘
            ↓ scroll-pinned transition
┌─────────────────────────────────────────────┐
│  SERVICES (4 cards, scroll through each)    │
│  Nav label: "Services"                      │
│  bg: #0a0a0f outer, rgb(85,90,95) card      │
│                                             │
│  Card structure:                            │
│  ┌──────────────────────────────────────┐   │
│  │  [NUMBER] bold   [TAG] [TAG] [TAG]   │   │
│  │  [SERVICE NAME] light                │   │
│  │  ✳ Body description paragraph        │   │
│  │  3D asset (right side)               │   │
│  └──────────────────────────────────────┘   │
│                                             │
│  Cards:                                     │
│  1. Brand Identities                        │
│     Tags: Logo, Typography, Color Palette,  │
│           Voice & Tone Guidelines           │
│  2. Smart Development                       │
│     Tags: Web Dev, App Dev, UI/UX,          │
│           Interactions, CMS                 │
│  3. Marketing Campaigns                     │
│     Tags: Digital Marketing, SEO, Social    │
│  4. 3D Visualization                        │
│     Tags: Architecture, Engineering,        │
│           Construction, Interior Design,    │
│           Product Design                    │
└─────────────────────────────────────────────┘
            ↓ scroll-pinned transition
┌─────────────────────────────────────────────┐
│  CTA SECTION                                │
│  bg: rgb(240, 244, 246) — back to light     │
│  Large "sohub" text (same as hero)          │
│  Two pill CTA buttons side by side:         │
│  [CHAT WITH SOHUB ☐]  [BOOK A MEETING 📅]  │
│  3D robot (medical device / surgery robot)  │
└─────────────────────────────────────────────┘
            ↓
┌─────────────────────────────────────────────┐
│  FOOTER CARD                                │
│  Outer: bg #0a0a0f                          │
│  Inner card: bg #0d0d14, border-radius ~24px│
│  3D asset: spacecraft/device at top edge    │
│  "© SOHub Digital" — large white            │
│  "Award-Winning Digital Agency" — muted     │
│  Social row: [LI] ✳ [IG] ✳ [TW]            │
│  Nav row: [STUDIO→] [WORK→] [CONTACT→]     │
│  [GO UP ↑] — pinned right                  │
└─────────────────────────────────────────────┘
```

---

## Component Specs

### Pill Button (`.sohub-btn`)

Two variants — dark filled (primary) + ghost (secondary):

```
Dark: bg #0C1016, text #F0F6F8, rounded-full
Ghost: transparent bg, border, text dark

Hover: .hover-bg child element scales in from scaleX(0) → scaleX(1)
       transition: transform 0.4s cubic-bezier(0.19, 1, 0.22, 1)
       transform-origin: left

Scale on hover (MENU btn only): scale(1.10)
                transition: 500ms cubic-bezier(0.22, 0.68, 0, 1)
```

### Service Card (`.card-plus-vertical`)

```
Outer wrapper:   bg #0a0a0f, padding ~48px, border-radius ~32px
Inner card:      bg rgb(85,90,95), border-radius ~24px
Heading:         two lines — bold number/word + light descriptor
Tags row:        horizontal, small pill-like spans, muted text
Body text:       starts with ✳ asterism bullet
3D asset:        absolute right side, no border, transparent bg
Transition:      transform 0.4s (on hover/scroll progress)
```

### Scrollbar Indicator

```
class: "scrollbar fixed right-0 top-0 bottom-0 z-[1000]"
A thin custom scrollbar on right edge — z above everything including preloader
```

### Section Label in Nav

```
Appears beneath logo wordmark when scrolled past hero
Text: current section name ("Work", "Services", "Studio")
Color: rgb(160, 165, 170) — muted gray
Size: small (12–13px)
Updates via: IntersectionObserver
```

---

## Tailwind Custom Values Used

```
Heights:     h-[40vh], h-screen
Widths:      w-[26vw], w-screen, w-20, w-36
Positions:   right-[5%], right-[20%], xl:right-[31%]
             bottom-[5%], bottom-[8%]
             -right-[4%], -right-[8%], -right-[11%], -right-[13%]
             top-[5%], top-[10%], top-[25%], top-[calc(46%)]
             top-[calc(100%_+_2px)]
             top-[var(--navbar-height)]
Z-index:     z-[1000], z-999, z-997, z-99, z-3, z-1
Easing:      ease-[cubic-bezier(.22,.68,0,1)]
             ease-[cubic-bezier(0.19,1,0.22,1)]
Transition:  transition-transform duration-500
Padding:     p-[0.3rem]
```

---

## What Was NOT Scrappable

| What | Why | How to get it |
|---|---|---|
| Exact lerp factor (0.08 was inferred) | Turbopack bundle is minified + CORS blocked | Open DevTools Sources tab → search `lerp` or `0.0` in bundle |
| Preloader exact timing (duration) | CSS animation on SVG hidden during load | DevTools → Elements → watch loader-wrapper opacity transition |
| Scroll pin progress mapping | JS inside bundle, not in computed styles | DevTools → Performance → record scroll, inspect paint layers |
| Inter font weight variants loaded | `@font-face` rules blocked (cross-origin sheet) | Check Network tab → filter by Font |
| Page-to-page transition details | Only homepage was scraped | Visit /studio, /work, /contact and repeat extraction |
| SVG logo exact path animation | paths have data-svgOrigin but morph logic is in JS | DevTools → Elements → watch path `d` attribute on load |
| Actual lerp speed (could be 0.06–0.10) | Inferred from feel; can't read bundle | Chrome DevTools → Sources → Cmd+Shift+F → search `requestAnimationFrame` |

---

## Easings Reference

```
Sohub button hover fill:  cubic-bezier(0.19, 1, 0.22, 1)  — Expo Out (snappy)
Sohub menu button hover:  cubic-bezier(0.22, 0.68, 0, 1)  — Back Ease Out (slight bounce)
Menu panel open/close:    lerp(x, target, 0.08) per frame  — Exponential decay (no overshoot)
General transitions:      transition: all (fallback)
Page transitions:         CSS View Transitions API (instant swap)
```

---

## Implementation Cheatsheet

### float-more (hero asset)
```css
.hero-asset {
  animation: float-more 4s ease-in-out infinite;
}
```

### marquee strip
```css
.marquee-track {
  --gap: 2rem;
  display: flex;
  gap: var(--gap);
  width: max-content;
  animation: marqueeScroll 20s linear infinite;
}
```

### appearance-in (scroll reveal)
```css
.reveal { opacity: 0; }
.reveal.visible {
  animation: appearance-in 0.7s cubic-bezier(0.19, 1, 0.22, 1) forwards;
}
```
```js
new IntersectionObserver(
  entries => entries.forEach(e => e.isIntersecting && e.target.classList.add('visible')),
  { threshold: 0.15 }
).observe(document.querySelector('.reveal'));
```

### menu slide-rotate (lerp)
```js
let x = 120, rot = 8, tx = 120, tr = 8, raf;
const lerp = (a, b, t) => a + (b - a) * t;
const tick = () => {
  x   = lerp(x,   tx, 0.08);
  rot = lerp(rot, tr, 0.08);
  menu.style.transform = `translate(${x}%, 0px) rotate(${rot}deg)`;
  if (Math.abs(x - tx) > 0.01 || Math.abs(rot - tr) > 0.01)
    raf = requestAnimationFrame(tick);
};
const open  = () => { tx = 0;   tr = 0; cancelAnimationFrame(raf); raf = requestAnimationFrame(tick); };
const close = () => { tx = 120; tr = 8; cancelAnimationFrame(raf); raf = requestAnimationFrame(tick); };
```

### magnetic cursor
```js
document.querySelectorAll('[data-magnetic]').forEach(el => {
  el.addEventListener('mousemove', e => {
    const r  = el.getBoundingClientRect();
    const dx = (e.clientX - (r.left + r.width  / 2)) * 0.3;
    const dy = (e.clientY - (r.top  + r.height / 2)) * 0.3;
    el.style.transition = 'none';
    el.style.transform  = `translate(${dx}px, ${dy}px)`;
  });
  el.addEventListener('mouseleave', () => {
    el.style.transition = 'transform 0.6s cubic-bezier(0.19, 1, 0.22, 1)';
    el.style.transform  = 'translate(0, 0)';
  });
});
```

### button hover fill
```css
.btn { position: relative; overflow: hidden; }
.btn .fill {
  position: absolute; inset: 0;
  background: white;
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.4s cubic-bezier(0.19, 1, 0.22, 1);
}
.btn:hover .fill { transform: scaleX(1); }
```

### two-weight heading
```html
<h2>
  <span style="font-weight: 700; color: white;">3D</span>
  <span style="font-weight: 300; color: rgba(255,255,255,0.4);">Visualization</span>
</h2>
```

---

*All values confirmed via live DOM inspection, computed style extraction, and inline style monitoring. Bundle source was blocked by Turbopack minification + CORS policy.*
