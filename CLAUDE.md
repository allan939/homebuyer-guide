# CLAUDE.md — homebuyer-guide

This file provides context for AI assistants (Claude Code, GitHub Actions Claude bot, etc.) working on this repository.

---

## Project Overview

**homebuyer-guide** is a single-page lead-capture landing page for **Allan Vega**, a Houston-based Realtor with AEA Realty. The page targets first-time homebuyers, new construction clients, and VA loan clients in the Houston, TX market.

**Brand:** Love Your Dirt
**Agent:** Allan Vega | AEA Realty | License #777820
**Primary contact:** 281-865-7146 | (832) 403-3681 | allan@askozzie.com
**Market:** Houston, TX (including Katy, Sugar Land, Cypress, The Woodlands)
**Audience:** First-time homebuyers, new construction buyers, VA clients (English and Spanish speakers)

---

## Repository Structure

```
homebuyer-guide/
├── index.html                    # Lead-capture landing page — all HTML, CSS, and JS in one file
├── guide.html                    # Redirect page → Google Drive PDF (Houston Homebuyer Guide)
├── new-construction-guide.html   # Redirect page → Google Drive PDF (New Construction Guide)
├── README.md                     # Minimal project description
├── CLAUDE.md                     # This file
└── .github/
    └── workflows/
        └── claude.yml            # GitHub Actions: Claude Code bot triggered by @claude mentions
```

This is a **zero-dependency, no-build static site**. There is no package.json, no npm, no bundler, no framework.

### Guide Redirect Pages

`guide.html` and `new-construction-guide.html` are minimal HTML files (no CSS framework, no JS) that use a `<meta http-equiv="refresh">` tag to forward visitors to a hosted PDF on Google Drive after 1 second. Each also shows a brief "Opening your guide…" message with a manual fallback link in case the redirect fails.

| File | Destination PDF |
|------|----------------|
| `guide.html` | Houston First-Time Homebuyer Guide (`19ttMZDUBfJAPjVg2Q7CJNFomzRYwqKeH`) |
| `new-construction-guide.html` | Houston New Construction Guide (`1xpsDcq9ymX8Pl5EBJsj3pG9UQ1t0usuG`) |

**When to edit these files:**
- Update `content="1; url=..."` in the `<meta>` tag if the Google Drive file ID changes
- Update the fallback `<a href="...">click here</a>` link to match
- Update the phone number or agent name if contact info changes
- Do **not** add complex CSS/JS — these pages are intentionally minimal; users see them for under 2 seconds

**Note:** These pages are the delivery mechanism for the guides. The lead-capture form in `index.html` does **not** redirect to these pages directly — the guide link is typically delivered via the follow-up email sent by the Google Apps Script backend. These pages may be linked from email campaigns, QR codes, or social ads independently.

---

## Technology Stack

| Layer | Detail |
|-------|--------|
| Markup | Vanilla HTML5 (semantic: `<header>`, `<main>`, `<section>`, `<footer>`) |
| Styles | Inline `<style>` block inside `index.html` — vanilla CSS3 with custom properties |
| Scripts | Inline `<script>` blocks inside `index.html` — vanilla ES6 JavaScript |
| Fonts | Google Fonts: **Playfair Display** (headings) + **DM Sans** (body) |
| Form backend | Google Apps Script macro (POST endpoint, stores leads in Google Sheets) |
| Analytics | Google Analytics 4 (`G-4L3ZXGG3JZ`) + Meta Pixel (`1792763298349619`) |
| Hosting | Static file — served as-is from any web server |
| CI/CD | GitHub Actions with `anthropics/claude-code-action@beta` |

---

## Brand & Design System

### Color Palette (CSS Custom Properties)

```css
--primary:       #1a472a   /* Dark green — primary brand color */
--primary-light: #2d5f3f   /* Medium green */
--accent:        #d4af37   /* Gold — highlights, badges, hover states */
--accent-light:  #f0d98d   /* Light gold */
--text:          #1a1a1a   /* Near-black body text */
--text-light:    #4a4a4a   /* Secondary/muted text */
--background:    #fefdfb   /* Off-white cream page background */
--card-bg:       #ffffff   /* Pure white for cards */
--shadow:        rgba(26, 71, 42, 0.08)
--shadow-lg:     rgba(26, 71, 42, 0.12)
```

**Rule:** Always use these CSS variables — never hardcode colors. Preserve the green/gold/white scheme.

### Typography

