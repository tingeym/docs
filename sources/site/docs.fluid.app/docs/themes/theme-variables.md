# Source: https://docs.fluid.app/docs/themes/theme-variables

# DB Variables & Template Scopes

Use this guide to understand which data your sections can access on each page type, plus a complete JSON reference of available variables.

---

## What data can a section access?

- Each page type exposes a specific set of data (its "template scope"). Sections on that page can only use data provided by that scope.
- In addition, a set of Global variables is always available on every page.
- Referencing data not exposed by the current page template (or Global variables) will render empty.

## Scope Rules (STRICT)

1. Use only variables exposed by the current page template.
2. Global variables are always available on all pages.
3. Do not assume cross-page access (e.g., media-specific data is not available on Home).

## Template scopes (by page type)

- Home: Access to Global variables; page-specific lists like featured enrollment packs may also be present.
- Shop/Collection: Access to `collection` data (filters, products, pagination) and Global variables.
- Product: Access to `product` data (images, variants, pricing, recommendations) and Global variables.
- Media: Access to `media` data (embed URLs, poster, file URLs) and Global variables.
- Enrollment Pack: Access to `enrollment_pack` data and Global variables.
- Library: Access to `library` data and Global variables.
- My Site: Access to `fluid_affiliate` data (links, favorites) and Global variables.
- Cart: Access to `cart` data and Global variables.
- Join: Access to enrollment pack listings (`collection`) and Global variables.

---

## Do / Don’t Examples

### Home template

- Do: Use `products` (Global) to render a list of products.

{'%' for p in products '%'}
 <a href\="{{ p.url }}" class\="block"\>{{ p.title | default: "Product" }}</a\>
{'%' endfor '%'}

- Don't: Use media-specific fields (e.g., `media.embed_url`) on Home; they are not exposed there.

### Shop/Collection template

- Do: Use `collection.filters` and `collection.products` as exposed on Shop/Collection pages.

{'%' if collection and collection.filters '%'}
 {'%' for f in collection.filters '%'}
 <div class\="filter"\>{{ f.label }}</div\>
 {'%' endfor '%'}
{'%' endif '%'}

- Don't: Reference `product.selected_or_first_available_variant` here; product-specific fields are only guaranteed on Product pages.

### Product template

- Do: Use `product.title`, `product.images`, `product.selected_or_first_available_variant.price` (Product scope).

<h1 class\="{{ section.settings.heading\_font\_family | default: 'font-primary' }}"\>{{ product.title | default: 'Product Title' }}</h1\>

- Don't: Assume `collection.filters` is present on the Product template unless explicitly exposed.

---

## Defensive Usage Patterns

- Always provide fallbacks using `default`:

{{ company.name | default: 'Company' }}

- Guard optional structures:

{'%' if product and product.images '%'}
 <img src\="{{ product.images\[0\].src }}" alt\="{{ product.title | default: 'Product' }}"\>
{'%' endif '%'}

---

## Implementation Steps for DB-Backed Sections

1. Identify the target template(s) where the section will be used (e.g., Product, Shop, Home).
2. List the exact fields you will reference from the page’s template scope and from Global variables; do not guess field names.
3. In your `index.liquid`, reference only those fields. Use guards and defaults for optional data.
4. Test the section within the correct template context in the Visual Editor.

---

## Commonly Used Global Variables

Always available on every page:

- Company (`company.*`): `company.name`, `company.logo_url`, `company.shop_page_url`, `company.checkout_url`, `company.points_label.singular`, `company.points_label.plural`
- Request (`request.*`): `request.path`, `request.host`, `request.page_type`, `request.query_parameters`
- Affiliate (`affiliate.*`): `affiliate.logged_in_rep_for_store`, `affiliate.name`, `affiliate.my_site_url`, `affiliate.sign_in_url`, `affiliate.sign_out_url`
- Localization (`localization.*`): `localization.country`, `localization.language`, plus `available_countries` and `available_languages`
- Products (`products`): Array of products with `title`, `url`, `price`, `images[n].src`, and `selected_or_first_available_variant.price`
- Collections (`collections`): Array of collections with `title`, `products`, and each product’s `title`, `url`, `price`, `images`
- Enrollment Packs (`enrollment_packs`): Array of enrollment packs with `title`, `price`, `images`, `description`

### Configurable Points label

Each company can rename "Points" to their own brand label (e.g. "Make Rewards", "Rain Cash"). Use these accessors instead of hardcoding "Point/Points" in templates:

- `company.points_label.singular` — singular label, defaults to `point`
- `company.points_label.plural` — plural label, defaults to `points`

The same accessor is also available on `product` and `variant` drops for convenience:

- `product.points_label.singular` / `product.points_label.plural`
- `variant.points_label.singular` / `variant.points_label.plural`

The drop returns the configured label string (or the default if unset). It does not pluralize for you — choose between `.singular` and `.plural` based on the count yourself.

#### Worked examples

Simple plural rendering:

{{ product.points }} {{ company.points\_label.plural }}

Pluralization driven by count:

{'%' if product.points == 1 '%'}
 Earn 1 {{ product.points\_label.singular }}.
{'%' else '%'}
 Earn {{ product.points }} {{ product.points\_label.plural }}.
{'%' endif '%'}

Capitalization at sentence start:

{{ company.points\_label.plural | capitalize }} earned: {{ product.points }}

Renders as `Points earned: 50` by default, or `Rain cash earned: 50` if the company has configured `rain cash` as the plural.

