# Source: https://docs.fluid.app/docs/sdk/fluid-sdk

# FairShare Web Widgets

This package provides web components for integrating FairShare into your website using a simple script tag approach.

## CDN/Script Tag Distribution

Use this method for direct inclusion in a website without a build step.

<!-- Basic server-side attribution -->
<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
 data-fluid-api-base-url\="https://api.your-custom-endpoint.com"
\></script\>

<!-- Enable Async updates -->
<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
 data-pusher-enabled\="true"
\></script\>

<!-- With attribution override -->
<script
 id\="fluid-cdn-script"
 src\="https://assets.fluid.app/scripts/fluid-sdk/latest/web-widgets/index.js"
 data-fluid-shop\="your-shop-id"
 data-share-guid\="your-affiliate-id"
\></script\>

<!-- Add the cart widget anywhere on your page -->
<fluid-cart-widget
 position\="bottom\_right"
 fluidShopDisplayName\="Your Store Name"
\></fluid-cart-widget\>

#### Key Benefits of CDN Distribution

- **No build process required** - Works with any HTML page
- **Automatic style injection** - No separate CSS file to include
- **Single script tag does everything** - Initializes SDK, loads styles, registers components
- **Server-side attribution** - Attribution calculated automatically server-side

#### Configuration Options

| Data Attribute | Description | Required |
| --- | --- | --- |
| data-fluid-shop | Your FairShare shop identifier | Yes |
| data-share-guid | Attribution override share GUID (overrides server-side) | No |
| data-fluid-rep-id | Attribution override Fluid Rep ID (numeric) | No |
| data-email | Attribution override email address | No |
| data-username | Attribution override username | No |
| data-external-id | Attribution override external ID | No |
| data-fluid-api-base-url | Custom API endpoint URL for development or testing environments | No |
| data-debug | Enable debug mode (set to "true" to enable) | No |
| data-pusher-enabled | Enable Async pusher updates (currently applies to cart) | No |

The `data-fluid-api-base-url` attribute allows you to specify a custom API endpoint for local development or testing against different environments. When not provided, the system uses the default production endpoint.

**Server-Side Attribution:** Attribution is calculated server-side automatically based on the current page URL. The system detects page changes (including SPA navigation) and refreshes attribution data as needed. Use attribution override attributes when you need to override this with specific attribution values. Attribution methods are checked in priority order: share-guid → fluid-rep-id → email → username → external-id.

## Controlling the Cart

There are two ways to control the cart functionality:

### Declarative Controls (HTML)

Use data attributes for the simplest implementation:

<button data-fluid-cart\="open"\>Open Cart</button\>
<button data-fluid-cart\="close"\>Close Cart</button\>
<button data-fluid-cart\="toggle"\>Toggle Cart</button\>

### JavaScript Controls

For programmatic control using the FairShare SDK:

// Primary SDK (recommended)
window.FairShareSDK.cart.control.open();
window.FairShareSDK.cart.control.close();
window.FairShareSDK.cart.control.toggle();

// Legacy cart controls (for backwards compatibility)
window.fluidCart.open();
window.fluidCart.close();
window.fluidCart.toggle();

### Cart Settings Override

You can dynamically override cart widget settings using the nested settings API:

// Override cart widget settings dynamically
window.FairShareSDK.cart.settings.set({
 hideWidget: true, // Hide the cart button but keep functionality
 position: "top\_left", // Change position: 'top\_left', 'top\_right', 'bottom\_left', 'bottom\_right'
 size: 80, // Change button size in pixels
 placement: "left", // Cart drawer placement: 'left' or 'right'
 primaryColor: "#ff6b35", // Override primary color
 secondaryColor: "#2d3748", // Override secondary color
 xMargin: 20, // Horizontal margin from screen edge
 yMargin: 50, // Vertical margin from screen edge
});

// Get current cart settings overrides
const cartSettings \= window.FairShareSDK.cart.settings.get();

// Clear all cart settings overrides (revert to server settings)
window.FairShareSDK.cart.settings.clear();

**Cart Settings Override Properties:**

