# Source: https://docs.fluid.app/docs/sdk/fairshare/installation

# Installation

Add the FairShare SDK to your website using a simple script tag.

## CDN Installation (Recommended)

<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
\></script\>

The `data-fluid-shop` attribute is required and identifies your FairShare store.

---

## Configuration Options

Configure the SDK using data attributes on the script tag:

| Attribute | Required | Description | Default |
| --- | --- | --- | --- |
| `data-fluid-shop` | Yes | Your Fluid shop identifier | — |
| `data-debug` | No | Enable debug logging | `"false"` |
| `data-fluid-api-base-url` | No | Custom API endpoint | `"https://api.fluid.app"` |
| `data-pusher-enabled` | No | Enable Pusher realtime sync | `"false"` |

### Attribution Override Options

Override the automatic server-side attribution with specific values. Attribution is automatically handled server-side by default—use these attributes only when you need to manually override attribution.

**Priority Order:** The first attribute found is used (in order listed below).

| Attribute | Description |
| --- | --- |
| `data-share-guid` | Attribution share GUID |
| `data-fluid-rep-id` | Fluid Rep ID (numeric) |
| `data-email` | Email address |
| `data-username` | Username |
| `data-external-id` | External ID |

Attribution methods are checked in priority order: `share-guid` → `fluid-rep-id` → `email` → `username` → `external-id`.

---

## Example Configurations

### Basic Setup

<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
\></script\>

### With Real-Time Updates

Enable Pusher for real-time cart synchronization across tabs:

<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
 data-pusher-enabled\="true"
\></script\>

### With Attribution Override

Override automatic attribution with a specific affiliate:

<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
 data-share-guid\="your-affiliate-id"
\></script\>

### Development/Testing

Point to a custom API endpoint for local development:

<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
 data-fluid-api-base-url\="https://api.your-custom-endpoint.com"
 data-debug\="true"
\></script\>

---

## Complete Example

<!DOCTYPE html\>
<html\>
 <head\>
 <title\>My Store</title\>
 </head\>
 <body\>
 <!-- Your page content -->

 <!-- Fluid SDK Script -->
 <script
 id\="fluid-cdn-script"
 src\="https://cdn.fluid.app/sdk/cdn.js"
 data-fluid-shop\="your-shop-id"
 data-debug\="true"
 data-pusher-enabled\="true"
 \></script\>

 <!-- Optional: Add cart widget -->
 <fluid-cart-widget
 position\="bottom-right"
 fluid-shop-display-name\="Your Shop"
 \></fluid-cart-widget\>
 </body\>
</html\>

---

## Server-Side Attribution

By default, attribution is calculated automatically based on the current page URL. The SDK:

- Detects page changes (including SPA navigation)
- Refreshes attribution data as needed
- Handles all tracking automatically

Use attribution override attributes only when you need to explicitly set attribution values.

---

## Manual Initialization

For advanced use cases, you can initialize the SDK programmatically:

window.FairShareSDK.initializeFluid({
 fluidShop: "your-shop-id",
 debug: true,
});

This is typically not needed as the SDK initializes automatically from the script tag attributes.

---

## Using the SDK After Installation

Once the script is loaded, all SDK functions are available on `window.FairShareSDK` (or `window.FluidCommerceSDK` for backwards compatibility).

// Wait for SDK to be ready
window.addEventListener('DOMContentLoaded', () \=> {
 // Access SDK functions
 const cart \= window.FairShareSDK.getCart();
 const sessionToken \= window.FairShareSDK.getSessionToken();
 const attribution \= window.FairShareSDK.getAttribution();

 // Track events
 window.FairShareSDK.trackCheckoutStarted({
 cart\_token: "cart\_123456"
 });

 // Cart operations
 window.FairShareSDK.addCartItems(12345, { quantity: 1 });
 window.FairShareSDK.openCart();
});

---

## Troubleshooting

### Script Not Loading

1. **Check the script path:** Ensure `https://cdn.fluid.app/sdk/cdn.js` is accessible
2. **Check browser console:** Enable `data-debug="true"` to see detailed logs
3. **Verify `data-fluid-shop`:** Must be a valid shop identifier and cannot be empty

### SDK Functions Not Available

1. **Wait for initialization:**
 
 window.addEventListener('DOMContentLoaded', () \=> {
 if (window.FairShareSDK) {
 // Use SDK
 }
 });
 
2. **Check script execution:** Ensure script tag is in `<body>` or before closing `</body>`
 

### Attribution Not Working

1. **Server-side attribution is default:** Attribution is automatically calculated from URL parameters—no client-side configuration needed
2. **Override if needed:** Use attribution override attributes only when necessary—only one attribution method is used (priority order applies)

---

## Best Practices

1. **Place script before closing `</body>` tag:** Ensures page content loads first for better performance
2. **Use CDN for production:** Faster loading with CDN caching and always up-to-date
3. **Enable debug only in development:** Set `data-debug="true"` only during development—disable in production for better performance
4. **Don't override attribution unless necessary:** Server-side attribution handles most cases—only use override attributes for specific scenarios

---

## Customizing Styles

Customize widget appearance using CSS variables:

:root {
 \--company-color: #3461ff; /\* Primary brand color \*/
 \--ff-body: "Your Custom Font", sans-serif; /\* Font family \*/
}

See individual widget documentation for more styling options.