
[README.md](README.md)

---
name: Akash Brand & Anti-AI-UI Standards
description: Enforces Akash's UI/UX and branding conventions when building, styling, or reviewing any website, app, landing page, or dashboard UI. Use this whenever generating frontend code or design — to avoid generic "AI-generated" visual patterns (default gradients, heavy borders/shadows, unnecessary badges, excessive emoji) and to auto-apply Akash's developer attribution, project-specific email, and legal footer link.
---

# Instructions

## 1. Avoid "AI-generated" look UI

When writing any UI/frontend code (landing pages, dashboards, cards, buttons, sections, etc.), avoid the following **unless the user explicitly asks for them**:

- Gradient backgrounds, buttons, or text used as *default* styling — use solid, purposeful colors instead
- Heavy or decorative borders, especially colored borders combined with rounded corners on every element
- Large `border-radius` applied uniformly everywhere "by default" — use radius intentionally, not as a blanket style
- Drop shadows / glow effects on cards, buttons, or sections added "just because" — shadows should signal real elevation/hierarchy, not decorate
- Badges, pills, or tags added without functional meaning (e.g. "NEW", "✨ AI-Powered", "🔥 Trending") unless the content genuinely needs a status indicator
- Emoji in headings, buttons, nav items, or UI copy — never use emoji in UI text unless the user explicitly asks for a playful brand
- Glassmorphism / neumorphism / blurred backgrounds used as a default style choice
- The generic "hero with gradient blob background + centered bold headline + subtext + two buttons" pattern — vary layout and structure meaningfully instead of defaulting to it

**Default to:** a restrained palette (2–3 colors max + neutrals), clear typographic hierarchy, real whitespace, and grid alignment as the primary design tools — not decoration layered on top of a generic template.

## 2. Developer attribution

Every site/app built must credit the developer, in the footer or an About/Credits section:

- Text: `Developer: CODEGUYAKASH` — always in full caps, never "Akash" in this line
- `CODEGUYAKASH` must be a clickable link to `https://codeguyakash.in`, opening in a new tab
- Akash's first name ("Akash") can be used elsewhere in the app (About, Contact) — the credit line specifically always says CODEGUYAKASH

## 3. Contact email — dynamic per project

Akash runs a catch-all inbox on `codeguyakash.in`, so every new project gets its own email automatically — never hardcode a single shared address.

- Pattern: `<project-slug>@codeguyakash.in` (lowercase, no spaces, derived from the app/project name)
  - Examples: `tabix@codeguyakash.in`, `seenify@codeguyakash.in`, `app@codeguyakash.in`
- When building a new app/site, infer the slug from the project's name and use it automatically for the Contact/footer/support email — don't ask the user unless the project name is genuinely ambiguous
- Fallback inbox `to@codeguyakash.in` is only for cases with no clear project name to derive a slug from

## 4. Legal footer link

Every footer must include:

- Text: `Learn More Privacy Term`
- Links to: `https://legal.codeguyakash.in`
- This is Akash's single universal Privacy Policy + Terms page shared across all projects — never generate a separate or custom privacy/terms page unless explicitly asked to

## 5. Footer template (reference)

```html
<footer>
  <p>Developer: <a href="https://codeguyakash.in" target="_blank" rel="noopener">CODEGUYAKASH</a></p>
  <p><a href="mailto:PROJECT_SLUG@codeguyakash.in">PROJECT_SLUG@codeguyakash.in</a></p>
  <a href="https://legal.codeguyakash.in" target="_blank" rel="noopener">Learn More Privacy Term</a>
</footer>
```

Replace `PROJECT_SLUG` with the current project's name, lowercased.