| Property | Type | Description |
| --- | --- | --- |
| hideWidget | boolean | Hide the cart button while keeping cart functionality accessible |
| position | string | Button position: 'top\_left', 'top\_right', 'bottom\_left', 'bottom\_right' |
| size | number | Button size in pixels |
| placement | string | Cart drawer placement: 'left' or 'right' |
| primaryColor | string | Primary color for cart styling |
| secondaryColor | string | Secondary color for cart styling |
| xMargin | number | Horizontal margin from screen edge in pixels |
| yMargin | number | Vertical margin from screen edge in pixels |
| shop | object | Override shop information (name, primary\_color, logo\_url) |
| affiliate | object | Override affiliate information (name, image\_url) |

**Page-Scoped Settings:**

Cart settings are automatically scoped to the current page URL path (excluding query parameters and fragments). This means:

- Settings on `/products` page won't affect `/checkout` page
- Settings persist across page reloads for the same path
- Each page can have its own independent cart configuration

// Settings are automatically scoped to current page
window.FairShareSDK.cart.settings.set({ hideWidget: true }); // Only affects current page

**Note:** Cart settings overrides are applied immediately and persist until cleared. They take precedence over server-side settings and original widget attributes.

### Chat Settings Override

You can dynamically override chat widget (lead capture) settings using the nested settings API:

// Override chat widget settings dynamically
window.FairShareSDK.chat.settings.set({
 hideWidget: true, // Hide the chat button but keep functionality
 position: "top\_left", // Change position: 'top\_left', 'top\_right', 'bottom\_left', 'bottom\_right'
 size: 80, // Change button size in pixels
 primaryColor: "#ff6b35", // Override primary color
 xMargin: 20, // Horizontal margin from screen edge
 yMargin: 50, // Vertical margin from screen edge
 title: "Need help?", // Override widget title
 description: "Chat with us!", // Override description text
 submitButtonText: "Send Message", // Override submit button text
 successMessage: "Thanks! We'll be in touch soon.", // Override success message
 contactMethod: "phone", // Override contact method: 'email' or 'phone'
});

// Get current chat settings overrides
const chatSettings \= window.FairShareSDK.chat.settings.get();

// Clear all chat settings overrides (revert to server settings)
window.FairShareSDK.chat.settings.clear();

**Chat Settings Override Properties:**

| Property | Type | Description |
| --- | --- | --- |
| hideWidget | boolean | Hide the chat button while keeping chat functionality accessible |
| position | string | Button position: 'top\_left', 'top\_right', 'bottom\_left', 'bottom\_right' |
| size | number | Button size in pixels |
| primaryColor | string | Primary color for chat styling |
| xMargin | number | Horizontal margin from screen edge in pixels |
| yMargin | number | Vertical margin from screen edge in pixels |
| title | string | Widget title text displayed in the header |
| description | string | Description text shown in the chat bubble |
| submitButtonText | string | Text displayed on the submit button |
| successMessage | string | Message shown after successful form submission |
| contactMethod | string | Contact method: 'email' or 'phone' |

**Page-Scoped Settings:**

Chat settings are automatically scoped to the current page URL path (excluding query parameters and fragments). This means:

- Settings on `/products` page won't affect `/checkout` page
- Settings persist across page reloads for the same path
- Each page can have its own independent chat configuration

// Settings are automatically scoped to current page
window.FairShareSDK.chat.settings.set({ hideWidget: true }); // Only affects current page

**Note:** Chat settings overrides are applied immediately and persist until cleared. They take precedence over server-side settings and original widget attributes.

## JavaScript SDK Functions

The FairShare SDK provides comprehensive functionality through `window.FairShareSDK`. All functions are also available on `window.FluidCommerceSDK` for backwards compatibility.

### Cart Management

// Get the current cart
const cart \= await window.FairShareSDK.getCart();

// Get cart from local storage (no API call)
const localCart \= window.FairShareSDK.getLocalCart();

// Get current cart item count
const itemCount \= window.FairShareSDK.getCartItemCount();

// Add items to cart
await window.FairShareSDK.addCartItems(\[
 { variant\_id: 123, quantity: 2, subscribe: true },
 { variant\_id: 456, quantity: 1 },
\]);

