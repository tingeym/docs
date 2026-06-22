# Source: https://docs.fluid.app/docs/sdk/fairshare

# FairShare SDK

The FairShare SDK provides web components and JavaScript APIs for integrating commerce capabilities into any website using a simple script tag approach.

## Quick Start

Add the SDK to your website with a single script tag:

<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
\></script\>

Then add components anywhere on your page:

<fluid-media library-id\="your-library-id"\></fluid-media\>
<fluid-attribution\></fluid-attribution\>

That's it! The SDK handles all commerce functionality automatically.

---

## How Configuration Works

The SDK uses a **layered configuration system**. Understanding this is important for all SDK functions:

┌─────────────────────────────────────────────────────────┐
│ Your UI Code │
│ (highest priority \- overrides everything) │
├─────────────────────────────────────────────────────────┤
│ Script Tag Attributes │
│ (data\-fluid\-shop, data\-share\-guid, etc.) │
├─────────────────────────────────────────────────────────┤
│ API Settings │
│ (defaults from your shop config) │
└─────────────────────────────────────────────────────────┘

1. **API Settings** — When the SDK initializes, it fetches your shop's default configuration (language, country, currency, etc.)
 
2. **Script Tag Attributes** — Attributes on the `<script>` tag can override API defaults
 
3. **Your UI Code** — JavaScript calls have the **highest priority** and will overwrite any previous settings
 

// This overrides whatever the API returned
await window.FairShareSDK.updateLocaleSettings({
 language: "es",
 country: "MX"
});

> **Important:** When you update settings via UI code, they **replace** the API values—they don't merge with them. See the [Settings Overview](https://docs.fluid.app/docs/sdk/fairshare/settings) for detailed examples.

---

## What's Included

The FairShare SDK provides components and JavaScript APIs:

### Components

Pre-built UI components that you can add to any HTML page:

| Component | Description |
| --- | --- |
| [Media](https://docs.fluid.app/docs/sdk/fairshare/components/media) | Display media content with popover or inline modes |
| [Playlist](https://docs.fluid.app/docs/sdk/fairshare/components/playlist) | Display a collection of media items |
| [Lead Capture](https://docs.fluid.app/docs/sdk/fairshare/components/lead-capture) | Capture customer leads with configurable forms |
| [Attribution](https://docs.fluid.app/docs/sdk/fairshare/components/attribution) | Display affiliate attribution information |
| [Country](https://docs.fluid.app/docs/sdk/fairshare/components/country) | Country selector for locale settings |
| [User](https://docs.fluid.app/docs/sdk/fairshare/components/user) | Display authenticated user info and login/logout |
| [Language](https://docs.fluid.app/docs/sdk/fairshare/components/language) | Language selector for locale settings |

### JavaScript API

Programmatic control over all commerce functions:

| API | Description |
| --- | --- |
| [Cart Functions](https://docs.fluid.app/docs/sdk/fairshare/cart/getcart) | Add items, update quantities, checkout |
| [Settings & Configuration](https://docs.fluid.app/docs/sdk/fairshare/settings/getsettings) | Locale settings, configuration |
| [Events & Analytics](https://docs.fluid.app/docs/sdk/fairshare/events/trackfairshareevent) | Track events, get attribution data |

### Declarative HTML Controls

Simple data attributes for common actions without writing JavaScript:

<!-- Open/close cart -->
<button data-fluid-cart\="open"\>View Cart</button\>

<!-- Add product to cart -->
<button data-fluid-add-to-cart\="123456"\>Add to Cart</button\>

---

## Key Benefits

- **No build process required** — Works with any HTML page
- **Automatic style injection** — No separate CSS file needed
- **Server-side attribution** — Attribution calculated automatically
- **Framework agnostic** — Works with React, Vue, vanilla JS, or any framework

---

## Installation

See the [Installation Guide](https://docs.fluid.app/docs/sdk/fairshare/installation) for complete setup instructions and configuration options.

## API Reference

All functions are available on `window.FairShareSDK`. For backwards compatibility, they're also available on `window.FluidCommerceSDK`.

// Example: Add item to cart
await window.FairShareSDK.addCartItems(\[
 { variant\_id: 123, quantity: 2 }
\]);

// Example: Open cart drawer
window.FairShareSDK.cart.control.open();