# B2B Commerce APIs & Extensions Reference

## Overview of B2B Commerce APIs

The B2B Commerce APIs use the **Connect Commerce API** for managing e-commerce functions within
Salesforce. These APIs handle product catalogs, inventory, customer carts, and order processing.

### Available API Resources

| API | Key Operations |
|-----|---------------|
| **Address Management** | GET/POST/PATCH/DELETE addresses; manage shipping addresses on My Profile page via Webstore Account Addresses API |
| **Cart** | Create/update/manage carts; add/remove items; adjust quantities; transfer to wishlists; apply promotions/coupons; split into delivery groups; batch add up to 100 items; multiple active carts per buyer |
| **Checkout** | Start checkout; gather cart items, customer details, shipping, payment; trigger shipping/tax calculations; manage saved info for registered users; complete order placement |
| **Import** | Bulk data import for products, pricing, inventory |
| **My Profile** | User profile management, saved addresses, payment methods |
| **Order Summary** | Order history, order details, order status tracking |
| **Pricing and Promotions** | Price calculations, promotional discounts, coupon validation, volume pricing |
| **Product and Category** | Product catalog browsing, category navigation, product details, variants |
| **Search Settings** | Search configuration, filterable/searchable attributes, facets, sort rules |
| **Tax** | Tax calculation integration, tax exemptions |
| **Wishlists** | Wishlist management, add/remove items, share wishlists |

### Cart API Details
- Create, update, and manage shopping carts
- Add or remove items, adjust quantities
- Transfer items between cart and wishlists
- Apply promotions and coupon codes
- Split cart into multiple delivery groups
- Support for multiple active carts per buyer (choose primary)
- Batch add up to 100 items to a cart
- Cart message visibility for real-time updates (availability, price changes, promotions)

### Checkout API Details
- Start checkout process for users
- Gather cart items, customer details, shipping info, payment preferences
- For registered users: return saved shipping addresses and payment methods
- When shipping address is saved: automatically calculate shipping methods and taxes
- Integration with shipping and tax services during checkout updates
- Payment authorization occurs before order placement
- Checkout flow is limited to payment authorization (doesn't collect/store payment info)

## B2B Commerce Extensions (Winter '24+)

Commerce Extensions are the **recommended** approach for customizing commerce services.
They replaced the older "Integrations" framework.

### Why Extensions Over Integrations
- More targeted customizations for B2B stores
- Available for more Commerce domains
- Better architecture for service-level customization
- Integrations framework still supported but extensions preferred

### Extension Domains
- **Pricing** — Custom pricing logic, negotiated prices
- **Inventory** — Custom inventory checks, real-time availability
- **Shipping** — Custom shipping calculations, carrier integration
- **Tax** — Custom tax calculations, tax service integration
- **Other services** — Extensible to additional commerce domains

### Extension Architecture
Extensions hook into specific Commerce lifecycle points:
- Executed during cart calculation, checkout, and other commerce operations
- Written as Apex classes implementing specific interfaces
- Registered via Commerce Extension configuration in Setup

## Integrations (Legacy Framework)

B2B Commerce integration points are embedded into the **cart and checkout experience**.
Integration services work across B2B stores.

### Integration Types
- **Tax Integration** — External tax calculation services
- **Shipping Integration** — External shipping rate providers
- **Payment Integration** — Payment gateway adapters
- **Inventory Integration** — Real-time inventory checks
- **Pricing Integration** — External pricing engines

### Integration Architecture
- Predefined flows simplify package integration for:
  - Tax providers
  - Shipping providers
  - Payment providers
- B2B stores created with LWR template use the new integration architecture
- B2B stores created with Aura template use the legacy integration architecture

### Cart Calculate API Framework
For ERP and external service integration:
- Hook into cart calculation lifecycle
- Synchronize order data between Salesforce and ERP
- Real-time pricing, tax, and shipping calculation
- Reduce errors before credit card authorization

## Payment Processing

### Architecture
- Create a **payment gateway** and connect to checkout
- Create a **payment gateway adapter** (server-side or client-side)
- Payment authorization occurs **before** order placement
- Checkout flow does NOT collect or store payment information in provided components
- Payment processing limited to **payment authorization** in checkout flow

### Server-Side Payment Processing
- Payment adapter runs on Salesforce server
- Communicates with external payment gateway
- More secure — sensitive data stays server-side
- Implement `commercepayments.PaymentGatewayAdapter` Apex interface

### Client-Side Payment Processing
- Payment form rendered client-side (e.g., Stripe Elements, PayPal)
- Uses an iframe or hosted payment fields
- Reduces PCI compliance scope
- Requires CSP configuration for third-party payment provider domains

### Setup Steps
1. Create payment gateway adapter (Apex class)
2. Register the payment gateway in Setup
3. Configure the payment gateway for your store
4. Connect the gateway to your checkout flow

## Cart and Checkout Features

### Cart Features
- Multiple active carts per buyer
- Cart messages for real-time notifications
- Batch item addition (up to 100 items)
- Delivery group splitting
- Coupon and promotion application
- Wishlist transfers

### Checkout Flow
1. Start checkout — gather customer info
2. Shipping address selection (saved or new)
3. Shipping method calculation
4. Tax calculation
5. Payment authorization
6. Order placement
7. Order confirmation
