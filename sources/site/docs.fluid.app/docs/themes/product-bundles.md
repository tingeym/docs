# Source: https://docs.fluid.app/docs/themes/product-bundles

# Product Bundles

A **product bundle** lets a single product be sold as a configurable kit of other products — a mix of fixed "included" items and customer-customizable groups. On the **Product template**, a bundle product exposes its full structure through a set of Liquid drops, so a section (for example, a custom bundle builder) can render the groups, items, pricing, and subscription options.

> Bundle variables are only populated for products that _are_ bundles. For a non-bundle product, `product.bundle_config` is an empty object (`{}`) and `product.product_bundle_groups` is an empty array.

---

## Where bundles are exposed

All bundle data hangs off the `product` drop (Product scope). These are the entry points:

{
 "product": {
 "bundle\_config": "Bundle configuration object — empty {} for non-bundle products",
 "mutually\_exclusive\_groups <Array>": "Array of mutually-exclusive group sort\_order pairs — groups that cannot be selected together",
 "is\_enrollment": "Boolean — true when the product's bundle is an enrollment bundle",
 "track\_inventory\_on\_bundle\_items": "Boolean — whether bundle item inventory is tracked at the item level",
 "bundle\_in\_stock": "Boolean — whether the full bundle is in stock for the storefront warehouse",
 "bundle\_available\_quantity": "Available quantity for the full bundle (null if unlimited)",
 "product\_bundles <Array>": "Statically bundled products — see ProductBundle below",
 "product\_bundle\_groups <Array>": "Bundle groups and their items — see ProductBundleGroup below",
 "subscription\_plans <Array>": "Bundle-wide subscription plans — the bundle product's own plans (same shape as an item's subscription\_plans). Drives the bundle-wide Subscribe & Save option; empty when the bundle has no plans."
 }
}

Guard for bundle data before rendering:

{'%' if product and product.product\_bundle\_groups '%'}
 {'%' for group in product.product\_bundle\_groups '%'}
 <h3\>{{ group.title | default: 'Group' }}</h3\>
 {'%' endfor '%'}
{'%' endif '%'}

---

## `product.product_bundle_groups[]` — ProductBundleGroup

Each entry is a bundle group: either a fixed set of `included` items, or a `customizable` group the customer picks from. Items live under `bundle_group_items`.