- **Headings (`h1`, `h2`, `h3`, `.success-message h3`, `.agent-info h3`):** `Playfair Display`, Georgia, serif — weights 600/700/800
- **Body, labels, buttons, inputs:** `DM Sans`, -apple-system, sans-serif — weights 400/500/600
- Responsive font sizes use `clamp()` (e.g., `clamp(36px, 5vw, 58px)`)

### Tone & Voice

- Friendly, approachable, and educational — never salesy or pushy
- Empowering for nervous first-time buyers
- Clear and jargon-free
- Can serve both English and Spanish-speaking clients

---

## Page Structure

The page is divided into these sections (top to bottom):

1. **`<header>`** — Sticky top bar with "Complimentary Guide" badge and quick contact links (phone + email)
2. **`.hero` section** — Floating house emoji, H1 title, subtitle, four benefit cards
3. **`.form-section`** — Dark green gradient background containing the lead capture form
4. **`.agent-section`** — Agent bio card (Allan Vega) with contact details
5. **`<footer>`** — Copyright line (update the year annually; currently reads `© 2025`)

### Decorative Elements

- **`.bg-decoration`** — A `position: fixed` full-page `<div>` with `z-index: -1` and `opacity: 0.03` that renders a subtle two-color dot pattern using `radial-gradient`. Do not remove it — it adds warmth to the off-white background without affecting readability.
- **`.form-section::before`** — A CSS `::before` pseudo-element that overlays a faint repeating SVG cross/plus pattern (inline `data:image/svg+xml`) on the dark green form background. `opacity: 0.4`. Do not remove it.

### Benefit Cards (`.benefit-card`)

Four cards in a responsive CSS Grid (`repeat(auto-fit, minmax(280px, 1fr))`), each with an emoji icon, heading, and description. Current cards:

1. 💰 Financial clarity you can trust
2. 🗺️ A proven step-by-step process
3. 🏘️ Houston's hidden gems
4. 📊 Credit requirements demystified

---

## Lead Capture Form

**Form ID:** `#leadForm`
**Submit button ID:** `#submitBtn`
**Success message ID:** `#successMessage`

### Fields

| Field | Type | Required |
|-------|------|----------|
| `name` | text | Yes |
| `email` | email | Yes |
| `phone` | tel | Yes |
| `timeline` | select | No |
| `budget` | select | No |
| `area` | text | No |
| `mustHaves` | textarea | No |
| `utm_source/medium/campaign/content/term` | hidden | Auto-populated |
| `fbclid` | hidden | Auto-populated |
| `source` | hidden | Fixed: "Landing Page" |
| `propertyType` | hidden | Fixed: "First-Time Buyer" |

### Form Submission Flow

```
Page load → JS reads URL params → populates hidden UTM/fbclid fields
     ↓
User submits form
     ↓
JS disables button, changes text to "⏳ Sending your guide..."
     ↓
fetch() POST to Google Apps Script (mode: 'no-cors')
     ↓
.then() OR .catch() (both show success — no-cors means response is opaque)
     ↓
Fire fbq('track', 'Lead')
Fire gtag('event', 'generate_lead', { category: 'form', label: 'homebuyer_guide_download', value: 1 })
     ↓
Hide #leadForm → show #successMessage
```

**Note on `#successMessage`:** Its default state is `display: none` via CSS. The JS sets `style.display = 'block'` after submission. Do not alter this CSS default.

**Google Apps Script endpoint:**
```
https://script.google.com/a/macros/askozzie.com/s/AKfycbw8GCudDgtHqMee2qithkWXLxDYpAS5zebBF_NUm720Qrii3rzT0RuMIVyO6-CzQk4U/exec
```

---

## CSS Conventions

- **Class names:** kebab-case (`.benefit-card`, `.form-section`, `.agent-section`, `.trust-badge`)
- **IDs:** camelCase (`submitBtn`, `successMessage`, `leadForm`)
- **Hidden field IDs:** snake_case matching the URL param name (`utm_source`, `fbclid`)
- All styles live in a single `<style>` block in `<head>`
- Responsive breakpoint: `@media (max-width: 768px)`
- Layout: CSS Grid and Flexbox — no floats
- Transitions: prefer CSS (`transition: all 0.3s`) over JS animations
- Keyframe animations defined: `slideDown`, `fadeIn`, `float`, `fadeInUp`, `popIn`

---

## JavaScript Conventions