// Add single item to cart
await window.FairShareSDK.addCartItems(123, { quantity: 2, subscribe: true });

// Remove/decrement cart item
await window.FairShareSDK.decrementCartItem(123, { quantity: 1 });

// Remove cart item by ID
await window.FairShareSDK.removeCartItemById(456);

// Update cart items
await window.FairShareSDK.updateCartItems(\[{ variant\_id: 123, quantity: 3 }\]);

// Subscribe/unsubscribe cart items
await window.FairShareSDK.subscribeCartItem(itemId, subscriptionPlanId);
await window.FairShareSDK.unsubscribeCartItem(itemId);

// Clear entire cart
await window.FairShareSDK.clearCart();

// Create empty cart
//optional country code param await window.FairShareSDK.createCart("US"); 
await window.FairShareSDK.createCart();

// Proceed to checkout
await window.FairShareSDK.checkout();

// Get checkout URL
const checkoutUrl \= window.FairShareSDK.getCheckoutUrl();

### Enrollment Packs

// Add enrollment pack to cart
await window.FairShareSDK.addEnrollmentPack(42);

### Lead Capture

// Capture lead information
await window.FairShareSDK.captureLead({
 first\_name: "John",
 last\_name: "Doe",
 email: "john@example.com",
 phone: "+1234567890",
 message: "I'm interested in your products",
});

### Media & Library Functions

// Get library/playlist data
const library \= await window.FairShareSDK.getLibrary("library-slug-or-id");

// Get media data
const media \= await window.FairShareSDK.getMedia("media-slug-or-id");

### Settings & Configuration

// Fetch settings from API
const settings \= await window.FairShareSDK.fetchSettings();

// Get cached settings
const cachedSettings \= window.FairShareSDK.getSettings();

// Get settings (fetch if not cached)
const settings \= await window.FairShareSDK.getOrFetchSettings();

// Update locale settings
await window.FairShareSDK.updateLocaleSettings({
 language: "en",
 country: "US",
});

// Get current country code and language
const countryCode \= window.FairShareSDK.getCountryCode();
const language \= window.FairShareSDK.getLanguage();

### Authentication

// Set authentication token
await window.FairShareSDK.setAuthentication("jwt-token");

// Get authenticated user
const user \= window.FairShareSDK.getAuthenticatedUser();

### Affiliate Lookup

// Look up affiliate by various attribution methods
const affiliate \= await window.FairShareSDK.lookupAffiliate({
 share\_guid: "affiliate-share-guid",
});

const affiliate \= await window.FairShareSDK.lookupAffiliate({
 email: "affiliate@example.com",
});

### Custom Checkout Handler

// Set custom checkout handler
window.FairShareSDK.setOnCheckout(async () \=> {
 // Custom checkout logic
 console.log("Custom checkout triggered");
 // Redirect to custom checkout page, etc.
});

// Get current checkout handler
const handler \= window.FairShareSDK.getOnCheckout();

### Event Tracking & Analytics

// Track custom events
window.FairShareSDK.trackFairshareEvent({
 event: "custom\_event",
 properties: { key: "value" },
});

// Track checkout started
window.FairShareSDK.trackCheckoutStarted({ cart\_token: "token" });

// Get session token
const sessionToken \= window.FairShareSDK.getSessionToken();

// Get attribution data
const attribution \= window.FairShareSDK.getAttribution();

// Flush pending events
await window.FairShareSDK.flushEvents();

// Flush events with beacon (for page unload)
await window.FairShareSDK.flushEventsWithBeacon();

### SDK Management

// Initialize FairShare SDK manually (usually done automatically via data attributes)
window.FairShareSDK.initializeFluid({
 fluidShop: "your-shop-id",
 debug: true,
});

// Reset SDK state
window.FairShareSDK.reset();

// Reset FairShare tracking only
window.FairShareSDK.resetFairshare();

## Displaying Cart Count

You can easily display the current cart item count anywhere on your page by using an element with the ID `fluid-cart-count`. The SDK will automatically update this element whenever the cart changes.

<!-- Simple cart count display -->
<span id\="fluid-cart-count"\>0</span\>

