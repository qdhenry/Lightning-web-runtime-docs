# LWR Sites — Quick Reference for LLM-Driven Development

## Critical Gotchas

1. **Must Republish After Changes**: Unlike Aura, ALL changes require republishing.
   Schema changes, component updates, label changes, permission changes — republish!

2. **No Generic Record Pages**: Must create object-specific pages. No fallback record page.

3. **500 Route Limit**: Hard limit. Design with dynamic content to minimize routes.

4. **Dynamic Imports Must Be Static**: `import("c/myComp")` works;
   `import("c/" + name)` does NOT.

5. **Property Names Must Be camelCase**: Never capitalize first letter in `@api` properties
   or js-meta.xml — it breaks the component.

6. **Shadow DOM Blocks Third-Party Scripts**: Use `<x-oasis-script>` tag, NOT `<script>`.

7. **No platform-show-toast-event**: Use `toast-container` component instead.

8. **No Lightning Locker**: LWR uses Lightning Web Security (LWS) — different security model.

9. **Guest User API Access**: Must explicitly enable "Allow guest users to access public APIs"
   for UI API, CMS, and Knowledge access.

10. **Referential Integrity Not Enforced in Routes**: Renaming objects breaks page references
    in enhanced LWR sites.

11. **CSP Strict by Default**: Must set to Relaxed for third-party scripts and add to
    Trusted Sites.

12. **Unpublished Reference Updates**: After renaming objects/resources, unpublished sites
    may take up to 2 hours to update references.

## Component Configuration Template

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
            <!-- String property -->
            <property name="headerText" type="String" default="Hello" />
            <!-- Boolean property -->
            <property name="showBorder" type="Boolean" default="true" />
            <!-- Integer property -->
            <property name="maxItems" type="Integer" default="5" />
            <!-- Screen-responsive property (enhanced LWR only) -->
            <property name="maxHeight" type="Integer"
                      screenResponsive="true" exposedTo="css" />
        </targetConfig>
    </targetConfigs>
</LightningComponentBundle>
```

## Theme Layout Component Template

```html
<template>
    <div data-region="header">
        <slot name="header"></slot>
    </div>
    <div data-region="content">
        <slot></slot>
    </div>
    <div data-region="footer">
        <slot name="footer"></slot>
    </div>
</template>
```

```xml
<!-- js-meta.xml for theme layout -->
<targets>
    <target>lightningCommunity__Theme_Layout</target>
    <target>lightningCommunity__Default</target>
</targets>
```

## Navigation Pattern

```javascript
import { LightningElement } from 'lwc';
import { NavigationMixin } from 'lightning/navigation';

export default class NavExample extends NavigationMixin(LightningElement) {
    // Navigate to named page
    goToPage() {
        this[NavigationMixin.Navigate]({
            type: 'comm__namedPage',
            attributes: { name: 'My_Page__c' }
        });
    }

    // Navigate to record
    goToRecord(recordId) {
        this[NavigationMixin.Navigate]({
            type: 'standard__recordPage',
            attributes: {
                recordId: recordId,
                actionName: 'view'
            }
        });
    }

    // Generate URL
    connectedCallback() {
        this[NavigationMixin.GenerateUrl]({
            type: 'comm__namedPage',
            attributes: { name: 'Home' }
        }).then(url => { this.homeUrl = url; });
    }
}
```

## --dxp Styling Pattern

```css
/* Custom component using --dxp hooks */
:host {
    --component-bg: var(--dxp-g-root);
    --component-text: var(--dxp-g-root-contrast);
    --component-brand: var(--dxp-g-brand);
    --component-link: var(--dxp-s-link-text-color);

    background-color: var(--component-bg);
    color: var(--component-text);
    font-family: var(--dxp-g-font-family);
}

.heading {
    color: var(--dxp-s-text-heading-large-color);
    font-size: var(--dxp-s-text-heading-large-font-size);
}

.btn-primary {
    background: var(--dxp-s-button-primary-color-background);
    color: var(--dxp-s-button-primary-color);
}
```

## Pre-Development Checklist

- [ ] Template selected: Build Your Own (LWR) or Microsite (LWR)
- [ ] Enhanced LWR site platform confirmed (Winter '23+)
- [ ] Object pages created for all Salesforce objects used in navigation
- [ ] Guest user API access enabled (if public pages needed)
- [ ] CSP set to Relaxed (if using third-party scripts)
- [ ] Third-party script URLs added to Trusted Sites
- [ ] `<x-oasis-script>` used for third-party library loading
- [ ] --dxp styling hooks enabled (older sites need head markup update)
- [ ] Route count estimated (stay under 250, max 500)
- [ ] All property names verified as camelCase
- [ ] Republish plan established for deployment workflow
- [ ] F6 navigation enabled in custom theme layouts
- [ ] Screen reader aria-live regions configured for SPA navigation
- [ ] Data Cloud integration configured (if analytics needed)
- [ ] Consent management configured for data tracking
- [ ] Languages configured (if multilingual)

## File Structure for LWR Component

```
force-app/main/default/lwc/
├── myComponent/
│   ├── myComponent.html          # Template
│   ├── myComponent.js            # Controller
│   ├── myComponent.css           # Styles (use --dxp hooks)
│   └── myComponent.js-meta.xml   # Configuration & targets
├── myThemeLayout/
│   ├── myThemeLayout.html        # Slots for header/content/footer
│   ├── myThemeLayout.js
│   ├── myThemeLayout.css
│   └── myThemeLayout.js-meta.xml # lightningCommunity__Theme_Layout target
└── myPageLayout/
    ├── myPageLayout.html         # Slots for page regions
    ├── myPageLayout.js
    ├── myPageLayout.css
    └── myPageLayout.js-meta.xml  # lightningCommunity__Page_Layout target
```

## Expression Quick Reference

| Expression | Use Case |
|-----------|----------|
| `{!Item.fieldName}` | CMS content field binding |
| `{!Record.fieldName}` | Salesforce record field binding |
| `{!Route.param}` | URL route parameter |
| `{!Site.basePath}` | Site base path |
| `{!User.Id}` | Current user ID |
| `{!User.isGuest}` | Check if guest user |

## @salesforce Module Quick Reference

| Import | Purpose |
|--------|---------|
| `@salesforce/apex/MyClass.myMethod` | Call Apex |
| `@salesforce/community/basePath` | Get site base path |
| `@salesforce/user/Id` | Current user ID |
| `@salesforce/user/isGuest` | Guest user check |
| `@salesforce/schema/Object.Field` | Reference schema |
| `@salesforce/label/c.MyLabel` | Import custom label |
| `@salesforce/resourceUrl/myResource` | Static resource URL |
| `@salesforce/contentAssetUrl/myAsset` | Content asset URL |
| `@salesforce/messageChannel/MyChannel__c` | Message channel |
| `@salesforce/customPermission/MyPerm` | Check permission |