Liquid's `capitalize` filter capitalizes the first character _and_ lowercases the rest. A label stored as `Rain Cash` (title case) renders as `Rain cash` — the capital `C` is silently dropped. Store plural labels in lowercase if you intend to use `| capitalize`, or skip the filter and rely on the configured casing as-is.

Example (Global products):

{'%' for p in products '%'}
 <a href\="{{ p.url }}" class\="block"\>{{ p.title | default: "Product" }}</a\>
{'%' endfor '%'}

Example (Global collections → Best Sellers carousel):

{'%'- assign collection\_name = section.settings.selected\_collection | downcase | strip -'%'}
{'%'- for c in collections -'%'}
 {'%'- if c.title | downcase | strip == collection\_name -'%'}
 <div class\="best-sellers"\>
 {'%'- for product in c.products -'%'}
 <a href\="{{ product.url }}" class\="best-seller-item"\>
 <img src\="{{ product.images.first.src }}" alt\="{{ product.title }}"\>
 <div class\="title"\>{{ product.title }}</div\>
 <div class\="price"\>{{ product.price }}</div\>
 </a\>
 {'%'- endfor -'%'}
 </div\>
 {'%'- endif -'%'}
{'%'- endfor -'%'}

{'%'- schema -'%'}
{
 "settings": \[
 {
 "type": "text",
 "id": "selected\_collection",
 "label": "Enter Collection Title",
 "default": "Best Sellers"
 }
 \]
}
{'%'- endschema -'%'}

---

## Variables Reference (JSON)

Use these JSON snapshots to see what variables are available globally and per template.

### Global (available on all pages)

