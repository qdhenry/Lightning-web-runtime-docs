# Building LWR Sites — Components, Pages & Layouts

## Creating an LWR Site

1. From Setup > Quick Find > "Digital Experiences" > **All Sites** > **New**
2. Select **Build Your Own (LWR)** or **Microsite (LWR)** template
3. Name your site and configure URL
4. Sites created Winter '23+ are **enhanced LWR sites** (partial deploy, content search, easy CMS)
5. Authenticated by default; can add public pages

## Page Types

### Standard Pages
- **Search** page available as standard page with route/URL parameter support
- Some standard pages like Account Management are **unsupported**

### Object Pages
- Use **User Interface API** for record data retrieval
- Only create object pages for **supported objects** in UI API
- **No generic record pages** — must create per specific object
- If using menu items linking to Salesforce objects, MUST create corresponding object pages

### Login Pages
- Use Login, Forgot Password, Register pages from **Login Page** standard page category
- Login pages appear in the login experience, NOT the site experience

### Content Pages
- Access CMS content from Enhanced CMS workspaces
- Set up Data Binding content components in Experience Builder

## Component Development

### Target Types in js-meta.xml

| Target | Purpose |
|--------|---------|
| `lightningCommunity__Page` | Drag-and-drop component on LWR/Aura site pages |
| `lightningCommunity__Page_Layout` | Page layout component in LWR sites |
| `lightningCommunity__Theme_Layout` | Theme layout component in LWR sites |
| `lightningCommunity__Default` | Configure design-time properties for Experience Builder |

### Sample js-meta.xml Configuration
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
            <property name="headerText" type="String" default="Welcome" />
            <property name="showBorder" type="Boolean" default="true" />
            <property name="maxItems" type="Integer" default="5" />
        </targetConfig>
    </targetConfigs>
</LightningComponentBundle>
```

### Property Rules
- **Always use camelCase** for property names in both `.js` and `js-meta.xml` files
- **Never capitalize the first letter** — it can break the component
- Default value in `js-meta.xml` determines out-of-the-box value
- If no default in XML, the `@api` variable value in `.js` is used
- Properties can be further edited via component property panel in Experience Builder

### Screen-Responsive Properties (Enhanced LWR Sites)
For screen-responsive components:

1. In `js-meta.xml`, add `screenResponsive="true"` and `exposedTo="css"`:
```xml
<property name="maxHeight" type="Integer" screenResponsive="true" exposedTo="css" />
```

2. In `.css`, use CSS variable `--dxp-c-<propertyName>` with media queries:
```css
:host {
    --component-max-height: var(--dxp-c-maxHeight, 200px);
}
@media (max-width: 768px) {
    :host {
        --component-max-height: var(--dxp-c-maxHeight, 100px);
    }
}
```

## Theme Layouts

Theme layouts define shared regions (header/footer) across site pages.

### Creating Theme Layout Components
```html
<!-- myThemeLayout.html -->
<template>
    <header>
        <slot name="header"></slot>
    </header>
    <main>
        <slot></slot> <!-- default slot = body/content region -->
    </main>
    <footer>
        <slot name="footer"></slot>
    </footer>
</template>
```

Key rules:
- **Named slots** (`<slot name="header">`) replace Aura component attributes
- **Default slot** (unnamed `<slot>`) replaces `{!v.body}` in Aura
- If theme layout exposes design properties, declare in `lightningCommunity__Default` targetConfig
- Assign theme layouts to pages in Experience Builder

### F6 Navigation in Custom Theme Layouts
Add `data-region` attributes for keyboard navigation:
```html
<div data-region="header">
    <slot name="header"></slot>
</div>
```

## Custom Layout Components

Layouts use LWC instead of Aura components:
- Use **slots** to define configurable regions
- Named slots = component attributes; default slot = `{!v.body}`
- See `Pass Markup into Slots` in LWC Dev Guide

## Navigation

### Lightning Navigation API
```javascript
import { NavigationMixin } from 'lightning/navigation';

export default class MyComponent extends NavigationMixin(LightningElement) {
    navigateToPage() {
        this[NavigationMixin.Navigate]({
            type: 'comm__namedPage',
            attributes: {
                name: 'My_Custom_Page__c'
            }
        });
    }
}
```

Rules:
- Use `name` attribute (NOT deprecated `pageName`) for `comm__namedPage`
- Page name = its **API name** (configurable during creation)
- Record detail page URLs include `detail` as recordName; canonical redirect happens on visit
- If user lacks record access, they see an error page (no page layout variants for error cases)

### Custom Navigation Menu Component
1. Configure navigation menu in **Settings > Navigation**
2. Create `NavigationLinkSet` and `NavigationMenuItems`
3. Build custom LWC with Apex controller to get navigation items
4. If menu items link to Salesforce objects, create corresponding object pages
5. Use Apex `ConnectApi.NavigationMenu.getCommunityNavigationMenu()` method

## Publishing Rules
- You MUST publish to make changes live
- You CAN publish at any time, even when unchanged
- Publish only one site at a time for live sites
- Schema changes, component updates, bug fixes ALL require republishing
- Preview in Experience Builder always shows latest (unpublished) changes
