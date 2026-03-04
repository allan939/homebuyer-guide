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
├── privacy-policy.html           # Privacy policy page (CCPA/GDPR compliance, ad platform requirement)
├── allan-vega.jpg                # Agent headshot — NOT currently committed; see note below
├── README.md                     # Minimal project description
├── CLAUDE.md                     # This file
└── .github/
    └── workflows/
        └── claude.yml            # GitHub Actions: Claude Code bot triggered by @claude mentions
```

> **Note on `allan-vega.jpg`:** This file is referenced in `index.html` (`./allan-vega.jpg`) and in the OG/Twitter meta tags (as `https://www.loveyourdirt.com/allan-vega.jpg`), but it is **not currently committed to the repository**. The `<img>` tag has an `onerror` fallback (👤 emoji) that displays when the file is absent. Upload the headshot to the repo root to activate the photo on the live site.

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

**Agent info in redirect page bodies:** Both pages display brief agent contact info in the `<body>` while the redirect is in progress. There are currently several inconsistencies between them:

| Detail | `guide.html` | `new-construction-guide.html` |
|--------|-------------|-------------------------------|
| Brokerage | ✅ "AEA Realty" shown | ❌ Omitted |
| Phone format | Separate `<p>` line with `📲` emoji | Same line as name, no emoji |
| Full display | "Allan Vega – Realtor® \| AEA Realty" then "📲 281-865-7146" | "Allan Vega – Realtor® \| 281-865-7146" |

Keep both pages in sync when updating contact info.

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
| Analytics | Google Analytics 4 (`G-4L3ZXGG3JZ`) + Meta Pixel (`2075120536666616`) |
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
3. **`.social-proof-banner`** — Three key stats (families helped, rating, years experience) just above the form
4. **`.form-section`** — Dark green gradient background containing the lead capture form
5. **`.testimonials-section`** — Three client testimonial cards (replace placeholder text with real reviews)
6. **`.agent-section`** — Agent bio card (Allan Vega) with real headshot (`allan-vega.jpg`) and contact details
7. **`<footer>`** — Copyright line (`© 2026`) and Privacy Policy link

### Decorative Elements

- **`.bg-decoration`** — A `position: fixed` full-page `<div>` with `z-index: -1` and `opacity: 0.03` that renders a subtle two-color dot pattern using `radial-gradient`. Do not remove it — it adds warmth to the off-white background without affecting readability.
- **`.form-section::before`** — A CSS `::before` pseudo-element that overlays a faint repeating SVG cross/plus pattern (inline `data:image/svg+xml`) on the dark green form background. `opacity: 0.4`. Do not remove it.

### Agent Section (`.agent-section`)

The agent card contains a circular avatar (`<div class="agent-avatar">`) with an `<img>` tag pointing to `./allan-vega.jpg`. An `onerror` handler swaps in a `.agent-avatar-fallback` div (with the `👤` emoji) if the image fails to load. The agent's name, title, and contact details follow:

- **Name (`<h3>`):** Allan Vega
- **Title (`.agent-title`):** Houston Realtor® | Market Strategist
- **Brokerage:** AEA Realty | License #777820
- **Phones:** 281-865-7146 (primary) | (832) 403-3681 (secondary)
- **Email:** allan@askozzie.com

### Trust Badges (`.trust-badges`)

Three trust signals displayed below the form container (inside `.form-section`, outside `#leadForm`):

1. 🔒 Secure & Private
2. 📧 No Spam, Ever
3. ⚡ Instant Access

These are decorative/reassurance elements. Do not remove them.

### Social Proof Banner (`.social-proof-banner`)

A gold-tinted stat strip displayed just above `.form-section`. Three `.proof-stat` items separated by `.proof-divider` bars:

1. **50+** Houston families helped
2. **5★** Average client rating
3. **10+** Years in Houston real estate

Update these numbers as milestones are reached. Each stat has a `.stat-number` (Playfair Display, 32px) and a `.stat-label`.

### Testimonials Section (`.testimonials-section`)

Three `.testimonial-card` elements in a responsive grid (`repeat(auto-fit, minmax(280px, 1fr))`). Each card has:
- `.testimonial-stars` — five gold star characters
- `.testimonial-quote` — italic quote text
- `.testimonial-author` — `.testimonial-name` + `.testimonial-meta` (role/city)

**Current testimonials (verify authenticity before driving paid traffic):**

| Name | Role | Location |
|------|------|----------|
| Maria & Carlos R. | First-Time Buyers | Katy, TX |
| DeShawn M. | New Construction Buyer | Cypress, TX |
| Sgt. James T. (Ret.) | VA Loan Buyer | Sugar Land, TX |

These appear to be real client names and cover all three target audiences (first-time buyers, new construction, VA loans). Confirm each is a verified review before running paid ads. Keep the same HTML structure when updating.

### Benefit Cards (`.benefit-card`)

Four cards in a responsive CSS Grid (`repeat(auto-fit, minmax(280px, 1fr))`), each with an emoji icon, heading, and description. Current cards:

1. 💰 Financial clarity you can trust
2. 🗺️ A proven step-by-step process
3. 🏘️ Houston's hidden gems
4. 📊 Credit requirements demystified

---

## Lead Capture Form