{
 "company": {
 "id": "Company id",
 "name": "Company name",
 "logo\_url": "Company logo url",
 "shop\_page\_url": "Shop page url",
 "checkout\_url": "Checkout url",
 "subdomain": "Company subdomain",
 "home\_page\_url": "Company home page url",
 "company\_color": "Company primary color",
 "points\_label": {
 "singular": "Configured singular label for rewards points (defaults to 'point')",
 "plural": "Configured plural label for rewards points (defaults to 'points')"
 }
 },
 "request": {
 "path": "Request path",
 "host": "Request host",
 "page\_type": "Page type, eg: shop, enrollment\_pack, library, media",
 "query\_parameters": "Request query parameters"
 },
 "share\_guid": "Share guid for affiliate",
 "external\_id": "External id for affiliate",
 "affiliate": {
 "web\_rep\_store\_enabled": "Should show login affiliate login bannar",
 "logged\_in\_rep\_for\_store": "Is affiliate logged in?",
 "name": "Affiliate name",
 "email": "Affiliate email",
 "avatar": "Affiliate avatar url",
 "initials": "Affiliate initials",
 "my\_site\_url": "Affiliate my site url",
 "sign\_in\_url": "Sign in url",
 "sign\_out\_url": "Sign out url"
 },
 "localization": {
 "available\_countries <Array>": {
 "currency": {
 "iso\_code": "Currency iso code",
 "name": "Currency name",
 "symbol": "Currency symbol"
 },
 "iso\_code": "Country iso code",
 "name": "Country name",
 "iso": "Country iso code (alias)",
 "selected": "Boolean if this country is selected",
 "unit\_system": "Unit system (metric/imperial)",
 "available\_languages": "Available languages for this country"
 },
 "available\_languages <Array>": {
 "iso\_code": "Language iso code",
 "name": "Language name",
 "iso": "Language iso code (alias)",
 "locale": "Language locale",
 "selected": "Boolean if this language is selected",
 "endonym\_name": "Language endonym name"
 },
 "country": "Current Country object",
 "language": "Current Language object"
 },
 "products <Array>": {
 "id": "Product ID",
 "title": "Product title",
 "url": "Product url",
 "short\_description": "Product short description",
 "description": "Product description (HTML sanitized)",
 "feature\_text": "Product feature text (HTML sanitized)",
 "out\_of\_stock": "Boolean to check if product is out of stock",
 "price": "Product display price",
 "subscription\_price": "Product display subscription price",
 "available\_for\_country": "Boolean to check if product is available for the selected country",
 "buyable\_quantity": "Maximum buyable quantity for the selected country",
 "active": "Boolean to check if product is active",
 "subscription\_only": "Boolean to check if product is subscription only",
 "allow\_subscription": "Boolean to check if product allows subscription",
 "available\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "images <Array>": {
 "id": "Image ID",
 "src": "Image src url",
 "url": "Image url"
 },
 "image": "Product image url",
 "ratings": "Average product rating (rounded)",
 "reviews <Array>": {
 "body": "Review body"
 },
 "selected\_or\_first\_available\_variant": {
 "available": "Boolean to check if variant is available",
 "id": "Variant ID",
 "price": "Variant display price",
 "title": "Variant title",
 "image\_url": "Variant image url"
 },
 "options\_available": "Boolean to check if product has options",
 "metadata": "Product metadata",
 "metafields": "Product metafields",
 "selected\_variant\_id": "Selected variant ID",
 "variants <Array>": {
 "id": "Variant ID",
 "title": "Variant title",
 "image\_url": "Variant image url",
 "is\_master": "Boolean to check if variant is master",
 "metafields": "Variant metafields",
 "option\_values <Array>": {
 "id": "Option value ID",
 "name": "Option value name",
 "selected": "Boolean to check if option value is selected",
 "option\_id": "Option ID"
 },
 "variant\_countries <Array>": {
 "id": "Variant country ID",
 "country\_iso": "Country iso code",
 "country\_name": "Country name",
 "currency\_code": "Currency code",
 "price": "Variant country price",
 "display\_price": "Variant country display price",
 "wholesale": "Variant country wholesale price",
 "display\_wholesale": "Variant country display wholesale price",
 "compare\_price": "Variant country compare-at price",
 "display\_compare\_price": "Variant country display compare-at price",
 "subscription\_price": "Variant country subscription price",
 "display\_subscription\_price": "Variant country display subscription price"
 },
 "images <Array>": {
 "id": "Image ID",
 "src": "Image src url",
 "url": "Image url"
 }
 },
 "selected\_subscription\_plan\_id": "Selected subscription plan ID",
 "subscription\_plans <Array>": {
 "id": "Subscription plan ID",
 "name": "Subscription plan name",
 "billing\_interval": "Billing interval",
 "billing\_interval\_unit": "Billing interval unit",
 "shipping\_interval": "Shipping interval",
 "shipping\_interval\_unit": "Shipping interval unit",
 "trial\_period": "Trial period",
 "trial\_period\_unit": "Trial period unit",
 "price\_adjustment\_type": "Price adjustment type",
 "price\_adjustment\_amount": "Price adjustment amount",
 "selected": "Boolean to check if subscription plan is selected",
 "max\_skips": "Maximum skips allowed",
 "subscription\_price": "Subscription price",
 "wholesale\_subscription\_price": "Wholesale subscription price",
 "display\_subscription\_price": "Display subscription price",
 "saved\_on\_subscription": "Amount saved on subscription", 
 "saved\_percentage\_on\_subscription": "Percentage saved on subscription"
 },
 "tags <Array>": "Array of product tags",
 "bundle\_config": "Bundle configuration object — empty {} for non-bundle products",
 "mutually\_exclusive\_groups <Array>": "Array of mutually-exclusive group sort\_order pairs",
 "is\_enrollment": "Boolean — true when the product's bundle is an enrollment bundle",
 "track\_inventory\_on\_bundle\_items": "Boolean — whether bundle item inventory is tracked at the item level",
 "bundle\_in\_stock": "Boolean — whether the full bundle is in stock for the storefront warehouse",
 "bundle\_available\_quantity": "Available quantity for the full bundle (null if unlimited)",
 "product\_bundles <Array>": {
 "id": "Bundle ID",
 "quantity": "Bundle quantity",
 "product\_id": "Parent product ID",
 "bundled\_variant\_id": "Bundled variant ID",
 "tax\_percentage": "Tax percentage",
 "display\_externally": "Boolean — whether to display externally",
 "title": "Bundled product title",
 "image\_url": "Bundled product image URL",
 "in\_stock": "Boolean — whether the bundled variant is in stock",
 "available\_quantity": "Available quantity of the bundled variant"
 },
 "product\_bundle\_groups <Array>": {
 "id": "Bundle group ID",
 "title": "Bundle group title",
 "description": "Bundle group description",
 "group\_type": "Group type (e.g., fixed, customizable)",
 "sort\_order": "Sort order of the group",
 "selection\_type": "Selection type (e.g., single, multiple)",
 "min\_selections": "Minimum number of selections required",
 "max\_selections": "Maximum number of selections allowed",
 "allow\_subscriptions": "Boolean — whether items in this group can be subscribed",
 "force\_subscriptions": "Boolean — whether subscription is required for this group",
 "pricing\_type": "Pricing type (e.g., fixed\_price, dynamic\_price)",
 "fixed\_price": "Group fixed price as string",
 "min\_price": "Minimum group price as string",
 "max\_price": "Maximum group price as string",
 "compare\_at\_price": "Compare-at price as string",
 "group\_cv": "Group commission value",
 "group\_qv": "Group qualifying value",
 "track\_quantity": "Boolean — whether to track quantity at the group level",
 "country\_pricing": "Country-specific pricing array (or empty)",
 "pricing\_config": "Raw pricing configuration object",
 "image\_urls <Array>": "Array of group image URLs",
 "images <Array>": {
 "id": "Image ID",
 "src": "Image src url",
 "url": "Image url"
 },
 "bundle\_group\_items <Array>": {
 "id": "Bundle group item ID",
 "quantity": "Quantity of this item in the bundle",
 "sort\_order": "Sort order of the item",
 "price": "Item price as string",
 "cv": "Item commission value",
 "qv": "Item qualifying value",
 "is\_default": "Boolean — whether this item is the default selection",
 "display\_quantity": "Display quantity for the item",
 "max\_quantity": "Maximum quantity allowed",
 "allow\_subscription": "Boolean — whether this item allows subscription",
 "force\_subscription": "Boolean — whether subscription is required for this item",
 "image\_url": "Item image URL",
 "available\_country\_codes <Array>": "Array of country ISO codes the item is available in",
 "country\_prices": "Object mapping country ISO to item price",
 "country\_subscription\_prices": "Object mapping country ISO to item subscription price",
 "subscription\_plan\_id": "Subscription plan ID for the item (if any)",
 "config": "Raw item configuration object",
 "product\_id": "ID of the product the item's variant belongs to",
 "product\_title": "Title of the product the item's variant belongs to",
 "product\_image\_url": "Image URL of the product the item's variant belongs to",
 "product\_bundles <Array>": {
 "id": "Nested bundle ID",
 "quantity": "Nested bundle quantity",
 "product\_id": "Nested bundle's parent product ID",
 "bundled\_variant\_id": "Nested bundled variant ID",
 "tax\_percentage": "Tax percentage",
 "display\_externally": "Boolean — whether to display externally",
 "title": "Nested bundled product title",
 "image\_url": "Nested bundled product image URL",
 "in\_stock": "Boolean — whether the nested bundled variant is in stock",
 "available\_quantity": "Available quantity of the nested bundled variant"
 },
 "in\_stock": "Boolean — whether the bundle item is in stock",
 "available\_quantity": "Available quantity of the bundle item",
 "variant": "Variant drop for the bundle item (same shape as products\[\].variants\[\])"
 }
 }
 },
 "enrollment\_packs <Array>": {
 "id": "Enrollment pack ID",
 "title": "Enrollment pack title",
 "url": "Enrollment pack url",
 "price": "Enrollment pack display price",
 "images": "Array of enrollment pack image urls",
 "description": "Enrollment pack description (HTML sanitized)",
 "membership\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "image": "Subscription product image url",
 "learn\_more\_path": "Learn more path",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products"
 },
 "subscription\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "image": "Subscription product image url",
 "learn\_more\_path": "Learn more path",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products"
 },
 "membership\_after\_one\_month": "Boolean to check if membership is after one month",
 "first\_payment\_date": "First payment date",
 "agreements <Array>": {
 "id": "Agreement ID",
 "title": "Agreement title",
 "description": "Agreement description",
 "required": "Boolean to check if the agreement is required"
 }
 }
}

