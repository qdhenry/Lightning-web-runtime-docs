# B2B Commerce — Custom Components, Open Source & Storefront Development

## Building Custom Components for B2B Commerce

B2B Commerce stores on LWR include dozens of **standard Lightning web components** for product
listings, cart, checkout, search, etc. Before building custom components, review the existing
LWR Store Components available in the Commerce component library.

### Development Prerequisites
- Salesforce Developer Experience (SFDX) environment
- Visual Studio Code with Salesforce Extension Pack
- Salesforce CLI installed and configured
- Git for version control

### Component Architecture
Custom B2B Commerce components follow the same LWC pattern as standard LWR:
- **HTML** — Template/structure
- **JavaScript** — Business logic, event handling, API calls
- **CSS** — Styling (use --dxp hooks for brand consistency)
- **js-meta.xml** — Component configuration and Experience Builder metadata

### Step-by-Step: Build a Custom Commerce Component

#### 1. Create an SFDX Project
```bash
sf project generate --name my-commerce-components
cd my-commerce-components
```

#### 2. Authorize Your Org
```bash
sf org login web --alias mycommerceorg
```

#### 3. Create a Lightning Web Component
```bash
sf lightning generate component --name myProductCard --output-dir force-app/main/default/lwc
```

#### 4. Configure for Commerce Experience Builder
```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>62.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightningCommunity__Page</target>
        <target>lightningCommunity__Default</target>
    </targets>
    <targetConfigs>
        <targetConfig targets="lightningCommunity__Default">
            <property name="productId" type="String" />
            <property name="showPrice" type="Boolean" default="true" />
        </targetConfig>
    </targetConfigs>
</LightningComponentBundle>
```

#### 5. Deploy to Your Store
```bash
sf project deploy start --target-org mycommerceorg
```

#### 6. Add to Store in Experience Builder
- Open Experience Builder for your Commerce store
- Drag the component from the Components panel onto a page
- Configure component properties in the property panel

## Open Source Commerce Components

Salesforce provides **open source components** with editable source code for standard B2B store
components. This allows you to modify functionality without building from scratch.

### How to Use Open Source Components
1. **Clone** the public Git repository
2. **Customize** and extend components to meet store requirements
3. **Deploy** updated versions to any B2B store using SFDX and VS Code
4. **Validate and test** in a sandbox environment before production

### Example: Personalize Product Pricing
Customize the Product Pricing Details component to:
- Display currency as code (USD) instead of symbol ($)
- Highlight main price in a specific color
- Show percent difference between main and strikethrough price

### Best Practices
- Always validate and test changes in sandbox before production
- See the README.md in the Git repository for detailed instructions
- Open source components are available for the LWR template

## Public Commerce LWR Library

The Public Commerce LWR Library provides reusable commerce components and utilities:
- Pre-built commerce UI patterns
- Storefront API wrappers
- Commerce-specific utility functions

### Storefront APIs
Commerce storefronts expose APIs for:
- Product data retrieval
- Cart management
- Search and filtering
- Category navigation
- User session management

## Pricing Component Example

### Create a Pricing Component with Discount Percentage

Build a custom component that displays:
- Original (list) price with strikethrough
- Discounted (negotiated) price highlighted
- Calculated discount percentage

```javascript
import { LightningElement, api } from 'lwc';

export default class PricingDisplay extends LightningElement {
    @api listPrice;
    @api negotiatedPrice;
    
    get hasDiscount() {
        return this.negotiatedPrice < this.listPrice;
    }
    
    get discountPercentage() {
        if (!this.hasDiscount) return 0;
        return Math.round((1 - this.negotiatedPrice / this.listPrice) * 100);
    }
    
    get formattedListPrice() {
        return new Intl.NumberFormat('en-US', {
            style: 'currency', currency: 'USD'
        }).format(this.listPrice);
    }
    
    get formattedNegotiatedPrice() {
        return new Intl.NumberFormat('en-US', {
            style: 'currency', currency: 'USD'
        }).format(this.negotiatedPrice);
    }
}
```

## Commerce Einstein Recommendations

You can create Commerce Einstein Recommendations components to:
- Display personalized product recommendations
- Show "frequently bought together" suggestions
- Present "customers also viewed" products
- Integrate AI-driven product discovery

## Key Differences: Commerce LWC vs Standard LWR LWC

| Aspect | Standard LWR | Commerce LWR |
|--------|-------------|--------------|
| Template | Build Your Own / Microsite | Commerce Experience Builder |
| Pre-built Components | Minimal | Dozens of commerce components |
| Data Source | UI API, Apex | Commerce APIs, Connect API |
| Data Model | Generic Salesforce objects | Commerce-specific objects (WebStore, WebCart, etc.) |
| Extensions | N/A | Commerce Extensions for pricing, tax, shipping, inventory |
| Payment | N/A | Payment gateway adapter framework |
| Search | Custom implementation | Built-in commerce search with facets, filters, sort |
| Checkout | Custom build | Pre-built checkout flow with integration hooks |

## Component Best Practices for Commerce

- Review existing standard components before building custom ones
- Use the Commerce APIs (Connect API) for data access, not generic UI API
- Leverage `--dxp` styling hooks for brand-consistent commerce components
- Test components with realistic data volumes (large catalogs, many buyer groups)
- Consider performance: lazy load images, minimize wire calls, use caching
- Validate in sandbox before deploying to production stores
