# LWR Sites for Experience Cloud — Architecture & Foundations

## What Are LWR Sites?

LWR (Lightning Web Runtime) sites use the **Build Your Own (LWR)** and **Microsite (LWR)** templates
for Experience Cloud. They are built exclusively on the **Lightning Web Components (LWC)** programming
model — NOT Aura components. They deliver blazing fast digital experiences (websites, portals,
microsites) with publish-time freezing and HTTP caching.

Key characteristics:
- Built on **LWC** (core Web Components standards) — lightweight, native browser performance
- Includes **Apex** and **User Interface API** support for Salesforce record data
- Uses a **new publishing model**: components are frozen at publish time and served statically
- Uses **Lightning Web Security (LWS)** instead of Lightning Locker
- Supports **custom URL paths** (no `/s` suffix for sites created Winter '23+)
- Sites are **authenticated by default** (Winter '23+) but can include public pages
- Runs as a **Single Page Application (SPA)**

## Templates Available

| Template | Use Case |
|----------|----------|
| **Build Your Own (LWR)** | Minimal template for developers comfortable with LWC, Salesforce DX, UI API, and Apex. Requires adding your own custom pages and components. |
| **Microsite (LWR)** | Includes responsive layout, preconfigured pages, and content components for landing pages, event sites, etc. Less customization needed. |

## Key Differences from Aura Templates

### Publishing Model
- **Aura sites**: Components delivered dynamically; changes go live immediately without publishing.
- **LWR sites**: Components frozen at publish time. You MUST publish to propagate any changes
  (bug fixes, features, schema updates, managed component updates).
- Preview in Experience Builder always shows latest versions (dynamic serving).
- When org schema changes (fields added/removed, permission changes), you MUST republish.
- Publish one site at a time for live sites.

### Security: Lightning Web Security (LWS)
- LWR sites use **LWS** instead of Lightning Locker.
- LWS uses browser-native **JavaScript sandboxes** (ShadowRealm-like).
- Components CAN access global objects (`document`, `window`) but see virtualized versions.
- Components in the same namespace CAN interact with each other's DOM.
- Components in different namespaces are isolated.
- **Content Security Policy (CSP)** is `script-src 'self'` by default. Relax CSP in
  Settings > Security & Privacy when adding third-party scripts.

### URL Paths
- Winter '23+ LWR sites: no `/s` suffix — e.g., `https://mycustomdomain.com/mypage`
- Older authenticated LWR sites had `/s` in URLs.
- Visualforce pages accessible via `/sitePrefix/apex/pageName`.
- Use the `{basePath}` merge field for relative links in head markup.

### Caching Policy
- HTTP caching ON by default; turning off not recommended.
- Page content can be cached by CDN, browser, and server.
- Published site content treated as immutable — changes require republish.
- Guest users: Data from `@wire` cached up to 5 minutes.
- Authenticated users: Data NEVER cached via HTTP (fresh on every request).
- Images & static resources: cached for 180 days.
- `Cache-Control: no-store` available per-page for sensitive data.
- CDN caching available for enhanced LWR sites.

### Head Markup
- Add custom `<meta>`, `<link>`, `<script>` tags in **Settings > Advanced > Edit Head Markup**.
- Use `{basePath}` merge field for relative URLs.
- For third-party scripts, use the **`<x-oasis-script>`** privileged tag (replaces `<script>`).
- Standard `<script>` tags are blocked by CSP.
- Scripts loaded via `<x-oasis-script>` bypass shadow DOM boundaries and run inside a special
  iframe, always executed asynchronously.

### Accessibility
- **F6 navigation** for keyboard/screen-reader navigation between page regions.
- Custom theme layouts need explicit F6 enablement via `data-region="header"` etc.
- Screen readers supported; SPA route changes announced via `aria-live` region.
- All custom LWC should follow WCAG guidelines.
- No built-in skip link (must build your own).

## Site Limitations (500-Route Limit)

- Max **500 routes** (unique URLs) per LWR site. Best performance under 250.
- Use **dynamic content** and record details to reduce routes.
- Don't create individual pages per content item — use a single page with record details.
- Enhanced LWR sites: referential integrity for object API names in routes is **unsupported**.
  Renaming an object breaks page component references.
- Dynamic imports: only **statically analyzable** imports work:
  - ✅ `import("c/analyzable")`
  - ❌ `import("c/" + "analyzable")` or `import("c/" + componentName)`

## Unsupported Features

- Default Aura components (Chatter feeds, etc.)
- Default themes and theme management
- RTL languages in standard components
- Progressive rendering
- Favicon changes at site root domain
- Template/page/theme export and Lightning Bolt Solutions
- App-level Aura events (use `CustomEvent` instead)
- `$Browser` and other mobile/value providers
- `pageAccessor` and `pageAction` bindings for content components
- Some `@salesforce/i18n` module properties
- Some base Lightning components (see component limitations section)

## Experience Builder Limitations
- Undo/Redo buttons and some Help options unavailable
- Theme panel, branding sets, theme settings, CSS editor unavailable
- For enhanced LWR sites: must enable **"Show all components"** in Advanced panel each session
- Some hover actions and action menu options in property editor unavailable
- Stale record data possible when modifying records during preview (refresh browser)
- Experience admins can't access record pages for objects they lack access to