### Product template

{
 "product": {
 "title": "Product Title",
 "url": "Product URL (affiliate\_product\_path)",
 "id": "Product ID",
 "short\_description": "Short Description of the product",
 "description": "Description of the product",
 "feature\_text": "Features of the product",
 "out\_of\_stock": "Boolean to check if the product is out of stock",
 "available\_for\_country": "Boolean to check if the product is available for the country",
 "price": "Display price of the product",
 "unit\_price": "Unit price of the product without currency",
 "unit\_subscription\_price": "Unit subscription price of the product without currency",
 "subscription\_price": "Subscription display price of the product",
 "subscription\_label\_text": "Subscription label text",
 "active": "Boolean to check if the product is active",
 "subscription\_only": "Boolean to check if the product is subscription only",
 "allow\_subscription": "Boolean to check if the product allows subscription",
 "limited\_stock": "Boolean if limited stock is applicable",
 "buyable\_quantity": "Quantity available to buy",
 "tags": "List of tag names",
 "saved\_percentage\_on\_subscription": "Saved percentage on subscription",
 "saved\_on\_subscription": "Amount saved on subscription",
 "thumbnail\_image": "Product thumbnail image URL",
 "external\_url": "External product URL if redirectable",
 "available\_values": "Product options with values",
 "metadata": "Metadata of the product",
 "metafields": "Metafields of the product",
 "images <Array>": {
 "id": "Image ID",
 "aspect\_ratio": "Aspect ratio of image",
 "attached\_to\_variant?": "Boolean attached to variant",
 "position": "Image position",
 "product\_id": "Product ID",
 "src": "Image URL",
 "url": "Image URL",
 "variants": \[\],
 "media\_type": "Media type, e.g., image",
 "preview\_image": {
 "src": "Preview Image URL",
 "width": "Preview Image Width",
 "height": "Preview Image Height",
 "aspect\_ratio": "Aspect ratio"
 }
 },
 "media": "Alias of images (same structure)",
 "featured\_media": "First media item (same structure as images)",
 "ratings": "Average rating of the product",
 "reviews <Array>": {
 "body": "Body of the review"
 },
 "recommendations <Array>": {
 "title": "Title of the recommended product",
 "description": "Description of the recommended product",
 "price": "Price of the recommended product",
 "image\_url": "Image URL of the recommended product",
 "url": "Product page URL of the recommended product",
 "short\_description": "Short description",
 "feature\_text": "Feature text",
 "tags": "Tags of recommended product",
 "variants <Array>": {
 "id": "Variant ID",
 "title": "Variant title",
 "image\_url": "Variant image URL",
 "is\_master": "Boolean to check if this is the master variant",
 "option\_values <Array>": {
 "id": "option value ID",
 "presentation": "Option value presentation",
 "selected": "Boolean to check if this value is selected",
 "option\_id": "Option ID"
 },
 "variant\_countries <Array>": {
 "id": "Variant Country ID",
 "country\_iso": "Country ISO code",
 "country\_name": "Country name",
 "currency\_code": "Currency code",
 "price": "Variant country price",
 "display\_price": "Price with currency",
 "wholesale": "Variant country wholesale price",
 "display\_wholesale": "Wholesale price with currency",
 "compare\_price": "Variant country compare-at price",
 "display\_compare\_price": "Compare-at price with currency",
 "subscription\_price": "Variant country subscription price",
 "display\_subscription\_price": "Subscription price with currency"
 }
 },
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Option Name",
 "selected\_value": "Selected Value",
 "values <Array>": {
 "id": "Value ID",
 "name": "Value Name",
 "selected": "Boolean if value is selected"
 }
 }
 },
 "selected\_or\_first\_available\_variant": {
 "available": "Always true in this context",
 "id": "Variant ID",
 "price": "Price of the variant"
 },
 "options\_available": "Boolean to check if options are available",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "presentation": "Presentation name of option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "presentation": "Value presentation",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "variants <Array>": {
 "id": "Variant ID",
 "title": "Variant title",
 "image\_url": "Variant image URL",
 "is\_master": "Boolean to check if this is the master variant",
 "option\_values <Array>": {
 "id": "option value ID",
 "presentation": "Option value presentation",
 "selected": "Boolean to check if this value is selected",
 "option\_id": "Option ID"
 },
 "variant\_countries <Array>": {
 "id": "Variant Country ID",
 "country\_iso": "Country ISO code",
 "country\_name": "Country name",
 "currency\_code": "Currency code",
 "price": "Variant country price",
 "display\_price": "Price with currency",
 "wholesale": "Variant country wholesale price",
 "display\_wholesale": "Wholesale price with currency",
 "compare\_price": "Variant country compare-at price",
 "display\_compare\_price": "Compare-at price with currency",
 "subscription\_price": "Variant country subscription price",
 "display\_subscription\_price": "Subscription price with currency"
 }
 },
 "selected\_subscription\_plan\_id": "Selected Subscription Plan ID",
 "subscription\_plans <Array>": {
 "id": "Subscription Plan ID",
 "name": "Name of the subscription plan",
 "billing\_interval": "Billing interval (e.g., 1)",
 "billing\_interval\_unit": "Billing interval unit (e.g., month)",
 "shipping\_interval": "Shipping interval (e.g., 1)",
 "shipping\_interval\_unit": "Shipping interval unit (e.g., month)",
 "trial\_period": "Trial period (e.g., 14)",
 "trial\_period\_unit": "Trial period unit (e.g., day)",
 "price\_adjustment\_type": "Type of price adjustment (e.g., fixed)",
 "price\_adjustment\_amount": "Amount of price adjustment (e.g., 5.00)",
 "saved\_on\_subscription": "Amount saved on subscription",
 "saved\_percentage\_on\_subscription": "Percentage saved on subscription"
 },
 "bundle\_config": "Bundle configuration object — empty {} for non-bundle products. Includes nested \`mutually\_exclusive\_groups\` (Array of group sort\_order pairs) and \`is\_enrollment\` (Boolean)",
 "track\_inventory\_on\_bundle\_items": "Boolean — whether bundle item inventory is tracked at the item level",
 "bundle\_in\_stock": "Boolean — whether the full bundle is in stock for the storefront warehouse",
 "bundle\_available\_quantity": "Available quantity for the full bundle (null if unlimited)",
 "product\_bundles <Array>": {
 "id": "Bundle ID",
 "quantity": "Bundle quantity",
 "product\_id": "Parent product ID",
 "bundled\_variant\_id": "Bundled variant ID",
 "tax\_percentage": "Tax percentage",
 "display\_externally": "Boolean — whether to display externally",
 "title": "Bundled product title",
 "image\_url": "Bundled product image URL",
 "in\_stock": "Boolean — whether the bundled variant is in stock",
 "available\_quantity": "Available quantity of the bundled variant"
 },
 "product\_bundle\_groups <Array>": {
 "id": "Bundle group ID",
 "title": "Bundle group title",
 "description": "Bundle group description",
 "group\_type": "Group type (e.g., fixed, customizable)",
 "sort\_order": "Sort order of the group",
 "selection\_type": "Selection type (e.g., single, multiple)",
 "min\_selections": "Minimum number of selections required",
 "max\_selections": "Maximum number of selections allowed",
 "allow\_subscriptions": "Boolean — whether items in this group can be subscribed",
 "force\_subscriptions": "Boolean — whether subscription is required for this group",
 "pricing\_type": "Pricing type (e.g., fixed\_price, dynamic\_price)",
 "fixed\_price": "Group fixed price as string",
 "min\_price": "Minimum group price as string",
 "max\_price": "Maximum group price as string",
 "compare\_at\_price": "Compare-at price as string",
 "group\_cv": "Group commission value",
 "group\_qv": "Group qualifying value",
 "track\_quantity": "Boolean — whether to track quantity at the group level",
 "country\_pricing": "Country-specific pricing array (or empty)",
 "pricing\_config": "Raw pricing configuration object",
 "image\_urls <Array>": "Array of group image URLs",
 "images <Array>": {
 "id": "Image ID",
 "src": "Image src url",
 "url": "Image url"
 },
 "bundle\_group\_items <Array>": {
 "id": "Bundle group item ID",
 "quantity": "Quantity of this item in the bundle",
 "sort\_order": "Sort order of the item",
 "price": "Item price as string",
 "cv": "Item commission value",
 "qv": "Item qualifying value",
 "is\_default": "Boolean — whether this item is the default selection",
 "display\_quantity": "Display quantity for the item",
 "max\_quantity": "Maximum quantity allowed",
 "allow\_subscription": "Boolean — whether this item allows subscription",
 "force\_subscription": "Boolean — whether subscription is required for this item",
 "image\_url": "Item image URL",
 "available\_country\_codes <Array>": "Array of country ISO codes the item is available in",
 "country\_prices": "Object mapping country ISO to item price",
 "country\_subscription\_prices": "Object mapping country ISO to item subscription price",
 "subscription\_plan\_id": "Subscription plan ID for the item (if any)",
 "config": "Raw item configuration object",
 "product\_id": "ID of the product the item's variant belongs to",
 "product\_title": "Title of the product the item's variant belongs to",
 "product\_image\_url": "Image URL of the product the item's variant belongs to",
 "product\_bundles <Array>": {
 "id": "Nested bundle ID",
 "quantity": "Nested bundle quantity",
 "product\_id": "Nested bundle's parent product ID",
 "bundled\_variant\_id": "Nested bundled variant ID",
 "tax\_percentage": "Tax percentage",
 "display\_externally": "Boolean — whether to display externally",
 "title": "Nested bundled product title",
 "image\_url": "Nested bundled product image URL",
 "in\_stock": "Boolean — whether the nested bundled variant is in stock",
 "available\_quantity": "Available quantity of the nested bundled variant"
 },
 "in\_stock": "Boolean — whether the bundle item is in stock",
 "available\_quantity": "Available quantity of the bundle item",
 "variant": "Variant drop for the bundle item (same shape as product.variants\[\])"
 }
 }
 },
 "all\_products <Array>": {
 "title": "Product title",
 "description": "Product description",
 "price": "Product display price",
 "image": "Product image url",
 "image\_url": "Product image url",
 "url": "Product url",
 "short\_description": "Product short description",
 "feature\_text": "Product feature text",
 "tags": "List of product tag names",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 }
 },
 "current\_user\_token": "sample",
 "shop\_path": "Affiliate shop path URL",
 "title": "Product Title (deprecated. use product.title)",
 "introduction": "Short Description of the product (deprecated. use product.short\_description)",
 "description": "Description of the product (deprecated. use product.description)",
 "feature\_text": "Features of the product (deprecated. use product.feature\_text)",
 "selected\_variant\_id": "Variant ID (deprecated. use product.selected\_or\_first\_available\_variant.id)",
 "subscription\_only": "Boolean to check if subscription only (deprecated. use product.subscription\_only)",
 "allow\_subscription": "Boolean to check if allows subscription (deprecated. use product.allow\_subscription)",
 "limited\_stock": "Boolean if limited stock is applicable (deprecated. no direct replacement)",
 "buyable\_quantity": "Quantity available to buy (deprecated. no direct replacement)",
 "subscription\_price": "Subscription display price (deprecated. use product.subscription\_price)",
 "price": "Product display price (deprecated. use product.price)",
 "unit\_price": "Unit price (deprecated. use product.unit\_price)",
 "unit\_subscription\_price": "Unit subscription price (deprecated. use product.unit\_subscription\_price)",
 "saved\_percentage\_on\_subscription": "Saved percentage on subscription (deprecated. use product.saved\_percentage\_on\_subscription)",
 "subscription\_label\_text": "Subscription label text (deprecated. use product.subscription\_label\_text)",
 "shop\_path": "Affiliate shop path URL (deprecated. use global shop\_path or product.url)",
 "thumbnail\_image": "Product thumbnail image (deprecated. use product.featured\_media.src)",
 "out\_of\_stock": "Boolean if product is out of stock (deprecated. use product.out\_of\_stock)",
 "options\_available": "Boolean to check if options are available (deprecated. use product.options\_available)",
 "available\_for\_country": "Boolean to check if available for country (deprecated. use product.available\_for\_country)",
 "active": "Boolean to check if active (deprecated. use product.active)",
 "external\_url": "External product URL if redirectable (deprecated. use product.external\_url)",
 "available\_values": "Product options with values (deprecated. use product.options\_with\_values)",
 "images": "Product images (deprecated. use product.images)",
 "ratings": "Product average rating (deprecated. use product.ratings)",
 "reviews": "Product reviews (deprecated. use product.reviews)",
 "recommendations": "Product recommendations (deprecated. use product.recommendations)"
}