{
 "id": "Bundle group ID",
 "title": "Bundle group title",
 "description": "Bundle group description",
 "group\_type": "Group type: \`included\` (fixed items) or \`customizable\` (customer picks)",
 "sort\_order": "Sort order of the group on the storefront",
 "selection\_type": "Completion rule for customizable groups: \`exact\`, \`min\_only\`, \`max\_only\`, or \`min\_and\_max\`",
 "min\_selections": "Minimum number of selections required (null for \`max\_only\` groups, which have no lower bound)",
 "max\_selections": "Maximum number of selections allowed (null/unbounded for \`min\_only\`; collapses to \`min\_selections\` for \`exact\`)",
 "pricing\_type": "Pricing type: \`fixed\_price\` or \`dynamic\_price\`",
 "fixed\_price": "Group fixed price as string",
 "min\_price": "Minimum group price as string (dynamic\_price groups)",
 "max\_price": "Maximum group price as string (dynamic\_price groups)",
 "compare\_at\_price": "Compare-at (strikethrough) price as string",
 "group\_cv": "Group commission value — deprecated, prefer per-country CV via country\_pricing",
 "group\_qv": "Group qualifying value — deprecated, prefer per-country QV via country\_pricing",
 "track\_quantity": "Boolean — whether to track quantity at the group level",
 "country\_pricing <Array>": {
 "country\_code": "ISO country code (e.g., US)",
 "enabled": "Boolean — whether this country entry is active",
 "price": "Country-specific price as string",
 "subscription\_price": "Country-specific subscription price as string",
 "compare\_price": "Country-specific compare-at price as string",
 "cv": "Country-specific commission value",
 "qv": "Country-specific qualifying value",
 "wholesale": "Country-specific wholesale price",
 "points": "Country-specific points value",
 "wholesale\_cv": "Country-specific wholesale commission value",
 "wholesale\_qv": "Country-specific wholesale qualifying value"
 },
 "pricing\_config": "Raw pricing configuration object (source for fixed\_price, country\_pricing, etc.)",
 "allow\_subscriptions": "Boolean — whether items in this group can be subscribed",
 "force\_subscriptions": "Boolean — whether subscription is required for every item in this group",
 "image\_urls <Array>": "Array of group image URLs",
 "images <Array>": {
 "id": "Image ID",
 "src": "Image src url",
 "url": "Image url"
 },
 "bundle\_group\_items <Array>": {
 "id": "Bundle group item ID",
 "quantity": "Quantity of this item in the bundle",
 "sort\_order": "Sort order of the item within the group",
 "price": "Item price as string",
 "cv": "Item commission value",
 "qv": "Item qualifying value",
 "is\_default": "Boolean — whether this item is pre-selected by default",
 "display\_quantity": "Display quantity for the item",
 "max\_quantity": "Maximum quantity a customer can add of this item",
 "allow\_subscription": "Boolean — whether this item allows subscription",
 "force\_subscription": "Boolean — whether subscription is required for this item",
 "image\_url": "Item image URL",
 "available\_country\_codes <Array>": "Array of country ISO codes the item is available in",
 "country\_prices": "Object mapping country ISO code to item price",
 "country\_subscription\_prices": "Object mapping country ISO code to item subscription price",
 "subscription\_plan\_id": "Subscription plan ID for the item (if any)",
 "subscription\_plans <Array>": {
 "id": "Subscription plan ID",
 "name": "Plan name",
 "billing\_interval": "Billing interval count",
 "billing\_interval\_unit": "Billing interval unit (e.g., month, week)",
 "shipping\_interval": "Shipping interval count",
 "shipping\_interval\_unit": "Shipping interval unit",
 "trial\_period": "Trial period count",
 "trial\_period\_unit": "Trial period unit",
 "price\_adjustment\_type": "Discount type: \`percentage\` or \`fixed\_amount\`",
 "price\_adjustment\_amount": "Discount amount",
 "max\_skips": "Maximum number of skips allowed",
 "savings\_display\_mode": "How savings are displayed"
 },
 "config": "Raw item configuration object",
 "product\_id": "ID of the product the item's variant belongs to",
 "product\_title": "Title of the product the item's variant belongs to",
 "product\_image\_url": "Image URL of the product the item's variant belongs to",
 "product\_bundles <Array>": "Statically bundled products for this item's product (same shape as \`product.product\_bundles\[\]\`)",
 "variant": "Variant drop for the bundle item (same shape as \`products\[\].variants\[\]\`)",
 "in\_stock": "Boolean — whether the bundle item is in stock",
 "available\_quantity": "Available quantity (number of sets) for the bundle item, or null if untracked"
 },
 "created\_at": "ISO8601 creation timestamp (or null)",
 "updated\_at": "ISO8601 update timestamp (or null)"
}

---

## `product.product_bundles[]` — ProductBundle

Each entry is a single statically-bundled product/variant included with the parent product.

{
 "id": "Product bundle ID",
 "quantity": "Quantity of the bundled product",
 "product\_id": "ID of the parent product that owns the bundle",
 "bundled\_variant\_id": "ID of the bundled variant",
 "tax\_percentage": "Tax percentage for this bundled item",
 "display\_externally": "Boolean — whether to display this item on the storefront",
 "title": "Bundled product title (null if the bundled variant is missing)",
 "image\_url": "Bundled product image URL (null if the bundled variant is missing)",
 "bundled\_product": "Product drop for the bundled product (null if the variant was discarded)",
 "bundled\_variant": "Variant drop for the bundled variant (same shape as \`products\[\].variants\[\]\`)",
 "in\_stock": "Boolean — whether the bundled variant is in stock",
 "available\_quantity": "Available quantity (number of sets) for the bundled variant, or null if untracked"
}

---

## Building a bundle section

A bundle product is just a product that exposes `product_bundle_groups`. To build a custom bundle builder, you follow six steps: **embed the data → render groups & items → track selections → enforce each group's rule → show a live total → submit to the cart.**

The reference implementation is the global `product_bundle` section (`app/themes/templates/global/sections/product_bundle/index.liquid`). This guide distills its core flow into the minimum that works — it deliberately leaves out the advanced features (see **What this guide leaves out** at the end). The server always re-validates the submitted bundle (`CartBundleValidator`), so the client logic here is for UX, not enforcement.

### 1\. Embed the bundle data

Render the bundle product as JSON into a `<script>` tag so your JavaScript can read the whole structure once. Guard it so it only renders for actual bundles:

{'%' if product.product\_bundle\_groups and product.product\_bundle\_groups.size > 0 '%'}
 <script type\="application/json" data-bundle-data\>{{ product | json }}</script\>
{'%' endif '%'}

