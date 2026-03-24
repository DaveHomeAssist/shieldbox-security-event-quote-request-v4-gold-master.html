# ShieldBox Fix Prompts — 5 Phases

Each phase is a self-contained prompt you can hand to Claude or Codex.
Run them in order. Each phase assumes the previous one is done.

---

## Phase 1 — Form Input Hardening

**Issues:** 001, 003, 004, 011, 013, 015, 016

```
You are editing a single-file static HTML site at index.html in this repo.
It is a quote intake form for an event security company. All CSS and JS are inline.
Read the full file before making changes.

Fix these input problems:

1. Every <input type="checkbox"> in the form needs a name attribute that matches
   its id. Search for all checkbox inputs and add name="<id>" to each one.

2. The event duration field currently accepts free text. Change it to
   type="number" with min="1", max="72", step="0.5", and a placeholder of
   "e.g. 4" . Add a small helper note below the field: "Hours on site (half-hour
   increments)".

3. The attendance/guest count input has no upper bound. Add max="10000" and
   add JS validation: if the value exceeds 5000, show an inline warning below
   the field — "Large event — custom quote recommended" — styled with
   color: var(--warn). Do not block submission.

4. The phone field (type="tel") needs a pattern attribute:
   pattern="[\d\s\-\+\(\)]{7,20}" and a title="Phone number" hint.

5. The event date field needs a min attribute set dynamically to today's date
   in YYYY-MM-DD format. Add a small inline script or add it inside the
   existing DOMContentLoaded block:
     document.querySelector('input[type="date"]').min =
       new Date().toISOString().split('T')[0];

6. Add a "format note" below the duration field if the user enters a value
   with a colon (like "2:30") — show "Use decimal hours, e.g. 2.5 for
   2 hours 30 minutes" styled with color: var(--warn).

Do not change any theme tokens, layout, or unrelated JS.
Do not add comments explaining what you changed.
```

---

## Phase 2 — Form Submission & Security

**Issues:** 006, 010, 014, 019, 020

```
You are editing index.html — a single-file static HTML site with inline JS.
Read the full file before making changes.

Fix these submission and security problems:

1. The form currently only validates on an explicit "Validate" button click,
   not on form submit. Add a submit event listener to the intake form that
   runs the same validation logic before allowing submission. If validation
   fails, call event.preventDefault() and scroll to the first invalid field.
   Keep the existing validate button working as-is.

2. The "Request Formal Proposal" button can be spam-clicked. After the first
   click, immediately disable the button and change its text to "Submitting…".
   If the fetch fails, re-enable after a 3-second cooldown (not immediately).
   Track a timestamp: if less than 10 seconds have passed since the last
   successful submission, block and show a toast "Please wait before
   resubmitting."

3. The honeypot field uses name="_honey" which is a well-known FormSubmit
   convention. Rename it to something less obvious — use name="_website" or
   name="company_url" with autocomplete="off", tabindex="-1", and
   aria-hidden="true". Keep it hidden with the existing CSS approach.

4. Add a simple client-side submission token: generate a random 8-char hex
   string on page load, store it in a hidden input named "_token", and on
   submission include it. This won't stop determined attackers but raises the
   bar above raw cURL spam. Also add a hidden input with name="_subject"
   and value="ShieldBox Quote Request" for FormSubmit.co email subject.

Do not change any theme tokens, layout, or unrelated code.
```

---

## Phase 3 — Accessibility & Keyboard

**Issues:** 005, 008, 018

```
You are editing index.html — a single-file static HTML site with inline CSS and JS.
Read the full file before making changes. Reference SCHEMA.md for the token
contract and component inventory.

Fix these accessibility problems:

1. COLOR CONTRAST — The following themes fail WCAG AA (4.5:1 for body text,
   3:1 for large text) on --ink-soft against their respective --field-bg and
   --surface backgrounds. For each theme listed, increase --ink-soft lightness
   until it passes AA against --surface. Use https://webaim.org/resources/
   contrastchecker math — the ratio must be >= 4.5:1.

   Themes to fix: editorial, slate, obsidian, concrete.

   Also check --label-ink against --surface for each. Adjust if needed.
   Do NOT change --ink (primary text) or any background tokens. Only adjust
   --ink-soft and --label-ink.

2. BILLING ROW KEYBOARD ACCESS — The billing table rows (.billing-row) are
   only selectable via click handler. Each row contains a radio input but the
   row-level click handler doesn't delegate to it properly for keyboard users.
   Fix: add tabindex="0" and a keydown listener for Enter and Space on each
   .billing-row that triggers the same selection behavior as click. Also add
   role="radio" and aria-checked matching the selected state. Update
   aria-checked when selection changes.

3. VALIDATION BUTTON LOADING STATE — The "Check Required Fields" button
   (.tiny-action or equivalent) has no loading/disabled state during
   validation. Add: on click, set aria-busy="true", disable the button, and
   add a brief "Checking…" text swap. After validation completes (sync, so
   essentially immediate), restore the original text and re-enable. This
   prevents double-click and gives screen readers feedback.

Do not change any theme background colors, layout structure, or form logic.
```