**Form ID:** `#leadForm`
**Submit button ID:** `#submitBtn` — initial text: `📩 Send My Free Guide`; changes to `⏳ Sending your guide...` while submitting
**Success message ID:** `#successMessage` — full text after submission:
> "Your Houston First-Time Homebuyer Guide is on its way to your inbox. Check your email in the next few minutes — and don't forget to check spam just in case!"

**Optional fields toggle:** A `<button id="optionalToggle">` toggles `<div id="optionalFields">` open/closed using a CSS class `.open` which switches from `display:none` to `display:contents`. The button's `aria-expanded` attribute and the `＋/－` icon (`#toggleIcon`) update on each click. Timeline, budget, area, and mustHaves fields are inside this collapsible group — keeping the form initially to just 3 required fields for lower friction.

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

**Dropdown option values (must match what the backend expects):**

`#timeline` options: `ASAP` | `1-3 months` | `3-6 months` | `6-12 months` | `Just researching`

`#budget` options: `Under $200k` | `$200k-$250k` | `$250k-$300k` | `$300k-$400k` | `$400k+`

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
- **Accessibility:** `.skip-link` is visually hidden until focused (`:focus { top: 0 }`); all form inputs have `aria-required`, `autocomplete`, and `aria-label` where needed; decorative dividers have `aria-hidden="true"`; landmark regions use `aria-labelledby`

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
Agent details are in `.agent-section` (around line 874). The agent currently has **two phone numbers**:
- Primary: `281-865-7146`
- Secondary: `(832) 403-3681`

Both are linked with `href="tel:..."`. Update the name, title, license, phones, and email links there. The header quick-contact links (around line 684) show only the primary phone and email — keep those in sync when the primary contact changes.

### Updating timeline or budget dropdown options
Edit the `<option>` elements inside `#timeline` and `#budget` selects. Keep the `value` attribute consistent with what should be sent to the form backend.

### Adding a new section
- Insert a new `<section>` inside `<main class="container">`
- Add corresponding CSS inside the `<style>` block
- Follow existing patterns: card-based layout, green/gold/white colors, `fadeIn` animation

### Adding or updating builder deals / incentives
The `claude.yml` workflow instructions reference this as a common task (DR Horton, Lennar, Perry Homes, etc.). When adding builder deal cards, follow the same card structure as `.benefit-card` (emoji icon, heading, description). If a dedicated builder-deals section is created, give it a new class name (e.g., `.builder-section`) and mirror the CSS pattern of `.agent-section` or `.form-section`. Always include an expiration date for promotional content.

### Updating the form endpoint
Change the `SCRIPT_URL` variable at the top of the `<script>` block (around line 910).

### Updating the footer copyright year
The footer currently reads `© 2026 Allan Vega | AEA Realty | All Rights Reserved`. Update the year as needed — no CSS or JS changes required, just edit the text. The footer also contains a link to `privacy-policy.html`.

### Updating the agent headshot
Replace `allan-vega.jpg` in the repository root with a new photo. The `<img>` in `.agent-avatar` has an `onerror` fallback that shows the `👤` emoji if the file is missing or fails to load. The OG image meta tags also reference this file — update the URL there too if hosting changes.

### Updating testimonials
Testimonial cards are in `.testimonials-section` inside `index.html`. Three named testimonials are currently in place (Maria & Carlos R., DeShawn M., Sgt. James T.). Verify these are real before driving paid traffic, or replace with confirmed reviews. Keep the existing card HTML structure (`.testimonial-card > .testimonial-stars + .testimonial-quote + .testimonial-author`).

### Updating social proof stats
The `.social-proof-banner` contains three `.proof-stat` blocks. Edit the `.stat-number` and `.stat-label` text directly in `index.html`. Update these as milestones are reached (e.g., "75+ Houston families helped").

---

## Analytics & Tracking IDs

| Service | ID |
|---------|----|
| Meta Pixel (Facebook) | `2075120536666616` |
| Google Analytics 4 | `G-4L3ZXGG3JZ` |

Both scripts load asynchronously in `<head>`. The Lead event fires after successful form submission. Do not remove or break these tracking integrations.

---

## GitHub Actions — Claude Code Integration

File: `.github/workflows/claude.yml`

The workflow triggers on four GitHub events, all requiring `@claude` to appear in the relevant text body:

| Event | Trigger condition |
|-------|------------------|
| `issue_comment` (created) | Comment body contains `@claude` |
| `pull_request_review_comment` (created) | Comment body contains `@claude` |
| `issues` (opened) | Issue body contains `@claude` |
| `pull_request` (opened) | PR body contains `@claude` |

It runs `anthropics/claude-code-action@beta` with write permissions and custom instructions.

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
13. **Testimonials must be verified:** The testimonial cards in `.testimonials-section` currently display named clients (Maria & Carlos R., DeShawn M., Sgt. James T.). Confirm these are verified real reviews before driving paid traffic to the page.
14. **Agent photo is missing from the repo:** `allan-vega.jpg` is referenced by `index.html` and the OG meta tags but is **not currently committed**. Add the headshot file to the repo root to activate it. A graceful fallback emoji (👤) is already in place for when the file is absent.
15. **Privacy policy is live:** `privacy-policy.html` is required for Meta/Google ad compliance. Do not delete it. Update it if contact details or third-party services change.
