# Branding & Styling LWR Sites with --dxp Hooks

## Overview

LWR sites use the **--dxp styling hook** system for consistent branding. These CSS custom properties
cascade through all descendant components, allowing single-point-of-change branding.

## Architecture

```
Theme Panel Properties (Experience Builder UI)
    ↓ maps to
--dxp Styling Hooks (high-level CSS custom properties)
    ↓ maps to
--slds-c / --slds-g Component Styling Hooks (granular per-component)
    ↓ applied to
Base & Custom Lightning Web Components
```

## Enabling --dxp Styling Hooks

Sites created **Summer '21 and later** automatically include DXP branding style sheets.

For older sites, add to Head Markup (Settings > Advanced > Edit Head Markup):
```html
<!-- Load AFTER salesforce-lightning-design-system.min.css -->
<link rel="stylesheet" href="{basePath}/assets/styles/dxp-styling-hooks.min.css" />
<link rel="stylesheet" href="{basePath}/assets/styles/dxp-slds-extension.min.css" />
```

## Color Hooks

### Root/Background Colors
| Theme Panel Property | --dxp Hook |
|---------------------|------------|
| Background Color | `--dxp-g-root`, `--dxp-g-root-1`, `--dxp-g-root-2`, `--dxp-g-root-3` |
| Text Color | `--dxp-g-root-contrast`, `-1`, `-2`, `-3` |
| Brand Color | `--dxp-g-brand`, `-1`, `-2`, `-3` |
| Brand Foreground | `--dxp-g-brand-contrast`, `-1`, `-2`, `-3` |

### Color Derivation
- If root background is **dark** → derivation colors become progressively **lighter**
- If root background is **light** → derivation colors become progressively **darker**
- Derivation colors used for interaction states (hover, active, focus)

### Button Colors
| Property | Hooks |
|----------|-------|
| Primary Background | `--dxp-s-button-primary-color-background`, `--dxp-s-button-primary-color-background-hover` |
| Primary Text | `--dxp-s-button-primary-color` |
| Secondary | `--dxp-s-button-secondary-*` |
| Tertiary | `--dxp-s-button-tertiary-*` |

## Text Hooks

| Property | Hook |
|----------|------|
| Base Font Family | Set on `<html>` element |
| Heading 1 Color | `--dxp-s-text-heading-extra-large-color` |
| Heading 2 Color | `--dxp-s-text-heading-large-color` |
| Heading 3 Color | `--dxp-s-text-heading-medium-color` |
| Body Color | `--dxp-s-body-text-color` |
| Link Color | `--dxp-s-link-text-color` |

## Site Spacing Hooks

| Property | Description |
|----------|-------------|
| Max Content Width | Maximum width of inner content for theme/page regions |
| Section Padding | Top & bottom spacing for rows, regions, columns |
| Component Padding | Spacing between components |
| Gutter Spacing | Space between items inside the same section component |

## Using --dxp Hooks in Custom Components

```css
/* In your custom component CSS */
:host {
    background-color: var(--dxp-g-root);
    color: var(--dxp-g-root-contrast);
    font-family: var(--dxp-g-font-family);
}

.my-button {
    background-color: var(--dxp-s-button-primary-color-background);
    color: var(--dxp-s-button-primary-color);
}

.my-heading {
    color: var(--dxp-s-text-heading-large-color);
    font-size: var(--dxp-s-text-heading-large-font-size);
}
```

**Important**: CSS custom property values resolve at evaluation time. If property A references
property B, and you override B in a lower scope, A won't automatically update. Override A directly
where needed.

## Color Palettes for Page Sections

1. In Experience Builder: Colors tab > Theme panel > Manage Color Palettes
2. Click **New Palette**, name it, set color properties
3. Apply palette to **Section** or **Columns** components via the Color Palette menu
4. Can also create palettes directly from a component's property panel

## Override Component Branding with Custom CSS

### Target Component Instances
Every component has `data-component-id` attribute:
```css
/* Target specific component instance */
[data-component-id="abc123"] {
    --dxp-s-text-heading-large-color: red;
}
```

### Target Component Parts
Use `::part()` selector for pre-approved parts:
```css
community_layout-section::part(content-region) {
    padding: 2rem;
}
```

**Warning**: Use custom CSS sparingly. Avoid targeting DOM elements without a `part` attribute —
internal DOM structure changes will break hard-coded selectors. Salesforce Support cannot help with
custom CSS issues.

## Custom Fonts

### Upload as Static Resource
1. Setup > Static Resources > New > Upload font file
2. Set Cache Control to **Public** for public site pages
3. In head markup:
```html
<style>
@font-face {
    font-family: 'MyFont';
    src: url('{basePath}/sfsites/c/resource/myFontResource');
}
</style>
```

### Reference Externally Hosted Fonts
```html
<link href="https://fonts.googleapis.com/css2?family=Roboto" rel="stylesheet" />
```

### Font URL Patterns
- Single file: `{basePath}/sfsites/c/resource/static_resource_name`
- File in archive: `{basePath}/sfsites/c/resource/archive_name/path/to/font.woff2`

## Removing SLDS

You can remove the Salesforce Lightning Design System (SLDS) from your LWR site if you want
full custom styling control. This removes all SLDS base styles and you'll need to provide your own.
