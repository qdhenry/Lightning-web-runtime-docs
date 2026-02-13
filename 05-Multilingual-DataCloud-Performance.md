# LWR Sites — Multilingual, Data Cloud Integration & Performance

## Multilingual LWR Sites

Support up to **40 languages** per site.

### Translatable Content
- Text values in component properties (text, URLs, alt text)
- NOT available: CMS content translations (only non-CMS component properties)

### Setup Steps
1. **Add Language**: Settings panel in Experience Builder > Add language
2. Language appears in language selector in Experience Builder
3. To make language visible to visitors: enable in language selector component
4. Translate component properties per language in Experience Builder

### Language Detection
- Automatic detection based on browser `Accept-Language` header
- Fallback to default site language if browser language not supported
- Can configure automatic language detection in Settings

### Translation Workflow (Export/Import)
1. **Export**: Export translatable content as XLIFF or CSV files
2. Send to translators
3. **Import**: Import translated files back
4. Publish to make translations live

### Delete a Language
- Can delete added languages from Settings
- Deleting removes all translations for that language
- Default language cannot be deleted (change default first)

## Data Cloud Integration

### Overview
LWR sites can send visitor engagement data to **Data Cloud** for:
- Building detailed user profiles
- Enhanced analytics
- Site personalization
- Audience segmentation

### Experience Tag Manager
- JavaScript library that captures user-interaction events
- Combines events with site data to generate web events
- Auto-installed when connecting enhanced LWR sites to Data Cloud
- Events sent to Data Cloud's **Website Engagement DMO** (Data Model Object)

### Setup
1. Connect Experience Cloud site to Data Cloud during setup
2. Connection and data streams auto-installed in selected data space
3. Verify streams are active in Data Cloud setup

### Consent Management
- By default, Tag Manager does NOT send data until user explicitly opts in
- Configure opt-in/opt-out behavior:
  - **Opt-in (default)**: No data sent until explicit consent
  - **Opt-out**: Data sent by default, user can opt out
- Use consent banner/component to present options to visitors

### Event Tracking
Default interaction events mapped to Website Engagement DMO:

| Category | Events |
|----------|--------|
| **Cart** | cart-add, cart-remove, cart-replace, checkout, checkout-step |
| **Catalog** | catalog-object-click, catalog-object-impression, catalog-object-detail-view |
| **Consent** | set-consent |
| **Email** | email-subscribe, email-update |
| **Engagement** | page-view, click, scroll, form-submit, video-play, custom-event |
| **Error** | error-report |
| **Search** | search-query, search-click |
| **Wishlist** | wishlist-add, wishlist-remove |

### Custom Event Tracking
```javascript
// Send custom interaction events
import { track } from 'experience/tagManager';

track('engagement', {
    interactionName: 'custom-event',
    interactionProperties: {
        eventName: 'button-click',
        eventAction: 'submit-form'
    }
});
```

### Line Item Data
Line items represent individual items within a transaction:
- Used for cart, checkout, and catalog interactions
- Include properties: itemId, itemName, quantity, price, currency, category

## Performance Optimization

### Experience Delivery (Beta/Pilot)
A new hosting infrastructure for Build Your Own (LWR) sites featuring:

- **Server-Side Rendering (SSR)**: HTML rendered on server, not client
- **Dedicated CDN**: Content served from edge locations
- **Sub-second page load times**
- **Improved SEO**: Search engines receive fully rendered HTML
- **Enhanced security**: Reduced client-side attack surface

### Current Architecture (CSR)
Standard LWR sites use **client-side rendering (CSR)**: all HTML, JS, CSS, and assets
downloaded to client before rendering in browser.

### SSR Benefits
- First meaningful paint much faster
- Better Core Web Vitals scores
- Better for crawlers and social media previews
- Reduced JavaScript execution on client

### HTTP Caching Best Practices
- Keep caching ON (default) for best performance
- Guest user `@wire` data cached up to 5 minutes
- Static resources cached 180 days
- Use `Cache-Control: no-store` only for pages with truly sensitive data
- CDN caching available for enhanced LWR sites

### Route Optimization
- Stay under 250 routes for best performance (max 500)
- Use dynamic content patterns instead of individual pages
- Leverage record detail pages with a single route

### Publishing Best Practices
- Publish after ANY schema changes, component updates, or permission changes
- Test in Experience Builder preview (dynamic) before publishing (static)
- Only one site can be published at a time
- Publishing creates a snapshot of all components in their current state