### Shop/Collection template

{
 "collection": {
 "all\_products\_count": "Total number of products",
 "products\_count": "Number of products in current page",
 "filters <Array>": {
 "active\_values": "List of active values",
 "inactive\_values": "List of inactive values",
 "label": "Filter label",
 "presentation": "Filter presentation",
 "operator": "Filter operator (always OR)",
 "param\_name": "Param name for the filter",
 "type": "Type of filter (always list)",
 "url\_to\_remove": "URL to remove filter",
 "values <Array>": {
 "active": "Boolean to check if the filter is selected",
 "label": "Value label",
 "param\_name": "Value param name",
 "value": "value",
 "url\_to\_add": "URL to add filter value",
 "url\_to\_remove": "URL to remove filter value",
 "image": "Swatch image URL (currently nil)",
 "swatch": "Swatch value (currently nil)",
 "count": "Count of items for this value"
 }
 },
 "sort\_options <Array>": { 
 "name": "Sort name", 
 "value": "Sort param value" 
 },
 "products <Array>": "List of products"
 },
 "pagination": {
 "total\_count": "Total number of products",
 "values": "Pagination values",
 "selected": "Selected pagination value",
 "current\_page": "Current page number",
 "per\_page": "Number of products per page",
 "pagination\_html": "Pagination links html"
 },
 "url": "Affiliate shop path URL",
 "search\_query": "Search query string from filters",
 "sorted\_by": "Currently selected sort value",
 "options <Array>": {
 "title": "Option name",
 "values <Array>": {
 "id": "Option value ID",
 "presentation": "Option value presentation",
 "selected": "Boolean to check if value is selected"
 }
 },
 "categories <Array>": {
 "title": "Category title",
 "id": "Category ID",
 "count": "Number of products in category",
 "selected": "Boolean to check if category is selected"
 },
 "price\_ranges <Array>": {
 "lower\_range": "Lower price range value",
 "upper\_range": "Upper price range value",
 "lower\_range\_display": "Display price for lower range",
 "upper\_range\_display": "Display price for upper range",
 "selected": "Boolean to check if price range is selected"
 }
}

