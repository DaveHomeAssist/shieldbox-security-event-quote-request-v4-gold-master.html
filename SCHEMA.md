# ShieldBox Security — Design Schema

Extracted from `shieldbox-security-event-quote-request-v4-gold-master.html`

---

## Head: Google Fonts

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/css2?family=Libre+Baskerville:wght@400;700&family=Libre+Franklin:wght@300;400;500;600;700&display=swap"
/>
```

---

## Theme Contract

- Default theme: tokens on `:root`
- Editorial: overrides on `body[data-theme="editorial"]`
- Slate: overrides on `body[data-theme="slate"]`

Every component references tokens only — never hard-coded colors.

---

## Token Override Skeleton

```css
:root {
  /* backgrounds */
  --bg: #0e1116;
  --surface: #151b24;
  --surface-2: #1b2430;

  /* borders */
  --line: #2d3949;
  --line-strong: #3d4c61;
  --panel-border: #3a4a5f;
  --field-border: #3a4a5f;
  --footer-divider: #3b4a5f;

  /* specialized */
  --field-bg: #1a2230;
  --billing-selected-bg: #232f3f;
  --billing-selected-border: #b76a44;
  --scope-selected-bg: #223044;
  --scope-selected-border: #5b6c83;

  /* ink */
  --ink: #e7edf6;
  --ink-soft: #b1bdd0;
  --label-ink: #c4cedf;

  /* accent + semantic */
  --accent: #b24b40;
  --accent-soft: rgba(178, 75, 64, 0.14);
  --ok: #4fa57f;
  --warn: #c99647;

  /* effects */
  --radius: 10px;
  --shadow-soft: 0 10px 28px rgba(0, 0, 0, 0.28);

  /* type */
  --font-display: "Libre Baskerville", serif;
  --font-body: "Libre Franklin", system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
}

body[data-theme="editorial"] {
  --bg: #f4f1eb;
  --surface: #ffffff;
  --surface-2: #f7f4ef;
  --ink: #1f1b17;
  --ink-soft: #61594f;
  --label-ink: #6f675d;
  --line: #d8d0c4;
  --line-strong: #baaea0;
  --panel-border: #d7d1c6;
  --field-bg: #fbfaf7;
  --field-border: #d7d1c6;
  --billing-selected-border: #a85734;
  --billing-selected-bg: #f8f3ef;
  --scope-selected-border: #ccbfae;
  --scope-selected-bg: #f8f3ef;
  --footer-divider: #e2dbd0;
  --accent: #8e3a30;
  --accent-soft: rgba(142, 58, 48, 0.1);
  --ok: #3d725e;
  --warn: #9d6f2b;
  --shadow-soft: 0 10px 24px rgba(26, 17, 12, 0.08);
}

body[data-theme="slate"] {
  --bg: #12151d;
  --surface: #1a212d;
  --surface-2: #212b3a;
  --ink: #e6ecf6;
  --ink-soft: #b1bfd4;
  --label-ink: #c1cee2;
  --line: #314055;
  --line-strong: #3d506a;
  --panel-border: #41546e;
  --field-bg: #1e2939;
  --field-border: #41546e;
  --billing-selected-border: #5f7fa9;
  --billing-selected-bg: #233146;
  --scope-selected-border: #5f7fa9;
  --scope-selected-bg: #233146;
  --footer-divider: #4a5f7d;
  --accent: #2f5d9b;
  --accent-soft: rgba(47, 93, 155, 0.16);
  --ok: #4b9b83;
  --warn: #b7934b;
}
```

---

## Page Shell (DOM order)

```html
<body>
  <a class="skip-link" href="#main">Skip to content</a>

  <nav class="site-nav">...</nav>

  <section class="landing">...</section>
  <div class="trust-strip">...</div>
  <section class="services">...</section>

  <div class="section-divider"></div>

  <div class="quote-section" id="main">
    <div class="layout">
      <div class="stack">
        <!-- section cards -->
      </div>
      <aside class="side-stack">
        <!-- sticky sidebar -->
      </aside>
    </div>
  </div>

  <footer class="site-footer">...</footer>

  <div id="toast" aria-live="polite" aria-atomic="true"></div>
</body>
```

---

## Component Inventory

```
GLOBAL
├─ :focus-visible         — 2px accent outline, 2px offset
├─ .skip-link             — keyboard-only skip nav
└─ color-mix()            — used throughout for dynamic tints

