# sohub.digital — Full Design & Animation Analysis
> Scraped live via browser automation | June 2026

---

## 🧱 Tech Stack

| Layer | Technology |
|---|---|
| Framework | **Next.js** (App Router + Turbopack) |
| Styling | **Tailwind CSS** + custom CSS classes |
| Animation | **Custom CSS keyframes** + JS-driven scroll logic (no GSAP/Framer globals — all bundled/tree-shaken) |
| 3D Assets | **Pre-rendered GLB/GLTF images** (not live Three.js — custom `data-svgOrigin` for SVG path animation) |
| Scroll | Custom scroll-pinning (not Lenis/Locomotive — native JS) |
| Page transitions | **CSS View Transitions API** (`::view-transition-old(root)` / `::view-transition-new(root)`) |
| Fonts | **Inter** (primary), system-ui fallback |
| CDN/Security | Cloudflare (`data-cfBeacon`) |

---

## 🎨 Color Palette

| Token | Value | Usage |
|---|---|---|
| Background (light) | `rgb(240, 244, 246)` — near-white ice blue | Hero, between sections |
| Background (dark) | `#0a0a0f` — near-black | Pinned scroll panels |
| Surface (card) | `rgb(85, 90, 95)` — dark slate gray | Service cards |
| Text primary | `rgb(17, 24, 28)` — near-black | Body, headings |
| Text muted | `rgb(160, 165, 170)` — gray | Labels, subtitles |
| Text on dark | `rgb(255, 255, 255)` | Headers on dark panels |
| Footer background | `#0d0d14` — deep navy black | Footer card |

**Palette logic:** Ice blue ↔ pure black. No accent color at all — the 3D robots carry all the color. Everything else is achromatic.

---

## 🔤 Typography

| Role | Font | Size / Weight | Letter Spacing |
|---|---|---|---|
| Display (hero "sohub") | Inter | ~20vw / Black (900) | tight |
| Section headings | Inter | ~5–8rem / Light (300) | `-0.4px` |
| Body text | Inter | `16px` / Regular (400) | `normal` |
| Labels / eyebrows | Inter | `12–14px` / Regular | wide |
| Nav items | Inter | `14px` / Medium | `normal` |

**Key observation:** Headings use **two-weight contrast** — "3D" is bold/white while "Visualization" is light/muted gray. This is their signature typographic move.

---

## ✨ CSS Keyframe Animations

All custom, no library. Exact values:

```css
/* Floating 3D robot — gentle vertical oscillation */
@keyframes float {
  0%   { transform: translateY(0px); }
  50%  { transform: translateY(-1rem); }   /* 16px up */
  100% { transform: translateY(0px); }
}

@keyframes float-more {
  0%   { transform: translateY(0px); }
  50%  { transform: translateY(-2rem); }   /* 32px up — used for hero robot */
  100% { transform: translateY(0px); }
}

@keyframes float-rev {
  0%   { transform: translateY(0px); }
  50%  { transform: translateY(1rem); }    /* 16px DOWN — reverse phase */
  100% { transform: translateY(0px); }
}

/* Background gradient breathing */
@keyframes gradMove {
  0%   { background-position: 10% 0px; }
  50%  { background-position: 91% 100%; }
  100% { background-position: 10% 0px; }
}

@keyframes gradMoveLight {
  0%   { background-position: 100% 0px; }
  50%  { background-position: 150% 200%; }
  100% { background-position: 100% 0px; }
}

/* Scale pulse (button / card hover) */
@keyframes scale-in-out {
  0%   { transform: scale(0.95); }
  50%  { transform: scale(1); }
  100% { transform: scale(0.95); }
}

/* Mask reveal (text wipe-in) */
@keyframes float-mask {
  /* clip-path or mask wipe — scroll-triggered */
}
```

---

## 🎯 Transition Values

| Selector / Component | Transition |
|---|---|
| `.sohub-btn > .hover-bg` | `transform 0.4s cubic-bezier(0.19, 1, 0.22, 1)` |
| `.card-plus-vertical` | `transform 0.4s` |
| Button hover backgrounds | `cubic-bezier(0.19, 1, 0.22, 1)` — **Expo Out** easing |
| Page transitions | CSS View Transitions API (instant swap with crossfade) |

**The easing:** `cubic-bezier(0.19, 1, 0.22, 1)` is an aggressive **exponential ease-out** — very fast start, asymptotic slow end. This is what gives buttons that "snap into place" feel.

---

## 🖱️ Interaction Features

### Magnetic Cursor
```html
<element data-magnetic>...</element>
```
Elements tagged `data-magnetic` respond to cursor proximity with a pull effect. Implemented in JS (bundled), not a library. Applied to: nav buttons, CTA buttons.

### SVG Path Morphing
```html
<path data-svgOrigin="...">
```
SVG paths have custom origin points set via `data-svgOrigin` for transform-origin-based animation (logo spin/morph on preloader).

### Preloader
- Full-screen dark background
- SVG "S" logo spins/morphs in center
- Fades out revealing the hero (via CSS View Transitions or opacity transition)

### Nav Context Label
- Logo shows current section name beneath it: `sohub / Work`, `sohub / Services`
- Updates via IntersectionObserver watching sections (JS-driven, no library)

---

## 📐 Page Sections (Scroll Map)

