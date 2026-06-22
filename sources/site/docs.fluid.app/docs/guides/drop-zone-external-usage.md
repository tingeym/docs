# Source: https://docs.fluid.app/docs/guides/drop-zone-external-usage

# Drop Zones External Usage

Drop Zones provide a flexible way to embed external content and services directly into your application interface. A Drop Zone is essentially an embed URL that renders in a specified location within your app, enabling seamless integration with third-party services and custom functionality.

## How Drop Zones Work

When you create a Drop Zone, you specify:

- Location: Where the embedded content should appear
- Embed URL: The external url that will be loaded in that location

## Checkout drop zone usage

## URL Parameters

The fluid checkout app automatically appends parameters to your embed URL to provide context about the current cart and user.

### Token Parameter

A cart token is always appended to enable cart operations:

**Example:**

Your embed URL: https://external\-service.com/cart\-widget
Rendered URL: https://external\-service.com/cart\-widget?token\=cart\_abc123xyz

### Company ID Parameter

When the cart is associated with a company (B2B context), the `company_id` parameter is also appended:

**Example:**

Your embed URL: https://external\-service.com/cart\-widget
Rendered URL: https://external\-service.com/cart\-widget?token\=cart\_abc123xyz&company\_id\=comp\_789def

This allows your external service to:

- Identify the company placing the order
- Apply company-specific pricing or discounts
- Enforce company purchasing policies
- Display company-specific content or branding

### What You Can Do With the Cart Token

Once your external service receives the cart token, you can interact with the cart through our Cart APIs:

**Available Operations:**

- Update item quantities
- Add or remove items
- Calculate shipping costs
- Process payments through external payment services
- Apply discounts or promotions
- Modify cart metadata

[API Reference: Cart APIs Documentation](https://docs.fluid.app/docs/openapi/carts-v0)