<!-- Cart count with styling -->
<div class\="cart-icon"\>
 <i class\="fas fa-shopping-cart"\></i\>
 <span class\="badge" id\="fluid-cart-count"\>0</span\>
</div\>

The cart count is updated automatically when:

- Items are added to the cart
- Items are removed from the cart
- Item quantities are updated
- The cart is cleared
- A new cart is created
- The cart is refreshed from the API

## Adding Products to Cart

### Declarative Product Controls (HTML)

Use data attributes to add products to cart without writing JavaScript:

<!-- Add a single product (variant) to cart -->
<button data-fluid-add-to-cart\="123456"\>Add to Cart</button\>

<!-- Specify quantity -->
<button data-fluid-add-to-cart\="123456" data-fluid-quantity\="2"\>
 Add 2 to Cart
</button\>

<!-- Add to cart and open cart drawer immediately -->
<button data-fluid-add-to-cart\="123456" data-fluid-open-cart-after-add\="true"\>
 Add to Cart and View
</button\>

<!-- Add with subscription enabled -->
<button data-fluid-add-to-cart\="123456" data-fluid-subscribe\="true"\>
 Add with Subscription
</button\>

<!-- Add with specific subscription enabled -->
<button data-fluid-add-to-cart\="123456" data-fluid-subscription-plan-id\=1234\>
 Add with Subscription
</button\>

<!-- Add multiple products with different subscription settings -->
<button
 data-fluid-add-to-cart\="123456,789012"
 data-fluid-subscribe\="true,false"
\>
 Add Multiple Items
</button\>

<!-- Combine subscription with quantity and cart opening -->
<button
 data-fluid-add-to-cart\="123456"
 data-fluid-subscribe\="true"
 data-fluid-quantity\="2"
 data-fluid-open-cart-after-add\="true"
\>
 Add 2 with Subscription and View
</button\>

### Adding Enrollment Packs

Enrollment packs can be added to the cart using data attributes:

<!-- Add an enrollment pack to cart -->
<button data-fluid-add-enrollment-pack\="42"\>Add Enrollment Pack</button\>

<!-- Add enrollment pack and open cart drawer immediately -->
<button
 data-fluid-add-enrollment-pack\="42"
 data-fluid-open-cart-after-add\="true"
\>
 Add Enrollment Pack and View
</button\>

## Customizing Widget Styles

You can customize the widgets to match your brand's color scheme and typography using CSS variables:

/\* In your CSS file \*/
:root {
 \--company-color: #3461ff; /\* Replace with your brand color \*/
 \--ff-body: "Your Custom Font", sans-serif; /\* Your custom font \*/
}

Available customization variables:

- `--company-color`: Controls primary colors, accents, and highlights
- `--ff-body`: Sets the font family used throughout the widgets (falls back to system fonts if not specified)

## Available Components

### Cart Widget (`<fluid-cart-widget>`)

| Attribute/Property | Type | Default | Description |
| --- | --- | --- | --- |
| useBrand | boolean | false | Whether to use brand colors |
| primaryColor | string | \- | Primary color for the cart widget |
| secondaryColor | string | \- | Secondary color for the cart widget |
| placement | string | \- | Placement of cart sheet: 'right' or 'left' |
| position | string | 'bottom\_right' | Position on screen: 'top\_left', 'top\_right', 'bottom\_left', 'bottom\_right' |
| size | number | 60 | Size of the widget button in pixels |
| xMargin | number | 40 | Horizontal margin from the edge of the screen in pixels |
| yMargin | number | 98 | Vertical margin from the edge of the screen in pixels |
| fluidShopDisplayName | string | 'Fluid' | Shop name displayed in the cart header |
| hideWidget | boolean | false | When true, hides button but keeps cart functionality |
| demoMode | boolean | false | Enable demo mode to prevent SDK API calls for testing |
| alwaysOpen | boolean | false | If true, the cart sidebar is always open (no floating button) |

### Lead Capture Widget (`<fluid-lead-capture-widget>`)