#### Collection object (used by multiple templates)

{
 "collection": {
 "id": "Collection ID",
 "url": "Collection URL",
 "title": "Collection title",
 "description": "Collection description",
 "handle": "Collection handle",
 "image": "Collection image URL",
 "products": "Products in the collection"
 }
}

### Home template

{
 "base\_url": "Base url",
 "logo\_url": "Logo url",
 "shop\_url": "Shop page url",
 "products\_pagination": {
 "total\_count": "Total number of products",
 "values": "Pagination values",
 "selected": "Selected pagination value",
 "current\_page": "Current page number. Can be updated using params 'products\_page'",
 "per\_page": "Number of products per page. Can be updated using params 'products\_per\_page'"
 },
 "enrollment\_packs <Array>": {
 "enrollment\_pack": {
 "id": "Enrollment pack id",
 "title": "Enrollment pack title",
 "images\_array <Array>": "Array of image urls",
 "description": "Description of the enrollment pack",
 "membership\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products"
 },
 "subscription\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "image": "Subscription product image url (thumbnail)",
 "learn\_more\_path": "Learn more path",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products"
 },
 "membership\_after\_one\_month": "Boolean to check if membership is after one month",
 "first\_payment\_date": "First payment date",
 "agreements <Array>": {
 "id": "Agreement ID",
 "title": "Agreement title",
 "description": "Agreement description",
 "required": "Boolean to check if the agreement is required"
 }
 }
 }
}