> Place the section on the **product** template (where `product` is in scope). For a standalone landing page, add a `{ "type": "product", "id": "bundle_product" }` schema setting and use `section.settings.bundle_product` instead — that is what the reference section does.

### 2\. Render the groups and items

Loop the groups (ordered by `sort_order`) and their items. Mark each card with the data the script needs, and distinguish `customizable` groups (the shopper picks) from `included` groups (fixed, read-only):

<div data-bundle-builder\>
 {'%' assign groups = product.product\_bundle\_groups | sort: 'sort\_order' '%'}
 {'%' for group in groups '%'}
 <div class\="bundle-group" data-group-id\="{{ group.id }}" data-max\="{{ group.max\_selections | default: 0 }}"\>
 <h3\>{{ group.title }}</h3\>

 {'%' for item in group.bundle\_group\_items '%'}
 <div class\="bundle-card" data-variant-id\="{{ item.variant.id }}"\>
 <span\>{{ item.product\_title | default: item.variant.title }}</span\>
 <span\>{{ item.price }}</span\>
 {'%' if group.group\_type == 'customizable' '%'}
 <button type\="button" data-add\>Add</button\>
 {'%' else '%'}
 <span\>{{ item.quantity }}× included</span\>
 {'%' endif '%'}
 </div\>
 {'%' endfor '%'}
 </div\>
 {'%' endfor '%'}

 <div class\="bundle-summary"\>
 <strong\>Total: <span data-total\>0.00</span\></strong\>
 <button type\="button" data-add-bundle disabled\>Add Bundle to Cart</button\>
 </div\>
</div\>

### 3–5. Track selections, enforce the rule, show the total

A small script holds the selection state, enforces each group's `selection_type`, computes a running total, and gates the "Add Bundle to Cart" button until every group is complete. The `groupComplete` helper mirrors `isGroupComplete` in the reference section — see the `selection_type` field in the ProductBundleGroup reference above for the four rules.

<script\>
(function () {
 var root = document.querySelector('\[data-bundle-builder\]');
 var data = JSON.parse(document.querySelector('\[data-bundle-data\]').textContent);
 var bundleVariantId = data.selected\_or\_first\_available\_variant.id;
 var selections = {}; // { groupId: { variantId: qty } }

 function countFor(gid) {
 var sel = selections\[gid\] || {}, n = 0;
 for (var v in sel) n += sel\[v\];
 return n;
 }

 // Completion rule per group — mirrors isGroupComplete in the reference section.
 function groupComplete(group, count) {
 if (group.group\_type !== 'customizable') return true;
 var min = group.min\_selections || 0;
 var max = group.max\_selections || Infinity;
 switch (group.selection\_type) {
 case 'exact': return count === min;
 case 'min\_only': return count >= min;
 case 'max\_only': return true;
 default: return count >= min && count <= max; // min\_and\_max
 }
 }

 // Add an item, respecting the group's max\_selections.
 root.addEventListener('click', function (e) {
 var btn = e.target.closest('\[data-add\]');
 if (!btn) return;
 var groupEl = btn.closest('.bundle-group');
 var gid = groupEl.dataset.groupId;
 var vid = btn.closest('.bundle-card').dataset.variantId;
 var max = parseInt(groupEl.dataset.max) || Infinity;
 selections\[gid\] = selections\[gid\] || {};
 if (countFor(gid) >= max) return;
 selections\[gid\]\[vid\] = (selections\[gid\]\[vid\] || 0) + 1;
 refresh();
 });

 // Recompute total + enable checkout when every group is complete.
 function refresh() {
 var total = 0, complete = true;
 data.product\_bundle\_groups.forEach(function (group) {
 var sel = selections\[group.id\] || {};
 for (var vid in sel) {
 var item = (group.bundle\_group\_items || \[\]).find(function (i) {
 return i.variant && String(i.variant.id) === vid;
 });
 if (item) total += parseFloat(item.price || 0) \* sel\[vid\];
 }
 if (!groupComplete(group, countFor(group.id))) complete = false;
 });
 root.querySelector('\[data-total\]').textContent = total.toFixed(2);
 root.querySelector('\[data-add-bundle\]').disabled = !complete;
 }

 refresh();

 // Wire the "Add Bundle to Cart" button here (step 6) — it shares
 // data, selections, and bundleVariantId from this same closure.
})();
</script\>

### 6\. Add the bundle to the cart