NAVIGATION
├─ .site-nav              — sticky top bar, blurred bg (56px)
│  ├─ .nav-brand          — shield SVG + .nav-wordmark
│  └─ .nav-links          — right-aligned
│     ├─ .nav-link-desktop — hidden ≤640px
│     └─ .nav-cta         — accent pill button

LANDING
├─ .landing               — full-viewport hero, centered
│  ├─ .hero-shield        — 72px SVG
│  ├─ .hero-tagline       — uppercase accent label
│  ├─ .hero-h1            — Baskerville, clamp(2.2rem–3.6rem)
│  ├─ .hero-sub           — light weight subtitle
│  └─ .hero-actions       — flex row
├─ .btn / .btn-primary / .btn-ghost

TRUST STRIP
└─ .trust-strip           — 4-col grid (gap:1px trick)
   └─ .trust-item         — Baskerville numeral + label

SERVICES
└─ .services              — max-width:1080px
   └─ .services-grid      — 3-col → 1-col ≤980px
      └─ .svc-card        — icon + name + desc

QUOTE SECTION
├─ .quote-section         — max-width:1240px
├─ .theme-switcher        — fixed pill, select dropdown
└─ .layout                — 2-col grid (1fr + 292px sidebar)
   ├─ .stack              — gap:22px content
   └─ .side-stack         — sticky sidebar
      └─ .side-card       — nav links / summary

SECTION CARDS
└─ .section               — bordered card, overflow:hidden
   ├─ .section-head       — surface-2 bar
   │  ├─ .section-icon    — 24px letter tag
   │  ├─ .section-title   — Baskerville
   │  └─ .section-note    — pill badge
   └─ .section-body

PROPERTY GRID
└─ .property-grid         — auto-fit, minmax(220px, 1fr)
   └─ .property           — .property-label + .property-value
└─ .notes-box             — .notes-title + .notes-copy

FORM
└─ .intake-form
   ├─ .form-grid          — 2-col → 1-col ≤980px
   │  └─ .form-field      — label + input
   ├─ .form-actions       — .tiny-action + .validation-msg
   └─ .core-fields → .core-chip

TABLE
└─ .simple-table-wrap → .simple-table (min-width:580px)

CHECKBOXES
└─ .check-grid → .check-item → .check-label + .check-copy
└─ .tip-box               — accent left-border callout

SCOPE (enhanced checkboxes)
└─ #scope .check-item     — :has(input:checked) states

RISK
├─ .risk-grid → .risk-item → .risk-name + .risk-level
│  Levels: .is-high / .is-medium / .is-low
└─ .recommend-box         — .recommend-title + .recommend-list
   └─ .recommend-row + .recommend-note

BILLING
├─ .billing-table-wrap → .billing-table (min-width:700px)
│  └─ .billing-row        — .is-selected highlight
│     ├─ .billing-model + .billing-desc
│     └─ .billing-choice  — radio + label
└─ .billing-note

PROMISE
└─ .promise-card          — .promise-title + .promise-lead
   + .promise-points + .promise-copy

NEXT STEP
└─ .next-step             — centered CTA block
   ├─ .next-title + .next-copy
   ├─ .next-actions → .cta / .cta-secondary
   └─ .next-turnaround

PILLS
└─ .pill                  — inline status indicator

FOOTER
└─ .site-footer           — .footer-brand + .footer-note

TOAST
└─ #toast → .show
```

---

## Implementation Notes

- `.layout` is a 2-col grid with fixed 292px sidebar until ≤980px where sidebar reflows above content.
- Scope selection uses `:has(input:checked)`. For wider browser support, add a JS class fallback.
- `color-mix(in srgb, ...)` is used throughout for dynamic tints — requires modern browser support.
- Print mode overrides all tokens to light values and hides nav, hero, trust, services, sidebar, theme switcher, toast, and CTA buttons.

---

## Responsive Breakpoints

| Breakpoint | Changes |
|---|---|
| ≤980px | Single column layout, sidebar reflows to top, risk/form/services grids collapse |
| ≤640px | Trust strip 2-col, desktop nav links hidden |

## Print

- Full token override to light palette
- Hides: `.site-nav`, `.landing`, `.trust-strip`, `.services`, `.section-divider`, `.quote-intro`, `.theme-switcher`, `.side-stack`, `.next-actions`, `#toast`
- `@page { margin: 14mm }`