### Join template

{
 "url": "URL to the join page",
 "collection": {
 "all\_enrollment\_packs\_count": "Total number of enrollment packs",
 "enrollment\_packs\_count": "Number of enrollment packs in current page",
 "enrollment\_packs <Array>": {
 "id": "Enrollment pack id",
 "url": "URL to the enrollment pack",
 "title": "Enrollment pack title",
 "images\_array <Array>": "Array of image urls",
 "description": "Description of the enrollment pack",
 "membership\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products"
 },
 "subscription\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "image": "Subscription product image url",
 "learn\_more\_path": "Learn more path",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products"
 },
 "membership\_after\_one\_month": "Boolean to check if membership is after one month",
 "first\_payment\_date": "First payment date",
 "agreements <Array>": {
 "id": "Agreement ID",
 "title": "Agreement title",
 "description": "Agreement description",
 "required": "Boolean to check if the agreement is required"
 }
 }
 },
 "search\_query": "Search query string from filters",
 "sorted\_by": "Currently selected sort value",
 "pagination": {
 "total\_count": "Total number of enrollment packs",
 "values": "Pagination values",
 "selected": "Selected pagination value",
 "current\_page": "Current page number",
 "per\_page": "Number of enrollment packs per page",
 "pagination\_html": "Pagination links html"
 }
}

### Media template

{
 "id": "Medium id",
 "title": "Medium title",
 "kind": "Medium kind",
 "description": "Medium description",
 "poster": "Medium poster",
 "embed\_url": "Embed url",
 "video\_url": "Video url",
 "pdf\_url": "Pdf url",
 "image\_url": "Image url",
 "powerpoint\_url": "Powerpoint url",
 "cta\_url": "Cta url",
 "cta\_button\_text": "Cta button text",
 "comment\_submit\_url": "Comment submit url",
 "display\_comments": "Boolean to check if comments are displayed",
 "comment\_visibility": "Comment visibility",
 "contact": {
 "full\_name": "Contact full name",
 "id": "Contact id",
 "email": "Contact email",
 "phone": "Contact phone"
 },
 "comments <Array>": {
 "id": "Comment id",
 "name": "Commentor name",
 "initials": "Commentor initials",
 "created\_at": "Comment created at",
 "body": "Comment body"
 },
 "display\_shop": "Boolean to check if shop should be displayed",
 "shop\_path": "Shop path",
 "learn\_more\_path": "Learn more path",
 "lead\_captureable": "Boolean to check if lead capture is enabled",
 "visitable\_on\_click": "Boolean to check if CTA is visitable on click",
 "social\_media": {
 "facebook": "Facebook URL",
 "twitter": "Twitter URL",
 "instagram": "Instagram URL",
 "pinterest": "Pinterest URL",
 "youtube": "Youtube URL"
 },
 "video\_status\_url": "Url to update video status",
 "video\_shopping\_enabled": "Boolean to check if video shopping is enabled"
}

### Enrollment Pack template

{
 "enrollment\_pack": {
 "id": "Enrollment pack id",
 "title": "Enrollment pack title",
 "images\_array <Array>": "Array of image urls",
 "description": "Description of the enrollment pack",
 "membership\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "variant\_id": "Applicable variant ID of the enroll product — keys bundle\_selections at enrollment checkout (FairShareSDK.addEnrollmentPack)",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products",
 "product\_bundle\_groups <Array>": "Product bundle groups (same structure as product.product\_bundle\_groups) — used to render bundle-group selectors on the enrollment page",
 "mutually\_exclusive\_groups <Array>": "Array of mutually-exclusive group sort\_order pairs"
 },
 "subscription\_products <Array>": {
 "title": "Product title",
 "price": "Prduct display price",
 "cv": "Product display cv",
 "variant\_id": "Applicable variant ID of the enroll product — keys bundle\_selections at enrollment checkout (FairShareSDK.addEnrollmentPack)",
 "image": "Subscription product image url",
 "learn\_more\_path": "Learn more path",
 "options\_with\_values <Array>": {
 "option\_id": "Option ID",
 "name": "Name of the option",
 "selected\_value": "Selected value of the option",
 "values <Array>": {
 "id": "Value ID",
 "name": "Name of the value",
 "selected": "Boolean to check if the value is selected"
 }
 },
 "bundle\_config": "Bundle configuration object (empty {} for non-bundle products) — includes bundle\_pricing\_enabled and bundle\_pricing\_config.country\_pricing (per-country price/wholesale/cv/qv) for bundle-priced products",
 "product\_bundle\_groups <Array>": "Product bundle groups (same structure as product.product\_bundle\_groups) — used to render bundle-group selectors on the enrollment page",
 "mutually\_exclusive\_groups <Array>": "Array of mutually-exclusive group sort\_order pairs"
 },
 "membership\_after\_one\_month": "Boolean to check if membership is after one month",
 "first\_payment\_date": "First payment date",
 "agreements <Array>": {
 "id": "Agreement ID",
 "title": "Agreement title",
 "description": "Agreement description",
 "required": "Boolean to check if the agreement is required"
 }
 }
}

