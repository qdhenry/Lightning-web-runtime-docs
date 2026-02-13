# B2B Commerce LWR — Performance, Image Optimization & Quick Reference

## Commerce LWR Storefront Performance Best Practices

Great performance is critical to commerce storefront success — fast loading sites achieve
**3 to 5x higher conversion rates** than slower sites.

### Performance Measurement Best Practices
- Measure before and after each change to quantify impact
- Use **Core Web Vitals** as primary metrics:
  - **LCP** (Largest Contentful Paint) — Loading performance
  - **FID/INP** (First Input Delay / Interaction to Next Paint) — Interactivity
  - **CLS** (Cumulative Layout Shift) — Visual stability
- Use industry-standard tools (Lighthouse, WebPageTest, Chrome DevTools)
- Analyze results and iterate

### CDN Caching Best Practices
- Enable CDN caching for enhanced LWR sites
- Static resources cached for 180 days
- Published site content treated as immutable
- Configure `Cache-Control` headers per page when needed
- Guest user `@wire` data cached up to 5 minutes

### Component Best Practices
- Minimize the number of components per page
- Avoid deep component nesting
- Use lazy loading for below-the-fold components
- Minimize `@wire` calls — batch data requests where possible
- Avoid unnecessary re-renders
- Use `disconnectedCallback` to clean up subscriptions and timers

### Image and Resource Optimization Best Practices
- Use responsive images with `srcset` and `sizes` attributes
- Compress images appropriately for web
- Use modern formats (WebP, AVIF) with fallbacks
- Lazy load images below the fold
- Set explicit width/height to prevent layout shifts (CLS)
- Use CDN for image delivery

### Network Optimization Best Practices
- Minimize HTTP requests
- Enable HTTP/2 for multiplexed connections
- Reduce JavaScript bundle sizes
- Defer non-critical scripts
- Use service workers for offline caching where appropriate

### Configuration Best Practices
- Optimize search configuration (limit filterable/searchable attributes)
- Configure appropriate caching policies per page type
- Use the publishing model efficiently (batch changes, then publish)
- Monitor and optimize Apex callouts in extensions/integrations

### Migration Recommendation
If currently using an **Aura storefront**, migrate to **LWR storefront** for optimal performance.

## Image Optimization Best Practices

Maintain fast and responsive sites across tablets, mobile, and desktop.

### Responsive Images with srcset and sizes
```html
<img 
    srcset="image-320w.jpg 320w,
            image-480w.jpg 480w,
            image-800w.jpg 800w"
    sizes="(max-width: 320px) 280px,
           (max-width: 480px) 440px,
           800px"
    src="image-800w.jpg"
    alt="Product image"
    width="800"
    height="600"
    loading="lazy"
/>
```

### Key Principles
- **`srcset`** — Define multiple image sources of different widths
- **`sizes`** — Define image element size based on viewport (use any length value, NOT percentages)
- Browser picks the most appropriate image based on screen size and resolution
- Always set explicit **width** and **height** attributes to prevent layout shifts
- Use `loading="lazy"` for images below the fold

### Image Format Best Practices
- Use WebP or AVIF for modern browsers with JPEG/PNG fallbacks
- Compress images to appropriate quality (60-80% for JPEG)
- Use appropriate dimensions — don't serve desktop-sized images to mobile

## Custom Rules for Product Readiness

Configure custom rules to determine when products are ready to be displayed in the storefront:
- Define conditions for product visibility
- Set up rules based on product attributes, inventory levels, pricing status
- Automate product readiness checks before publishing

## Enterprise Example: ElectroFast B2B Commerce

A real-world architecture pattern for a large B2B Commerce implementation:

### Product & Catalog Setup
- Use Product and Catalog data model to categorize products into B2B segments
- Cart and Inventory data models shared across B2B stores
- Real-time inventory and pricing in customer carts

### ERP Integration
- Integrate with existing ERP using Salesforce APIs and Cart Calculate API framework
- Synchronize order data between systems
- Reduce manual data entry and errors

### Multi-Channel Architecture
- Multiple B2B stores sharing common data models
- Custom pricing per buyer group/account
- Integration with Salesforce Order Management for fulfillment
- Service Cloud integration for post-sale support