On checkout, build the `bundled_items` payload from the **customizable** selections — plain `included`\-group items are reconstituted server-side (submitting them too is rejected as a duplicate). The one exception is `included` groups that belong to a **mutually exclusive set**: those _must_ be submitted so the server knows which branch was chosen — the Mutual exclusivity section below shows the one-line guard change for that case. Add this inside the same IIFE (it shares `data`, `selections`, and `bundleVariantId`):

root.querySelector('\[data-add-bundle\]').addEventListener('click', function () {
 var bundledItems \= \[\];
 data.product\_bundle\_groups.forEach(function (group) {
 if (group.group\_type !== 'customizable') return; // included items reconstituted server-side (see exclusivity exception below)
 var sel \= selections\[group.id\] || {};
 for (var vid in sel) {
 bundledItems.push({
 variant\_id: parseInt(vid),
 quantity: sel\[vid\],
 product\_bundle\_group\_id: parseInt(group.id)
 });
 }
 });

 window.FairShareSDK.addCartItems(bundleVariantId, {
 quantity: 1,
 bundled\_items: bundledItems
 });
});

`addCartItems(bundleVariantId, options)` is the integration contract. Each `bundled_items` entry needs `variant_id`, `quantity`, and `product_bundle_group_id`. The server (`CartBundleValidator`) validates selection counts, availability, mutual exclusivity, pricing, and subscription rules, then recomputes the price — so a tampered client payload is caught at checkout.

### Mutual exclusivity

Some groups are wired so that **only one of them can be active at a time** — e.g. "Choose your free gift: A _or_ B". This lives on `product.mutually_exclusive_groups`, a list of sets. Two things to know:

- Members are referenced by group **`sort_order`**, not `id` — so map them once.
- A set is either a bare array (`[0, 1]`) or an object carrying a default branch (`{ "ids": [0, 1], "default": 0 }`).

Build the set membership up front:

// mutually\_exclusive\_groups members are group SORT\_ORDERS, not ids.
function buildExclusiveSets(data) {
 var sortToId \= {};
 data.product\_bundle\_groups.forEach(function (g) { sortToId\[g.sort\_order\] \= String(g.id); });
 return (data.mutually\_exclusive\_groups || \[\]).map(function (set) {
 var sortOrders \= Array.isArray(set) ? set : (set.ids || \[\]);
 return {
 ids: sortOrders.map(function (so) { return sortToId\[so\]; }).filter(Boolean),
 defaultId: (set && set.default != null) ? sortToId\[set.default\] : null
 };
 });
}

function siblingsOf(sets, gid) {
 var out \= \[\];
 sets.forEach(function (set) {
 if (set.ids.indexOf(String(gid)) \=== \-1) return;
 set.ids.forEach(function (id) { if (id !== String(gid)) out.push(id); });
 });
 return out;
}

First, alongside `selections` at the **top of the IIFE** (page scope — these must persist across clicks, so they do _not_ live inside the handler):

var exclusiveSets \= buildExclusiveSets(data);
var disabledGroups \= {}; // exclusive branches the shopper isn't using

Then, **inside** the `[data-add]` click handler — picking in one branch clears its siblings and marks them disabled so the completeness check can skip them:

// inside the \[data-add\] click handler, before adding the item:
siblingsOf(exclusiveSets, gid).forEach(function (sib) {
 selections\[sib\] \= {}; // discard the losing branch's selected items
 disabledGroups\[sib\] \= true; // and skip it in refresh()
});
delete disabledGroups\[gid\]; // the branch just picked is active again

A cleared branch keeps empty selections, so `refresh()` **must** skip disabled groups — otherwise a customizable branch (e.g. `min_selections: 1`, `selection_type: 'exact'`) stays incomplete and pins the button disabled forever. Update its per-group completeness check:

// in refresh(), replacing the completeness line:
if (!disabledGroups\[group.id\] && !groupComplete(group, countFor(group.id))) complete \= false;

On first load, if a set declares a `default`, pre-select that branch and clear the others so the shopper never starts in a two-branch conflict:

exclusiveSets.forEach(function (set) {
 if (!set.defaultId) return;
 set.ids.forEach(function (id) {
 if (id !== set.defaultId) { selections\[id\] \= {}; disabledGroups\[id\] \= true; }
 });
});

**Checkout exception.** When a set includes an `included` (static) branch, that branch's items _must_ be submitted — otherwise the server can't tell which branch was chosen and rejects the cart with a 422 (`mutually_exclusive_groups_selected`). Adjust the step 6 guard so exclusive branches are never skipped (here `exclusiveSets` / `siblingsOf` are in scope):

