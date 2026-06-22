# Source: https://docs.fluid.app/docs/themes/schema-components

# Schema Components: Complete Developer Guide

This comprehensive guide explains how to build dynamic, customizable theme sections using Fluid's schema system. Learn through the from production themes, with complete code snippets showing both schema definitions and Liquid implementations.

## What You'll Learn

- **Resource selectors** - How to let users select products, variants, collections, categories, and posts
- **Global loops** - How to display all items from a resource type with pagination
- **Blocks** - How to create repeatable, reorderable content units
- **Settings** - How to configure section-level options
- **Real examples** - Production-ready patterns from actual implementations
- **Template integration** - How templates and sections work together

---

## How Templates and Sections Work Together

Understanding the three-layer architecture is crucial for building Fluid themes. This section explains how layouts, templates, and sections work together.

### The Three-Layer Architecture

1. Layout (theme.liquid)
 └── Wraps entire page with <html\>, <head\>, <body\>
 └── Includes global sections (navbar, footer)
 └── Renders template via {{ content\_for\_layout }}
 
2. Template (e.g., home\_page/default/index.liquid, product/default/index.liquid)
 └── Defines which sections to include
 └── Has its own schema defining section order and default settings
 
3. Sections (e.g., hero\_section, main\_product, related\_products)
 └── Each Section has:
 ├── Schema (defines settings/blocks available)
 ├── Liquid Template (renders content)
 ├── Styles (CSS)
 └── Settings Values (stored per template instance)

### How It Works: Real Example

#### Layer 1: Layout (theme.liquid)

The layout wraps every page with HTML structure and global sections:

**File:** `app/themes/templates/base/layouts/theme.liquid`

