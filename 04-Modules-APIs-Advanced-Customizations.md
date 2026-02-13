# @salesforce Modules, APIs & Advanced Customizations for LWR Sites

## @salesforce Modules Reference

LWR sites support these `@salesforce` modules with notes on behavior:

| Module | Description | Republish Required? | Notes |
|--------|-------------|-------------------|-------|
| `@salesforce/apex` | Import Apex methods | Yes, if method signature changes | Same behavior in LWR and Aura |
| `@salesforce/client` | Get hardware form factor (desktop/tablet/mobile) | — | — |
| `@salesforce/community` | Get site info (network ID, base path) | No, if base path changes | — |
| `@salesforce/contentAssetUrl` | Import content asset files | No, if name changes | URL format differs from Aura |
| `@salesforce/customPermission` | Check custom permissions | No, live sites auto-update | — |
| `@salesforce/i18n` | Internationalization values (locale, language, currency) | — | Some properties unsupported |
| `@salesforce/label` | Import custom labels | Yes, if label updated | Preview always fetches latest |
| `@salesforce/messageChannel` | Lightning Message Service (cross-DOM pub/sub) | Yes, if updated | — |
| `@salesforce/resourceUrl` | Import static resources | No, if renamed | URL format: `/webruntime/org-asset/<hash>/resource/<id>` |
| `@salesforce/schema` | Reference objects, fields, relationships | — | Supports referential integrity |
| `@salesforce/site` | Site ID (NetworkMember record) | — | — |
| `@salesforce/user` | Get current user info (ID, guest status) | — | — |
| `@salesforce/userPermission` | Check user permissions | — | — |

### Referential Integrity
- Most modules support it: can't delete referenced objects without removing component first
- Name changes auto-update references in published sites
- Unpublished sites: source code can take **up to 2 hours** to update

### Unsupported Modules
- `@salesforce/navigation` (use `lightning/navigation` instead)
- Some `@salesforce/i18n` properties

## User Interface API

Use UI API to get Salesforce record data in LWC:

```javascript
import { LightningElement, wire, api } from 'lwc';
import { getRecord, getFieldValue } from 'lightning/uiRecordApi';
import NAME_FIELD from '@salesforce/schema/Account.Name';

export default class RecordViewer extends LightningElement {
    @api recordId;

    @wire(getRecord, { recordId: '$recordId', fields: [NAME_FIELD] })
    account;

    get accountName() {
        return getFieldValue(this.account.data, NAME_FIELD);
    }
}
```

### Guest User Settings (for public pages)
Enable in Administration workspace > Preferences:
- **"Allow guest users to access public APIs"** — for LWC using UI API, CMS, Knowledge pages
- **"Let guest users view asset files, library files, and CMS content"** — for CMS content

## Custom Record Components

The Build Your Own (LWR) template has NO built-in record components. You must build custom ones.

### Custom Cell for Record Lists
Extend `LightningDatatable` to define custom data types:
```javascript
import LightningDatatable from 'lightning/datatable';
import customNameCell from './customNameCell.html';

export default class CustomDatatable extends LightningDatatable {
    static customTypes = {
        customName: {
            template: customNameCell,
            standardCellLayout: true,
            typeAttributes: ['recordId', 'recordName']
        }
    };
}
```

## Expressions in LWR Sites

Syntax: `{!expression}`

### Data Binding Expressions
Use in data-bound components (Banner, Card, Grid, Tile), HTML Editor, Rich Content Editor.

| Expression | Description |
|-----------|-------------|
| `{!Item.fieldName}` | CMS content field |
| `{!Record.fieldName}` | Salesforce record field |
| `{!Route.param}` | URL route parameter |
| `{!Site.basePath}` | Site base path |
| `{!User.Id}` | Current user ID |

### Content Component Expressions
Works in Rich Content Editor, HTML Editor when nested in data-bound components.

## Apex and SOQL for Search

### Custom Search Implementation
1. Create a **Search** standard page (search results landing page)
2. Build a **Search Bar** component in the theme layout header
3. Search Bar uses `lightning/navigation` to navigate to Search page
4. Search Results component reads `{!Route.term}` expression
5. Send search term to Apex controller that uses **SOQL** for query
6. Return and display results

## Custom Logout Component

```javascript
import { LightningElement } from 'lwc';
import basePath from '@salesforce/community/basePath';
import isGuest from '@salesforce/user/isGuest';

export default class LogoutLink extends LightningElement {
    get showLogout() {
        return !isGuest;
    }

    get logoutUrl() {
        const sitePrefix = basePath.replace(/\/s$/i, '');
        return `${sitePrefix}/secur/logout.jsp`;
    }
}
```

Default post-logout redirect: login page. Configurable in Experience Workspaces >
Administration > Login & Registration > Logout Page URL.

## Privileged Script Tag: <x-oasis-script>

LWR sites encapsulate elements in **shadow DOM**, blocking global DOM queries from third-party
libraries. Use `<x-oasis-script>` to bypass this:

```html
<!-- In Head Markup -->
<x-oasis-script src="https://cdn.example.com/library.js"></x-oasis-script>

<!-- Inline usage -->
<x-oasis-script>
    // This runs inside a special iframe, exempt from shadow DOM
    document.querySelectorAll('*'); // Can query all elements globally
</x-oasis-script>
```

Key points:
- Same syntax as `<script>` tag
- Runs inside special iframe exempt from shadow DOM
- Always executed **asynchronously**
- Can add event listeners and import/export global variables
- Required for Google Analytics, Google Tag Manager integration
- Must set CSP to **Relaxed** in Settings > Security & Privacy
- Add third-party script URLs to **Trusted Sites for Scripts**

## Google Tag Manager Integration

1. Set CSP to **Relaxed** in Experience Builder
2. Add GTM script URL to Trusted Sites for Scripts
3. Use `<x-oasis-script>` in head markup to load GTM
4. Listen for events and send captured details to the data layer
5. Triggers/events loaded via `<x-oasis-script>` are NOT visible in GTM preview mode
6. In GTM preview mode, use `dataLayer.push({'event':'preview'})` to see tag firing

## Base Lightning Component Limitations

### Unsupported Components & Events
- `lightning-emp-api`
- `lightningsnapin` components
- `platform-show-toast-event` (use `toast-container` instead)

### Partially Supported
- `lightning-input-field`: No lookup search on desktop; unsupported on mobile
- File opening (per LWC Dev Guide) unsupported
- Panels and modals unsupported

### Available
- `lightning-messageService` for cross-component communication
- Most other Lightning Component Library components