### Library template

{
 "id": "Library id",
 "title": "Library title",
 "library\_items <Array>": {
 "id": "Library item id",
 "type": "Library item type",
 "title": "Library item title",
 "description": "Library item description",
 "image": "Library item image",
 "url": "Library item url",
 "display\_price": "Product display price. Only for product or enrollment\_pack type",
 "price": "Product price. Only for product type",
 "product": "Product details. Only for product type",
 "enrollment\_pack": "Enrollment pack details. Only for enrollment pack type",
 "kind": "Medium kind. Only for medium type",
 "video\_url": "Medium video url. Only for medium type",
 "pdf\_url": "Medium pdf url. Only for medium type",
 "image\_url": "Medium image url. Only for medium type",
 "powerpoint\_url": "Medium powerpoint url. Only for medium type"
 },
 "social\_media": {
 "facebook": "Facebook URL",
 "twitter": "Twitter URL",
 "instagram": "Instagram URL",
 "pinterest": "Pinterest URL",
 "youtube": "Youtube URL"
 },
 "active\_product\_ids": "List of active product ids",
 "comment\_submit\_url": "Url to submit comments",
 "contact": {
 "full\_name": "Contact full name",
 "id": "Contact id",
 "email": "Contact email",
 "phone": "Contact phone"
 },
 "comments <Array>": {
 "id": "Comment id",
 "name": "Commentor name",
 "initials": "Commentor initials",
 "created\_at": "Comment created at",
 "body": "Comment body"
 }
}

### My Site template

{
 "fluid\_affiliate": {
 "name": "Full name of the affiliate (fluid\_affiliate.full\_name)",
 "bio": "Bio of the affiliate (fluid\_affiliate.bio)",
 "image": "Avatar URL, 70 × 70 px (fluid\_user.image\_transformations('n-avatar\_70') or fallback)",
 "facebook\_url": "Facebook profile URL or null",
 "twitter\_url": "Twitter profile URL or null",
 "instagram\_url": "Instagram profile URL or null",
 "linkedin\_url": "LinkedIn profile URL or null",
 "youtube\_url": "YouTube channel URL or null",
 "tiktok\_url": "TikTok profile URL or null",
 "pinterest\_url": "Pinterest profile URL or null",
 "whatsapp\_url": "WhatsApp link or null",
 "wechat\_url": "WeChat link or null",
 "links <Array>": {
 "text": "Link title",
 "url": "Destination URL"
 },
 "my\_site\_displayables <Array>": {
 "id": "Favorite ID",
 "favoriteable\_type": "\\"Product\\", \\"EnrollmentPack\\" or \\"Medium\\"",
 "favoriteable\_id": "ID of the underlying record",
 "favoriteable": {
 "id": "Underlying record ID",
 "type": "\\"product\\", \\"enrollmentpack\\" or \\"medium\\" (lower-case)",
 "title": "Title of the item",
 "description": "Plain-text description",
 "image": "Card/cover image URL (n-mysite\_card)",
 "display\_price": "Display price string",
 "price": "Unit price (number, no currency)",
 "kind": "Medium kind (e.g., \\"video\\", \\"pdf\\", \\"image\\")",
 "video\_url": "Video URL if kind == video",
 "pdf\_url": "PDF URL if kind == pdf",
 "image\_url": "Image URL if kind == image",
 "powerpoint\_url": "PowerPoint URL if kind == ppt"
 }
 }
 }
}

### Cart template

{
 "cart": {
 "id": "Cart Id",
 "cart\_token": "Cart Token",
 "amount\_total": "Total Amount",
 "amount\_total\_in\_currency": "Total Amount with currency",
 "currency\_code": "Currency Code",
 "currency\_symbol": "Currency Symbol",
 "cv\_total": "CV Total",
 "discount\_total": "Discount Total",
 "discount\_total\_in\_currency": "Discount Total with currency",
 "email": "Email",
 "enrollment\_fee": "Enrollment Fee",
 "enrollment\_fee\_in\_currency": "Enrollment Fee with currency",
 "items": \[\],
 "shipping\_total": "Total Shipping",
 "shipping\_total\_for\_display": "Total Shipping for display",
 "shipping\_total\_in\_currency": "Total Shipping with currency",
 "sub\_total": "Sub Total",
 "sub\_total\_in\_currency": "Sub Total with currency",
 "tax\_total": "Tax Total",
 "tax\_total\_in\_currency": "Tax Total with currency",
 "checkout\_url": "Checkout URL",
 "items <Array>": {
 "id": "Cart Item Id",
 "price": "Price",
 "price\_in\_currency": "Price with currency",
 "product": {
 "id": "Product Id",
 "image\_url": "Image URL",
 "price": "Product Price",
 "price\_in\_currency": "Product Price with currency",
 "tax": "Tax",
 "tax\_in\_currency": "Tax with currency",
 "title": "Product Title"
 },
 "product\_title": "Product Title",
 "quantity": "Item Quantity",
 "subscription\_price": "Subscription Price",
 "subscription\_price\_in\_currency": "Subscription Price with currency",
 "subscription\_start": "Subscription Start",
 "tax": "Tax",
 "tax\_in\_currency": "Tax with currency",
 "variant\_id": "Variant Id",
 "variant\_image": "Variant Image",
 "variant\_title": "Variant Title"
 }
 }
}