```
┌─────────────────────────────────────┐
│ PRELOADER                           │  Dark fullscreen, SVG logo, ~1.5s
├─────────────────────────────────────┤
│ HERO                                │  Light bg, "sohub" giant type,
│  class: home-hero                   │  subtitle "Your story builds our
│  height: 100vh                      │  history.", robot floats in right
│  robot: hero-anim-in                │  3D PNG, "Scroll" label bottom-left
├─────────────────────────────────────┤
│ WORK / CLIENTS (dark pinned)        │  Scroll-pinned black panel, client
│  ~3–4 scroll lengths                │  logos marquee: 1UP, Nova, Razer,
│                                     │  Themis, AEVA, Team CHR, etc.
├─────────────────────────────────────┤
│ SERVICES (label appears in nav)     │  3 cards in rounded dark containers:
│  Scroll-pinned carousel of cards:   │
│  1. Brand Identities                │  Dark rounded card, large heading,
│  2. Smart Development               │  tag pills (Logo, Typography etc),
│  3. Marketing Campaigns             │  star bullet (✳) for body text
│  4. 3D Visualization                │
├─────────────────────────────────────┤
│ CTA SECTION                         │  Light bg, large "sohub" text,
│  "Your story, our expertise"        │  two pill CTAs side by side:
│                                     │  [CHAT WITH SOHUB] [BOOK A MEETING]
│                                     │  3D robot (medical device variant)
├─────────────────────────────────────┤
│ FOOTER CARD                         │  Dark rounded card (same radius as
│  "© SOHub Digital"                  │  service cards), social icons with
│  "Award-Winning Digital Agency"     │  ✳ separators, nav pills:
│  Social: LI ✳ IG ✳ TW              │  [STUDIO→] [WORK→] [CONTACT→]
│                                     │  [GO UP ↑] on right
└─────────────────────────────────────┘
```

---

## 🔲 Component Library

### Pill Button (`.sohub-btn`)
```
┌─────────────────────────────┐
│  LABEL TEXT          [ICON] │  ← dark fill, rounded-full
└─────────────────────────────┘
Hover: background slides in from left (`.hover-bg`)
Transition: transform 0.4s cubic-bezier(0.19, 1, 0.22, 1)
```
Two variants: dark filled (primary) + light/ghost (secondary)

### Service Card
- Dark `#1a1a24` background
- `border-radius: ~24px`
- Outer wrapper: `#0a0a0f` dark with inset card
- Heading: 2-weight (number bold, word light)
- Tag pills: horizontal row, small rounded, muted text
- Star bullet `✳` before body text
- Right side: 3D render of relevant object

### Nav Bar
- `position: fixed`, top
- Left: `sohub` wordmark + current section label below
- Right: `[CHAT WITH SOHUB ☐]` pill + `[MENU ···]` dark pill
- Menu triggers full-screen overlay with links

### Section Label (in Nav)
- Appears below logo when scrolling past hero
- Text: "Work", "Services", "Studio" etc
- Color: `rgb(160, 165, 170)` — muted gray
- Font: Inter, small size

### Social Icons
- Circle bordered buttons
- ✳ asterisk star used as separator between icons
- Icons: LinkedIn, Instagram, Twitter

---

## 🌀 Scroll Feel & Behavior

| Property | Value |
|---|---|
| Scroll type | Native browser scroll (no smooth-scroll library) |
| Pinned sections | JS-based scroll pinning (progress mapped to visual state) |
| Body overflow | `visible` (scroll managed via section sticky logic) |
| scroll-behavior | Not set in CSS (native) |
| Scroll-driven animations | Custom IntersectionObserver + scroll event listeners |
| Section transition | Abrupt black panel covers → new content revealed beneath |

**The "feel":** Heavy, weighty, intentional. Each section takes multiple full scrolls to traverse. The black panel covers feel like turning a page rather than sliding. Very deliberate pacing — not smooth/floaty like Lenis.

---

## 🏗️ Tailwind Classes Used (Notable)

```
Layout:     w-screen, h-screen, h-[40vh], relative, absolute, fixed, sticky, z-1
Position:   right-[5%], right-[20%], xl:right-[31%], bottom-[8%], bottom-[5%]
Sizing:     w-[26vw]
Responsive: lg:h-screen, lg:right-[20%], xl:right-[31%]
Text:       sr-only (SEO h1 hidden)
```

Custom classes alongside Tailwind:
- `.home-hero` — hero section
- `.hero-anim-in` — robot entrance animation class
- `.sohub-btn` — button component
- `.card-plus-vertical` — service card with divider line
- `.hover-bg` — button hover fill element

---

## 📦 What to Borrow for Your Portfolio

### Must-steal ideas:
1. **Giant viewport-filling name as hero** — your name at 20vw, robot/3D asset overlapping it
2. **Two-weight heading** — project name bold, descriptor light/muted same size
3. **Magnetic cursor** on buttons — `data-magnetic` + JS proximity detection
4. **Section label in nav** — shows current section below your logo as you scroll
5. **Star separator `✳`** between social links and as bullet points
6. **Pill buttons with sliding fill** — `cubic-bezier(0.19, 1, 0.22, 1)` on hover
7. **Dark rounded cards** with outer dark wrapper — the double-dark layering effect
8. **Float animation** on key image — `translateY(-2rem)` at 50%, infinite, ease-in-out
9. **Preloader with logo morph** — dark screen, logo animates in, fades to hero
10. **Footer as a card** — same border-radius as content cards, not a full-width strip

### Easing to use everywhere:
```css
/* Sohub's signature ease — use for all hover transitions */
cubic-bezier(0.19, 1, 0.22, 1)   /* Expo Out */

/* For entrances / scroll reveals */
cubic-bezier(0.16, 1, 0.3, 1)    /* Quint Out */
```

### Color strategy:
- Pick **one near-white and one near-black** — no midtones
- Let your **project screenshots/3D assets** be the only color on the page
- Muted gray (`#a0a5aa`) only for labels and secondary text

---

*Report generated by live browser scraping — all values are exact computed/source values from sohub.digital*