---

## Phase 4 — Dynamic Behavior & Quote Engine

**Issues:** 002, 007, 017

```
You are editing index.html — a single-file static HTML site with inline JS.
Read the full file before making changes. This is the most complex phase.

Build the missing dynamic behavior:

1. QUOTE CALCULATION ENGINE — The billing section displays rate tiers but no
   JS computes an actual estimate. Build a lightweight estimator:

   Inputs (read from form state):
   - selectedBillingModel (which .billing-row is selected — get its rate)
   - duration (hours from duration field)
   - attendance (guest count)
   - selectedScopes (which scope checkboxes are checked)

   Logic:
   - Base = billingRate * duration
   - If attendance > 500, multiply by 1.15 (large event surcharge)
   - If attendance > 2000, multiply by 1.35 instead
   - Each checked scope item beyond the first 2 adds 8% to the total
   - Round to nearest $50

   Output: display the estimate in the sidebar summary card. Add a line like
   "Estimated range: $X – $Y" where Y = X * 1.2 (to show a range, not a
   fixed price). Style with font-family: var(--font-display) and
   color: var(--accent). Add a disclaimer below: "Planning estimate only.
   Final pricing confirmed in formal proposal."

   Recalculate on: billing row change, duration change, attendance change,
   scope checkbox change.

2. DYNAMIC RISK ASSESSMENT — The risk grid currently shows static labels.
   Make it reactive:

   - Crowd Density risk: LOW if attendance < 200, MEDIUM if < 1000, HIGH if >= 1000
   - Alcohol risk: HIGH if any scope checkbox related to bar/alcohol is checked,
     otherwise MEDIUM
   - Duration risk: LOW if < 4 hours, MEDIUM if < 8, HIGH if >= 8
   - Access Complexity: MEDIUM by default, HIGH if attendance > 500 AND
     duration > 6

   Update the .risk-level elements and their .is-low / .is-medium / .is-high
   classes. Recalculate whenever attendance, duration, or scope changes.
   Update the recommendation box text to match the highest risk level found.

3. Wire both systems to fire on input/change events. Use event delegation on
   the form element where possible instead of individual listeners.

Do not change any CSS tokens, theme definitions, or HTML structure outside
the sidebar summary and risk grid areas.
```

---

## Phase 5 — UX Polish & Edge Cases

**Issues:** 009, 012, 021

```
You are editing index.html — a single-file static HTML site with inline CSS and JS.
Read the full file before making changes.

Fix these remaining UX issues:

1. XSS PATTERN — The theme switcher builds swatches using innerHTML with
   template literal interpolation from the themes array. Replace innerHTML
   usage with DOM API calls (createElement, style.setProperty, textContent).
   The themes array is hardcoded so this is defense-in-depth, not an active
   exploit.

2. EMAIL TYPO DETECTION — After the email field, add a lightweight typo
   check. On blur of the email input, check the domain portion against a
   list of common domains: ["gmail.com", "yahoo.com", "outlook.com",
   "hotmail.com", "icloud.com", "aol.com", "protonmail.com"]. If the
   domain is close but not exact (e.g. "gmial.com", "gmal.com",
   "yahooo.com"), show an inline suggestion: "Did you mean [correct]?"
   with a clickable link that corrects the value. Use Levenshtein distance
   <= 2 as the threshold. Keep the implementation under 30 lines.

3. FORM RESET THEME — When "Start Another Quote" is clicked and the form
   resets, also reset the theme to default. Remove the localStorage theme
   key and set body's data-theme attribute back to the default (no attribute
   or empty string). Reset the theme switcher dropdown to match.

Do not change any theme tokens, billing logic, risk assessment, or
validation behavior.
```

---

## Run Order

| Phase | Scope | Issues Fixed | Risk |
|-------|-------|-------------|------|
| 1 | Input attributes + field validation | 001, 003, 004, 011, 013, 015, 016 | Low — HTML attributes + small JS |
| 2 | Submission flow + spam defense | 006, 010, 014, 019, 020 | Low — event handlers + hidden fields |
| 3 | Contrast + keyboard + ARIA | 005, 008, 018 | Medium — token math, must verify visually |
| 4 | Quote engine + risk reactivity | 002, 007, 017 | Medium — new JS logic, most lines of code |
| 5 | XSS hardening + email UX + reset | 009, 012, 021 | Low — DOM cleanup + small features |

After each phase, open the page in a browser and test:
- All 8 themes render correctly
- Form submits with valid data
- Keyboard-only navigation works through the full form
- Risk grid updates when inputs change (after phase 4)