## B2B Commerce on LWR — Complete Checklist

### Store Setup
- [ ] Commerce Experience Builder template selected
- [ ] WebStore configured with appropriate settings
- [ ] Product catalogs created and populated
- [ ] Buyer groups configured with entitlements
- [ ] Pricing: price books, negotiated prices, volume discounts set up
- [ ] Search: filterable/searchable attributes configured (within limits)
- [ ] Tax integration configured (extension or integration)
- [ ] Shipping integration configured (extension or integration)
- [ ] Payment gateway adapter created and connected

### Development Environment
- [ ] Salesforce CLI (SFDX) installed
- [ ] VS Code with Salesforce Extension Pack installed
- [ ] SFDX project created
- [ ] Org authorized
- [ ] Git repository initialized

### Custom Components
- [ ] Standard LWR Store Components reviewed before building custom
- [ ] Custom components use Commerce APIs (Connect API) for data
- [ ] Components configured with proper js-meta.xml targets
- [ ] `--dxp` styling hooks used for brand consistency
- [ ] Components tested with realistic data volumes
- [ ] Deployed and validated in sandbox before production

### Extensions & Integrations
- [ ] Commerce Extensions used (preferred over legacy Integrations)
- [ ] Pricing extension configured (if custom pricing needed)
- [ ] Inventory extension configured (if real-time inventory needed)
- [ ] Tax extension configured (if external tax service needed)
- [ ] Shipping extension configured (if external shipping needed)
- [ ] Payment gateway adapter implemented (server-side or client-side)
- [ ] ERP integration via Cart Calculate API framework (if applicable)

### Performance
- [ ] Core Web Vitals benchmarked (LCP, INP, CLS)
- [ ] CDN caching enabled for enhanced LWR sites
- [ ] Images optimized with srcset/sizes and lazy loading
- [ ] Component count and nesting minimized per page
- [ ] JavaScript bundles optimized
- [ ] Search configuration optimized (attribute limits respected)

### LWR Platform (from Guides 01-06)
- [ ] All LWR foundation requirements met (see Guide 06 checklist)
- [ ] Publishing workflow established (republish after ALL changes)
- [ ] Route count under 250 (max 500)
- [ ] CSP configured for third-party integrations
- [ ] `<x-oasis-script>` used for third-party scripts
- [ ] Accessibility: F6 navigation, screen reader support

## API Quick Reference

| API Resource | Base Endpoint Pattern |
|-------------|----------------------|
| Cart | `/commerce/webstores/{webstoreId}/carts` |
| Checkout | `/commerce/webstores/{webstoreId}/checkouts` |
| Products | `/commerce/webstores/{webstoreId}/products` |
| Product Categories | `/commerce/webstores/{webstoreId}/product-categories` |
| Search | `/commerce/webstores/{webstoreId}/search` |
| Pricing | `/commerce/webstores/{webstoreId}/pricing` |
| Promotions | `/commerce/webstores/{webstoreId}/promotions` |
| Order Summaries | `/commerce/webstores/{webstoreId}/order-summaries` |
| Addresses | `/commerce/webstores/{webstoreId}/accounts/{accountId}/addresses` |
| Tax | `/commerce/webstores/{webstoreId}/tax` |
| Wishlists | `/commerce/webstores/{webstoreId}/wishlists` |

## File Structure for Commerce SFDX Project

```
my-commerce-project/
├── force-app/main/default/
│   ├── lwc/
│   │   ├── myProductCard/
│   │   │   ├── myProductCard.html
│   │   │   ├── myProductCard.js
│   │   │   ├── myProductCard.css
│   │   │   └── myProductCard.js-meta.xml
│   │   ├── myCartSummary/
│   │   └── myCheckoutStep/
│   ├── classes/
│   │   ├── MyPaymentGatewayAdapter.cls     # Payment gateway
│   │   ├── MyTaxExtension.cls              # Tax extension
│   │   ├── MyShippingExtension.cls         # Shipping extension
│   │   └── MyPricingExtension.cls          # Pricing extension
│   └── experiences/
│       └── MyStore/                        # Store metadata
├── sfdx-project.json
└── README.md
```
