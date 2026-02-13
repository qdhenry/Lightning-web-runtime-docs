# B2B Commerce on LWR — Architecture, Data Model & Setup

## Overview

Salesforce B2B Commerce runs on the **Lightning Web Runtime (LWR)** platform using the
**Commerce Experience Builder** template. This is a purpose-built storefront template — distinct
from the generic "Build Your Own (LWR)" template — that ships with pre-built commerce components
(product listings, cart, checkout, search, etc.) and a pre-configured commerce data model.

All foundational LWR concepts apply (LWC programming, shadow DOM, LWS security, publishing model,
--dxp branding, `<x-oasis-script>`, etc.), but B2B Commerce adds a substantial commerce-specific
layer on top.

### Required Editions
- Available in: **Enterprise**, **Unlimited**, and **Developer** Editions
- Available in: **B2B Commerce**

## Commerce Experience Builder Template

The Commerce Experience Builder template is the foundational tool for B2B Commerce:
- Creates personalized commerce environments
- Serves retailers, wholesalers, distributors, and consumers
- Includes dozens of pre-built LWR store components
- Customizable through Lightning web components
- Supports seamless third-party integration

### Key Capabilities
- Product catalogs with categories, variants, bundles
- Cart and checkout experiences with real-time inventory
- Buyer groups with volume-discounted and negotiated pricing
- Search with faceted filtering
- Payment gateway integration (server-side and client-side)
- Integration with ERP, Salesforce Order Management, and Service Cloud

## B2B Commerce Data Model

The Commerce store template is built on a pre-configured data model supporting standard and
customizable business objects.

### Core Data Model Areas

| Data Model | Key Objects & Purpose |
|------------|----------------------|
| **Cart** | `WebCart`, `CartItem`, `CartDeliveryGroup` — Shopping cart management, delivery grouping |
| **Inventory** | Inventory tracking and availability per store |
| **Product & Catalog** | `Product2`, `ProductCategory`, `ProductCatalog` — Product organization, variants, bundles |
| **Product & Category Media** | Product images, category images, media associations |
| **Product Attributes** | Configurable product characteristics (size, color, etc.) |
| **Pricing** | `PricebookEntry`, `Pricebook2` — Price books, negotiated pricing, buyer group pricing |
| **Promotions** | Discount rules, coupon codes, promotional pricing |
| **Search** | Searchable/filterable attributes, faceted search configuration, sort rules |
| **Shipping** | Shipping methods, delivery calculations |
| **Store** | `WebStore` — Store configuration, buyer groups, entitlements |
| **Tax** | Tax calculation configuration and integration |

### Search Data Limits

| Object | Limit | Scope | Notes |
|--------|-------|-------|-------|
| Filter/Facet Values per Attribute | 100 | N/A | Selectable limit is 100 |
| Filter Fields in a Query | 10 | N/A | Fields a customer can select |
| Facets per Search Query | 10 | N/A | Top 10 most populated if auto-faceting |
| Filterable Attributes per Store | 50 | Store | Contact support for up to 100 |
| Searchable Attributes per Store | 25 | Store | Contact support for up to 100 |
| Sort Rules per Store | 10 | Store | Combined total with attributes can't exceed 70 |

### Store Data Limits

The Store Data Model includes limits for:
- Store data limits (products per store, categories, etc.)
- Price book data limits
- Entitlement data limits
- Shopper and buyer group data limits

### Key Object Relationships
- Product catalogs provision stores
- Product variants (size, shape, color) configurable via attributes
- Buyer groups associated with specific products and volume-discounted prices
- Tax, shipping, and payment configured per cart checkout
- Order processing integrated with Salesforce Order Management

## B2B Data Deployment

### Store and Experience Metadata
- Store configuration deployed via metadata
- Experience metadata includes page layouts, theme configurations
- Use SFDX CLI for deployment to scratch orgs and stores

### Deployment Approach
- Use Salesforce DX for source-driven development
- Deploy packages and components to scratch orgs
- Sync source code between Salesforce orgs and version control

## Implementation Lifecycle: Personas

Key personas in B2B Commerce implementation:
- **Commerce Admin** — Store setup, configuration, and management
- **Developer** — Custom component development, API integration, extension development
- **Business User** — Product management, pricing, promotions
- **System Integrator** — ERP/payment/shipping integration

## Commerce SFDX Environment Setup

Salesforce recommends **SFDX** for building LWCs and custom integration packages.

### Required Tools
1. **Salesforce CLI (SFDX CLI)** — Source code synchronization
   - Install CLI, verify with `sfdx` command
2. **Visual Studio Code** — IDE with Salesforce Extension Pack
3. **Salesforce Extensions for VS Code** — Code completion, debugging, deployment
4. **Git** — Version control for component source code

### Setup Steps
1. Install Salesforce SFDX CLI
2. Install VS Code with Salesforce Extension Pack
3. Create an SFDX project (`sf project generate`)
4. Authorize your org (`sf org login web`)
5. Create scratch org or connect to sandbox
6. Develop and deploy components

### GitHub Repositories
Salesforce provides public Git repositories with:
- LWC templates for commerce components
- Reference packages for tax, shipping, and payment integrations
- Open source commerce components (clone, customize, deploy)