data.product\_bundle\_groups.forEach(function (group) {
 var inExclusive \= siblingsOf(exclusiveSets, group.id).length \> 0;
 if (group.group\_type !== 'customizable' && !inExclusive) return; // skip only plain included groups
 // ...build and push each entry exactly as in step 6
});

> The reference section also adds the _optional_ UX around this: a per-group **"Choose this group"** button, visually dimming the inactive branch, and re-rendering sibling cards. (Skipping `disabledGroups` in `refresh()`, shown above, is required for a working button — not polish.) See `getMutuallyExclusiveGroupIds` / `updateMutualExclusivity` in `index.liquid`.

### Subscriptions

Bundles support **two independent subscription layers**. Both rely on `subscription_plans` (the plan list documented above) and both ride along in the same `addCartItems` call.

#### Per-item subscriptions

An item whose product has `subscription_plans` can be subscribed on its own. The mode is derived exactly like the reference's `resolveItemSubscriptionMode`:

// "hidden" | "optional" | "forced"
function itemSubMode(item, group) {
 if (!(item.subscription\_plans || \[\]).length) return 'hidden';
 if (item.force\_subscription || group.force\_subscriptions) return 'forced';
 if (item.allow\_subscription) return 'optional';
 return 'hidden';
}

Render a Subscribe checkbox for `optional` / `forced` items (checked **and** disabled when `forced`), plus a plan `<select>` when the product has 2+ plans. Track the choice keyed by group + variant:

var itemSubs \= {}; // { groupId: { variantId: { active, planId } } }

// ...when the customer ticks the checkbox / picks a plan:
itemSubs\[gid\] \= itemSubs\[gid\] || {};
itemSubs\[gid\]\[vid\] \= { active: checkbox.checked, planId: parseInt(planSelect.value) };

#### Bundle-wide subscription

The whole bundle can be sold as a subscription using the **bundle product's own** plans (`data.subscription_plans`). Offer a one-time vs. subscribe toggle; if there are no bundle plans, omit the toggle entirely:

var bundlePlans \= data.subscription\_plans || \[\]; // empty -> no bundle-wide option
var bundleSub \= { active: false, planId: null };

// ...on the subscribe radio / plan select:
bundleSub.active \= (radio.value \=== 'subscription' && radio.checked);
bundleSub.planId \= parseInt(planSelect.value) || (bundlePlans\[0\] && bundlePlans\[0\].id);

#### Attaching both at checkout

Extend the step-6 payload. Per-item subscriptions decorate each `bundled_items` entry; the bundle-wide subscription attaches to the **top-level** options — and note the top-level key is `subscribe`, **not** `subscription`:

// build each entry as a variable so you can decorate it:
for (var vid in sel) {
 var entry \= {
 variant\_id: parseInt(vid),
 quantity: sel\[vid\],
 product\_bundle\_group\_id: parseInt(group.id)
 };
 var sub \= (itemSubs\[group.id\] || {})\[vid\];
 if (sub && sub.active && sub.planId) {
 entry.subscription \= true;
 entry.subscription\_plan\_id \= sub.planId;
 }
 bundledItems.push(entry);
}

// after building bundledItems:
var options \= { quantity: 1, bundled\_items: bundledItems };
if (bundleSub.active && bundleSub.planId) {
 options.subscribe \= true; // top-level uses \`subscribe\`, not \`subscription\`
 options.subscription\_plan\_id \= bundleSub.planId;
}
window.FairShareSDK.addCartItems(bundleVariantId, options);

> `CartBundleValidator` confirms each `subscription_plan_id` belongs to the item's (or bundle's) product, so no client-side ownership check is needed. A plan's `price_adjustment_type` / `price_adjustment_amount` are informational for showing savings — the charged amount is always recomputed server-side.

### What this guide leaves out

The reference section also handles the following. Reach for it (and the variable reference above) when you need them — each maps to fields already documented on the drops:

- **Quantity steppers & per-item `max_quantity`** — `+`/`−` controls instead of a single Add.
- **Country / region pricing & availability** — `country_pricing`, `country_prices`, `available_country_codes`, and bundle-level `bundle_pricing_config`.
- **Stock states** — `in_stock` / `available_quantity` for out-of-stock and low-stock messaging.
- **Progress UI** — a completion bar driven by each group's required count.

> Full reference: `app/themes/templates/global/sections/product_bundle/index.liquid`.