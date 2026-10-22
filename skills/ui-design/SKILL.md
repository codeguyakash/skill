---
name: ui-design
description: Enforces Akash's UI/UX and branding conventions when building, styling, or reviewing any website, app, landing page, or dashboard. Use whenever generating frontend code or design, to avoid generic AI-looking UI and to include required footer, contact, legal, and changelog.
---

# UI Design

## 1. Avoid the "AI-generated" look
Avoid these **unless the user explicitly asks**:
- Gradient backgrounds, buttons, or text as default styling. Use solid, purposeful colors.
- Heavy or decorative borders; colored borders + rounded corners on everything.
- Large `border-radius` applied uniformly. Use radius intentionally.
- Drop shadows / glows added "just because". Shadows signal real elevation only.
- Badges, pills, tags without function ("NEW", "AI-Powered", "Trending").
- Emoji in headings, buttons, nav, or UI copy.
- Glassmorphism / neumorphism / blurred backgrounds as a default.
- The generic hero: gradient blob + centered bold headline + subtext + two buttons. Vary layout and structure.

**Default to:** 2-3 colors max + neutrals, clear type hierarchy, real whitespace, grid alignment. Hierarchy comes from size, weight, spacing, and contrast, not decoration.

## 2. Design tokens (define first, use everywhere)
```css
:root {
  /* color: neutrals + one accent */
  --bg: #ffffff;  --surface: #f6f6f7;  --border: #e2e2e5;
  --text: #111113; --text-muted: #5f6066;
  --accent: #2563eb; --accent-contrast: #ffffff;
  --danger: #dc2626; --success: #16a34a; --warning: #d97706;

  /* spacing: 4px scale */
  --space-1: 4px; --space-2: 8px; --space-3: 12px; --space-4: 16px;
  --space-6: 24px; --space-8: 32px; --space-12: 48px; --space-16: 64px;

  /* type */
  --font-sans: ui-sans-serif, system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --text-sm: 0.875rem; --text-base: 1rem; --text-lg: 1.25rem;
  --text-xl: 1.75rem; --text-2xl: 2.5rem;

  --radius: 6px;   /* one radius, used with intent */
  --focus: 2px solid var(--accent);
}
@media (prefers-color-scheme: dark) {
  :root {
    --bg: #0e0e10; --surface: #17171a; --border: #2a2a2f;
    --text: #f2f2f3; --text-muted: #9a9aa2;
  }
}
```
Never hardcode colors or spacing in components; reference tokens.

## 3. Layout & responsiveness
- Mobile-first. Breakpoints: 640 / 768 / 1024 / 1280.
- CSS Grid for page layout, Flexbox for components. Max content width ~1200px; text measure 60-75ch.
- Use `rem`/`clamp()` for fluid type; no fixed pixel widths for containers.
- No horizontal scroll at 320px.
- Touch targets ≥ 44x44px.

## 4. Accessibility (WCAG 2.2 AA)
- Semantic elements: `header nav main section article footer button a`. A `div` is not a button.
- Contrast ≥ 4.5:1 text, 3:1 UI components.
- Visible `:focus-visible` outline on everything interactive; never `outline: none` without replacement.
- Labels on all inputs; `alt` on images (empty alt if decorative); `aria-label` on icon-only buttons.
- Full keyboard operation; logical tab order; trap focus in modals, restore on close.
- Respect `prefers-reduced-motion`.
- Announce dynamic updates with `aria-live`.

## 5. Components must have all states
default, hover, focus-visible, active, disabled, loading, error, empty. See `skills/event-feedback`.

## 6. Performance
- Images: modern formats (WebP/AVIF), explicit `width`/`height`, `loading="lazy"` below the fold, `next/image` in Next.js.
- Fonts: system stack by default; if custom, `font-display: swap`, subset, preload one weight.
- Animate only `transform`/`opacity`; keep transitions 150-250ms.
- Avoid layout shift (CLS < 0.1); LCP < 2.5s; INP < 200ms.
- Code-split routes; lazy-load heavy components.

## 7. Required: Developer attribution
Every site/app must credit the developer in the footer or an About/Credits section.
- Text: `Developer: CODEGUYAKASH`, always full caps, never "Akash" in this line.
- `CODEGUYAKASH` is a link to `https://codeguyakash.in`, opening in a new tab.
- "Akash" can appear elsewhere (About, Contact); the credit line always says CODEGUYAKASH.

## 8. Required: Contact email, dynamic per project
Catch-all inbox on `codeguyakash.in`; every project gets its own address.
- Pattern: `<project-slug>@codeguyakash.in` (lowercase, no spaces, derived from project name). Examples: `tabix@codeguyakash.in`, `seenify@codeguyakash.in`.
- Infer the slug from the project name; don't ask unless genuinely ambiguous.
- Fallback `to@codeguyakash.in` only when no project name exists.

## 9. Required: Legal footer link
Every footer includes the text `Learn More Privacy Term` linking to `https://legal.codeguyakash.in`. Do not generate separate privacy/terms pages unless explicitly asked.

## 10. Required: Changelog page
Every project ships a `/changelog` route.
- Footer links to it with the text `Changelog`, near the version number if shown.
- Newest first; each entry has version (or date), short title, 2-5 bullets grouped Added / Fixed / Changed.
- New projects start with `v0.1.0 — Initial release`.
- Every shipped feature or fix on an existing project adds a new entry, without being asked.

```
## v1.2.0 — 2026-10-23

### Added
- Short description of new feature

### Fixed
- Short description of bug fix

### Changed
- Short description of changed behavior
```

## 11. Footer template
```html
<footer>
  <p>Developer: <a href="https://codeguyakash.in" target="_blank" rel="noopener">CODEGUYAKASH</a></p>
  <p><a href="mailto:PROJECT_SLUG@codeguyakash.in">PROJECT_SLUG@codeguyakash.in</a></p>
  <a href="/changelog">Changelog</a>
  <a href="https://legal.codeguyakash.in" target="_blank" rel="noopener">Learn More Privacy Term</a>
</footer>
```
Replace `PROJECT_SLUG` with the lowercased project name.

## 12. UI review checklist
- [ ] No gradients, glows, emoji, decorative badges, uniform big radius
- [ ] Tokens used; no hardcoded colors/spacing
- [ ] Looks right at 375 / 768 / 1280
- [ ] Keyboard + screen-reader usable; contrast passes
- [ ] All states present; feedback on every action
- [ ] Footer: developer credit, project email, Changelog, legal link
- [ ] `/changelog` entry added
- [ ] Lighthouse: performance, accessibility, best practices ≥ 90