| Attribute/Property | Type | Default | Description |
| --- | --- | --- | --- |
| use-brand | boolean | false | Whether to use brand colors |
| primary-color | string | \- | Primary color for the widget |
| secondary-color | string | \- | Secondary color for the widget |
| title | string | 'Have a question?' | Title of the widget |
| description | string | "We'd love to get in touch!" | Description text |
| submit-button-text | string | 'Send Message' | Text for the submit button |
| success-message | string | "Thank you for your message! We'll get back to you soon." | Success message shown after form submission |
| show | boolean | true | Whether to show the widget |
| position | string | 'bottom\_right' | Position on screen: 'top\_left', 'top\_right', 'bottom\_left', 'bottom\_right' |
| size | number | 60 | Size of the widget trigger button in pixels |
| z-index | number | 9999 | Z-index for layering |
| x-margin | number | 40 | Horizontal margin from the edge of the screen in pixels |
| y-margin | number | 98 | Vertical margin from the edge of the screen in pixels |
| hide-widget | boolean | false | When true, hides trigger button but keeps functionality accessible |
| avatar-src | string | \- | Avatar image URL |
| logo-src | string | \- | Logo URL for the footer |
| logo-alt | string | \- | Logo alt text |
| footer-text | string | 'Use is subject to terms' | Footer text |
| terms-url | string | 'https://fluid.app/terms-conditions' | URL for the terms and conditions page |
| fluid-shop-display-name | string | 'Fluid' | Shop display name for the consent text |
| contact-method | string | 'email' | Preferred contact method: 'email' or 'phone' |
| attract-bubble-show-delay | number | 1000 | Milliseconds to wait before showing the attract bubble |
| attract-bubble-hide-delay | number | 20000 | Milliseconds to wait before hiding the attract bubble |

### Media Widget (`<fluid-media-widget>`)

| Attribute/Property | Type | Default | Description |
| --- | --- | --- | --- |
| library-id | string | \- | Library ID to fetch media from |
| media-id | string | \- | Single media ID to display |
| hide-widget | boolean | false | Whether to hide the media widget |
| class-name | string | \- | Optional additional CSS classes |
| height | string | \- | Height of the thumbnail |
| width | string | \- | Width of the thumbnail |

### Available Options

| Attribute/Property | Type | Default | Description |
| --- | --- | --- | --- |
| embed-type | "popover" | "inline" | "inline-shopping" | "default" | "popover" | Controls how the media widget displays |
| auto-open | boolean | false | Automatically opens the media widget on load (only works when embedType is "popover", ignored when embedType is "inline") |
| thumbnail-foam | boolean | false | When true, uses object-cover to fill the provided size (may crop to fit). When false, maintains aspect ratio. (only works for embedType "popover") |

## Future features coming soon:

### Options

| Attribute/Property | Type | Default | Description |
| --- | --- | --- | --- |
| dismissible | boolean | true | |
| expandable | boolean | true | |
| trackable | boolean | true | |
| comments | boolean | true | |
| autoplay | boolean | false | |
| autoplay\_audio | boolean | false | |
| loop | boolean | false | |
| cover\_image | string | Passed from API | Options to grab cover image from timestamp in video |
| button | {text: "Click Here", url: "https://google.com"} | null | |
| video\_controls | boolean | true | |
| start\_position | integer | 0 | |
| language | language | fairshare\_current\_language | |
| message | string | null | |

### Functions

- `play / pause`
- `seek(12) [integer of seconds]`
- `mute / unmute`
- `expand`
- `dismiss`
- `autoplay`
- `addMessage`
- `src`

### Event Listeners

- `onLoad`
- `onPlay`
- `onPause`
- `onTrack`
- `onComment`
- `onButtonClick`
- `onStart`
- `onComplete`

#### Usage Examples

<!-- Basic media widget with library ID -->
<fluid-media-widget library-id\="your-library-id"\></fluid-media-widget\>

<!-- Media widget with specific media ID -->
<fluid-media-widget media-id\="your-media-id"\></fluid-media-widget\>

<!-- Media widget with custom dimensions -->
<fluid-media-widget
 library-id\="your-library-id"
 height\="300px"
 width\="500px"
\></fluid-media-widget\>

<!-- Media widget with custom CSS classes -->
<fluid-media-widget
 media-id\="your-media-id"
 class-name\="custom-media-widget my-style"
\></fluid-media-widget\>