<!DOCTYPE html>
<html lang="{{localization.language.iso\_code}}">
 <head>
 {{ content\_for\_header }}
 {\`%\`- comment -\`%\`} CSS, fonts, global settings {\`%\`- endcomment -\`%\`}
 </head>
 <body>
 {\`%\`- comment -\`%\`} Global section - appears on every page {\`%\`- endcomment -\`%\`}
 {\`%\` section 'navbar' \`%\`}
 
 {\`%\`- comment -\`%\`} Template content renders here {\`%\`- endcomment -\`%\`}
 {{ content\_for\_layout }}
 
 {\`%\`- comment -\`%\`} Global section - appears on every page {\`%\`- endcomment -\`%\`}
 {\`%\` section 'footer' \`%\`}
 
 {\`%\`- comment -\`%\`} Global scripts {\`%\`- endcomment -\`%\`}
 <script src="{{ 'global.js' | asset\_url }}"></script>
 </body>
</html>

#### Layer 2: Template Defines Sections

**File:** `app/themes/templates/base/home_page/default/index.liquid`

 {\`%\`- comment -\`%\`} Each template calls specific sections {\`%\`- endcomment -\`%\`}
 {\`%\` section 'hero\_section', id: 'hero\_section' \`%\`}
 {\`%\` section 'intro\_section', id: 'intro\_section' \`%\`}
 {\`%\` section 'multiple\_slider', id: 'multiple\_slider' \`%\`}
 {\`%\` section 'banner1', id: 'banner1' \`%\`}
 {\`%\` section 'testimonial', id: 'testimonial' \`%\`}
 {\`%\` section 'feature', id: 'feature' \`%\`}
 {\`%\` section 'blog', id: 'blog' \`%\`}
 {\`%\` section 'brand', id: 'brand' \`%\`}

{\`%\`- comment -\`%\`} Template schema defines which sections are available {\`%\`- endcomment -\`%\`}
{\`%\` schema \`%\`}
{
 "name": "Home Page",
 "sections": {
 "hero\_section": {
 "type": "hero\_section"
 },
 "intro\_section": {
 "type": "intro\_section"
 },
 "multiple\_slider": {
 "type": "multiple\_slider"
 }
 // ... more sections
 }
}
{\`%\` endschema \`%\`}

**Another Template:** `app/themes/templates/base/product/default/index.liquid`

{\`%\`- comment -\`%\`} Product template uses different sections {\`%\`- endcomment -\`%\`}
{\`%\` section 'main\_product', id: 'main\_product' \`%\`}
{\`%\` section 'testimonial\_slider', id: 'testimonial\_slider' \`%\`}
{\`%\` section 'related\_products', id: 'related\_products' \`%\`}
{\`%\` section 'cta\_banner5', id: 'cta\_banner5' \`%\`}

{\`%\` schema \`%\`}
{
 "sections": {
 "main\_product": {
 "type": "main\_product",
 "settings": {
 "show\_breadcrumb": true
 }
 },
 "related\_products": {
 "type": "related\_products",
 "settings": {
 "heading": "Related Products",
 "heading\_size": "h1"
 }
 }
 }
}
{\`%\` endschema \`%\`}

#### Layer 3: Section Has Schema

**File:** `app/themes/templates/base/sections/hero_section/index.liquid`

{\`%\`- comment -\`%\`} Section renders its content {\`%\`- endcomment -\`%\`}
<section class="hero">
 <h1>{{ section.settings.heading }}</h1>
 <p>{{ section.settings.subtitle }}</p>
</section>

{\`%\`- comment -\`%\`} Section schema defines what settings are available {\`%\`- endcomment -\`%\`}
{\`%\` schema \`%\`}
{
 "name": "Hero Section",
 "tag": "section",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Hero Heading",
 "default": "Welcome"
 },
 {
 "type": "textarea",
 "id": "subtitle",
 "label": "Subtitle",
 "default": "Your journey starts here"
 }
 \]
}
{\`%\` endschema \`%\`}

### Key Concepts

**1\. Layout = Global Wrapper**

- One layout per theme (usually `theme.liquid`)
- Contains `<html>`, `<head>`, `<body>` tags
- Includes global sections (navbar, footer)
- Uses `{{ content_for_layout }}` to inject template content

**2\. Template = Page Structure**

- Each page type has a template (home\_page, product, collection, etc.)
- Defines which sections appear on that page type
- Has its own schema listing available sections
- Can set default settings for sections

**3\. Section Schema = Settings Definition**

- Defines what settings/blocks are _available_
- Does NOT store actual values
- Reusable across multiple templates

**4\. Template Data = Actual Values**

- Each template instance stores section settings
- Same section, different values per template

### Data Flow Example

When a user visits the home page:

1. Fluid loads: theme.liquid
 ├── Renders <head\> with global CSS
 ├── Renders {\`%\` section 'navbar' \`%\`} (global)
 │
2. Fluid injects: {{ content\_for\_layout }}
 └── Loads: home\_page/default/index.liquid
 ├── Reads template schema
 ├── Renders {\`%\` section 'hero\_section' \`%\`}
 │ └── Loads sections/hero\_section/index.liquid
 │ ├── Reads section schema
 │ ├── Gets settings from home\_page data
 │ └── Renders with section.settings.heading
 │
 ├── Renders {\`%\` section 'intro\_section' \`%\`}
 └── Renders other sections...
 │
3. Back to theme.liquid
 ├── Renders {\`%\` section 'footer' \`%\`} (global)
 └── Closes </body\></html\>

### Settings Storage Structure

{
 "home\_page": {
 "sections": {
 "hero\_section": {
 "type": "hero\_section",
 "settings": {
 "heading": "Transform Your Life Today",
 "subtitle": "Join thousands of satisfied customers"
 }
 },
 "intro\_section": {
 "type": "intro\_section",
 "settings": {
 "text": "Welcome to our store..."
 }
 }
 }
 },
 "product": {
 "sections": {
 "main\_product": {
 "type": "main\_product",
 "settings": {
 "show\_breadcrumb": true
 }
 },
 "related\_products": {
 "type": "related\_products",
 "settings": {
 "heading": "Related Products"
 }
 }
 }
 }
}

**Important:** Each template (home\_page, product, etc.) stores independent section settings.

### Template Schema vs Section Schema

Understanding the difference between these two schemas is critical:

#### Template Schema

Located in the template file (e.g., `home_page/default/index.liquid`):

{\`%\` schema \`%\`}
{
 "name": "Home Page",
 "sections": {
 "hero\_section": {
 "type": "hero\_section" // References section by type
 },
 "intro\_section": {
 "type": "intro\_section",
 "settings": {
 "heading": "Default Heading" // Default values for THIS template
 }
 }
 }
}
{\`%\` endschema \`%\`}

**Purpose:**

- Lists which sections can appear on this template
- Sets default values for section settings (optional)
- Defines section order
- Each template has ONE schema

#### Section Schema

Located in the section file (e.g., `sections/hero_section/index.liquid`):

{\`%\` schema \`%\`}
{
 "name": "Hero Section",
 "tag": "section",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Hero Heading",
 "default": "Welcome" // Default when section is first added
 }
 \]
}
{\`%\` endschema \`%\`}

**Purpose:**

- Defines what settings/blocks are available
- Provides UI labels and controls
- Sets default values when section is first added
- Reusable across multiple templates

### How Schema Inheritance Works

When a template includes a section, here's what happens:

1. Template calls: {\`%\` section 'hero\_section', id: 'hero\_section' \`%\`}
 │
2. Fluid looks up: sections/hero\_section/index.liquid
 │
3. Reads section schema to know available settings
 │
4. Looks for stored data in template data:
 └── IF found: Use customized values
 └── IF NOT found: Use template schema defaults OR section schema defaults
 │
5. Renders section with section.settings object

#### Example Flow

**Section defines structure:**

{
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "default": "Welcome"
 }
 \]
}

**Template sets instance defaults:**

{
 "sections": {
 "hero\_section": {
 "type": "hero\_section",
 "settings": {
 "heading": "Transform Your Life" // Template-specific default
 }
 }
 }
}

**User customizes in editor:**

{
 "home\_page": {
 "sections": {
 "hero\_section": {
 "settings": {
 "heading": "Welcome to Our Store" // User's custom value
 }
 }
 }
 }
}

**Fallback chain:**

1. User's custom value ✅ "Welcome to Our Store"
2. Template schema default (if no custom value)
3. Section schema default (if no template default)

### Accessing Section Data in Liquid

When a section renders, it automatically receives data through the `section` object:

{\`%\`- comment -\`%\`} Inside any section file {\`%\`- endcomment -\`%\`}

{\`%\`- comment -\`%\`} Section metadata {\`%\`- endcomment -\`%\`}
{{ section.id }} {\`%\`- comment -\`%\`} Unique ID: "hero\_section" {\`%\`- endcomment -\`%\`}
{{ section.type }} {\`%\`- comment -\`%\`} Section type: "hero\_section" {\`%\`- endcomment -\`%\`}

{\`%\`- comment -\`%\`} Section settings {\`%\`- endcomment -\`%\`}
{{ section.settings }} {\`%\`- comment -\`%\`} Object with all setting values {\`%\`- endcomment -\`%\`}
{{ section.settings.heading }}
{{ section.settings.background\_color }}

{\`%\`- comment -\`%\`} Section blocks {\`%\`- endcomment -\`%\`}
{{ section.blocks }} {\`%\`- comment -\`%\`} Array of block objects {\`%\`- endcomment -\`%\`}
{{ section.blocks.size }} {\`%\`- comment -\`%\`} Number of blocks {\`%\`- endcomment -\`%\`}

{\`%\`- comment -\`%\`} Loop through blocks {\`%\`- endcomment -\`%\`}
{\`%\` for block in section.blocks \`%\`}
 {{ block.id }} {\`%\`- comment -\`%\`} Block ID {\`%\`- endcomment -\`%\`}
 {{ block.type }} {\`%\`- comment -\`%\`} Block type {\`%\`- endcomment -\`%\`}
 {{ block.settings }} {\`%\`- comment -\`%\`} Block settings {\`%\`- endcomment -\`%\`}
 {{ block.fluid\_attributes }} {\`%\`- comment -\`%\`} Required for editor {\`%\`- endcomment -\`%\`}
{\`%\` endfor \`%\`}

### Real-World Example: Same Section, Different Templates

Let's see how the same `related_products` section has different settings in different templates:

#### The Section (Reusable)

**File:** `sections/related_products/index.liquid`

<section class="related-products">
 <h2>{{ section.settings.heading }}</h2>
 
 {\`%\` for block in section.blocks \`%\`}
 {\`%\` assign product = block.settings.product \`%\`}
 <div class="product-card">
 <h3>{{ product.title }}</h3>
 <p>{{ product.price | money }}</p>
 </div>
 {\`%\` endfor \`%\`}
</section>

{\`%\` schema \`%\`}
{
 "name": "Related Products",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Section Heading",
 "default": "You May Also Like"
 }
 \],
 "blocks": \[
 {
 "type": "product\_card",
 "name": "Product",
 "settings": \[
 {
 "type": "product",
 "id": "product",
 "label": "Select Product"
 }
 \]
 }
 \]
}
{\`%\` endschema \`%\`}

#### Used in Product Template

**File:** `product/default/index.liquid`

{\`%\` section 'related\_products', id: 'related\_products' \`%\`}

{\`%\` schema \`%\`}
{
 "sections": {
 "related\_products": {
 "type": "related\_products",
 "settings": {
 "heading": "Complete Your Collection"
 }
 }
 }
}
{\`%\` endschema \`%\`}

**Stored data for Product A:**

{
 "product\_A": {
 "sections": {
 "related\_products": {
 "settings": {
 "heading": "Perfect Pairings"
 },
 "blocks": \[
 {
 "type": "product\_card",
 "settings": { "product": 123 }
 }
 \]
 }
 }
 }
}

#### Used in Home Page Template

**File:** `home_page/default/index.liquid`

{\`%\` section 'related\_products', id: 'featured\_products' \`%\`}

{\`%\` schema \`%\`}
{
 "sections": {
 "featured\_products": {
 "type": "related\_products",
 "settings": {
 "heading": "Staff Favorites"
 }
 }
 }
}
{\`%\` endschema \`%\`}

**Stored data for Home Page:**

{
 "home\_page": {
 "sections": {
 "featured\_products": {
 "settings": {
 "heading": "This Month's Bestsellers"
 },
 "blocks": \[
 {
 "type": "product\_card",
 "settings": { "product": 456 }
 }
 \]
 }
 }
 }
}

**Result:** Same section schema, completely different data per template!

### Blocks: Template-Specific Arrays

Blocks work the same way - they're stored per template instance:

#### Section Schema Defines Block Structure

{
 "name": "Features",
 "blocks": \[
 {
 "type": "feature",
 "name": "Feature Item",
 "settings": \[
 {
 "type": "text",
 "id": "title",
 "label": "Title"
 }
 \]
 }
 \]
}

#### Home Page Has Different Blocks

{
 "home\_page": {
 "sections": {
 "features\_section": {
 "type": "features",
 "blocks": \[
 {
 "id": "block\_1",
 "type": "feature",
 "settings": { "title": "Fast Shipping" }
 },
 {
 "id": "block\_2",
 "type": "feature",
 "settings": { "title": "Easy Returns" }
 }
 \]
 }
 }
 }
}

#### About Page Has Different Blocks

{
 "about\_page": {
 "sections": {
 "features\_section": {
 "type": "features",
 "blocks": \[
 {
 "id": "block\_3",
 "type": "feature",
 "settings": { "title": "Founded in 2020" }
 },
 {
 "id": "block\_4",
 "type": "feature",
 "settings": { "title": "Family Owned" }
 }
 \]
 }
 }
 }
}

#### Accessing in Liquid (Same Code, Different Results)

{\`%\` for block in section.blocks \`%\`}
 <div {{ block.fluid\_attributes }}>
 <h3>{{ block.settings.title }}</h3>
 </div>
{\`%\` endfor \`%\`}

**On home\_page:** Shows "Fast Shipping", "Easy Returns" 
**On about\_page:** Shows "Founded in 2020", "Family Owned"

### Global Sections vs Template Sections

#### Global Sections (in theme.liquid)

These appear on EVERY page:

{\`%\`- comment -\`%\`} In layouts/theme.liquid {\`%\`- endcomment -\`%\`}
{\`%\` section 'navbar' \`%\`}
{{ content\_for\_layout }}
{\`%\` section 'footer' \`%\`}

- Rendered outside of templates
- Same content across all pages
- Settings stored at theme level (not per template)
- User customizes once, affects all pages

#### Template Sections (in templates)

These are template-specific:

{\`%\`- comment -\`%\`} In home\_page/default/index.liquid {\`%\`- endcomment -\`%\`}
{\`%\` section 'hero\_section', id: 'hero\_section' \`%\`}
{\`%\` section 'features', id: 'features' \`%\`}

- Rendered inside `{{ content_for_layout }}`
- Different content per template
- Settings stored per template
- User customizes per template

### Real-World Example: Product Template

Let's see how a product template uses sections:

**File:** `app/themes/templates/YourTheme/product/default/index.liquid`

<!DOCTYPE html>
<html>
<body>
 
 {\`%\`- comment -\`%\`} Each section inherits its schema {\`%\`- endcomment -\`%\`}
 {\`%\` section 'product-header' \`%\`}
 {\`%\` section 'product-details' \`%\`}
 {\`%\` section 'also-bought' \`%\`}
 {\`%\` section 'reviews' \`%\`}
 
</body>
</html>

**Section:** `sections/also-bought/index.liquid`

<section class="also-bought">
 <h2>{{ section.settings.heading }}</h2>
 
 {\`%\` for block in section.blocks \`%\`}
 {\`%\` assign product = block.settings.product \`%\`}
 <div class="product-card">
 <h3>{{ product.title }}</h3>
 <p>{{ product.price | money }}</p>
 </div>
 {\`%\` endfor \`%\`}
</section>

{\`%\` schema \`%\`}
{
 "name": "Also Bought",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Section Heading",
 "default": "Customers Also Bought"
 }
 \],
 "blocks": \[
 {
 "type": "product\_card",
 "name": "Product",
 "settings": \[
 {
 "type": "product",
 "id": "product",
 "label": "Select Product"
 }
 \]
 }
 \]
}
{\`%\` endschema \`%\`}

**Stored Data for Product A:**

{
 "product\_template\_A": {
 "sections": {
 "also\_bought\_123": {
 "type": "also-bought",
 "settings": {
 "heading": "People Who Bought This Also Liked"
 },
 "blocks": \[
 {
 "type": "product\_card",
 "settings": {
 "product": 456
 }
 }
 \]
 }
 }
 }
}

**Stored Data for Product B:**

{
 "product\_template\_B": {
 "sections": {
 "also\_bought\_789": {
 "type": "also-bought",
 "settings": {
 "heading": "You May Also Like"
 },
 "blocks": \[
 {
 "type": "product\_card",
 "settings": {
 "product": 789
 }
 }
 \]
 }
 }
 }
}

### Section Variables in Liquid

When a section renders, several variables are automatically available:

{\`%\`- comment -\`%\`} section object {\`%\`- endcomment -\`%\`}
{{ section.id }} {\`%\`- comment -\`%\`} Unique ID: "hero\_section\_abc123" {\`%\`- endcomment -\`%\`}
{{ section.settings }} {\`%\`- comment -\`%\`} Object with all setting values {\`%\`- endcomment -\`%\`}
{{ section.blocks }} {\`%\`- comment -\`%\`} Array of block objects {\`%\`- endcomment -\`%\`}
{{ section.blocks.size }} {\`%\`- comment -\`%\`} Number of blocks {\`%\`- endcomment -\`%\`}

{\`%\`- comment -\`%\`} Individual settings {\`%\`- endcomment -\`%\`}
{{ section.settings.heading }}
{{ section.settings.background\_color }}

{\`%\`- comment -\`%\`} Loop through blocks {\`%\`- endcomment -\`%\`}
{\`%\` for block in section.blocks \`%\`}
 {{ block.id }} {\`%\`- comment -\`%\`} Block ID {\`%\`- endcomment -\`%\`}
 {{ block.type }} {\`%\`- comment -\`%\`} Block type {\`%\`- endcomment -\`%\`}
 {{ block.settings }} {\`%\`- comment -\`%\`} Block settings {\`%\`- endcomment -\`%\`}
 {{ block.fluid\_attributes }} {\`%\`- comment -\`%\`} Required for editor {\`%\`- endcomment -\`%\`}
{\`%\` endfor \`%\`}

### Dynamic Sections vs Static Sections

**Dynamic Sections** (can be added/removed/reordered):

{\`%\`- comment -\`%\`} In template {\`%\`- endcomment -\`%\`}
{\`%\` section 'hero' \`%\`}
{\`%\` section 'features' \`%\`}
{\`%\`- comment -\`%\`} User can add/remove these in editor {\`%\`- endcomment -\`%\`}

**Static Sections** (always present):

{\`%\`- comment -\`%\`} In template {\`%\`- endcomment -\`%\`}
{\`%\` render 'header' \`%\`}
{\`%\`- comment -\`%\`} Always rendered, cannot be removed {\`%\`- endcomment -\`%\`}

### Schema Updates and Backward Compatibility

When you update a section's schema, existing template data continues to work:

#### Adding a New Setting

**Before:**

{
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Heading"
 }
 \]
}

**After:**

{
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Heading"
 },
 {
 "type": "text",
 "id": "subheading",
 "label": "Subheading",
 "default": "New field" // ← Existing instances use this
 }
 \]
}

**Result:**

- Existing templates: Get the default value
- New templates: Also get the default value
- No breaking changes ✅

#### Removing a Setting

**Before:**

{
 "settings": \[
 {
 "type": "text",
 "id": "old\_field",
 "label": "Old Field"
 },
 {
 "type": "text",
 "id": "heading",
 "label": "Heading"
 }
 \]
}

**After:**

{
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Heading"
 }
 \]
}

**Result:**

- Old data in templates is preserved but ignored
- Section continues to work normally
- No breaking changes ✅

#### Changing a Setting ID (Breaking Change!)

**Before:**

{ "id": "title" }

**After:**

{ "id": "heading" }

**Result:**

- Creates a NEW setting with default value
- Old `title` data is preserved but NOT accessible
- Existing templates lose their customizations ❌
- **This is a breaking change!**

**Better approach:**

1. Add new setting with new ID
2. Keep old setting temporarily
3. Migration script to copy old → new
4. Remove old setting after migration

### Best Practices: Layout-Template-Section Integration

✅ **DO:**

**Architecture:**

- Keep global sections (navbar, footer) in `theme.liquid`
- Use templates to define page-specific section arrangements
- Make sections reusable across multiple templates
- Use descriptive IDs when calling sections: `{`%`section 'hero', id: 'home_hero'`%`}`

**Schema Design:**

- Provide sensible defaults in section schemas
- Set template-specific defaults in template schemas
- Document which templates use which sections
- Use consistent naming conventions

**Data Handling:**

- Use `| default` filter for optional settings: `{{ section.settings.heading | default: "Default" }}`
- Check for blank values before rendering: `{`%`if section.settings.text != blank`%`}`
- Always include `{{ block.fluid_attributes }}` on block elements
- Handle empty blocks gracefully: `{`%`if section.blocks.size > 0`%`}`

**Performance:**

- Limit number of sections per template (10-15 max recommended)
- Use lazy loading for images in sections
- Minimize database lookups in section loops

❌ **DON'T:**

**Architecture:**

- Don't put page-specific content in `theme.liquid`
- Don't hardcode values that should be settings
- Don't duplicate section code - make sections reusable
- Don't forget that same section = different data per template

**Schema Changes:**

- Don't change setting IDs without migration plan
- Don't remove settings without considering backward compatibility
- Don't assume settings always have values
- Don't forget to provide defaults for new settings

**Data Access:**

- Don't use global variables when section settings exist
- Don't access template data directly - use `section.settings`
- Don't forget to validate resource selectors (product/collection might not exist)
- Don't skip `fluid_attributes` on blocks (breaks editor)

### Debugging: Template and Section Data

**Check the rendering flow:**

{\`%\`- comment -\`%\`} In theme.liquid {\`%\`- endcomment -\`%\`}
<p>Layout: theme.liquid loaded</p>
{{ content\_for\_layout }}

{\`%\`- comment -\`%\`} In home\_page/default/index.liquid {\`%\`- endcomment -\`%\`}
<p>Template: home\_page loaded</p>
{\`%\` section 'hero\_section', id: 'hero\_section' \`%\`}

{\`%\`- comment -\`%\`} In sections/hero\_section/index.liquid {\`%\`- endcomment -\`%\`}
<p>Section: hero\_section loaded</p>

**Output section data:**

{\`%\`- comment -\`%\`} Debug: Output all section settings {\`%\`- endcomment -\`%\`}
<pre>
 Section ID: {{ section.id }}
 Section Type: {{ section.type }}
 Settings: {{ section.settings | json }}
 Blocks Count: {{ section.blocks.size }}
 Blocks: {{ section.blocks | json }}
</pre>

**Check if setting has value:**

{\`%\` if section.settings.heading != blank \`%\`}
 <h1>{{ section.settings.heading }}</h1>
{\`%\` else \`%\`}
 <p>DEBUG: No heading set (using default or blank)</p>
{\`%\` endif \`%\`}

**Validate resource selectors:**

{\`%\` assign product = block.settings.product \`%\`}

{\`%\` if product \`%\`}
 <p>Product found: {{ product.title }}</p>
{\`%\` else \`%\`}
 <p>DEBUG: No product selected in this block</p>
{\`%\` endif \`%\`}

**Count and inspect blocks:**

<p>DEBUG: This section has {{ section.blocks.size }} blocks</p>

{\`%\` if section.blocks.size > 0 \`%\`}
 {\`%\` for block in section.blocks \`%\`}
 <p>Block {{ forloop.index }}: Type = {{ block.type }}, ID = {{ block.id }}</p>
 {\`%\` endfor \`%\`}
{\`%\` else \`%\`}
 <p>DEBUG: No blocks added yet - add some in the theme editor!</p>
{\`%\` endif \`%\`}

**Check template context:**

{\`%\`- comment -\`%\`} Available in templates, not in sections {\`%\`- endcomment -\`%\`}
<p>Template: {{ template.name }}</p>
<p>Template suffix: {{ template.suffix }}</p>

---

## Resource Selectors: The Complete Guide

Resource selectors are one of the most powerful features in Fluid's schema system. They allow users to pick specific items from your store (products, collections, categories, posts) and display them in sections. This section covers everything you need to know with real production examples.

### When to Use Resource Selectors vs Global Loops

**Use Resource Selectors when:**

- You want merchants to hand-pick specific items to feature
- Order matters (e.g., "Staff Picks", "Best Sellers")
- You need manual curation (e.g., seasonal promotions)
- You want block-level control (each item can have unique overrides)

**Use Global Loops when:**

- You want to show all items automatically (e.g., blog listing pages)
- Content should update automatically when new items are added
- You need pagination for large datasets
- You're building main template pages (e.g., `blog.liquid`, `shop.liquid`)

---

## Collection Resource Selector

The `collection` type lets users select a single collection.

This example is from a production theme showing how to build a dynamic collection showcase with auto-scroll carousel.

#### Complete Schema Definition

{
 "name": "Shop by Collections",
 "tag": "section",
 "class": "shop-by-collections-section",
 "settings": \[
 {
 "type": "header",
 "content": "Section Header"
 },
 {
 "type": "text",
 "id": "heading",
 "label": "Heading Text",
 "default": "Ready to Find Your Perfect Routine?"
 },
 {
 "type": "text",
 "id": "shop\_page\_url",
 "label": "Shop Page URL",
 "default": "/shop",
 "info": "URL of the shop page where collection filters will be applied"
 },
 {
 "type": "range",
 "id": "card\_height",
 "label": "Card Height (px)",
 "min": 300,
 "max": 600,
 "step": 20,
 "default": 400
 }
 \],
 "blocks": \[
 {
 "type": "collection\_item",
 "name": "Collection Item",
 "settings": \[
 {
 "type": "collection",
 "id": "collection",
 "label": "Collection",
 "info": "Select a collection to display. The collection's title, image, and products will be used automatically."
 },
 {
 "type": "text",
 "id": "collection\_title",
 "label": "Collection Title (Manual Override)",
 "info": "Only used if no collection is selected above."
 },
 {
 "type": "image\_picker",
 "id": "background\_image",
 "label": "Background Image (Override)",
 "info": "Override the collection's default image"
 },
 {
 "type": "url",
 "id": "collection\_url",
 "label": "Collection URL (Manual Override)"
 }
 \]
 }
 \],
 "presets": \[
 {
 "name": "Shop by Collections",
 "blocks": \[
 {
 "type": "collection\_item"
 }
 \]
 }
 \]
}

#### Complete Liquid Implementation

<section class="shop-by-collections {{ section.settings.background\_color }}">
 <div class="container">
 
 <!-- Section Header -->
 <div class="text-center mb-2xl">
 {\`%\` if section.settings.heading != blank \`%\`}
 <h2 class="text-3xl lg:text-5xl font-bold">
 {{ section.settings.heading }}
 </h2>
 {\`%\` endif \`%\`}
 </div>
 
 <!-- Collection Carousel -->
 {\`%\` if section.blocks.size > 0 \`%\`}
 <div class="collection-carousel">
 
 {\`%\`- comment -\`%\`} Iterate through blocks {\`%\`- endcomment -\`%\`}
 {\`%\` for block in section.blocks \`%\`}
 {\`%\` if block.type == 'collection\_item' \`%\`}
 
 {\`%\`- comment -\`%\`} Step 1: Initialize variables {\`%\`- endcomment -\`%\`}
 {\`%\`- assign current\_collection = blank -\`%\`}
 {\`%\`- assign collection\_url = block.settings.collection\_url | default: '#' -\`%\`}
 {\`%\`- assign collection\_title = 'Collection' -\`%\`}
 {\`%\`- assign background\_image = block.settings.background\_image -\`%\`}
 
 {\`%\`- comment -\`%\`} Step 2: If collection is selected, find it from global collections {\`%\`- endcomment -\`%\`}
 {\`%\`- if block.settings.collection != blank -\`%\`}
 {\`%\`- assign collection\_id = block.settings.collection -\`%\`}
 
 {\`%\`- comment -\`%\`} Find collection object from global collections array {\`%\`- endcomment -\`%\`}
 {\`%\`- for c in collections -\`%\`}
 {\`%\`- if c.id == collection\_id -\`%\`}
 {\`%\`- assign current\_collection = c -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 
 {\`%\`- comment -\`%\`} Step 3: Use collection data if found {\`%\`- endcomment -\`%\`}
 {\`%\`- if current\_collection != blank -\`%\`}
 {\`%\`- comment -\`%\`} Build filtered shop URL {\`%\`- endcomment -\`%\`}
 {\`%\`- assign shop\_url = section.settings.shop\_page\_url | default: '/shop' -\`%\`}
 {\`%\`- assign collection\_url = shop\_url | append: '?filterrific\[by\_collection\]\[\]=' | append: current\_collection.id -\`%\`}
 
 {\`%\`- comment -\`%\`} Use collection title {\`%\`- endcomment -\`%\`}
 {\`%\`- assign collection\_title = current\_collection.title | default: 'Collection' -\`%\`}
 
 {\`%\`- comment -\`%\`} Use collection image if no manual override {\`%\`- endcomment -\`%\`}
 {\`%\`- if background\_image == blank -\`%\`}
 {\`%\`- if current\_collection.image != blank -\`%\`}
 {\`%\`- assign background\_image = current\_collection.image -\`%\`}
 {\`%\`- elsif current\_collection.products.first != blank -\`%\`}
 {\`%\`- assign background\_image = current\_collection.products.first.images.first -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\`- comment -\`%\`} Step 4: Fallback to manual overrides if no collection {\`%\`- endcomment -\`%\`}
 {\`%\`- if current\_collection == blank and block.settings.collection\_title != blank -\`%\`}
 {\`%\`- assign collection\_title = block.settings.collection\_title -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\`- comment -\`%\`} Step 5: Render the card {\`%\`- endcomment -\`%\`}
 <div class="collection-card-wrapper">
 <a href="{{ collection\_url }}" 
 class="collection-card"
 style="height: {{ section.settings.card\_height | default: 400 }}px;"
 {{ block.fluid\_attributes }}
 {\`%\` if current\_collection != blank \`%\`}data-collection-id="{{ current\_collection.id }}"{\`%\` endif \`%\`}>
 
 <!-- Background Image -->
 <div class="collection-bg">
 {\`%\` if background\_image \`%\`}
 <img src="{{ background\_image | image\_url: width: 800 }}" 
 alt="{{ collection\_title }}"
 class="w-full h-full object-cover"
 loading="lazy">
 {\`%\` else \`%\`}
 <img src="{{ 'placeholder-image.png' | asset\_url }}" 
 alt="Placeholder"
 class="w-full h-full object-cover">
 {\`%\` endif \`%\`}
 </div>
 
 <!-- Content -->
 <div class="collection-content">
 <h3 class="collection-title">{{ collection\_title }}</h3>
 </div>
 
 </a>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endfor \`%\`}
 
 </div>
 {\`%\` endif \`%\`}
 
 </div>
</section>

#### Key Techniques Explained

**1\. Finding Collection from ID**

When a user selects a collection in the editor, Fluid stores the collection ID. You need to find the full collection object from the global `collections` array:

{\`%\`- assign collection\_id = block.settings.collection -\`%\`}

{\`%\`- for c in collections -\`%\`}
 {\`%\`- if c.id == collection\_id -\`%\`}
 {\`%\`- assign current\_collection = c -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
{\`%\`- endfor -\`%\`}

**2\. Building Filtered Shop URLs**

To link to a shop page filtered by collection, use the `filterrific` parameter:

{\`%\`- assign shop\_url = '/shop' -\`%\`}
{\`%\`- assign collection\_url = shop\_url | append: '?filterrific\[by\_collection\]\[\]=' | append: current\_collection.id -\`%\`}

This creates URLs like: `/shop?filterrific[by_collection][]=123`

**3\. Fallback Chain for Images**

Provide multiple fallback options for images:

{\`%\`- if background\_image == blank -\`%\`}
 {\`%\`- comment -\`%\`} Try collection image first {\`%\`- endcomment -\`%\`}
 {\`%\`- if current\_collection.image != blank -\`%\`}
 {\`%\`- assign background\_image = current\_collection.image -\`%\`}
 {\`%\`- comment -\`%\`} Fall back to first product image {\`%\`- endcomment -\`%\`}
 {\`%\`- elsif current\_collection.products.first != blank -\`%\`}
 {\`%\`- assign background\_image = current\_collection.products.first.images.first -\`%\`}
 {\`%\`- endif -\`%\`}
{\`%\`- endif -\`%\`}

**4\. Manual Overrides**

Allow merchants to override collection data for marketing purposes:

{\`%\`- comment -\`%\`} Use manual title if no collection or as override {\`%\`- endcomment -\`%\`}
{\`%\`- if current\_collection == blank and block.settings.collection\_title != blank -\`%\`}
 {\`%\`- assign collection\_title = block.settings.collection\_title -\`%\`}
{\`%\`- endif -\`%\`}

### Best Practices: Collection Selector

✅ **DO:**

- Always loop through `collections` to find the full object
- Provide manual override fields for edge cases
- Use `{{ block.fluid_attributes }}` for editor highlighting
- Include fallback images (collection image → first product → placeholder)
- Build filter URLs for shop integration
- Add `data-collection-id` attributes for JavaScript targeting

❌ **DON'T:**

- Assume the collection object is directly available
- Forget to handle `blank` collections
- Hardcode shop URLs (use settings)
- Skip the `{`%`break`%`}` in collection lookup loops
- Forget to escape/validate user input in URLs

---

## Product Resource Selector (Single)

The `product` type lets users select a single product. Perfect for blocks where each item represents one product with customization options.

This shows a production pattern for product recommendations with manual overrides, star ratings, and add-to-cart functionality.

#### Block Schema Definition

{
 "type": "product\_card",
 "name": "Product Card",
 "limit": 6,
 "settings": \[
 {
 "type": "header",
 "content": "Product Selection"
 },
 {
 "type": "product",
 "id": "product",
 "label": "Product"
 },
 {
 "type": "header",
 "content": "Manual Override (Optional)"
 },
 {
 "type": "text",
 "id": "title",
 "label": "Title Override",
 "info": "Leave blank to use product title"
 },
 {
 "type": "textarea",
 "id": "description",
 "label": "Description Override",
 "info": "Leave blank to use product description"
 },
 {
 "type": "image\_picker",
 "id": "product\_image",
 "label": "Image Override"
 },
 {
 "type": "url",
 "id": "product\_url",
 "label": "URL Override"
 },
 {
 "type": "header",
 "content": "Rating & Reviews"
 },
 {
 "type": "range",
 "id": "star\_rating",
 "label": "Star Rating",
 "min": 1.0,
 "max": 5.0,
 "step": 0.1,
 "default": 4.8
 },
 {
 "type": "number",
 "id": "review\_count",
 "label": "Review Count",
 "default": 126
 }
 \]
}

#### Liquid Implementation

<div class="products-grid">
 {\`%\` for block in section.blocks \`%\`}
 {\`%\` if block.type == 'product\_card' \`%\`}
 {\`%\`- comment -\`%\`} Get product and variant {\`%\`- endcomment -\`%\`}
 {\`%\` assign product = block.settings.product \`%\`}
 {\`%\` assign variant = product.selected\_or\_first\_available\_variant \`%\`}
 {\`%\` assign variant\_id = variant.id | default: product.variants.first.id \`%\`}
 
 <div class="product-card" {{ block.fluid\_attributes }}>
 
 <!-- Product Image -->
 <div class="product-image-wrapper">
 <a href="{{ product.url | default: block.settings.product\_url | default: '#' }}">
 {\`%\` if product.image\_url != blank \`%\`}
 <img 
 src="{{ product.image\_url | image\_url: width: 600 }}" 
 alt="{{ product.title | default: block.settings.title }}"
 loading="lazy">
 {\`%\` elsif block.settings.product\_image != blank \`%\`}
 <img 
 src="{{ block.settings.product\_image | image\_url: width: 600 }}" 
 alt="{{ block.settings.title }}"
 loading="lazy">
 {\`%\` else \`%\`}
 <div class="placeholder-image">
 <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor">
 <rect x="3" y="3" width="18" height="18" rx="2"/>
 <circle cx="8.5" cy="8.5" r="1.5"/>
 <polyline points="21,15 16,10 5,21"/>
 </svg>
 </div>
 {\`%\` endif \`%\`}
 </a>
 </div>

 <!-- Product Info -->
 <div class="product-info">
 
 <!-- Title with fallback -->
 <h3 class="product-title">
 <a href="{{ product.url | default: block.settings.product\_url | default: '#' }}">
 {{ product.title | default: block.settings.title | default: 'Product Name' }}
 </a>
 </h3>

 <!-- Description with fallback -->
 {\`%\` if section.settings.show\_description \`%\`}
 <p class="product-description">
 {\`%\` if block.settings.description != blank \`%\`}
 {{ block.settings.description }}
 {\`%\` elsif product.short\_description != blank \`%\`}
 {{ product.short\_description | truncate: 60 }}
 {\`%\` elsif product.description != blank \`%\`}
 {{ product.description | strip\_html | truncate: 60 }}
 {\`%\` endif \`%\`}
 </p>
 {\`%\` endif \`%\`}

 <!-- Star Rating -->
 {\`%\` if section.settings.show\_rating \`%\`}
 {\`%\` assign star\_rating = block.settings.star\_rating | default: 5.0 \`%\`}
 {\`%\` assign review\_count = block.settings.review\_count | default: 0 \`%\`}
 
 <div class="product-rating">
 <div class="star-container">
 {\`%\`- comment -\`%\`} Calculate star display {\`%\`- endcomment -\`%\`}
 {\`%\` assign full\_stars = star\_rating | floor \`%\`}
 {\`%\` assign decimal\_part = star\_rating | modulo: 1 \`%\`}
 {\`%\` assign has\_half = false \`%\`}
 
 {\`%\` if decimal\_part >= 0.25 and decimal\_part < 0.75 \`%\`}
 {\`%\` assign has\_half = true \`%\`}
 {\`%\` elsif decimal\_part >= 0.75 \`%\`}
 {\`%\` assign full\_stars = full\_stars | plus: 1 \`%\`}
 {\`%\` endif \`%\`}
 
 {\`%\` assign empty\_stars = 5 | minus: full\_stars \`%\`}
 {\`%\` if has\_half \`%\`}
 {\`%\` assign empty\_stars = empty\_stars | minus: 1 \`%\`}
 {\`%\` endif \`%\`}
 
 {\`%\`- comment -\`%\`} Render stars {\`%\`- endcomment -\`%\`}
 {\`%\`- for i in (1..full\_stars) -\`%\`}
 <span class="star star-full">★</span>
 {\`%\`- endfor -\`%\`}
 
 {\`%\`- if has\_half -\`%\`}
 <span class="star star-half">
 <span class="star-bg">★</span>
 <span class="star-fill">★</span>
 </span>
 {\`%\`- endif -\`%\`}
 
 {\`%\`- if empty\_stars > 0 -\`%\`}
 {\`%\`- for i in (1..empty\_stars) -\`%\`}
 <span class="star star-empty">★</span>
 {\`%\`- endfor -\`%\`}
 {\`%\`- endif -\`%\`}
 </div>
 
 <span class="rating-text">
 ({{ star\_rating }} stars) • {{ review\_count }} reviews
 </span>
 </div>
 {\`%\` endif \`%\`}

 <!-- Add to Cart Button -->
 {\`%\` if section.settings.show\_add\_to\_cart \`%\`}
 <button 
 type="button"
 class="add-to-cart-btn"
 data-fluid-add-to-cart="{{ variant\_id }}"
 data-fluid-quantity="1"
 {\`%\` if variant\_id == blank \`%\`}disabled{\`%\` endif \`%\`}>
 {{ section.settings.button\_text | default: 'ADD TO CART' }}
 </button>
 {\`%\` endif \`%\`}
 
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endfor \`%\`}
</div>

#### Key Techniques Explained

**1\. Product Variant Handling**

Always get the correct variant ID for add-to-cart functionality:

{\`%\` assign product = block.settings.product \`%\`}
{\`%\` assign variant = product.selected\_or\_first\_available\_variant \`%\`}
{\`%\` assign variant\_id = variant.id | default: product.variants.first.id \`%\`}

**2\. Manual Override Pattern**

Use the `default` filter to prioritize block settings over product data:

{{ product.title | default: block.settings.title | default: 'Product Name' }}

This creates a fallback chain: block override → product data → hardcoded default

**3\. Star Rating Calculation**

Calculate full, half, and empty stars with Liquid math:

{\`%\` assign full\_stars = star\_rating | floor \`%\`}
{\`%\` assign decimal\_part = star\_rating | modulo: 1 \`%\`}
{\`%\` assign has\_half = false \`%\`}

{\`%\` if decimal\_part >= 0.25 and decimal\_part < 0.75 \`%\`}
 {\`%\` assign has\_half = true \`%\`}
{\`%\` elsif decimal\_part >= 0.75 \`%\`}
 {\`%\` assign full\_stars = full\_stars | plus: 1 \`%\`}
{\`%\` endif \`%\`}

**4\. Direct Add-to-Cart Integration**

Use Fluid's cart attributes for direct add-to-cart:

<button 
 type="button"
 data-fluid-add-to-cart="{{ variant\_id }}"
 data-fluid-quantity="1"
 {\`%\` if variant\_id == blank \`%\`}disabled{\`%\` endif \`%\`}>
 ADD TO CART
</button>

### Best Practices: Product Selector

✅ **DO:**

- Always get the variant ID, not just the product
- Provide manual override fields for marketing flexibility
- Use multi-level fallbacks for images and descriptions
- Calculate star ratings server-side with Liquid
- Disable buttons when variant is unavailable
- Truncate descriptions to prevent layout breaks

❌ **DON'T:**

- Assume products always have variants
- Forget to handle missing images
- Hardcode button text (use settings)
- Skip accessibility attributes on images
- Use client-side JavaScript for simple star calculations
- Display raw HTML in descriptions (use `| strip_html`)

---

## Variant Resource Selector (Single)

The `variant` type lets users select a single product **variant** — one exact, purchasable unit (a specific size, color, or option combination) rather than a whole product. Reach for it when a block must point at a precise variant: a "Featured variant" spotlight, a curated gift pick, or any add-to-cart tile where the merchant — not the theme — decides exactly which variant sells.

### Why a variant selector (vs the product selector)

With the `product` selector you receive a product and must _derive_ a variant before you can add to cart:

{\`%\` assign product = block.settings.product \`%\`}
{\`%\` assign variant = product.selected\_or\_first\_available\_variant \`%\`}
{\`%\` assign variant\_id = variant.id | default: product.variants.first.id \`%\`}

The `variant` selector removes that step — and the ambiguity that comes with it. The stored value is the variant id itself, so `{{ variant.id }}` is ready for add-to-cart with no guessing about which variant the merchant meant.

> \[!NOTE\] The selector stores **only** the variant id. A variant already knows its product, so there is never a separate product id to keep in sync. The lookup is scoped to the store's own products, and an unset or invalid selection resolves to an empty object — always guard with `{`%`if variant != blank`%`}`.

#### Block Schema Definition

{
 "type": "variant\_card",
 "name": "Featured Variant",
 "limit": 4,
 "settings": \[
 {
 "type": "variant",
 "id": "variant",
 "label": "Variant"
 },
 {
 "type": "text",
 "id": "badge",
 "label": "Badge Text",
 "info": "Optional label shown over the image"
 }
 \]
}

#### Liquid Implementation

<div class="variant-grid">
 {\`%\` for block in section.blocks \`%\`}
 {\`%\` if block.type == 'variant\_card' \`%\`}
 {\`%\` assign variant = block.settings.variant \`%\`}

 {\`%\`- comment -\`%\`} Unset / invalid selections resolve to {} {\`%\`- endcomment -\`%\`}
 {\`%\` if variant != blank and variant.id != blank \`%\`}
 <div class="variant-card" {{ block.fluid\_attributes }}>

 <div class="variant-image-wrapper">
 {\`%\` if block.settings.badge != blank \`%\`}
 <span class="variant-badge">{{ block.settings.badge }}</span>
 {\`%\` endif \`%\`}
 {\`%\` if variant.image\_url != blank \`%\`}
 <img
 src="{{ variant.image\_url }}"
 alt="{{ variant.display\_name | default: variant.title }}"
 loading="lazy">
 {\`%\` endif \`%\`}
 </div>

 <div class="variant-info">
 <h3 class="variant-title">{{ variant.display\_name | default: variant.title }}</h3>
 {\`%\` if variant.sku != blank \`%\`}
 <p class="variant-sku">SKU: {{ variant.sku }}</p>
 {\`%\` endif \`%\`}

 {\`%\`- comment -\`%\`} Price lives under variant\_countries, one entry per country {\`%\`- endcomment -\`%\`}
 {\`%\` for country in variant.variant\_countries \`%\`}
 {\`%\` if country.country\_iso == localization.country.iso\_code \`%\`}
 <p class="variant-price">{{ country.display\_price }}</p>
 {\`%\` endif \`%\`}
 {\`%\` endfor \`%\`}

 <button
 type="button"
 class="add-to-cart-btn"
 data-fluid-add-to-cart="{{ variant.id }}"
 data-fluid-quantity="1"
 {\`%\` unless variant.available \`%\`}disabled{\`%\` endunless \`%\`}>
 {\`%\` if variant.available \`%\`}ADD TO CART{\`%\` else \`%\`}SOLD OUT{\`%\` endif \`%\`}
 </button>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` endfor \`%\`}
</div>

#### Key Techniques Explained

**1\. The variant id is ready to use**

No `selected_or_first_available_variant` step — bind the cart attribute straight to the resolved id:

data-fluid-add-to-cart="{{ variant.id }}"

**2\. Always guard for an empty selection**

An unset or removed variant resolves to an empty object, so check before rendering:

{\`%\` assign variant = block.settings.variant \`%\`}
{\`%\` if variant != blank and variant.id != blank \`%\`}
 ...
{\`%\` endif \`%\`}

**3\. Price comes from `variant_countries`, not a flat field**

A variant carries per-country pricing rather than a single `price`. Read the entry that matches the buyer's country:

{\`%\` for country in variant.variant\_countries \`%\`}
 {\`%\` if country.country\_iso == localization.country.iso\_code \`%\`}
 {{ country.display\_price }}
 {\`%\` endif \`%\`}
{\`%\` endfor \`%\`}

**4\. Availability**

`variant.available` reports whether the variant is buyable in the current country — use it to enable or disable the cart button.

### Best Practices: Variant Selector

✅ **DO:**

- Use it whenever the merchant must choose an exact variant to sell
- Bind `data-fluid-add-to-cart` directly to `{{ variant.id }}`
- Guard every block with `{`%`if variant != blank`%`}`
- Read price from the matching `variant_countries` entry
- Disable the button when `variant.available` is false

❌ **DON'T:**

- Reach for `variant.price` — there is no flat price field (see `variant_countries`)
- Store a separate product id alongside it — the variant already knows its product
- Assume a selection exists — unset settings resolve to `{}`

---

## Multiple Resource Selectors: Lists

Multiple resource selectors (`product_list`, `collection_list`, `category_list`, `posts_list`) allow users to select several items at once. These are perfect for "Featured Products" carousels, "Shop by Collection" sections, or curated content grids.

### Key Difference: Single vs Multiple

| Selector Type | Use Case | Access Pattern |
| --- | --- | --- |
| **Single** (`product`, `collection`, `category`, `posts`) | Select ONE item, often in blocks | Need to find from global array |
| **Multiple** (`product_list`, `collection_list`, etc.) | Select MULTIPLE items at once | Direct iteration |

---

## Product List Selector

The `product_list` type lets users select multiple products in one setting. Perfect for "Featured Products" or "Best Sellers" sections.

### Schema Definition

{
 "name": "Featured Products Carousel",
 "tag": "section",
 "settings": \[
 {
 "type": "header",
 "content": "Products Selection"
 },
 {
 "type": "text",
 "id": "heading",
 "label": "Section Heading",
 "default": "Featured Products"
 },
 {
 "type": "product\_list",
 "id": "featured\_products",
 "label": "Select Products",
 "limit": 12,
 "info": "Choose up to 12 products to feature. Drag to reorder."
 },
 {
 "type": "range",
 "id": "products\_per\_row",
 "label": "Products Per Row",
 "min": 2,
 "max": 4,
 "step": 1,
 "default": 3
 },
 {
 "type": "checkbox",
 "id": "show\_add\_to\_cart",
 "label": "Show Add to Cart Button",
 "default": true
 }
 \],
 "presets": \[
 {
 "name": "Featured Products",
 "settings": {
 "heading": "Staff Picks"
 }
 }
 \]
}

### Liquid Implementation

<section class="featured-products {{ section.settings.background\_color }}">
 <div class="container">
 
 <!-- Section Heading -->
 {\`%\` if section.settings.heading != blank \`%\`}
 <h2 class="section-heading">{{ section.settings.heading }}</h2>
 {\`%\` endif \`%\`}
 
 {\`%\`- comment -\`%\`} Check if products were selected {\`%\`- endcomment -\`%\`}
 {\`%\` if section.settings.featured\_products.size > 0 \`%\`}
 
 <div class="products-grid" style="--columns: {{ section.settings.products\_per\_row }};">
 
 {\`%\`- comment -\`%\`} Direct iteration - no need to find from global array {\`%\`- endcomment -\`%\`}
 {\`%\` for product in section.settings.featured\_products \`%\`}
 
 <div class="product-card">
 
 <!-- Product Image -->
 <a href="{{ product.url }}" class="product-image-link">
 {\`%\` if product.image\_url != blank \`%\`}
 <img 
 src="{{ product.image\_url | image\_url: width: 600 }}" 
 alt="{{ product.title }}"
 loading="lazy">
 {\`%\` else \`%\`}
 <div class="placeholder-image">No image</div>
 {\`%\` endif \`%\`}
 </a>
 
 <!-- Product Info -->
 <div class="product-info">
 <h3 class="product-title">
 <a href="{{ product.url }}">{{ product.title }}</a>
 </h3>
 
 <p class="product-price">{{ product.price | money }}</p>
 
 <!-- Add to Cart -->
 {\`%\` if section.settings.show\_add\_to\_cart \`%\`}
 {\`%\` assign variant = product.selected\_or\_first\_available\_variant \`%\`}
 {\`%\` assign variant\_id = variant.id | default: product.variants.first.id \`%\`}
 
 <button 
 type="button"
 class="btn-add-to-cart"
 data-fluid-add-to-cart="{{ variant\_id }}"
 data-fluid-quantity="1"
 {\`%\` if variant\_id == blank \`%\`}disabled{\`%\` endif \`%\`}>
 Add to Cart
 </button>
 {\`%\` endif \`%\`}
 </div>
 
 </div>
 
 {\`%\` endfor \`%\`}
 </div>
 
 {\`%\` else \`%\`}
 
 <!-- Empty State -->
 <div class="empty-state">
 <p>No products selected. Please select products in the theme editor.</p>
 </div>
 
 {\`%\` endif \`%\`}
 
 </div>
</section>

### Key Points: product\_list

1. **Direct Access** - Products are already full objects, no need to find them
2. **Check Size** - Use `section.settings.featured_products.size` to check if any selected
3. **Maintain Order** - Products appear in the order the merchant arranged them
4. **Set Limits** - Use `"limit"` to prevent performance issues (recommended: 8-12)

---

## Collection List Selector

The `collection_list` (or `collections_list`) type lets users select multiple collections.

### Schema Definition

{
 "name": "Shop by Collections",
 "tag": "section",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Heading",
 "default": "Shop by Collection"
 },
 {
 "type": "collections\_list",
 "id": "featured\_collections",
 "label": "Select Collections",
 "limit": 6,
 "info": "Choose up to 6 collections to display"
 },
 {
 "type": "text",
 "id": "shop\_page\_url",
 "label": "Shop Page URL",
 "default": "/shop"
 }
 \]
}

### Liquid Implementation

<section class="shop-by-collections">
 <div class="container">
 
 <h2>{{ section.settings.heading }}</h2>
 
 {\`%\` if section.settings.featured\_collections.size > 0 \`%\`}
 
 <div class="collections-grid">
 
 {\`%\`- comment -\`%\`} Direct iteration over selected collections {\`%\`- endcomment -\`%\`}
 {\`%\` for collection in section.settings.featured\_collections \`%\`}
 
 <div class="collection-card">
 
 {\`%\`- comment -\`%\`} Build filtered shop URL {\`%\`- endcomment -\`%\`}
 {\`%\` assign shop\_url = section.settings.shop\_page\_url | default: '/shop' \`%\`}
 {\`%\` assign collection\_url = shop\_url | append: '?filterrific\[by\_collection\]\[\]=' | append: collection.id \`%\`}
 
 <a href="{{ collection\_url }}" class="collection-link">
 
 <!-- Collection Image -->
 {\`%\` if collection.image\_url != blank \`%\`}
 <img 
 src="{{ collection.image\_url | image\_url: width: 800 }}" 
 alt="{{ collection.title }}"
 loading="lazy">
 {\`%\` elsif collection.products.first.image\_url != blank \`%\`}
 {\`%\`- comment -\`%\`} Fallback to first product image {\`%\`- endcomment -\`%\`}
 <img 
 src="{{ collection.products.first.image\_url | image\_url: width: 800 }}" 
 alt="{{ collection.title }}"
 loading="lazy">
 {\`%\` else \`%\`}
 <div class="placeholder-image">{{ collection.title }}</div>
 {\`%\` endif \`%\`}
 
 <!-- Collection Info -->
 <div class="collection-info">
 <h3 class="collection-title">{{ collection.title }}</h3>
 {\`%\` if collection.products\_count \`%\`}
 <p class="products-count">{{ collection.products\_count }} products</p>
 {\`%\` endif \`%\`}
 </div>
 
 </a>
 </div>
 
 {\`%\` endfor \`%\`}
 </div>
 
 {\`%\` else \`%\`}
 <p class="empty-state">No collections selected.</p>
 {\`%\` endif \`%\`}
 
 </div>
</section>

### Key Points: collection\_list

1. **Direct Access** - Collections are full objects with all fields
2. **Build Filter URLs** - Use `?filterrific[by_collection][]=` for shop page links
3. **Image Fallback** - Collection image → first product image → placeholder
4. **Products Count** - Access `collection.products_count` for count display

---

## Category List Selector

The `category_list` (or `categories_list`) type lets users select multiple categories.

### Schema Definition

{
 "name": "Shop by Categories",
 "tag": "section",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Heading",
 "default": "Browse Categories"
 },
 {
 "type": "categories\_list",
 "id": "featured\_categories",
 "label": "Select Categories",
 "limit": 8,
 "info": "Choose up to 8 categories to feature"
 },
 {
 "type": "select",
 "id": "layout",
 "label": "Layout Style",
 "options": \[
 { "value": "grid", "label": "Grid" },
 { "value": "carousel", "label": "Carousel" }
 \],
 "default": "grid"
 }
 \]
}

### Liquid Implementation

<section class="shop-by-categories">
 <div class="container">
 
 <h2>{{ section.settings.heading }}</h2>
 
 {\`%\` if section.settings.featured\_categories.size > 0 \`%\`}
 
 <div class="categories-{{ section.settings.layout }}">
 
 {\`%\`- comment -\`%\`} Direct iteration over selected categories {\`%\`- endcomment -\`%\`}
 {\`%\` for category in section.settings.featured\_categories \`%\`}
 
 <div class="category-card">
 
 {\`%\`- comment -\`%\`} Build category shop URL {\`%\`- endcomment -\`%\`}
 {\`%\` assign category\_url = '/shop?filterrific\[with\_category\_id\]\[\]=' | append: category.id \`%\`}
 
 <a href="{{ category\_url }}" class="category-link">
 
 <!-- Category Image -->
 {\`%\` if category.image\_url != blank \`%\`}
 <div class="category-image">
 <img 
 src="{{ category.image\_url | image\_url: width: 600 }}" 
 alt="{{ category.title }}"
 loading="lazy">
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Category Info -->
 <div class="category-info">
 <h3 class="category-title">{{ category.title }}</h3>
 
 {\`%\` if category.description != blank \`%\`}
 <p class="category-description">
 {{ category.description | strip\_html | truncate: 80 }}
 </p>
 {\`%\` endif \`%\`}
 </div>
 
 </a>
 </div>
 
 {\`%\` endfor \`%\`}
 </div>
 
 {\`%\` else \`%\`}
 <p class="empty-state">No categories selected.</p>
 {\`%\` endif \`%\`}
 
 </div>
</section>

### Key Points: category\_list

1. **Direct Access** - Categories are full objects
2. **Filter URLs** - Use `?filterrific[with_category_id][]=` for shop links
3. **Description** - Categories can have descriptions (strip HTML and truncate)
4. **Products Access** - Can access `category.products` if needed

---

## Posts List Selector

The `posts_list` type lets users select multiple blog posts. Perfect for "Related Posts" or "Featured Articles" sections.

### Schema Definition

{
 "name": "Featured Blog Posts",
 "tag": "section",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Section Heading",
 "default": "Featured Articles"
 },
 {
 "type": "posts\_list",
 "id": "featured\_posts",
 "label": "Select Posts",
 "limit": 6,
 "info": "Choose up to 6 blog posts to feature"
 },
 {
 "type": "checkbox",
 "id": "show\_excerpt",
 "label": "Show Post Excerpt",
 "default": true
 },
 {
 "type": "checkbox",
 "id": "show\_date",
 "label": "Show Post Date",
 "default": true
 },
 {
 "type": "select",
 "id": "columns",
 "label": "Columns",
 "options": \[
 { "value": "2", "label": "2 Columns" },
 { "value": "3", "label": "3 Columns" }
 \],
 "default": "3"
 }
 \]
}

### Liquid Implementation

<section class="featured-posts">
 <div class="container">
 
 <h2>{{ section.settings.heading }}</h2>
 
 {\`%\` if section.settings.featured\_posts.size > 0 \`%\`}
 
 <div class="posts-grid" style="--columns: {{ section.settings.columns }};">
 
 {\`%\`- comment -\`%\`} Direct iteration over selected posts {\`%\`- endcomment -\`%\`}
 {\`%\` for post in section.settings.featured\_posts \`%\`}
 
 <article class="post-card">
 
 <a href="{{ post.preview\_url }}" class="post-link">
 
 <!-- Post Image -->
 {\`%\` assign image\_url = '' \`%\`}
 {\`%\` if post.image\_url \`%\`}
 {\`%\` assign image\_url = post.image\_url \`%\`}
 {\`%\` elsif post.image \`%\`}
 {\`%\` assign image\_url = post.image | image\_url \`%\`}
 {\`%\` elsif post.images.size > 0 \`%\`}
 {\`%\` assign image\_url = post.images\[0\].src \`%\`}
 {\`%\` endif \`%\`}
 
 {\`%\` if image\_url != '' \`%\`}
 <div class="post-image">
 <img 
 src="{{ image\_url | image\_url: width: 600 }}" 
 alt="{{ post.title }}"
 loading="lazy">
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Post Content -->
 <div class="post-content">
 
 <!-- Category Badge -->
 {\`%\` if post.category \`%\`}
 <span class="post-category">{{ post.category.title }}</span>
 {\`%\` endif \`%\`}
 
 <!-- Post Title -->
 <h3 class="post-title">{{ post.title }}</h3>
 
 <!-- Post Meta -->
 {\`%\` if section.settings.show\_date \`%\`}
 {\`%\` assign display\_date = post.post\_date | default: post.created\_at \`%\`}
 <time datetime="{{ display\_date | date: '%Y-%m-%d' }}" class="post-date">
 {{ display\_date | date: '%B %d, %Y' }}
 </time>
 {\`%\` endif \`%\`}
 
 <!-- Post Excerpt -->
 {\`%\` if section.settings.show\_excerpt \`%\`}
 <p class="post-excerpt">
 {\`%\` if post.summary != blank \`%\`}
 {{ post.summary | strip\_html | truncate: 120 }}
 {\`%\` elsif post.description != blank \`%\`}
 {{ post.description | strip\_html | truncate: 120 }}
 {\`%\` endif \`%\`}
 </p>
 {\`%\` endif \`%\`}
 
 <!-- Read More Link -->
 <span class="read-more">Read More →</span>
 
 </div>
 </a>
 
 </article>
 
 {\`%\` endfor \`%\`}
 </div>
 
 {\`%\` else \`%\`}
 <p class="empty-state">No posts selected.</p>
 {\`%\` endif \`%\`}
 
 </div>
</section>

### Key Points: posts\_list

1. **Direct Access** - Posts are full objects with all fields
2. **Image Fallback** - Check `post.image_url`, `post.image`, then `post.images` array
3. **Date Handling** - Use `post.post_date` with fallback to `post.created_at`
4. **HTML Content** - Use `| strip_html` for excerpts, `| unescape` for full content
5. **Category Access** - Posts have `post.category` object

---

## Comparison Table: Single vs Multiple Selectors

| Feature | Single Selector | Multiple Selector (List) |
| --- | --- | --- |
| **Schema Type** | `product`, `collection`, `category`, `post` | `product_list`, `collection_list`, `category_list`, `posts_list` |
| **Selection** | One item | Multiple items (with limit) |
| **Typical Use** | Within blocks | Direct in section settings |
| **Access Pattern** | Need to find from global array | Direct iteration |
| **Order Control** | N/A (single item) | User can drag to reorder |
| **Best For** | Custom blocks with overrides | Simple featured grids/carousels |

### When to Use Which?

**Use Single Selector (in blocks) when:**

- Need per-item customization (override title, image, description)
- Want different block types mixed together
- Need flexibility in layout/styling per item
- Example: "Also Bought" with custom messaging

**Use Multiple Selector (list) when:**

- Just need to display selected items
- All items have same styling/layout
- Simpler configuration needed
- Example: "Featured Products Carousel"

---

## Combined Example: Products with Fallback

Sometimes you want to use a product list, but allow fallback to all products if nothing selected:

<section class="products-section">
 <div class="container">
 
 {\`%\`- comment -\`%\`} Use selected products if available, otherwise show all products {\`%\`- endcomment -\`%\`}
 {\`%\` if section.settings.featured\_products.size > 0 \`%\`}
 {\`%\` assign products\_to\_show = section.settings.featured\_products \`%\`}
 {\`%\` else \`%\`}
 {\`%\` assign products\_to\_show = products \`%\`}
 {\`%\` endif \`%\`}
 
 <div class="products-grid">
 {\`%\` for product in products\_to\_show limit: 8 \`%\`}
 <div class="product-card">
 <a href="{{ product.url }}">
 <img src="{{ product.image\_url | image\_url: width: 400 }}" alt="{{ product.title }}">
 <h3>{{ product.title }}</h3>
 <p>{{ product.price | money }}</p>
 </a>
 </div>
 {\`%\` endfor \`%\`}
 </div>
 
 </div>
</section>

### Best Practices: Multiple Resource Selectors

✅ **DO:**

- Always check `.size > 0` before looping
- Set reasonable limits (8-12 for products, 4-8 for collections)
- Provide empty state messaging
- Use `loading="lazy"` on images
- Build proper filter URLs for shop links
- Handle missing images with fallbacks

❌ **DON'T:**

- Allow unlimited selections (causes performance issues)
- Forget to check if list is empty
- Assume all items have images
- Hardcode shop URLs (use settings)
- Skip the `limit` parameter in schema
- Forget to optimize image sizes with `| image_url: width:`

---

## Global Loops with Pagination

Global loops are used on main template pages (like `post_page`, `shop_page`) to display all items of a type automatically. Unlike resource selectors where merchants hand-pick items, global loops show everything and update automatically when new content is added.

### When to Use Global Loops

**Perfect for:**

- Blog listing pages (`blog_page.liquid`)
- Shop/collection pages (`collection`, `shop_page`)
- Large datasets that need pagination
- Content that should auto-update

**Not suitable for:**

- Curated "featured" sections
- Hand-picked recommendations
- Content that needs specific ordering

---

## Blog Post Lists: The Global Loop Pattern

Example shows how to build a paginated blog listing page that automatically displays all posts.

### Complete Implementation

#### Schema Definition

{
 "name": "Blog List",
 "tag": "section",
 "enabled\_on": {
 "templates": \["blog"\]
 },
 "settings": \[
 {
 "type": "header",
 "content": "Section Title"
 },
 {
 "type": "text",
 "id": "title",
 "label": "Title",
 "default": "Blog Posts"
 },
 {
 "type": "select",
 "id": "title\_font\_size",
 "label": "Font Size (Mobile)",
 "options": \[
 { "value": "text-2xl", "label": "2XL" },
 { "value": "text-3xl", "label": "3XL" },
 { "value": "text-4xl", "label": "4XL" }
 \],
 "default": "text-2xl"
 },
 {
 "type": "header",
 "content": "Post Card Settings"
 },
 {
 "type": "checkbox",
 "id": "show\_post\_description",
 "label": "Show Post Description",
 "default": true
 },
 {
 "type": "select",
 "id": "post\_card\_background",
 "label": "Post Card Background Color",
 "options": \[
 { "value": "", "label": "None (Transparent)" },
 { "value": "bg-white", "label": "White" },
 { "value": "bg-neutral-light", "label": "Neutral Light" }
 \],
 "default": "bg-neutral-light"
 },
 {
 "type": "text",
 "id": "empty\_state\_text",
 "label": "Empty state text",
 "default": "No posts found"
 }
 \]
}

#### Liquid Implementation

<section class="blog-posts {{ section.settings.background\_color }} {{ section.settings.section\_padding\_y\_mobile }} {{ section.settings.section\_padding\_y\_desktop }}">
 <div class="container">
 
 <!-- Section Title -->
 {\`%\` if section.settings.title != blank \`%\`}
 <div class="title-section mb-lg">
 <h3 class="{{ section.settings.title\_font\_family }} {{ section.settings.title\_font\_size }} {{ section.settings.title\_font\_size\_desktop }} {{ section.settings.title\_font\_weight }} {{ section.settings.title\_color }}">
 {{ section.settings.title }}
 </h3>
 </div>
 {\`%\` endif \`%\`}
 
 {\`%\`- comment -\`%\`} 
 CRITICAL: Wrap the entire section in {\`%\` paginate \`%\`}
 This provides the posts collection and pagination controls
 {\`%\`- endcomment -\`%\`}
 {\`%\`- paginate posts by 10 -\`%\`}
 
 <div class="post-list-section">
 
 {\`%\` if posts.size > 0 \`%\`}
 
 <!-- Posts Grid -->
 <div class="post-list grid grid-cols-1 gap-lg mt-xl">
 
 {\`%\`- comment -\`%\`} Loop through posts provided by paginate {\`%\`- endcomment -\`%\`}
 {\`%\` for post in posts \`%\`}
 
 <a href="{{ post.preview\_url }}" class="blog-card {{ section.settings.post\_card\_background }} {{ section.settings.card\_border\_radius }}">
 
 <!-- Post Image -->
 <div class="post-image-link">
 <div class="post-image {{ section.settings.image\_border\_radius }}">
 
 {\`%\`- comment -\`%\`} Image fallback chain {\`%\`- endcomment -\`%\`}
 {\`%\` assign image\_url = '' \`%\`}
 {\`%\` if post.image\_url \`%\`}
 {\`%\` assign image\_url = post.image\_url \`%\`}
 {\`%\` elsif post.image \`%\`}
 {\`%\` assign image\_url = post.image | image\_url \`%\`}
 {\`%\` elsif post.images.size > 0 \`%\`}
 {\`%\` assign image\_url = post.images\[0\].src \`%\`}
 {\`%\` endif \`%\`}
 
 {\`%\` if image\_url != '' \`%\`}
 <img 
 src="{{ image\_url }}"
 alt="{{ post.title }}"
 class="image-cover"
 loading="lazy" />
 {\`%\` else \`%\`}
 <div class="h-full w-full flex items-center justify-center bg-gray-100">
 <span class="text-gray-400">No image available</span>
 </div>
 {\`%\` endif \`%\`}
 
 </div>
 </div>
 
 <!-- Post Content -->
 <div class="desc px-md py-md">
 
 <!-- Post Title -->
 <div class="post-title {{ section.settings.post\_title\_font\_family }} {{ section.settings.post\_title\_font\_size }} {{ section.settings.post\_title\_font\_size\_desktop }} {{ section.settings.post\_title\_font\_weight }} {{ section.settings.post\_title\_color }} line-clamp-2">
 {{ post.title }}
 </div>
 
 <!-- Post Description -->
 {\`%\` if section.settings.show\_post\_description \`%\`}
 <div class="post-desc {{ section.settings.post\_desc\_font\_size }} {{ section.settings.post\_desc\_font\_size\_desktop }} {{ section.settings.post\_desc\_font\_weight }} {{ section.settings.post\_desc\_color }} line-clamp-2 mt-sm">
 {\`%\` if post.summary \`%\`}
 {{ post.summary | unescape }}
 {\`%\` elsif post.description \`%\`}
 {{ post.description | unescape }}
 {\`%\` endif \`%\`}
 </div>
 {\`%\` endif \`%\`}
 
 </div>
 </a>
 
 {\`%\` endfor \`%\`}
 </div>
 
 {\`%\` else \`%\`}
 
 <!-- Empty State -->
 <div class="text-center py-2xl">
 <p class="text-gray-500">{{ section.settings.empty\_state\_text | default: 'No posts found' }}</p>
 </div>
 
 {\`%\` endif \`%\`}
 </div>
 
 {\`%\`- comment -\`%\`} Pagination Controls {\`%\`- endcomment -\`%\`}
 {\`%\`- if paginate.pages > 1 -\`%\`}
 <div class="mt-12">
 {\`%\` render 'pagination', paginate: paginate, anchor: '' \`%\`}
 </div>
 {\`%\`- endif -\`%\`}
 
 {\`%\` endpaginate \`%\`}
 
 </div>
</section>

### Key Concepts Explained

#### 1\. The Paginate Tag

The `{`%`paginate`%`}` tag is **required** for global loops. It:

- Provides the `posts` collection
- Handles page numbers automatically
- Creates the `paginate` object for controls

{\`%\`- paginate posts by 10 -\`%\`}
 {\`%\`- comment -\`%\`} posts collection is now available {\`%\`- endcomment -\`%\`}
 {\`%\` for post in posts \`%\`}
 ...
 {\`%\` endfor \`%\`}
 
 {\`%\`- comment -\`%\`} paginate object for controls {\`%\`- endcomment -\`%\`}
 {\`%\` if paginate.pages > 1 \`%\`}
 {\`%\` render 'pagination', paginate: paginate \`%\`}
 {\`%\` endif \`%\`}
{\`%\` endpaginate \`%\`}

**Important:** Everything that needs access to `posts` or `paginate` must be inside the `{`%`paginate`%`}` tags.

#### 2\. Post Object Fields

When looping through posts, you have access to these fields:

| Field | Type | Description | Example |
| --- | --- | --- | --- |
| `post.title` | String | Post title | `"5 Tips for Better Sleep"` |
| `post.preview_url` | String | Link to post detail page | `"/blog/5-tips-for-better-sleep"` |
| `post.image_url` | String | Featured image URL | Direct image URL |
| `post.image` | Object | Featured image object | Use with \` |
| `post.images` | Array | All post images | `post.images[0].src` |
| `post.summary` | String | Short description/excerpt | May contain HTML |
| `post.description` | String | Full post content | May contain HTML |
| `post.post_date` | Date | Publication date | Use with \` |
| `post.post_author` | String | Author name | `"John Doe"` |
| `post.category` | Object | Post category | `post.category.title` |
| `post.collections` | Array | Associated collections | For tags/categories |
| `post.created_at` | Date | Creation timestamp | Use with \` |
| `post.updated_at` | Date | Last update timestamp | Use with \` |

#### 3\. Image Fallback Pattern

Always provide multiple fallback options for images:

{\`%\` assign image\_url = '' \`%\`}

{\`%\`- comment -\`%\`} Try image\_url first (direct URL) {\`%\`- endcomment -\`%\`}
{\`%\` if post.image\_url \`%\`}
 {\`%\` assign image\_url = post.image\_url \`%\`}
 
{\`%\`- comment -\`%\`} Try image object (needs filter) {\`%\`- endcomment -\`%\`}
{\`%\` elsif post.image \`%\`}
 {\`%\` assign image\_url = post.image | image\_url \`%\`}
 
{\`%\`- comment -\`%\`} Try images array {\`%\`- endcomment -\`%\`}
{\`%\` elsif post.images.size > 0 \`%\`}
 {\`%\` assign image\_url = post.images\[0\].src \`%\`}
{\`%\` endif \`%\`}

{\`%\`- comment -\`%\`} Render image or placeholder {\`%\`- endcomment -\`%\`}
{\`%\` if image\_url != '' \`%\`}
 <img src="{{ image\_url }}" alt="{{ post.title }}" loading="lazy">
{\`%\` else \`%\`}
 <div class="placeholder">No image available</div>
{\`%\` endif \`%\`}

#### 4\. HTML Content Handling

Post summaries and descriptions often contain HTML. Use the `unescape` filter:

{\`%\` if post.summary \`%\`}
 {{ post.summary | unescape }}
{\`%\` elsif post.description \`%\`}
 {{ post.description | unescape }}
{\`%\` endif \`%\`}

For plain text previews, strip HTML and truncate:

{{ post.description | strip\_html | truncate: 150 }}

#### 5\. Pagination Snippet

Create a reusable `pagination.liquid` snippet:

{\`%\`- comment -% components/pagination.liquid {\`%\`- endcomment -\`%\`}
{\`%\` if paginate.pages > 1 \`%\`}
 <nav class="pagination" role="navigation">
 
 {\`%\`- if paginate.previous -\`%\`}
 <a href="{{ paginate.previous.url }}" class="pagination-prev">
 ← Previous
 </a>
 {\`%\`- else -\`%\`}
 <span class="pagination-prev disabled">← Previous</span>
 {\`%\`- endif -\`%\`}
 
 <span class="pagination-info">
 Page {{ paginate.current\_page }} of {{ paginate.pages }}
 </span>
 
 {\`%\`- if paginate.next -\`%\`}
 <a href="{{ paginate.next.url }}" class="pagination-next">
 Next →
 </a>
 {\`%\`- else -\`%\`}
 <span class="pagination-next disabled">Next →</span>
 {\`%\`- endif -\`%\`}
 
 </nav>
{\`%\` endif \`%\`}

Use it in your template:

{\`%\` if paginate.pages > 1 \`%\`}
 {\`%\` render 'pagination', paginate: paginate, anchor: '' \`%\`}
{\`%\` endif \`%\`}

### Best Practices: Global Loops

✅ **DO:**

- Always wrap content in `{`%`paginate`%`}`
- Use appropriate page sizes (10-12 for blogs, 24-48 for products)
- Provide empty state messaging
- Include pagination controls when needed
- Use `loading="lazy"` on images
- Strip HTML from descriptions when needed
- Check for `posts.size > 0` before rendering
- Add empty state text as a setting

❌ **DON'T:**

- Forget to close `{`%`endpaginate`%`}`
- Access `posts` outside paginate tags
- Use huge page sizes (causes performance issues)
- Forget fallbacks for missing images
- Display raw HTML without `| unescape`
- Hardcode empty state messages
- Skip the pagination controls
- Use `{`%`paginate`%`}` for manually curated content

---

## Post Details Page: Single Resource Pattern

For post detail pages, you work with a single `post` object rather than a collection. This is similar to product detail pages.

This shows how to display a full blog post with all its metadata.

#### Key Liquid Implementation

<section class="post-details">
 <div class="container">
 
 {\`%\` if post \`%\`}
 <article class="post-wrapper">
 
 <!-- Post Header -->
 <div class="post-header mb-xl">
 
 <!-- Category Badge -->
 {\`%\` if section.settings.show\_category and post.category \`%\`}
 <div class="post-category mb-md">
 <a href="{{ post.category.preview\_url | default: '#' }}" class="category-badge {{ section.settings.category\_badge\_background }} {{ section.settings.category\_badge\_border\_radius }}">
 {{ post.category.title }}
 </a>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Post Title -->
 {\`%\` if section.settings.show\_title \`%\`}
 <h1 class="post-title {{ section.settings.title\_font\_family }} {{ section.settings.title\_font\_size }} {{ section.settings.title\_font\_weight }} {{ section.settings.title\_color }} mb-md">
 {{ post.title }}
 </h1>
 {\`%\` endif \`%\`}
 
 <!-- Post Meta (Author, Date) -->
 {\`%\` if section.settings.show\_meta \`%\`}
 <div class="post-meta flex flex-wrap items-center gap-md {{ section.settings.meta\_font\_size }} {{ section.settings.meta\_color }}">
 
 {\`%\` if section.settings.show\_author and post.post\_author \`%\`}
 <div class="post-author flex items-center gap-sm">
 {\`%\` if section.settings.show\_author\_icon \`%\`}
 <svg class="author-icon" fill="none" stroke="currentColor" viewBox="0 0 24 24">
 <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path>
 </svg>
 {\`%\` endif \`%\`}
 <span class="author-name">{{ post.post\_author }}</span>
 </div>
 {\`%\` endif \`%\`}
 
 {\`%\`- comment -\`%\`} Date with fallback {\`%\`- endcomment -\`%\`}
 {\`%\` assign display\_date = post.post\_date \`%\`}
 {\`%\` if display\_date == blank or display\_date == null \`%\`}
 {\`%\` assign display\_date = post.created\_at \`%\`}
 {\`%\` endif \`%\`}
 
 {\`%\` if section.settings.show\_date and display\_date \`%\`}
 <div class="post-date flex items-center gap-sm">
 {\`%\` if section.settings.show\_date\_icon \`%\`}
 <svg class="date-icon" fill="none" stroke="currentColor" viewBox="0 0 24 24">
 <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"></path>
 </svg>
 {\`%\` endif \`%\`}
 <time datetime="{{ display\_date | date: '%Y-%m-%d' }}" class="date-text">
 {{ display\_date | date: section.settings.date\_format | default: '%B %d, %Y' }}
 </time>
 </div>
 {\`%\` endif \`%\`}
 
 </div>
 {\`%\` endif \`%\`}
 </div>
 
 <!-- Featured Image -->
 {\`%\` if section.settings.show\_featured\_image \`%\`}
 {\`%\` assign image\_url = '' \`%\`}
 {\`%\` if post.image\_url \`%\`}
 {\`%\` assign image\_url = post.image\_url \`%\`}
 {\`%\` elsif post.image \`%\`}
 {\`%\` assign image\_url = post.image | image\_url \`%\`}
 {\`%\` elsif post.images.size > 0 \`%\`}
 {\`%\` assign image\_url = post.images\[0\].src \`%\`}
 {\`%\` endif \`%\`}
 
 {\`%\` if image\_url != '' \`%\`}
 <div class="post-featured-image-wrapper mb-xl">
 <div class="post-featured-image {{ section.settings.featured\_image\_border\_radius }} overflow-hidden">
 <img 
 src="{{ image\_url }}"
 alt="{{ post.title }}"
 loading="lazy"
 class="featured-image" />
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 
 <!-- Post Summary -->
 {\`%\` if section.settings.show\_summary and post.summary \`%\`}
 <div class="post-summary mb-xl">
 <div class="summary-card {{ section.settings.summary\_background }} {{ section.settings.summary\_padding }} {{ section.settings.summary\_border\_radius }}">
 <div class="summary-content {{ section.settings.summary\_font\_size }} {{ section.settings.summary\_font\_weight }} {{ section.settings.summary\_color }}">
 {{ post.summary | unescape }}
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Post Content -->
 {\`%\` if section.settings.show\_description and post.description \`%\`}
 <div class="post-content mb-xl">
 <div class="content-wrapper {{ section.settings.description\_font\_size }} {{ section.settings.description\_font\_weight }} {{ section.settings.description\_color }} trix-content">
 {{ post.description | unescape }}
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Post Collections (Tags) -->
 {\`%\` if section.settings.show\_collections and post.collections.size > 0 \`%\`}
 <div class="post-collections mb-xl">
 {\`%\` if section.settings.collections\_title \`%\`}
 <h3 class="collections-title {{ section.settings.collections\_title\_font\_size }} {{ section.settings.collections\_title\_font\_weight }} {{ section.settings.collections\_title\_color }} mb-md">
 {{ section.settings.collections\_title }}
 </h3>
 {\`%\` endif \`%\`}
 <div class="collections-list flex flex-wrap gap-sm">
 {\`%\` for collection in post.collections \`%\`}
 <a href="{{ collection.preview\_url | default: '#' }}" class="collection-badge {{ section.settings.collection\_badge\_background }} {{ section.settings.collection\_badge\_border\_radius }} {{ section.settings.collection\_font\_size }} {{ section.settings.collection\_font\_weight }} {{ section.settings.collection\_color }} {{ section.settings.collection\_padding }}">
 {{ collection.title }}
 </a>
 {\`%\` endfor \`%\`}
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 </article>
 
 {\`%\` else \`%\`}
 
 <!-- Post Not Found -->
 <div class="text-center py-2xl">
 <p class="text-gray-500">{{ section.settings.empty\_state\_text | default: 'Post not found' }}</p>
 </div>
 
 {\`%\` endif \`%\`}
 
 </div>
</section>

### Key Techniques for Post Details

#### 1\. Date Handling with Fallback

Posts may have `post_date` or `created_at`. Always provide a fallback:

{\`%\` assign display\_date = post.post\_date \`%\`}
{\`%\` if display\_date == blank or display\_date == null \`%\`}
 {\`%\` assign display\_date = post.created\_at \`%\`}
{\`%\` endif \`%\`}

{\`%\` if display\_date \`%\`}
 <time datetime="{{ display\_date | date: '%Y-%m-%d' }}">
 {{ display\_date | date: '%B %d, %Y' }}
 </time>
{\`%\` endif \`%\`}

#### 2\. Rich Content Rendering

Post content is stored as HTML. Use the `trix-content` class for proper styling and `unescape`:

<div class="trix-content">
 {{ post.description | unescape }}
</div>

The `trix-content` class should style all HTML elements (headings, lists, links, images, etc.) that might appear in the rich text editor output.

#### 3\. Collections as Tags

Post collections work like tags or categories:

{\`%\` if post.collections.size > 0 \`%\`}
 <div class="tags">
 {\`%\` for collection in post.collections \`%\`}
 <a href="{{ collection.preview\_url }}" class="tag">
 {{ collection.title }}
 </a>
 {\`%\` endfor \`%\`}
 </div>
{\`%\` endif \`%\`}

### Best Practices: Post Details

✅ **DO:**

- Check if `post` exists before rendering
- Provide date fallbacks (post\_date → created\_at)
- Use semantic HTML (`<article>`, `<time>`, etc.)
- Style rich content with `trix-content` class
- Use `| unescape` for HTML content
- Provide empty state messaging
- Use proper datetime attributes on `<time>` tags

❌ **DON'T:**

- Assume post always exists
- Display raw dates without formatting
- Forget to `| unescape` HTML content
- Skip checks for empty collections
- Hardcode labels (use settings)
- Forget alt text on images
- Skip the empty state handler

---

## Complete Resource Reference

This section documents all available resource types and their fields.

### Product Fields Reference

When working with product objects from any source (selectors, loops, or blocks):

| Field | Type | Description | Usage Example |
| --- | --- | --- | --- |
| `product.id` | Number | Unique product identifier | `{{ product.id }}` |
| `product.title` | String | Product name | `{{ product.title }}` |
| `product.url` | String | Link to product detail page | `<a href="{{ product.url }}">` |
| `product.price` | Number | Current price in cents | \`{{ product.price |
| `product.compare_at_price` | Number | Original price (for sales) | \`{{ product.compare\_at\_price |
| `product.image_url` | String | Primary product image URL (direct) | `<img src="{{ product.image_url }}">` |
| `product.featured_image` | Object | Primary product image object | \`{{ product.featured\_image |
| `product.images` | Array | All product images | `{{ product.images[0].src }}` |
| `product.description` | String | Full HTML description | \`{{ product.description |
| `product.short_description` | String | Brief description | `{{ product.short_description }}` |
| `product.variants` | Array | Product variants | `{`%`for v in product.variants`%`}` |
| `product.variants.first` | Object | First variant | `{{ product.variants.first.id }}` |
| `product.selected_or_first_available_variant` | Object | Best variant to show | `{`%`assign v = product.selected_or_first_available_variant`%`}` |
| `product.available` | Boolean | In stock status | `{`%`if product.available`%`}` |
| `product.tags` | Array | Product tags | `{`%`for tag in product.tags`%`}` |
| `product.vendor` | String | Product vendor/brand | `{{ product.vendor }}` |
| `product.type` | String | Product type | `{{ product.type }}` |

**Critical:** Always use `{{ product.price | money }}` - never display raw price numbers.

### Variant Fields Reference

Returned by the `variant` selector (scoped to the store's own products). Resolves to an empty object when unset or invalid.

| Field | Type | Description | Usage Example |
| --- | --- | --- | --- |
| `variant.id` | Number | Variant id — ready for add-to-cart | `data-fluid-add-to-cart="{{ variant.id }}"` |
| `variant.sku` | String | Stock-keeping unit | `{{ variant.sku }}` |
| `variant.title` | String | Variant title | `{{ variant.title }}` |
| `variant.display_name` | String | Human label including options (e.g. "Large / Red") | `{{ variant.display_name }}` |
| `variant.is_master` | Boolean | Whether this is the product's master variant | `{`%`if variant.is_master`%`}` |
| `variant.image_url` | String | Variant image URL (direct) | `<img src="{{ variant.image_url }}">` |
| `variant.images` | Array | All variant images | `{{ variant.images[0].src }}` |
| `variant.option_values` | Array | Selected option values | `{`%`for o in variant.option_values`%`}` |
| `variant.variant_countries` | Array | Per-country pricing & points (see below) | `{`%`for c in variant.variant_countries`%`}` |
| `variant.available` | Boolean | Buyable in the current country | `{`%`if variant.available`%`}` |
| `variant.buyable` | Boolean | Alias of `available` | `{`%`if variant.buyable`%`}` |
| `variant.available_for_country` | Boolean | Active, buyable, and sold in the buyer's country | `{`%`if variant.available_for_country`%`}` |
| `variant.active` | Boolean | Variant is active | `{`%`if variant.active`%`}` |
| `variant.points` | Number | Loyalty points for the current country | `{{ variant.points }}` |

Each `variant.option_values` entry exposes `option_name` (e.g. "Size"), `presentation` (e.g. "Large"), `id`, `option_id`, and `selected`.

Each `variant.variant_countries` entry exposes `country_iso`, `currency_code`, `price`, `display_price`, `subscription_price`, `display_subscription_price`, `cv`, `qv`, and `points`.

**Critical:** A variant has no flat `price` — read `display_price` from the `variant_countries` entry matching the buyer's country.

### Collection Fields Reference

| Field | Type | Description | Usage Example |
| --- | --- | --- | --- |
| `collection.id` | Number | Unique collection identifier | `{{ collection.id }}` |
| `collection.title` | String | Collection name | `{{ collection.title }}` |
| `collection.handle` | String | URL-friendly identifier | `{{ collection.handle }}` |
| `collection.url` | String | Link to collection page | `<a href="{{ collection.url }}">` |
| `collection.image` | Object | Featured image object | \`{{ collection.image |
| `collection.image_url` | String | Direct image URL | `<img src="{{ collection.image_url }}">` |
| `collection.description` | String | Collection description | `{{ collection.description }}` |
| `collection.products` | Array | Products in collection | `{`%`for p in collection.products`%`}` |
| `collection.products.first` | Object | First product in collection | `{{ collection.products.first.image_url }}` |
| `collection.products_count` | Number | Total products | `{{ collection.products_count }} items` |

### Category Fields Reference

| Field | Type | Description | Usage Example |
| --- | --- | --- | --- |
| `category.id` | Number | Unique category identifier | `{{ category.id }}` |
| `category.title` | String | Category name | `{{ category.title }}` |
| `category.handle` | String | URL-friendly identifier | `{{ category.handle }}` |
| `category.image` | Object | Featured image | \`{{ category.image |
| `category.image_url` | String | Direct image URL | `<img src="{{ category.image_url }}">` |
| `category.description` | String | Category description | `{{ category.description }}` |
| `category.products` | Array | Products in category | `{`%`for p in category.products`%`}` |

**Category Shop Links:** Use `/shop?filterrific[with_category_id][]={{ category.id }}`

### Post Fields Reference (Covered in detail above)

See "Blog Post Lists: The Global Loop Pattern" section for complete post field documentation.

---

## Blocks: Reorderable Dynamic Content

Blocks allow merchants to add, remove, reorder, and customize multiple instances of content. Perfect for testimonials, features, FAQs, slides, etc.

### Basic Block Pattern

#### Schema

{
 "name": "Features Grid",
 "blocks": \[
 {
 "type": "feature",
 "name": "Feature Item",
 "settings": \[
 {
 "type": "text",
 "id": "title",
 "label": "Feature Title",
 "default": "Fast Shipping"
 },
 {
 "type": "textarea",
 "id": "description",
 "label": "Description"
 },
 {
 "type": "image\_picker",
 "id": "icon",
 "label": "Icon"
 }
 \]
 }
 \],
 "max\_blocks": 6
}

#### Liquid

<div class="features-grid">
 {\`%\` for block in section.blocks \`%\`}
 {\`%\` if block.type == 'feature' \`%\`}
 <div class="feature-item" {{ block.fluid\_attributes }}>
 {\`%\` if block.settings.icon \`%\`}
 <img src="{{ block.settings.icon | image\_url: width: 80 }}" alt="Icon">
 {\`%\` endif \`%\`}
 <h3>{{ block.settings.title }}</h3>
 <p>{{ block.settings.description }}</p>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endfor \`%\`}
</div>

**Critical:** Always include `{{ block.fluid_attributes }}` on the block wrapper for editor functionality.

### Advanced Block Pattern: Multiple Block Types

Sections can accept different block types for flexibility:

#### Schema

{
 "name": "Content Section",
 "blocks": \[
 {
 "type": "text\_block",
 "name": "Text Content",
 "settings": \[
 {
 "type": "richtext",
 "id": "content",
 "label": "Content"
 }
 \]
 },
 {
 "type": "image\_block",
 "name": "Image",
 "settings": \[
 {
 "type": "image\_picker",
 "id": "image",
 "label": "Image"
 },
 {
 "type": "text",
 "id": "caption",
 "label": "Caption"
 }
 \]
 },
 {
 "type": "video\_block",
 "name": "Video Embed",
 "settings": \[
 {
 "type": "text",
 "id": "video\_picker",
 "label": "Video URL (YouTube or Vimeo)"
 }
 \]
 }
 \]
}

#### Liquid

<div class="content-blocks">
 {\`%\` for block in section.blocks \`%\`}
 
 {\`%\` if block.type == 'text\_block' \`%\`}
 <div class="text-block" {{ block.fluid\_attributes }}>
 {{ block.settings.content }}
 </div>
 
 {\`%\` elsif block.type == 'image\_block' \`%\`}
 <div class="image-block" {{ block.fluid\_attributes }}>
 {\`%\` if block.settings.image \`%\`}
 <img src="{{ block.settings.image | image\_url: width: 1200 }}" alt="{{ block.settings.caption }}">
 {\`%\` if block.settings.caption \`%\`}
 <p class="caption">{{ block.settings.caption }}</p>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 </div>
 
 {\`%\` elsif block.type == 'video\_block' \`%\`}
 <div class="video-block" {{ block.fluid\_attributes }}>
 {\`%\` if block.settings.video\_url contains 'youtube' \`%\`}
 <iframe src="{{ block.settings.video\_url }}" frameborder="0" allowfullscreen></iframe>
 {\`%\` elsif block.settings.video\_url contains 'vimeo' \`%\`}
 <iframe src="{{ block.settings.video\_url }}" frameborder="0" allowfullscreen></iframe>
 {\`%\` endif \`%\`}
 </div>
 
 {\`%\` endif \`%\`}
 {\`%\` endfor \`%\`}
</div>

### Block Limits and Presets

Control how many blocks can be added and provide starting content:

{
 "name": "Testimonials",
 "blocks": \[
 {
 "type": "testimonial",
 "name": "Testimonial",
 "limit": 12,
 "settings": \[...\]
 }
 \],
 "max\_blocks": 12,
 "presets": \[
 {
 "name": "Testimonials Section",
 "blocks": \[
 {
 "type": "testimonial",
 "settings": {
 "quote": "This product changed my life!",
 "author": "Jane Doe",
 "rating": 5
 }
 },
 {
 "type": "testimonial",
 "settings": {
 "quote": "Excellent quality and fast shipping.",
 "author": "John Smith",
 "rating": 5
 }
 }
 \]
 }
 \]
}

### Best Practices: Blocks

✅ **DO:**

- Always include `{{ block.fluid_attributes }}`
- Check block type with `{`%`if block.type == 'name'`%`}`
- Use `section.blocks.size` to check if blocks exist
- Provide meaningful default values
- Use `limit` to prevent performance issues
- Include helpful `info` text in settings
- Create useful presets with example content

❌ **DON'T:**

- Forget `{{ block.fluid_attributes }}`
- Skip block type checking
- Allow unlimited blocks (causes editor issues)
- Use generic names like "Block 1", "Block 2"
- Forget to handle empty blocks gracefully
- Mix blocks and section settings for the same purpose

---

## Complete Settings Reference

This is the exhaustive list of all supported schema setting types in Fluid.

### Text Input Types

| Type | Description | Use Case | Example |
| --- | --- | --- | --- |
| `text` | Single-line text | Titles, labels, short text | Heading, button text |
| `plaintext` | Single-line plain text, with no rich-text formatting | Short text that must stay unformatted | SKUs, codes, plain labels |
| `textarea` | Multi-line plain text | Longer text without formatting | Descriptions, captions |
| `richtext` or `rich_text` | Rich text editor | Formatted content | About sections, long descriptions |
| `html` or `html_textarea` | Raw HTML input | Custom HTML code | Embeds, custom widgets |
| `url` | URL input with validation | Links, external resources | Button links, social links |

**Example:**

{
 "type": "text",
 "id": "heading",
 "label": "Section Heading",
 "default": "Welcome to Our Store",
 "info": "This appears at the top of the section"
}

### Number & Selection Types

| Type | Description | Use Case | Example |
| --- | --- | --- | --- |
| `range` | Slider with min/max | Numeric settings with bounds | Font size, opacity, spacing |
| `select` | Dropdown menu | Predefined options | Font family, color scheme |
| `radio` | Radio buttons | Visual choice selection | Layout options |
| `checkbox` | Toggle on/off | Boolean settings | Show/hide elements |

> \[!NOTE\] The `number` type is **not supported**. Use `range` instead for numeric inputs.

**Example:**

{
 "type": "range",
 "id": "font\_size",
 "label": "Font Size",
 "min": 12,
 "max": 72,
 "step": 2,
 "default": 24,
 "unit": "px"
}

### Visual & Media Types

| Type | Description | Use Case | Example |
| --- | --- | --- | --- |
| `color` | Color picker | Simple colors | Text color, background |
| `color_background` | Color with gradient support | Complex backgrounds | Hero backgrounds |
| `font_picker` | Font family selector | Typography | Heading fonts |
| `image` or `image_picker` | Image upload/select | Images | Logos, backgrounds, photos |
| `video_picker` | Video upload/select | Videos | Hero videos, product demos |
| `media_picker` | Image **or** video select, with embed options (stores a media object) | Mixed media | Hero media, banners — anything that may be an image or a video |

**Example:**

{
 "type": "color",
 "id": "text\_color",
 "label": "Text Color",
 "default": "#000000"
}

### Media Picker

The `media_picker` type lets users choose **either an image or a video** from the media library and configure how it embeds. You declare it exactly like `image_picker` or `video_picker` — the same `type` / `id` / `label` / `info` keys:

{
 "type": "media\_picker",
 "id": "banner\_media",
 "label": "Banner Media",
 "info": "Choose an image or a video"
}

**Supported image formats:** `.jpg`, `.png`, `.gif`, `.webp`, `.svg`, and `.avif`.

What sets it apart is **what it stores**. Where `image_picker` and `video_picker` save a plain URL string, `media_picker` saves a media **object** — because it has to carry both the media URL and the embed configuration:

{
 url: string; // direct URL to the image or video
 fluid\_media\_id?: number; // 0 for a plain uploaded image/video; a positive id embeds a Fluid media item
 embed\_type?: string; // "default" | "popover" | "inline" | "inline-shopping" — see Embed types below
 embed\_settings?: {
 width?: number | "";
 height?: number | "";
 responsive?: boolean;
 autoOpen?: boolean;
 hideCta?: boolean;
 fairShareVisible?: boolean;
 };
}

> \[!NOTE\] Because the stored value is an object, you read its `url` property in Liquid — `{{ section.settings.banner_media.url }}`, **not** `{{ section.settings.banner_media }}`. For `image_picker` / `video_picker`, which store the string directly, you use the setting value as-is.

#### Stored data example

Here is how a configured media picker is actually saved in a section's template data. In this section the setting id is `media`, so the editor writes its alt text to the sibling `media_alt` setting (other section settings omitted for brevity):

{
 "type": "Enrollment Header",
 "settings": {
 "media": {
 "url": "https://cdn.example.com/media/banner.jpeg",
 "fluid\_media\_id": 0,
 "embed\_type": "popover",
 "embed\_settings": {
 "width": 560,
 "height": 315,
 "responsive": true,
 "autoOpen": false,
 "hideCta": false,
 "fairShareVisible": false
 }
 },
 "media\_alt": "new"
 }
}

Here `fluid_media_id` is `0`, so the media is a plain uploaded image — the `.jpeg` at `url` — not a Fluid media embed. A **positive** `fluid_media_id` is what signals the embed widget should be used. (`embed_type` and `embed_settings` are still stored either way.)

#### The auto-managed `<id>_alt` companion

You never declare alt text for a media picker. The editor automatically creates and maintains a companion setting named `<id>_alt` (for the example above, `banner_media_alt`) that holds the alt text and its translations. You don't add it to your schema — just read it alongside the media object:

<img
 src="{{ section.settings.banner\_media.url }}"
 alt="{{ section.settings.banner\_media\_alt }}">

#### Liquid Implementation

A media picker may hold a plain image/video or a Fluid media item. When `fluid_media_id` is a **positive** number, render Fluid's embed widget; otherwise (id `0` or absent) fall back to the plain `url`. Compare with `> 0`, not `!= blank` — Liquid treats `0` as truthy, so a plain image (which stores `fluid_media_id: 0`) would wrongly take the widget branch:

{\`%\`- assign media = section.settings.banner\_media -\`%\`}

{\`%\`- if media != blank and media.url != blank -\`%\`}
 {\`%\`- if media.fluid\_media\_id > 0 -\`%\`}
 {\`%\`- comment -\`%\`} Fluid media item — render the embed widget {\`%\`- endcomment -\`%\`}
 <fluid-media-widget
 media-id="{{ media.fluid\_media\_id }}"
 embed-type="{{ media.embed\_type | default: 'default' }}"
 responsive="{{ media.embed\_settings.responsive | default: true }}"
 data-fluid-widget="true">
 </fluid-media-widget>
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Plain image / video URL {\`%\`- endcomment -\`%\`}
 <img src="{{ media.url }}" alt="{{ section.settings.banner\_media\_alt }}" loading="lazy">
 {\`%\`- endif -\`%\`}
{\`%\`- endif -\`%\`}

#### Embed types

`embed_type` is passed straight through to the `embed-type` attribute of `<fluid-media-widget>`. The widget recognizes these values:

| `embed_type` | Behavior |
| --- | --- |
| `default` | Shows the media with its associated CTA options (the shareable media-page view) |
| `popover` | Shows a thumbnail that opens the media in a popover overlay — pair with `embed_settings.autoOpen` (the widget's `auto-open`) to open it on load |
| `inline` | Embeds the media directly in the page |
| `inline-shopping` | Inline embed variant with shopping enabled |

See the [Media component](https://docs.fluid.app/docs/sdk/fairshare/components/media#display-modes) reference for the full `<fluid-media-widget>` attribute list (including `auto-open`, `width`, `height`, and the video playback options) and a live example of each mode.

> \[!TIP\] An unset media picker resolves to `blank`, and `embed_settings` may be absent when the editor never configured embed options. Always confirm the object and its `url` are present before reading nested properties.

### Resource Selector Types

#### Single Resource Selectors

Select **one** item from the specified resource type:

| Type | Description | Returns |
| --- | --- | --- |
| `product` | Single product | Product ID (need to find from global array) |
| `products` | Single product (alias) | Same as `product` |
| `collection` | Single collection | Collection ID (need to find from global array) |
| `collections` | Single collection (alias) | Same as `collection` |
| `category` | Single category | Category ID (need to find from global array) |
| `categories` | Single category (alias) | Same as `category` |
| `post` | Single blog post | Post ID (need to find from global array) |
| `posts` | Single blog post (alias) | Same as `post` |
| `blog` | Single blog | Blog ID |
| `forms` | Single form | Form ID |
| `enrollment` | Single enrollment | Enrollment ID |
| `enrollments` | Single enrollment (alias) | Same as `enrollment` |
| `enrollment_pack` | Single enrollment pack | Enrollment pack ID |
| `variant` | Single product variant | Full variant object (resolved from the stored variant id) |

> \[!TIP\] Some types have singular/plural aliases (e.g., `product` and `products` both work for single selection). Use whichever feels more natural. `variant` has no plural alias.

#### Multiple Resource Selectors (Lists)

Select **multiple** items - direct iteration possible:

| Type | Description | Returns |
| --- | --- | --- |
| `product_list` or `products_list` | Multiple products | Array of full product objects |
| `collection_list` or `collections_list` | Multiple collections | Array of full collection objects |
| `category_list` or `categories_list` | Multiple categories | Array of full category objects |
| `posts_list` | Multiple blog posts | Array of full post objects |
| `enrollments_list` or `enrollment_list` | Multiple enrollments | Array of enrollment objects |

> \[!TIP\] Use `product_list` (singular) or `products_list` (plural) - both work the same way. Same applies to collections and categories.

**Example (Single):**

{
 "type": "product",
 "id": "featured\_product",
 "label": "Select Product"
}

**Example (Multiple):**

{
 "type": "product\_list",
 "id": "featured\_products",
 "label": "Featured Products",
 "limit": 8,
 "info": "Select up to 8 products to feature"
}

### Organization Types

| Type | Description | Use Case |
| --- | --- | --- |
| `header` | Section divider with heading | Group related settings |

**Example:**

{
 "type": "header",
 "content": "Typography Settings"
}

### Special Types

| Type | Description | Use Case |
| --- | --- | --- |
| `text_alignment` | Text alignment picker | Left/center/right alignment |
| `link_list` | Menu selector | Navigation menus |

---

## Unsupported Types

The following types from other platforms (like Shopify) are **NOT supported** in Fluid:

> \[!CAUTION\] Using these types in your `{`%`schema`%`}` will cause errors or prevent the section from rendering properly in the editor.

### Not Supported - Use Alternatives Instead

| Unsupported Type | Alternative | Notes |
| --- | --- | --- |
| `number` | Use `range` | Fluid uses range sliders for numeric inputs |
| `paragraph` | Use `header` with description | Headers can include instructional text |
| `inline_richtext` | Use `text` or `richtext` | Not available in Fluid |
| `article` | Use `post` | Fluid uses "posts" instead of "articles" |
| `article_list` | Use `posts_list` | Fluid uses "posts" instead of "articles" |
| `video` | Use `video_picker` or `url` | Different implementation in Fluid |
| `video_url` | Use `url` or `video_picker` | Use URL type for video links |
| `page` | Not available | Pages are handled differently in Fluid |
| `liquid` | Not available | Cannot inject raw Liquid code |
| `color_scheme` | Use `color` | Color schemes not implemented |
| `color_scheme_group` | Use multiple `color` settings | Group colors manually |
| `metaobject` | Not available | Metaobjects not implemented |
| `metaobject_list` | Not available | Metaobjects not implemented |

### Common Migration Tips

**Coming from Shopify?** Here's how to adapt:

// ❌ Shopify (not supported)
{
 "type": "article",
 "id": "featured\_article"
}

// ✅ Fluid (correct)
{
 "type": "post",
 "id": "featured\_post"
}

// ❌ Shopify (not supported)
{
 "type": "number",
 "id": "quantity"
}

// ✅ Fluid (correct)
{
 "type": "range",
 "id": "quantity",
 "min": 1,
 "max": 10,
 "step": 1,
 "default": 1
}

// ❌ Shopify (not supported)
{
 "type": "video\_url",
 "id": "video"
}

// ✅ Fluid (correct)
{
 "type": "url",
 "id": "video\_url",
 "label": "Video URL (YouTube or Vimeo)"
}

---

## Global Theme Settings

While section schemas control individual components, **Global Settings** control site-wide configurations.

### Configuration File

Global settings are defined in:

config/settings\_schema.json

This file uses an array of objects, where each object represents a "Tab" in the Theme Settings.

**Example:**

\[
 {
 "name": "Colors",
 "settings": \[
 {
 "type": "color",
 "id": "color\_primary",
 "label": "Primary Brand Color",
 "default": "#1C0F8A"
 },
 {
 "type": "color",
 "id": "color\_secondary",
 "label": "Secondary Color",
 "default": "#FF6B6B"
 }
 \]
 },
 {
 "name": "Typography",
 "settings": \[
 {
 "type": "font\_picker",
 "id": "font\_heading",
 "label": "Heading Font",
 "default": "helvetica\_n4"
 },
 {
 "type": "font\_picker",
 "id": "font\_body",
 "label": "Body Font",
 "default": "helvetica\_n4"
 }
 \]
 },
 {
 "name": "Social Media",
 "settings": \[
 {
 "type": "url",
 "id": "social\_instagram",
 "label": "Instagram URL"
 },
 {
 "type": "url",
 "id": "social\_facebook",
 "label": "Facebook URL"
 }
 \]
 }
\]

### Accessing Global Settings

Global settings use the `settings` object (not `section.settings`):

<style>
 :root {
 --primary-color: {{ settings.color\_primary | default: '#000000' }};
 --secondary-color: {{ settings.color\_secondary | default: '#666666' }};
 --font-heading: {{ settings.font\_heading.family }};
 --font-body: {{ settings.font\_body.family }};
 }
</style>

<a href="{{ settings.social\_instagram }}" target="\_blank">
 Follow us on Instagram
</a>

> \[!IMPORTANT\] Global settings are perfect for design tokens (colors, fonts, spacing) that should remain consistent across all pages and sections.

---

## Common Mistakes and How to Avoid Them

### ❌ Mistake 1: Using Generic IDs

**Bad:**

{
 "type": "text",
 "id": "title",
 "label": "Title"
}

**Problem:** "title" is too generic and may conflict with other sections.

**Good:**

{
 "type": "text",
 "id": "hero\_title",
 "label": "Hero Title"
}

### ❌ Mistake 2: Forgetting Money Filter

**Bad:**

<span class="price">{{ product.price }}</span>

**Result:** Displays "1999" instead of "$19.99"

**Good:**

<span class="price">{{ product.price | money }}</span>

### ❌ Mistake 3: Hardcoding Asset URLs

**Bad:**

<img src="logo.png">

**Result:** Image won't load

**Good:**

<img src="{{ 'logo.png' | asset\_url }}">

### ❌ Mistake 4: Not Using fluid\_attributes

**Bad:**

{\`%\` for block in section.blocks \`%\`}
 <div class="block">...</div>
{\`%\` endfor \`%\`}

**Result:** Blocks can't be highlighted in editor

**Good:**

{\`%\` for block in section.blocks \`%\`}
 <div class="block" {{ block.fluid\_attributes }}>...</div>
{\`%\` endfor \`%\`}

### ❌ Mistake 5: Assuming Resources Exist

**Bad:**

<h2>{{ section.settings.featured\_collection.title }}</h2>

**Result:** Crashes if no collection selected

**Good:**

{\`%\` if section.settings.featured\_collection != blank \`%\`}
 <h2>{{ section.settings.featured\_collection.title }}</h2>
{\`%\` endif \`%\`}

### ❌ Mistake 6: Invalid JSON in Schema

**Bad:**

{
 "name": "My Section",
 "settings": \[
 {
 "type": "text",
 "id": "title"
 },
 \]
}

**Problem:** Trailing comma after last item

**Good:**

{
 "name": "My Section",
 "settings": \[
 {
 "type": "text",
 "id": "title"
 }
 \]
}

---

## Troubleshooting & FAQ

### Q: Why isn't my setting showing up in the editor?

**A:** Check your JSON syntax. A missing comma, mismatched bracket, or trailing comma will prevent the section from loading. Use a JSON validator.

### Q: My `product_list` is empty even though I selected products.

**A:** Verify you're using the correct setting ID:

{\`%\`- comment -\`%\`} Schema has id: "featured\_products" {\`%\`- endcomment -\`%\`}
{\`%\` for product in section.settings.featured\_products \`%\`}
 {\`%\`- comment -\`%\`} NOT section.settings.products {\`%\`- endcomment -\`%\`}
{\`%\` endfor \`%\`}

### Q: How do I make a setting only appear if a checkbox is checked?

**A:** Currently, conditional settings (visible/hidden based on other settings) are not supported. Use clear labels and organization with headers to guide users.

### Q: Images from resource selectors look distorted.

**A:** Use CSS `object-fit: cover` on images:

.product-image {
 width: 100%;
 height: 300px;
 object-fit: cover;
}

### Q: My collection selector isn't working.

**A:** Remember to loop through the global `collections` array to find your collection:

{\`%\` assign collection\_id = block.settings.collection \`%\`}

{\`%\` for c in collections \`%\`}
 {\`%\` if c.id == collection\_id \`%\`}
 {\`%\` assign current\_collection = c \`%\`}
 {\`%\` break \`%\`}
 {\`%\` endif \`%\`}
{\`%\` endfor \`%\`}

{\`%\` if current\_collection != blank \`%\`}
 {\`%\`- comment -\`%\`} Now use current\_collection {\`%\`- endcomment -\`%\`}
{\`%\` endif \`%\`}

### Q: Pagination isn't working.

**A:** Make sure everything that needs `posts` or `products` is inside the `{`%`paginate`%`}` tags:

{\`%\` paginate posts by 12 \`%\`}
 {\`%\`- comment -\`%\`} All code accessing posts goes here {\`%\`- endcomment -\`%\`}
 {\`%\` for post in posts \`%\`}...{\`%\` endfor \`%\`}
 
 {\`%\`- comment -\`%\`} Pagination controls also go here {\`%\`- endcomment -\`%\`}
 {\`%\` if paginate.pages > 1 \`%\`}
 {\`%\` render 'pagination', paginate: paginate \`%\`}
 {\`%\` endif \`%\`}
{\`%\` endpaginate \`%\`}

---

## Quick Reference: Decision Tree

**Need to display products/collections/categories?**

├─ Hand\-picked by merchant?
│ ├─ YES → Use resource selector (product, collection, category)
│ │ with blocks for flexibility
│ └─ NO → Continue below
│
├─ Show everything automatically?
│ ├─ YES → Use global loop ({\`%\` for item in items \`%\`})
│ │ Add pagination if needed ({\`%\` paginate \`%\`})
│ └─ NO → Use resource selector
│
└─ Need pagination?
 ├─ YES → Use {\`%\` paginate \`%\`} with global loop
 └─ NO → Direct loop or resource selector

**Need to let users customize?**

├─ Section\-level settings?
│ └─ Use section.settings with schema types
│
├─ Repeatable items?
│ └─ Use blocks with block.settings
│
└─ Site\-wide settings?
 └─ Use config/settings\_schema.json

---

## Final Best Practices Summary

### Schema Design

1. **Write for the User**: Use labels like "Mobile Heading Font Size" instead of "heading\_fs\_mb"
2. **Organize with Headers**: Use `"type": "header"` to group related settings
3. **Provide Defaults**: Always include sensible default values
4. **Add Info Text**: Use `"info"` to explain complex settings
5. **Validate JSON**: Always validate before saving

### Liquid Implementation

1. **Check for Blank**: Always check if resources exist before using them
2. **Use Filters**: Apply `| money`, `| image_url`, `| unescape` correctly
3. **Include Fallbacks**: Provide multiple fallback options for images and data
4. **Add fluid\_attributes**: Include `{{ block.fluid_attributes }}` on all blocks
5. **Handle Empty States**: Show helpful messages when no content exists

### Performance

1. **Limit Selections**: Use `"limit"` on resource selectors to prevent huge selections
2. **Lazy Load Images**: Add `loading="lazy"` to images below the fold
3. **Optimize Image Sizes**: Use `| image_url: width: 600` to request appropriate sizes
4. **Use Pagination**: Always paginate large datasets
5. **Minimize Loops**: Avoid nested loops when possible

### Accessibility

1. **Alt Text**: Always provide alt text for images
2. **Semantic HTML**: Use `<article>`, `<time>`, `<nav>` appropriately
3. **Keyboard Navigation**: Ensure all interactive elements are keyboard accessible
4. **ARIA Labels**: Add aria-labels for icon-only buttons
5. **Color Contrast**: Ensure text has sufficient contrast against backgrounds

---

## Need More Help?

- **Section Examples**: Browse `app/themes/templates/` for real production examples
- **Liquid Filters**: See Liquid documentation for available filters
- **Schema Validation**: Use a JSON validator before saving schemas
- **Community**: Check internal documentation or ask the team

---

## All Schema Types: Complete Reference

This section contains the complete liquid implementation and schema settings for all supported schema types in the Fluid theme system.

### Liquid Implementation

<section 
 class="all-schema-type-section {{ section.settings.background\_color | default: 'bg-white' }}"
 style="padding-top: var(--padding-2xl); padding-bottom: var(--padding-2xl);"
 >
 <div class="all-schema-type-container container">
 
 <!-- Section Heading -->
 {\`%\` if section.settings.section\_heading != blank \`%\`}
 <div class="section-heading mb-2xl text-center">
 <h1 class="{{ section.settings.heading\_font\_size | default: 'text-3xl' }} {{ section.settings.heading\_font\_size\_desktop | default: 'lg:text-5xl' }} {{ section.settings.heading\_font\_weight | default: 'font-bold' }} {{ section.settings.heading\_color | default: 'text-black' }}">
 {{ section.settings.section\_heading }}
 </h1>
 {\`%\` if section.settings.section\_subheading != blank \`%\`}
 <p class="{{ section.settings.subheading\_font\_size | default: 'text-base' }} {{ section.settings.subheading\_color | default: 'text-neutral-dark' }} mt-md">
 {{ section.settings.section\_subheading }}
 </p>
 {\`%\` endif \`%\`}
 </div>
 {\`%\` endif \`%\`}
 
 <!-- TEXT INPUT TYPES -->
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Text Input Types</h2>
 
 <div class="schema-grid">
 <!-- Text -->
 {\`%\` if section.settings.text\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Text Field:</label>
 <div class="schema-value">{{ section.settings.text\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Plaintext -->
 {\`%\` if section.settings.plaintext\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Plaintext Field:</label>
 <div class="schema-value">{{ section.settings.plaintext\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Textarea -->
 {\`%\` if section.settings.textarea\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Textarea Field:</label>
 <div class="schema-value">{{ section.settings.textarea\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Richtext -->
 {\`%\` if section.settings.richtext\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Richtext Field:</label>
 <div class="schema-value richtext-content">{{ section.settings.richtext\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- HTML -->
 {\`%\` if section.settings.html\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">HTML Field:</label>
 <div class="schema-value html-content">{{ section.settings.html\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- URL -->
 {\`%\` if section.settings.url\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">URL Field:</label>
 <div class="schema-value">
 <a href="{{ section.settings.url\_field }}" target="\_blank" class="text-primary hover:underline">
 {{ section.settings.url\_field }}
 </a>
 </div>
 </div>
 {\`%\` endif \`%\`}
 </div>
 </div>
 
 <!-- NUMBER & SELECTION TYPES -->
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Number & Selection Types</h2>
 
 <div class="schema-grid">
 <!-- Range -->
 {\`%\` if section.settings.range\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Range Field:</label>
 <div class="schema-value">{{ section.settings.range\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Select -->
 {\`%\` if section.settings.select\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Select Field:</label>
 <div class="schema-value">{{ section.settings.select\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Radio -->
 {\`%\` if section.settings.radio\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Radio Field:</label>
 <div class="schema-value">{{ section.settings.radio\_field }}</div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Checkbox -->
 {\`%\` if section.settings.show\_checkbox\_field \`%\`}
 <div class="schema-item">
 <label class="schema-label">Checkbox Field:</label>
 <div class="schema-value">
 {\`%\` if section.settings.checkbox\_field \`%\`}
 <span class="text-success">✓ Enabled</span>
 {\`%\` else \`%\`}
 <span class="text-neutral-medium">✗ Disabled</span>
 {\`%\` endif \`%\`}
 </div>
 </div>
 {\`%\` endif \`%\`}
 </div>
 </div>
 
 <!-- VISUAL & MEDIA TYPES -->
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Visual & Media Types</h2>
 
 <div class="schema-grid">
 <!-- Color -->
 {\`%\` if section.settings.color\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Color Field:</label>
 <div class="schema-value flex items-center gap-sm">
 <div class="color-swatch" style="background-color: {{ section.settings.color\_field }}; width: 40px; height: 40px; border-radius: 4px; border: 1px solid #ddd;"></div>
 <span>{{ section.settings.color\_field }}</span>
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Color Background -->
 {\`%\` if section.settings.color\_background\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Color Background Field:</label>
 <div class="schema-value">
 <div class="color-background-preview" style="background: {{ section.settings.color\_background\_field }}; width: 100%; height: 60px; border-radius: 4px; border: 1px solid #ddd;"></div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Font Picker -->
 {\`%\` if section.settings.font\_picker\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Font Picker Field:</label>
 <div class="schema-value" style="font-family: {{ section.settings.font\_picker\_field }};">
 Sample Text: {{ section.settings.font\_picker\_field }}
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Image Picker -->
 {\`%\` if section.settings.image\_picker\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Image Picker Field:</label>
 <div class="schema-value">
 <img 
 src="{{ section.settings.image\_picker\_field | image\_url: width: 400 }}" 
 alt="{{ section.settings.image\_alt\_text | default: 'Image' }}"
 class="schema-image"
 loading="lazy">
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Video Picker -->
 {\`%\` if section.settings.video\_picker\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Video Picker Field:</label>
 <div class="schema-value">
 <video 
 controls 
 class="schema-video"
 {\`%\` if section.settings.video\_poster != blank \`%\`}
 poster="{{ section.settings.video\_poster | image\_url: width: 800 }}"
 {\`%\` endif \`%\`}>
 <source src="{{ section.settings.video\_picker\_field }}" type="video/mp4">
 Your browser does not support the video tag.
 </video>
 </div>
 </div>
 {\`%\` endif \`%\`}

 <!-- Media Picker (stores an object, not a URL) -->
 {\`%\` if section.settings.media\_picker\_field != blank and section.settings.media\_picker\_field.url != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Media Picker Field:</label>
 <div class="schema-value">
 {\`%\` if section.settings.media\_picker\_field.fluid\_media\_id > 0 \`%\`}
 <fluid-media-widget
 media-id="{{ section.settings.media\_picker\_field.fluid\_media\_id }}"
 embed-type="{{ section.settings.media\_picker\_field.embed\_type | default: 'default' }}"
 responsive="true"
 data-fluid-widget="true">
 </fluid-media-widget>
 {\`%\` else \`%\`}
 <img
 src="{{ section.settings.media\_picker\_field.url | image\_url: width: 400 }}"
 alt="{{ section.settings.media\_picker\_field\_alt | default: 'Media' }}"
 class="schema-image"
 loading="lazy">
 {\`%\` endif \`%\`}
 </div>
 </div>
 {\`%\` endif \`%\`}
 </div>
 </div>
 
 <!-- RESOURCE SELECTORS - SINGLE -->
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Resource Selectors (Single)</h2>
 
 <div class="schema-grid">
 <!-- Product -->
 {\`%\` if section.settings.product\_field != blank \`%\`}
 {\`%\`- assign product\_setting = section.settings.product\_field -\`%\`}
 {\`%\`- assign selected\_product = blank -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if it's already a full product object {\`%\`- endcomment -\`%\`}
 {\`%\`- if product\_setting.title != blank and product\_setting.url != blank -\`%\`}
 {\`%\`- assign selected\_product = product\_setting -\`%\`}
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Extract ID from object or use directly {\`%\`- endcomment -\`%\`}
 {\`%\`- assign product\_id = blank -\`%\`}
 {\`%\`- if product\_setting.id != blank -\`%\`}
 {\`%\`- assign product\_id = product\_setting.id -\`%\`}
 {\`%\`- elsif product\_setting != blank -\`%\`}
 {\`%\`- assign product\_id = product\_setting -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\`- comment -\`%\`} Find product from global products array {\`%\`- endcomment -\`%\`}
 {\`%\`- if product\_id != blank -\`%\`}
 {\`%\`- if products != blank -\`%\`}
 {\`%\`- for p in products -\`%\`}
 {\`%\`- if p.id == product\_id -\`%\`}
 {\`%\`- assign selected\_product = p -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\` if selected\_product != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Product Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 <a href="{{ selected\_product.url }}" class="resource-card">
 {\`%\` assign product\_image = '' \`%\`}
 {\`%\` if selected\_product.image\_url \`%\`}
 {\`%\` assign product\_image = selected\_product.image\_url | image\_url: width: 200 \`%\`}
 {\`%\` elsif selected\_product.image \`%\`}
 {\`%\` assign product\_image = selected\_product.image | image\_url: width: 200 \`%\`}
 {\`%\` elsif selected\_product.images.size > 0 \`%\`}
 {\`%\` assign product\_image = selected\_product.images.first | image\_url: width: 200 \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if product\_image != '' \`%\`}
 <img src="{{ product\_image }}" alt="{{ selected\_product.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ selected\_product.title }}</div>
 <div class="text-sm text-neutral-medium">{{ selected\_product.price | money }}</div>
 </div>
 </a>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 
 <!-- Collection -->
 {\`%\` if section.settings.collection\_field != blank \`%\`}
 {\`%\`- assign collection\_setting = section.settings.collection\_field -\`%\`}
 {\`%\`- assign selected\_collection = blank -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if it's already a full collection object {\`%\`- endcomment -\`%\`}
 {\`%\`- if collection\_setting.title != blank -\`%\`}
 {\`%\`- assign selected\_collection = collection\_setting -\`%\`}
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Extract ID from object or use directly {\`%\`- endcomment -\`%\`}
 {\`%\`- assign collection\_id = blank -\`%\`}
 {\`%\`- if collection\_setting.id != blank -\`%\`}
 {\`%\`- assign collection\_id = collection\_setting.id -\`%\`}
 {\`%\`- elsif collection\_setting != blank -\`%\`}
 {\`%\`- assign collection\_id = collection\_setting -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\`- comment -\`%\`} Find collection from global collections array {\`%\`- endcomment -\`%\`}
 {\`%\`- if collection\_id != blank -\`%\`}
 {\`%\`- if collections != blank -\`%\`}
 {\`%\`- for c in collections -\`%\`}
 {\`%\`- if c.id == collection\_id -\`%\`}
 {\`%\`- assign selected\_collection = c -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\` if selected\_collection != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Collection Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 <a href="{{ selected\_collection.url }}" class="resource-card">
 {\`%\` assign collection\_image = '' \`%\`}
 {\`%\` if selected\_collection.image \`%\`}
 {\`%\` assign collection\_image = selected\_collection.image | image\_url: width: 200 \`%\`}
 {\`%\` elsif selected\_collection.image\_url \`%\`}
 {\`%\` assign collection\_image = selected\_collection.image\_url \`%\`}
 {\`%\` elsif selected\_collection.products.size > 0 and selected\_collection.products.first.images.size > 0 \`%\`}
 {\`%\` assign collection\_image = selected\_collection.products.first.images.first.src \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if collection\_image != '' \`%\`}
 <img src="{{ collection\_image }}" alt="{{ selected\_collection.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ selected\_collection.title }}</div>
 <div class="text-sm text-neutral-medium">{{ selected\_collection.products\_count }} products</div>
 </div>
 </a>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 
 <!-- Category -->
 {\`%\` if section.settings.category\_field != blank \`%\`}
 {\`%\`- assign category\_setting = section.settings.category\_field -\`%\`}
 {\`%\`- assign selected\_category = blank -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if it's already a full category object {\`%\`- endcomment -\`%\`}
 {\`%\`- if category\_setting.title != blank -\`%\`}
 {\`%\`- assign selected\_category = category\_setting -\`%\`}
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Extract ID from object or use directly {\`%\`- endcomment -\`%\`}
 {\`%\`- assign category\_id = blank -\`%\`}
 {\`%\`- if category\_setting.id != blank -\`%\`}
 {\`%\`- assign category\_id = category\_setting.id -\`%\`}
 {\`%\`- elsif category\_setting != blank -\`%\`}
 {\`%\`- assign category\_id = category\_setting -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\`- comment -\`%\`} Find category from global categories array {\`%\`- endcomment -\`%\`}
 {\`%\`- if category\_id != blank -\`%\`}
 {\`%\`- if categories != blank -\`%\`}
 {\`%\`- for cat in categories -\`%\`}
 {\`%\`- if cat.id == category\_id -\`%\`}
 {\`%\`- assign selected\_category = cat -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\` if selected\_category != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Category Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 <a href="{{ selected\_category.url }}" class="resource-card">
 {\`%\` if selected\_category.image\_url \`%\`}
 <img src="{{ selected\_category.image\_url | image\_url: width: 200 }}" alt="{{ selected\_category.title }}" class="resource-image">
 {\`%\` elsif selected\_category.image \`%\`}
 <img src="{{ selected\_category.image | image\_url: width: 200 }}" alt="{{ selected\_category.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ selected\_category.title }}</div>
 {\`%\` if selected\_category.description != blank \`%\`}
 <div class="text-sm text-neutral-medium">{{ selected\_category.description | strip\_html | truncate: 60 }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 
 <!-- Post (using posts schema type) -->
 {\`%\` if section.settings.post\_field != blank \`%\`}
 {\`%\`- assign post\_setting = section.settings.post\_field -\`%\`}
 {\`%\`- assign selected\_post = blank -\`%\`}
 
 {\`%\`- comment -\`%\`} Try to access post - if it's already a full object, use it directly {\`%\`- endcomment -\`%\`}
 {\`%\`- if post\_setting.title != blank -\`%\`}
 {\`%\`- assign selected\_post = post\_setting -\`%\`}
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} If it's an ID, we need to look it up in posts array (only available in paginate) {\`%\`- endcomment -\`%\`}
 {\`%\`- assign post\_id = post\_setting.id | default: post\_setting -\`%\`}
 
 {\`%\`- if post\_id != blank -\`%\`}
 {\`%\`- paginate posts by 1000 -\`%\`}
 {\`%\`- for p in posts -\`%\`}
 {\`%\`- assign id\_to\_match = post\_id | plus: 0 -\`%\`}
 {\`%\`- if p.id == id\_to\_match or p.id == post\_id or p.slug == post\_id -\`%\`}
 {\`%\`- assign selected\_post = p -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endpaginate -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\` if selected\_post != blank and selected\_post.title != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Post Field (posts type):</label>
 <div class="schema-value">
 <div class="resource-grid">
 <a href="{{ selected\_post.preview\_url }}" class="resource-card">
 {\`%\` assign image\_url = '' \`%\`}
 {\`%\` if selected\_post.image\_url \`%\`}
 {\`%\` assign image\_url = selected\_post.image\_url \`%\`}
 {\`%\` elsif selected\_post.image \`%\`}
 {\`%\` assign image\_url = selected\_post.image | image\_url \`%\`}
 {\`%\` elsif selected\_post.images.size > 0 \`%\`}
 {\`%\` assign image\_url = selected\_post.images\[0\].src \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if image\_url != '' \`%\`}
 <img src="{{ image\_url }}" alt="{{ selected\_post.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ selected\_post.title }}</div>
 {\`%\` if selected\_post.summary \`%\`}
 <div class="text-sm text-neutral-medium">{{ selected\_post.summary | strip\_html | truncate: 60 }}</div>
 {\`%\` elsif selected\_post.description \`%\`}
 <div class="text-sm text-neutral-medium">{{ selected\_post.description | strip\_html | truncate: 60 }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}

 <!-- Enrollment Pack -->
 {\`%\` if section.settings.enrollment\_pack\_field != blank \`%\`}
 {\`%\`- assign enrollment\_pack\_setting = section.settings.enrollment\_pack\_field -\`%\`}
 {\`%\`- assign selected\_enrollment\_pack = blank -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if it's already a full enrollment\_pack object {\`%\`- endcomment -\`%\`}
 {\`%\`- if enrollment\_pack\_setting.title != blank -\`%\`}
 {\`%\`- assign selected\_enrollment\_pack = enrollment\_pack\_setting -\`%\`}
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Extract ID from object or use directly {\`%\`- endcomment -\`%\`}
 {\`%\`- assign enrollment\_pack\_id = enrollment\_pack\_setting.id | default: enrollment\_pack\_setting -\`%\`}
 
 {\`%\`- comment -\`%\`} Enrollment packs are resolved by backend, so if it's an ID, it should be looked up {\`%\`- endcomment -\`%\`}
 {\`%\`- if enrollment\_pack\_id != blank -\`%\`}
 {\`%\`- comment -\`%\`} Backend resolves enrollment\_pack type, so setting should already be full object {\`%\`- endcomment -\`%\`}
 {\`%\`- assign selected\_enrollment\_pack = enrollment\_pack\_setting -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\` if selected\_enrollment\_pack != blank and selected\_enrollment\_pack.title != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Enrollment Pack Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 <a href="{{ selected\_enrollment\_pack.url }}" class="resource-card">
 {\`%\` assign enrollment\_pack\_image = '' \`%\`}
 {\`%\` if selected\_enrollment\_pack.images\_array.size > 0 \`%\`}
 {\`%\` assign enrollment\_pack\_image = selected\_enrollment\_pack.images\_array\[0\] \`%\`}
 {\`%\` elsif selected\_enrollment\_pack.images.size > 0 \`%\`}
 {\`%\` assign enrollment\_pack\_image = selected\_enrollment\_pack.images\[0\] \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if enrollment\_pack\_image != '' \`%\`}
 <img src="{{ enrollment\_pack\_image }}" alt="{{ selected\_enrollment\_pack.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ selected\_enrollment\_pack.title }}</div>
 {\`%\` if selected\_enrollment\_pack.price \`%\`}
 <div class="text-sm text-neutral-medium">{{ selected\_enrollment\_pack.price }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}

 <!-- Enrollment -->
 {\`%\` if section.settings.enrollment\_field != blank \`%\`}
 {\`%\`- assign enrollment\_setting = section.settings.enrollment\_field -\`%\`}
 {\`%\`- assign selected\_enrollment = blank -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if it's already a full enrollment object {\`%\`- endcomment -\`%\`}
 {\`%\`- if enrollment\_setting.title != blank -\`%\`}
 {\`%\`- assign selected\_enrollment = enrollment\_setting -\`%\`}
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Extract ID from object or use directly {\`%\`- endcomment -\`%\`}
 {\`%\`- assign enrollment\_id = enrollment\_setting.id | default: enrollment\_setting -\`%\`}
 
 {\`%\`- comment -\`%\`} Enrollments are resolved by backend, so if it's an ID, it should be looked up {\`%\`- endcomment -\`%\`}
 {\`%\`- if enrollment\_id != blank -\`%\`}
 {\`%\`- comment -\`%\`} Backend resolves enrollment type, so setting should already be full object {\`%\`- endcomment -\`%\`}
 {\`%\`- assign selected\_enrollment = enrollment\_setting -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 
 {\`%\` if selected\_enrollment != blank and selected\_enrollment.title != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Enrollment Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 <a href="{{ selected\_enrollment.url }}" class="resource-card">
 {\`%\` assign enrollment\_image = '' \`%\`}
 {\`%\` if selected\_enrollment.images\_array.size > 0 \`%\`}
 {\`%\` assign enrollment\_image = selected\_enrollment.images\_array\[0\] \`%\`}
 {\`%\` elsif selected\_enrollment.images.size > 0 \`%\`}
 {\`%\` assign enrollment\_image = selected\_enrollment.images\[0\] \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if enrollment\_image != '' \`%\`}
 <img src="{{ enrollment\_image }}" alt="{{ selected\_enrollment.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ selected\_enrollment.title }}</div>
 {\`%\` if selected\_enrollment.price \`%\`}
 <div class="text-sm text-neutral-medium">{{ selected\_enrollment.price }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\` endif \`%\`}
 </div>
 </div>
 
 <!-- RESOURCE SELECTORS - MULTIPLE (LISTS) -->
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Resource Selectors (Multiple Lists)</h2>
 
 <!-- Product List -->
 {\`%\` if section.settings.product\_list\_field.size > 0 \`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Product List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\` for product in section.settings.product\_list\_field \`%\`}
 <a href="{{ product.url }}" class="resource-card">
 {\`%\` if product.image\_url \`%\`}
 <img src="{{ product.image\_url | image\_url: width: 200 }}" alt="{{ product.title }}" class="resource-image">
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ product.title }}</div>
 <div class="text-sm text-neutral-medium">{{ product.price | money }}</div>
 </div>
 </a>
 {\`%\` endfor \`%\`}
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Collection List -->
 {\`%\` if section.settings.collection\_list\_field != blank \`%\`}
 {\`%\`- assign collection\_list\_setting = section.settings.collection\_list\_field -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if already resolved (has title) or needs ID lookup {\`%\`- endcomment -\`%\`}
 {\`%\`- if collection\_list\_setting.size > 0 -\`%\`}
 {\`%\`- assign first\_item = collection\_list\_setting\[0\] -\`%\`}
 {\`%\`- if first\_item.title != blank -\`%\`}
 {\`%\`- comment -\`%\`} Already resolved - use directly {\`%\`- endcomment -\`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Collection List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\` for collection in collection\_list\_setting \`%\`}
 <a href="{{ collection.url }}" class="resource-card">
 {\`%\` assign collection\_image = '' \`%\`}
 {\`%\` if collection.image \`%\`}
 {\`%\` assign collection\_image = collection.image | image\_url: width: 200 \`%\`}
 {\`%\` elsif collection.image\_url \`%\`}
 {\`%\` assign collection\_image = collection.image\_url \`%\`}
 {\`%\` elsif collection.products.size > 0 and collection.products.first.images.size > 0 \`%\`}
 {\`%\` assign collection\_image = collection.products.first.images.first.src \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if collection\_image != '' \`%\`}
 <img src="{{ collection\_image }}" alt="{{ collection.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ collection.title }}</div>
 <div class="text-sm text-neutral-medium">{{ collection.products\_count }} products</div>
 </div>
 </a>
 {\`%\` endfor \`%\`}
 </div>
 </div>
 </div>
 {\`%\`- elsif collections != blank -\`%\`}
 {\`%\`- comment -\`%\`} Array of IDs - look them up in global collections array {\`%\`- endcomment -\`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Collection List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\`- for collection\_id in collection\_list\_setting -\`%\`}
 {\`%\`- assign id\_to\_check = collection\_id.id | default: collection\_id -\`%\`}
 {\`%\`- for c in collections -\`%\`}
 {\`%\`- if c.id == id\_to\_check or c.slug == id\_to\_check -\`%\`}
 <a href="{{ c.url }}" class="resource-card">
 {\`%\` assign collection\_image = '' \`%\`}
 {\`%\` if c.image \`%\`}
 {\`%\` assign collection\_image = c.image | image\_url: width: 200 \`%\`}
 {\`%\` elsif c.image\_url \`%\`}
 {\`%\` assign collection\_image = c.image\_url \`%\`}
 {\`%\` elsif c.products.size > 0 and c.products.first.images.size > 0 \`%\`}
 {\`%\` assign collection\_image = c.products.first.images.first.src \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if collection\_image != '' \`%\`}
 <img src="{{ collection\_image }}" alt="{{ c.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ c.title }}</div>
 <div class="text-sm text-neutral-medium">{{ c.products\_count }} products</div>
 </div>
 </a>
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endfor -\`%\`}
 </div>
 </div>
 </div>
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\` endif \`%\`}
 
 <!-- Category List -->
 {\`%\` if section.settings.category\_list\_field != blank \`%\`}
 {\`%\`- assign category\_list\_setting = section.settings.category\_list\_field -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if already resolved (has title) or needs ID lookup {\`%\`- endcomment -\`%\`}
 {\`%\`- if category\_list\_setting.size > 0 -\`%\`}
 {\`%\`- assign first\_item = category\_list\_setting\[0\] -\`%\`}
 {\`%\`- if first\_item.title != blank -\`%\`}
 {\`%\`- comment -\`%\`} Already resolved - use directly {\`%\`- endcomment -\`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Category List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\` for category in category\_list\_setting \`%\`}
 <a href="{{ category.url }}" class="resource-card">
 {\`%\` if category.image\_url \`%\`}
 <img src="{{ category.image\_url | image\_url: width: 200 }}" alt="{{ category.title }}" class="resource-image">
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ category.title }}</div>
 {\`%\` if category.description \`%\`}
 <div class="text-sm text-neutral-medium">{{ category.description | strip\_html | truncate: 60 }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 {\`%\` endfor \`%\`}
 </div>
 </div>
 </div>
 {\`%\`- elsif categories != blank -\`%\`}
 {\`%\`- comment -\`%\`} Array of IDs - look them up in global categories array {\`%\`- endcomment -\`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Category List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\`- for category\_id in category\_list\_setting -\`%\`}
 {\`%\`- assign id\_to\_check = category\_id.id | default: category\_id -\`%\`}
 {\`%\`- for cat in categories -\`%\`}
 {\`%\`- if cat.id == id\_to\_check or cat.slug == id\_to\_check -\`%\`}
 <a href="{{ cat.url }}" class="resource-card">
 {\`%\` if cat.image\_url \`%\`}
 <img src="{{ cat.image\_url | image\_url: width: 200 }}" alt="{{ cat.title }}" class="resource-image">
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ cat.title }}</div>
 {\`%\` if cat.description \`%\`}
 <div class="text-sm text-neutral-medium">{{ cat.description | strip\_html | truncate: 60 }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endfor -\`%\`}
 </div>
 </div>
 </div>
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\` endif \`%\`}
 
 <!-- Posts List -->
 {\`%\` if section.settings.posts\_list\_field != blank \`%\`}
 {\`%\`- assign posts\_list\_setting = section.settings.posts\_list\_field -\`%\`}
 
 {\`%\`- comment -\`%\`} Check if already resolved (has title) or needs ID lookup {\`%\`- endcomment -\`%\`}
 {\`%\`- if posts\_list\_setting.size > 0 -\`%\`}
 {\`%\`- assign first\_item = posts\_list\_setting\[0\] -\`%\`}
 {\`%\`- if first\_item.title != blank -\`%\`}
 {\`%\`- comment -\`%\`} Already resolved - use directly {\`%\`- endcomment -\`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Posts List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\` for post in posts\_list\_setting \`%\`}
 <a href="{{ post.preview\_url }}" class="resource-card">
 {\`%\` assign image\_url = '' \`%\`}
 {\`%\` if post.image\_url \`%\`}
 {\`%\` assign image\_url = post.image\_url \`%\`}
 {\`%\` elsif post.image \`%\`}
 {\`%\` assign image\_url = post.image | image\_url \`%\`}
 {\`%\` elsif post.images.size > 0 \`%\`}
 {\`%\` assign image\_url = post.images\[0\].src \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if image\_url != '' \`%\`}
 <img src="{{ image\_url }}" alt="{{ post.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ post.title }}</div>
 {\`%\` if post.summary \`%\`}
 <div class="text-sm text-neutral-medium">{{ post.summary | strip\_html | truncate: 60 }}</div>
 {\`%\` elsif post.description \`%\`}
 <div class="text-sm text-neutral-medium">{{ post.description | strip\_html | truncate: 60 }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 {\`%\` endfor \`%\`}
 </div>
 </div>
 </div>
 {\`%\`- else -\`%\`}
 {\`%\`- comment -\`%\`} Array of IDs - need to look them up using paginate {\`%\`- endcomment -\`%\`}
 {\`%\`- paginate posts by 1000 -\`%\`}
 {\`%\`- assign found\_any = false -\`%\`}
 {\`%\`- comment -\`%\`} Quick check if any posts exist before rendering {\`%\`- endcomment -\`%\`}
 {\`%\`- for post\_id in posts\_list\_setting -\`%\`}
 {\`%\`- assign id\_to\_check = post\_id.id | default: post\_id -\`%\`}
 {\`%\`- assign id\_to\_match = id\_to\_check | plus: 0 -\`%\`}
 {\`%\`- for p in posts -\`%\`}
 {\`%\`- if p.id == id\_to\_match or p.id == id\_to\_check or p.slug == id\_to\_check -\`%\`}
 {\`%\`- assign found\_any = true -\`%\`}
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- if found\_any -\`%\`}{\`%\`- break -\`%\`}{\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 
 {\`%\` if found\_any \`%\`}
 <div class="schema-item mb-lg">
 <label class="schema-label">Posts List Field:</label>
 <div class="schema-value">
 <div class="resource-grid">
 {\`%\`- for post\_id in posts\_list\_setting -\`%\`}
 {\`%\`- assign id\_to\_check = post\_id.id | default: post\_id -\`%\`}
 {\`%\`- assign id\_to\_match = id\_to\_check | plus: 0 -\`%\`}
 {\`%\`- for p in posts -\`%\`}
 {\`%\`- if p.id == id\_to\_match or p.id == id\_to\_check or p.slug == id\_to\_check -\`%\`}
 <a href="{{ p.preview\_url }}" class="resource-card">
 {\`%\` assign image\_url = '' \`%\`}
 {\`%\` if p.image\_url \`%\`}
 {\`%\` assign image\_url = p.image\_url \`%\`}
 {\`%\` elsif p.image \`%\`}
 {\`%\` assign image\_url = p.image | image\_url \`%\`}
 {\`%\` elsif p.images.size > 0 \`%\`}
 {\`%\` assign image\_url = p.images\[0\].src \`%\`}
 {\`%\` endif \`%\`}
 {\`%\` if image\_url != '' \`%\`}
 <img src="{{ image\_url }}" alt="{{ p.title }}" class="resource-image">
 {\`%\` else \`%\`}
 <div class="resource-image-placeholder">
 <span class="text-neutral-medium">No image</span>
 </div>
 {\`%\` endif \`%\`}
 <div class="resource-info">
 <div class="font-semibold">{{ p.title }}</div>
 {\`%\` if p.summary \`%\`}
 <div class="text-sm text-neutral-medium">{{ p.summary | strip\_html | truncate: 60 }}</div>
 {\`%\` elsif p.description \`%\`}
 <div class="text-sm text-neutral-medium">{{ p.description | strip\_html | truncate: 60 }}</div>
 {\`%\` endif \`%\`}
 </div>
 </a>
 {\`%\`- break -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endfor -\`%\`}
 {\`%\`- endfor -\`%\`}
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 {\`%\`- endpaginate -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\`- endif -\`%\`}
 {\`%\` endif \`%\`}

 </div>
 
 <!-- SPECIAL TYPES -->
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Special Types</h2>
 
 <div class="schema-grid">
 <!-- Text Alignment -->
 {\`%\` if section.settings.text\_alignment\_field != blank \`%\`}
 <div class="schema-item">
 <label class="schema-label">Text Alignment Field:</label>
 <div class="schema-value" style="text-align: {{ section.settings.text\_alignment\_field }};">
 This text is aligned {{ section.settings.text\_alignment\_field }}
 </div>
 </div>
 {\`%\` endif \`%\`}
 
 <!-- Link List (Menu) -->
 {\`%\` if section.settings.link\_list\_field != blank and section.settings.link\_list\_field.menu\_items.size > 0 \`%\`}
 <div class="schema-item">
 <label class="schema-label">Link List Field:</label>
 <div class="schema-value">
 <div class="link-list-menu">
 <div class="font-semibold mb-md">{{ section.settings.link\_list\_field.title }}</div>
 <ul class="link-list-items" style="list-style: none; padding: 0;">
 {\`%\` for item in section.settings.link\_list\_field.menu\_items \`%\`}
 <li class="mb-sm">
 <a href="{{ item.url }}" class="text-primary hover:underline">
 {{ item.title }}
 </a>
 {\`%\` if item.sub\_menu\_items.size > 0 \`%\`}
 <ul class="ml-lg mt-sm" style="list-style: none;">
 {\`%\` for sub\_item in item.sub\_menu\_items \`%\`}
 <li class="mb-xs">
 <a href="{{ sub\_item.url }}" class="text-sm text-neutral-medium hover:underline">
 {{ sub\_item.title }}
 </a>
 </li>
 {\`%\` endfor \`%\`}
 </ul>
 {\`%\` endif \`%\`}
 </li>
 {\`%\` endfor \`%\`}
 </ul>
 </div>
 </div>
 </div>
 {\`%\` endif \`%\`}
 </div>
 </div>
 
 <!-- BLOCKS DEMONSTRATION - Displayed separately by block type at bottom -->
 
 <div class="schema-group mb-3xl">
 <h2 class="group-title mb-xl">Blocks Demonstration</h2>
 
 <!-- Demo Blocks -->
 {\`%\` assign demo\_blocks = section.blocks | where: "type", "demo\_block" \`%\`}
 {\`%\` if demo\_blocks.size > 0 \`%\`}
 <div class="block-type-group mb-2xl">
 <h3 class="block-type-title">Demo Blocks</h3>
 <div class="blocks-container">
 {\`%\` for block in demo\_blocks \`%\`}
 <div class="block-item" {{ block.fluid\_attributes }}>
 <div class="block-content">
 <h4 class="block-title">{{ block.settings.block\_title | default: 'Demo Block' }}</h4>
 {\`%\` if block.settings.block\_text != blank \`%\`}
 <p class="block-text">{{ block.settings.block\_text }}</p>
 {\`%\` endif \`%\`}
 {\`%\` if block.settings.block\_image != blank \`%\`}
 <img src="{{ block.settings.block\_image | image\_url: width: 400 }}" alt="{{ block.settings.block\_title }}" class="block-image">
 {\`%\` endif \`%\`}
 </div>
 </div>
 {\`%\` endfor \`%\`}
 </div>
 </div>
 {\`%\` endif \`%\`}

 </div>
 
 </div>
 </section>
 

### Schema Settings

{\`%\` schema \`%\`}
{
 "name": "All Schema Types",
 "tag": "section",
 "class": "all-schema-type-section",
 "settings": \[
 {
 "type": "header",
 "content": "Section Heading"
 },
 {
 "type": "text",
 "id": "section\_heading",
 "label": "Section Heading",
 "default": "All Schema Types Demonstration"
 },
 {
 "type": "textarea",
 "id": "section\_subheading",
 "label": "Section Subheading",
 "default": "This section demonstrates all supported schema types in Fluid theme system"
 },
 {
 "type": "select",
 "id": "heading\_font\_size",
 "label": "Heading Font Size (Mobile)",
 "options": \[
 { "value": "text-xl", "label": "Extra Large" },
 { "value": "text-2xl", "label": "2X Large" },
 { "value": "text-3xl", "label": "3X Large" },
 { "value": "text-4xl", "label": "4X Large" }
 \],
 "default": "text-3xl"
 },
 {
 "type": "select",
 "id": "heading\_font\_size\_desktop",
 "label": "Heading Font Size (Desktop)",
 "options": \[
 { "value": "lg:text-3xl", "label": "3X Large" },
 { "value": "lg:text-4xl", "label": "4X Large" },
 { "value": "lg:text-5xl", "label": "5X Large" },
 { "value": "lg:text-6xl", "label": "6X Large" }
 \],
 "default": "lg:text-5xl"
 },
 {
 "type": "select",
 "id": "heading\_font\_weight",
 "label": "Heading Font Weight",
 "options": \[
 { "value": "font-normal", "label": "Normal" },
 { "value": "font-medium", "label": "Medium" },
 { "value": "font-semibold", "label": "Semibold" },
 { "value": "font-bold", "label": "Bold" }
 \],
 "default": "font-bold"
 },
 {
 "type": "select",
 "id": "heading\_color",
 "label": "Heading Color",
 "options": \[
 { "value": "text-primary", "label": "Primary" },
 { "value": "text-black", "label": "Black" },
 { "value": "text-neutral-dark", "label": "Neutral Dark" }
 \],
 "default": "text-black"
 },
 {
 "type": "select",
 "id": "subheading\_font\_size",
 "label": "Subheading Font Size",
 "options": \[
 { "value": "text-sm", "label": "Small" },
 { "value": "text-base", "label": "Base" },
 { "value": "text-lg", "label": "Large" }
 \],
 "default": "text-base"
 },
 {
 "type": "select",
 "id": "subheading\_color",
 "label": "Subheading Color",
 "options": \[
 { "value": "text-neutral-dark", "label": "Neutral Dark" },
 { "value": "text-neutral", "label": "Neutral" },
 { "value": "text-neutral-medium", "label": "Neutral Medium" }
 \],
 "default": "text-neutral-dark"
 },
 {
 "type": "header",
 "content": "Text Input Types"
 },
 {
 "type": "text",
 "id": "text\_field",
 "label": "Text Field",
 "default": "This is a text field example",
 "info": "Single-line text input"
 },
 {
 "type": "plaintext",
 "id": "plaintext\_field",
 "label": "Plaintext Field",
 "default": "Single-line plain text — no formatting",
 "info": "Single-line plain text input with no rich-text formatting"
 },
 {
 "type": "textarea",
 "id": "textarea\_field",
 "label": "Textarea Field",
 "default": "This is a textarea field example.\\nIt supports multiple lines of plain text.",
 "info": "Multi-line plain text input"
 },
 {
 "type": "richtext",
 "id": "richtext\_field",
 "label": "Richtext Field",
 "default": "<p>This is a <strong>richtext</strong> field example with <em>formatting</em> support.</p><ul><li>List item 1</li><li>List item 2</li></ul>",
 "info": "Rich text editor with formatting options"
 },
 {
 "type": "html",
 "id": "html\_field",
 "label": "HTML Field",
 "default": "<div style='padding: 16px; background: #f0f0f0; border-radius: 4px;'><p>This is raw HTML content</p></div>",
 "info": "Raw HTML input for custom code"
 },
 {
 "type": "url",
 "id": "url\_field",
 "label": "URL Field",
 "default": "https://example.com",
 "info": "URL input with validation"
 },
 {
 "type": "header",
 "content": "Number & Selection Types"
 },
 {
 "type": "range",
 "id": "range\_field",
 "label": "Range Field",
 "min": 0,
 "max": 100,
 "step": 5,
 "default": 50,
 "unit": "px",
 "info": "Slider for numeric values"
 },
 {
 "type": "select",
 "id": "select\_field",
 "label": "Select Field",
 "options": \[
 { "value": "option1", "label": "Option 1" },
 { "value": "option2", "label": "Option 2" },
 { "value": "option3", "label": "Option 3" }
 \],
 "default": "option1",
 "info": "Dropdown menu selection"
 },
 {
 "type": "radio",
 "id": "radio\_field",
 "label": "Radio Field",
 "options": \[
 { "value": "radio1", "label": "Radio Option 1" },
 { "value": "radio2", "label": "Radio Option 2" },
 { "value": "radio3", "label": "Radio Option 3" }
 \],
 "default": "radio1",
 "info": "Radio button selection"
 },
 {
 "type": "checkbox",
 "id": "checkbox\_field",
 "label": "Checkbox Field",
 "default": false,
 "info": "Toggle on/off boolean setting"
 },
 {
 "type": "checkbox",
 "id": "show\_checkbox\_field",
 "label": "Show Checkbox Field",
 "default": true,
 "info": "Toggle to show/hide the checkbox field display"
 },
 {
 "type": "header",
 "content": "Visual & Media Types"
 },
 {
 "type": "color",
 "id": "color\_field",
 "label": "Color Field",
 "default": "#49473e",
 "info": "Color picker for simple colors"
 },
 {
 "type": "color\_background",
 "id": "color\_background\_field",
 "label": "Color Background Field",
 "default": "linear-gradient(135deg, #667eea 0%, #764ba2 100%)",
 "info": "Color picker with gradient support"
 },
 {
 "type": "font\_picker",
 "id": "font\_picker\_field",
 "label": "Font Picker Field",
 "default": "helvetica\_n4",
 "info": "Font family selector"
 },
 {
 "type": "image\_picker",
 "id": "image\_picker\_field",
 "label": "Image Picker Field",
 "info": "Image upload/select"
 },
 {
 "type": "text",
 "id": "image\_alt\_text",
 "label": "Image Alt Text",
 "default": "Schema demonstration image"
 },
 {
 "type": "video\_picker",
 "id": "video\_picker\_field",
 "label": "Video Picker Field",
 "info": "Video upload/select"
 },
 {
 "type": "image\_picker",
 "id": "video\_poster",
 "label": "Video Poster Image",
 "info": "Poster/thumbnail image for video"
 },
 {
 "type": "media\_picker",
 "id": "media\_picker\_field",
 "label": "Media Picker Field",
 "info": "Image or video select — stores a media object (the editor also manages a companion media\_picker\_field\_alt setting)"
 },
 {
 "type": "header",
 "content": "Resource Selectors (Single)"
 },
 {
 "type": "product",
 "id": "product\_field",
 "label": "Product Field",
 "info": "Select a single product"
 },
 {
 "type": "collection",
 "id": "collection\_field",
 "label": "Collection Field",
 "info": "Select a single collection"
 },
 {
 "type": "category",
 "id": "category\_field",
 "label": "Category Field",
 "info": "Select a single category"
 },
 {
 "type": "posts",
 "id": "post\_field",
 "label": "Post Field",
 "info": "Select a single blog post"
 },
 {
 "type": "enrollment\_pack",
 "id": "enrollment\_pack\_field",
 "label": "Enrollment Pack Field",
 "info": "Select a single enrollment pack"
 },
 {
 "type": "enrollment",
 "id": "enrollment\_field",
 "label": "Enrollment Field",
 "info": "Select a single enrollment"
 },
 {
 "type": "header",
 "content": "Resource Selectors (Multiple Lists)"
 },
 {
 "type": "product\_list",
 "id": "product\_list\_field",
 "label": "Product List Field",
 "limit": 8,
 "info": "Select multiple products (up to 8)"
 },
 {
 "type": "collections\_list",
 "id": "collection\_list\_field",
 "label": "Collection List Field",
 "limit": 6,
 "info": "Select multiple collections (up to 6)"
 },
 {
 "type": "category\_list",
 "id": "category\_list\_field",
 "label": "Category List Field",
 "limit": 8,
 "info": "Select multiple categories (up to 8)"
 },
 {
 "type": "posts\_list",
 "id": "posts\_list\_field",
 "label": "Posts List Field",
 "limit": 6,
 "info": "Select multiple blog posts (up to 6)"
 },
 {
 "type": "header",
 "content": "Special Types"
 },
 {
 "type": "text\_alignment",
 "id": "text\_alignment\_field",
 "label": "Text Alignment Field",
 "default": "left",
 "info": "Text alignment picker (left/center/right)"
 },
 {
 "type": "link\_list",
 "id": "link\_list\_field",
 "label": "Link List Field",
 "default": "main-menu",
 "info": "Select a menu/navigation list"
 },
 {
 "type": "header",
 "content": "Layout Settings"
 },
 {
 "type": "select",
 "id": "background\_color",
 "label": "Section Background Color",
 "options": \[
 { "value": "bg-primary", "label": "Primary" },
 { "value": "bg-secondary", "label": "Secondary" },
 { "value": "bg-secondary-light", "label": "Secondary Light" },
 { "value": "bg-accent-warm", "label": "Accent Warm" },
 { "value": "bg-banner", "label": "Banner" },
 { "value": "bg-success", "label": "Success" },
 { "value": "bg-error", "label": "Error" },
 { "value": "bg-white", "label": "White" },
 { "value": "bg-black", "label": "Black" },
 { "value": "bg-neutral", "label": "Neutral" },
 { "value": "bg-neutral-light", "label": "Neutral Light" },
 { "value": "bg-neutral-dark", "label": "Neutral Dark" },
 { "value": "bg-page", "label": "Page Background" }
 \],
 "default": "bg-white"
 }
 \],
 "blocks": \[
 {
 "type": "demo\_block",
 "name": "Demo Block",
 "limit": 12,
 "settings": \[
 {
 "type": "text",
 "id": "block\_title",
 "label": "Block Title",
 "default": "Demo Block Title"
 },
 {
 "type": "textarea",
 "id": "block\_text",
 "label": "Block Text",
 "default": "This is a demo block demonstrating block functionality in Fluid schema system."
 },
 {
 "type": "image\_picker",
 "id": "block\_image",
 "label": "Block Image"
 }
 \]
 }
 \],
 "max\_blocks": 12,
 "presets": \[
 {
 "name": "All Schema Types",
 "settings": {
 "section\_heading": "All Schema Types Demonstration",
 "section\_subheading": "This section demonstrates all supported schema types in Fluid theme system",
 "text\_field": "Example text field",
 "plaintext\_field": "Example plaintext field",
 "textarea\_field": "Example textarea field with multiple lines",
 "checkbox\_field": true,
 "range\_field": 50,
 "select\_field": "option1",
 "radio\_field": "radio1",
 "color\_field": "#49473e",
 "text\_alignment\_field": "left"
 },
 "blocks": \[
 {
 "type": "demo\_block",
 "settings": {
 "block\_title": "Example Demo Block",
 "block\_text": "This is a demo block demonstrating block functionality"
 }
 }
 \]
 }
 \]
}
{\`%\` endschema \`%\`}

_This documentation is based on real production implementations from the YoliOne, Base, Fluid themed and represents current best practices for Fluid theme development._