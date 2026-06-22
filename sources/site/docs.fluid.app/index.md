# Source: https://docs.fluid.app/

![We-Commerce Logo](https://docs.fluid.app/assets/we-commerce.23377a4df482613c188f7810f82289c1b598511f73f1b62aa0d01cf0c0c9d721.db72ec42.svg) 

# Fluid 🖤 We-Commerce Developer Portal

Welcome to Fluid's We-Commerce platform documentation. In here, you'll find everything you need to know to get started.

_We believe in people. We know e-commerce._

We-commerce is where people and e-commerce come together. Everything about our platform is designed to enable sellers and merchants to work together in full transparency.

[Quick Start Guide](https://docs.fluid.app/docs/guides/quickstart) [Sign Up for We-Commerce (free)](https://admin.fluid.app/sign-up) [Help Center](https://fluidsupport.zendesk.com/)

### Understanding the Fluid OS

Many large features had to come together to create the We-Commerce experience. Here's a breakdown of a few of the key components to keep you oriented with the Fluid terminology.

Note: We've included the "Problem Space" section to align on the before state of technology stacks and why we decided to include said feature in our stack.

##### 🔗 Fluid Connect

_Keep commission and e-commerce data perfectly synchronized._

Connect to your commission engine, map your data, set your sync schedule, and view all data transfers right in the platform. Full bi-directional sync in minutes, not months.

**Commission Engine Integration Problem Space:**

- Manual integrations custom built for each new app.
- Middleware files must be tracked and maintained by IT.
- No visual feedback on the status of the sync.
- Resyncs require a command line prompt.
- Order data must be 100% accurate with complex transformations.

Currently supports Exigo, ByDesign, Pillars, InfoTrax, and Shopify. Custom integrations are available.

[View Connect in Admin](https://admin.fluid.app/droplets) [Learn More about Connect](https://docs.fluid.app/docs/apis/swagger)

---

##### 💳 Fluid Payments

_Accept money globally while maintaining control of customer payment data._

Fluid Payments is a global payment aggregator that allows you to accept payments from customers around the world without having to set up or manage your own providers. Payments is a fully integrated gateway that routes transactions to the appropriate provider based on country, fraud, recurring, cost, and more.

Payments works closely with the We-Commerce Checkout to give you access to over 300 Alternative Payment Methods (APMs) in 100+ countries. All of your customers' payment data is stored in a PCI-compliant vault so you don't have to worry about a provider shutting you down and losing your tokens.

**Direct Sales Payments Problem Space:**

- Payment providers discontinue services and token migration kills subscriptions.
- Every country has different APMs that require extensive integration (Apple Pay, Google Pay, GCash, AliPay, etc.).
- Payment routing requires extensive middleware, configuration, and experience.
- Payments and Checkout are separate systems that don't share data for optimal acceptance rates.

Payments handles opening, managing, and optimizing payment providers on your behalf so you only need to worry about selling. All payment methods are fully integrated into Checkout - no setup required.

[Sign Up for Payments](https://admin.fluid.app/sign-up) [Learn More about Payments](https://docs.fluid.app/docs/apis/swagger)

##### 🤝 Fair Share

_Ensure reps receive accurate credit for every sale._

Fair Share is a JavaScript SDK installed on all Fluid websites by default. This widget uses cookies, fingerprints, routes, and many other tracking methods to accurately attribute credit to reps as they share. The Fair Share widget can be installed on external sites as well to tie visitors across multiple sites.

**Attribution Problem Space:**

- Traditional e-commerce platforms don't offer attribution features.
- Custom built attribution lacks redundancy. Relies on one domain based attribution.
- Attribution is only considered for one domain while shopping happens across multiple websites.
- Reporting lacks full order journey path for trust between all parties.

Fair Share's SDK gives you options to extend the data and functionality on every web page.

[View Fair Share in Admin](https://admin.fluid.app/fair-share) [Fair Share SDK Documentation](https://docs.fluid.app/docs/sdk/fluid-sdk)

##### 📦 We-Commerce Checkout

_Boost conversions with speedy one-page checkouts that handle address validation, shipping, tax, inventory, discounts, and payment processing._

Gives customers and reps a seamless checkout experience with saved profiles that reduces cart abandonment and increases conversions while maintaining full attribution with Fair Share.

**E-commerce Checkout Problem Space:**

- Dedicated servers for custom built carts don't scale for high-traffic loads.
- Custom carts require extensive development for payment options and maintenance.
- Home built carts missing speedy one-page checkout and saved profile for high conversion.
- E-commerce platforms missing attribution and direct sales payment processing.

Use [Droplets](https://docs.fluid.app/docs/guides/creating-and-using-droplets), [Drop Zones](https://docs.fluid.app/docs/apis/swagger/drop-zones), [Middleware](https://docs.fluid.app/docs/guides/build-shopping-cart), and [Webhooks](https://docs.fluid.app/docs/apis/swagger/webhooks) to fully customize your checkout experience. All checkouts are served on checkout.fluid.app.

[View We-Commerce Docs](https://docs.fluid.app/docs/apis/swagger)

##### 🔐 Fluid Login

_Authenticate your customers, reps, and admins with simple magic links and 3rd party integrations._

Fluid Login provides a unified authentication system that works across all Fluid services as well as any extended services that you wish to use. Reps and Customers can sign in with magic links, social providers, or email MFA codes. The system handles multi-factor authentication, session management, and role-based access control automatically.

**Authentication Problem Space:**

- Multiple authentication systems across different platforms create user confusion.
- Social login integrations require extensive setup and maintenance for each provider.
- Demographics frequently lose passwords.

Fluid Login handles all authentication complexity behind the scenes, providing a single sign-on experience across your entire platform with enterprise-grade security.

[View Login](https://auth.fluid.app) [Login Webhooks](https://docs.fluid.app/docs/apis/swagger/webhooks)

##### 💳 Fluid Pay

_Enable one-click checkout with secure profiles and millions of verified customers._

Fluid Pay creates secure customer profiles that enable instant checkout across all Fluid-powered storefronts. Customers can save payment methods, shipping addresses, and preferences once and use them everywhere. The system includes fraud protection, PCI compliance, and automatic payment method optimization.

**Customer Profile Problem Space:**

- Customers must re-enter payment and shipping information on every site.
- Payment method storage typically handled at individual payment processor and not platform.
- Cross-site customer recognition has never been possible in traditional direct sales.

Fluid Pay creates a unified customer experience across your entire network, reducing cart abandonment and increasing conversion rates while providing access to over 2M payment-ready customers.

##### 🛍️ Droplet Marketplace

_Install premium e-commerce apps instantly with zero integration required._

The Droplet Marketplace is a curated collection of pre-built we-commerce ready applications that can be installed with a single click. Each droplet is fully integrated with Fluid's ecosystem and includes features like inventory management, customer service tools, analytics dashboards, and marketing automation.

**Commission Engine Integration Problem Space:**

- Third-party apps require extensive integration work and custom development.
- Data synchronization between apps is complex and error-prone.
- E-commerce app stores don't consider reps in link sharing and tracking.

Droplets are designed to work seamlessly together, sharing data and functionality without conflicts. All droplets are tested for compatibility and automatically updated to ensure optimal performance.

[Browse Droplet Marketplace](https://admin.fluid.app/droplets) [Droplet Development Guide](https://docs.fluid.app/docs/guides/creating-and-using-droplets)

##### 📱 App Builder

_Create custom mobile and desktop apps using simple drag-and-drop tools._

Fluid's App Builder allows you to create native mobile and desktop applications without writing code. Use the visual interface to design screens, connect to your data sources, and deploy apps to iOS, Android, Windows, and macOS. All apps are automatically connected to your Fluid backend and can access all your e-commerce data and functionality.

**App Problem Space:**

- Apps are not connected to e-commerce so functionality and data is limited for reps.
- Single-purpose sales apps do not allow for brand expression and full use cases.
- Field reps have frequent changing opnions and hard coded applications don't allow for flexibility.
- Rep needs are unique by country, language, and rank and trying to solve all use cases with one view is not possible.

App Builder generates native applications that perform like custom-built apps while providing the speed and simplicity of a visual development environment. All apps automatically sync with your Fluid data and can be updated instantly without app store approval. All views can be scope to specific user profiles and permissions.

[Start Building Apps](https://admin.fluid.app/app-builder) [App Builder Guide](https://docs.fluid.app/docs/guides/app-builder)

##### Website Editor

_Design pixel-perfect we-commerce sites without coding._

Fluid's Website Editor gives you full control over all the pixels on your website, while tethering Fair Share and Checkout seamlessly so reps get credit. The theme library allows brands to get started and deploy in minutes.

Fluid uses the liquid templating language to allow for full customization of the website. The website editor gives marketers full control without the need for code, including the ability to add, edit, and delete pages, sections, and blocks.

**Website Problem Space:**

- Websites are not connected to Fair Share and proper routing for full attribution credit.
- Composable commerce does not let marketers build freely requiring lots of IT intervention.
- Messaging and compliance needs are unique by country and language.

Built upon Fluid's translations and page router your website is ready to go with full attribution and internationalization.

[Edit Your Website](https://admin.fluid.app/themes) [Themes Guide](https://docs.fluid.app/docs/themes/themes) [Web Editor Guide](https://docs.fluid.app/docs/themes/paged-editor) [Themes Developer Guide](https://docs.fluid.app/docs/themes/developer-guide)

[API Reference](https://docs.fluid.app/docs/apis/swagger) [More Documentation](https://docs.fluid.app/docs/guides)

### 📌 Featured Articles

[**Who We Are**\\ \\ Build a sleek, responsive platform for your business, no coding expertise necessary.](https://docs.fluid.app/docs/about-us/who-we-are) [**The Fluid API**\\ \\ Fluid APIs offer a powerful toolkit for seamlessly integrating Fluid's services into your applications.](https://docs.fluid.app/docs/apis/swagger)

### 💁 Need help?

[Join our community](https://www.fluid.app) [Read the docs](https://docs.fluid.app/docs/apis/swagger)