- Vanilla ES5/ES6 — no libraries, no imports
- IIFE pattern used for UTM capture to avoid global scope pollution
- `var` used throughout (ES5 style — maintain consistency, do not refactor to `let`/`const` unless asked)
- All JS lives in a single `<script>` block at the bottom of `<body>`
- The Google Apps Script URL is stored in `var SCRIPT_URL`

---

## Common Maintenance Tasks

### Updating text content
Edit the relevant HTML directly inside `index.html`. Keep the same element tags and class names.

### Updating benefit cards
Each card follows this template — maintain this structure:
```html
<div class="benefit-card">
    <span class="benefit-icon">EMOJI</span>
    <h3>Card Heading</h3>
    <p>Card description text.</p>
</div>
```

### Updating agent contact info
Agent details are in `.agent-section` (around line 612). The agent currently has **two phone numbers**:
- Primary: `281-865-7146`
- Secondary: `(832) 403-3681`

Both are linked with `href="tel:..."`. Update the name, title, license, phones, and email links there. The header quick-contact links (around line 483) show only the primary phone and email — keep those in sync when the primary contact changes.

### Updating timeline or budget dropdown options
Edit the `<option>` elements inside `#timeline` and `#budget` selects. Keep the `value` attribute consistent with what should be sent to the form backend.

### Adding a new section
- Insert a new `<section>` inside `<main class="container">`
- Add corresponding CSS inside the `<style>` block
- Follow existing patterns: card-based layout, green/gold/white colors, `fadeIn` animation

### Adding or updating builder deals / incentives
The `claude.yml` workflow instructions reference this as a common task (DR Horton, Lennar, Perry Homes, etc.). When adding builder deal cards, follow the same card structure as `.benefit-card` (emoji icon, heading, description). If a dedicated builder-deals section is created, give it a new class name (e.g., `.builder-section`) and mirror the CSS pattern of `.agent-section` or `.form-section`. Always include an expiration date for promotional content.

### Updating the form endpoint
Change the `SCRIPT_URL` variable at the top of the `<script>` block (around line 645).

### Updating the footer copyright year
The footer `<p>` tag currently reads `© 2025 Allan Vega | AEA Realty | All Rights Reserved`. Update the year as needed — no CSS or JS changes required, just edit the text.

---

## Analytics & Tracking IDs

| Service | ID |
|---------|----|
| Meta Pixel (Facebook) | `1792763298349619` |
| Google Analytics 4 | `G-4L3ZXGG3JZ` |

Both scripts load asynchronously in `<head>`. The Lead event fires after successful form submission. Do not remove or break these tracking integrations.

---

## GitHub Actions — Claude Code Integration

File: `.github/workflows/claude.yml`

The workflow triggers when any issue, PR, or comment **contains `@claude`**. It runs `anthropics/claude-code-action@beta` with write permissions and custom instructions.

**Permissions granted to the bot:**
- `contents: write`
- `issues: write`
- `pull-requests: write`
- `id-token: write`

**Required secret:** `ANTHROPIC_API_KEY` must be set in the repository's GitHub secrets.

**Note on `custom_instructions`:** The instructions embedded directly in `claude.yml` are a condensed subset (brand, market, common task types, basic rules) for space and brevity. This `CLAUDE.md` is the authoritative, full reference. When the two conflict, follow `CLAUDE.md`.

---

## Key Rules for AI Assistants

1. **Single file:** All changes go to `index.html` unless explicitly told otherwise.
2. **Preserve branding:** Never change the green/gold/white color scheme or font choices.
3. **Preserve sections:** Do not remove any existing sections unless explicitly instructed.
4. **Keep tone consistent:** Friendly, educational, approachable — not pushy.
5. **Keep analytics intact:** Do not remove or alter the Meta Pixel or Google Analytics scripts.
6. **No build process:** Do not introduce package.json, npm scripts, bundlers, or frameworks.
7. **Format consistency:** When adding builder deals, incentives, or new cards, match the existing card structure and CSS class patterns.
8. **Mobile-first:** Any new sections must be responsive. Use the existing 768px breakpoint.
9. **Validate HTML:** Ensure the file remains valid HTML5 with no unclosed tags.
10. **Do not change the form endpoint** unless explicitly asked — changing it will break lead capture.
11. **Preserve decorative elements:** Do not remove `.bg-decoration` or `.form-section::before` — they are intentional design elements, not dead code.
12. **Keep redirect pages minimal:** `guide.html` and `new-construction-guide.html` are intentionally plain. Do not add frameworks, styles, or scripts — users see them for under 2 seconds before being forwarded to Google Drive.
