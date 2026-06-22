# Source: https://docs.fluid.app/docs/themes/developer-guide

# Build Fluid Themes

Build fast, flexible themes at scale using Liquid templating language, JSON sections, and modern web technologies including HTML, CSS, and JavaScript.

**Build a new theme** - Create a new theme based on Fluid's root themes (Fluid, Vox, or Base)

**Customize a theme** - Update the look and feel of an existing theme to tailor it to your company's unique brand and requirements

---

## Table of Contents

1. [Getting Started](https://docs.fluid.app/#getting-started)
2. [Key Concepts](https://docs.fluid.app/#key-concepts)
3. [Architecture](https://docs.fluid.app/#architecture)
4. [Layouts](https://docs.fluid.app/#layouts)
5. [Templates](https://docs.fluid.app/#templates)
6. [Sections](https://docs.fluid.app/#sections)
7. [Blocks](https://docs.fluid.app/#blocks)
8. [Settings](https://docs.fluid.app/#settings)
9. [Config](https://docs.fluid.app/#config)
10. [Locales](https://docs.fluid.app/#locales)
11. [Best Practices](https://docs.fluid.app/#best-practices)

---

## Getting Started

### Overview

Fluid themes are a package of template files, building blocks, and supporting assets that define how your company's pages look and function. The theme system provides multiple levels of customization, from simple CSS variable changes to complete template overrides.

### Quick Start

1. **Choose a Root Theme**: Select from Fluid, Vox, or Base themes
2. **Clone for Customization**: Create a company-specific copy
3. **Configure Variables**: Set brand colors, fonts, and spacing
4. **Customize Templates**: Override specific page layouts as needed
5. **Publish**: Activate your theme for all users

The theme system handles the technical implementation behind the scenes, allowing you to focus on design and customization.

---

## Key Concepts

### Themes vs Application Themes

**Legacy Themes** - Simple theme selection system for basic switching **Application Themes** - Full-featured system with template management, versioning, and customization

### Theme Hierarchy

- **Root Themes**: Platform-provided base themes (`fluid`, `vox`, `base`)
- **Company Themes**: Company-specific themes that clone and customize root themes
- **Template Inheritance**: Company themes can override specific templates while inheriting others

### Template Types

Templates in Fluid themes handle different content types and page layouts:

| Template Type | Purpose | Examples |
| --- | --- | --- |
| **Content Templates** | Render specific content items | `product`, `medium`, `library` |
| **Page Templates** | Handle page types | `home_page`, `shop_page`, `cart_page` |
| **Layout Components** | Provide page structure | `navbar`, `footer`, `layouts` |
| **Structural Components** | Reusable template parts | `sections`, `components`, `config` |

---

## Architecture

### Theme-Company Relationships

CompanyintidPKstringnamestringsubdomainApplicationThemeintidPKintcompany\_idFKNULL for root themesstringnametextvariablesJSON CSS variablestextcustom\_stylesheettextglobal\_stylesheetstringversionintstatus0=draft, 1=activeApplicationThemeTemplateintidPKintcompany\_idFKintapplication\_theme\_idFKstringnameintthemeable\_typeproduct, medium, home\_page, etctextcontentLiquid or JSON templatestringformatliquid or jsonintstatus0=draft, 1=activebooleandefaultdefault for themeable\_typeFileReferenceProductMediumPagehas many themeshas one current\_themecontains templatesreferences filescan have specific templatecan have specific templatecan have specific template

### Template Resolution Priority

1. **URL Parameter Override** - `?template_id=123` (highest priority)
2. **Content-Specific Assignment** - Individual content items with assigned templates
3. **Company Theme Default** - Default templates in company's active theme
4. **Root Theme Fallback** - Inherit from root theme if company theme lacks template

### Request Flow

User Request → Controller → Template Resolution → Variable Population → Rendering → Response

---

## Layouts

Layouts provide the structural wrapper for your content, defining the overall page structure including HTML document structure, navigation, and footer elements. The layout file (`theme.liquid`) serves as the foundation of your theme and is located in the `layout` folder. This file contains:

- Common elements (headers, footers)
- Shared JavaScript files
- Global CSS styles
- Other repeated theme elements

### Layout Folder Structure

theme/
├── layout/
│ ├── theme.liquid
├── ..
:

#### `content_for_layout` variables

Dynamically returns content based on the current template.

Include `{{ content_for_layout }}` in your layout files between the `<body>` and `</body>` HTML tags.

#### `content_for_header` variables

Returns the content of 'app/views/layouts/\_theme\_header.html.slim' which includes all metadata, scripts, global variables, and other required data by Fluid.

Include `{{ content_for_header }}` in your layout files between the `<head>` and `</head>` HTML tags.

_Important_: You must include the `{{ content_for_header }}` and `{{ content_for_layout }}` in your layout files for the theme to render correctly.

### Layout Structure

<!DOCTYPE html\>
<html lang\="{{ request.locale.iso\_code }}"\>
 <head\>
 {{ content\_for\_header }}
 <style\>
 :root {
 --primary-color: {{ settings.primary\_color }};
 --font-family: {{ settings.font\_family }};
 }
 </style\>
 </head\>
 <body\>
 <!-- Navigation -->
 {'%' section 'navbar' '%'}
 
 <!-- Main content area -->
 {{ content\_for\_layout }}
 
 <!-- Footer -->
 {'%' section 'footer' '%'}
 
 <!-- JavaScript -->
 <script src\="{{ 'theme.js' | asset\_url }}"\></script\>
 </body\>
</html\>

### Layout Assignment

Templates can specify their layout using:

**Liquid Templates:**

{'%' layout 'theme' '%'}
<!-- Template content -->

### Custom Layouts

Create specialized layouts for different page types:

<!-- layouts/minimal.liquid -->
<!DOCTYPE html\>
<html\>
 <head\>
 <title\>{{ page\_title }}</title\>
 {{ theme.global\_stylesheet\_with\_variables | raw }}
 </head\>
 <body class\="minimal-layout"\>
 {{ content\_for\_layout }}
 </body\>
</html\>

To use a different layout from the default theme.liquid, add the `{'%' layout "custom_layout_name" '%'}` tag at on your template file:

`{'%' layout "minimal" '%'}`

#### Disabling layout

To render a template without any layout, use `{'%' layout none '%'}` on your template file:

{'%' layout none '%'}
<!DOCTYPE html\>
<html\>
 <head\>
 {{ content\_for\_header }}
 </head\>
 <body\>
 <h1\>Welcome to the Dashboard</h1\>
 </body\>
</html\>

You will still have access to `content_for_header` variable which includes all necessary metadata, scripts and stylesheets required to run the page smoothly.

---

## Templates

Templates define the structure and content for specific page types. Fluid supports both Liquid and JSON template formats.

### Liquid Templates

Traditional server-side templating using Liquid syntax:

{'%' comment '%'}
 Product page template
{'%' endcomment '%'}

<article class\="product-page"\>
 <header class\="product-header"\>
 <h1\>{{ product.name }}</h1\>
 <div class\="product-price"\>
 {{ product.price | money }}
 </div\>
 {'% if product.tags.size > 0 %'}
 <div class\="product-tags"\>
 {{ product.tags | join: ', ' }}
 </div\>
 {'% endif %'}
 </header\>

 <div class\="product-gallery"\>
 {'% for image in product.images %'}
 <img src\="{{ image.url | img\_url: '600x400' }}" 
 alt\="{{ image.alt }}" />
 {'% endfor %'}
 </div\>

 <div class\="product-description"\>
 {{ product.description }}
 </div\>

 {'% if product.available %'}
 <button class\="btn-purchase" data-product-id\="{{ product.id }}"\>
 {{ 'products.add\_to\_cart' | t }}
 </button\>
 {'%' endif '%'}
</article\>

### Available Template Types

- `product` - Individual product pages
- `medium` - Media post pages
- `enrollment_pack` - Enrollment pack pages
- `home_page` - Homepage layout
- `shop_page` - Shop/catalog pages
- `cart_page` - Shopping cart
- `page` - Custom pages
- `navbar` - Navigation header
- `footer` - Page footer
- `sections` - Reusable sections
- `layouts` - Page wrapper layouts
- `components` - Resuable blocks
- `join_page` - List of enrollment packs page
- `collection_page` - List of collections page
- `collection` - Individual collection page
- `category_page` - List of categories page

#### Navbar and Footer templates

Navbar and Footer templates are located in the `navbar` and `footer` folders respectively. They are referenced in the `layout` as:

`{'%' section 'navbar' '%'}`

`{'%' section 'footer' '%'}`

theme/
:
├── navbar/
│ ├── default/
│ │ ├── index.liquid
│ │ └── styles.css
├── footer/
│ ├── default/
│ │ ├── index.liquid
│ │ └── styles.css
└── ...
:

---

## Sections

Sections are reusable components that can be added to templates. Each section has its own Liquid template and JSON schema defining available settings.

### Section Template Structure

<!-- sections/product\_header.liquid -->
<div class\="product-header" 
 style\="background-color: {{ section.settings.background\_color }};"\>
 <div class\="product-header-content"\>
 {'%' if section.settings.show\_vendor and product.vendor '%'}
 <div class\="product-vendor"\>{{ product.vendor }}</div\>
 {'%' endif '%'}

 <h1 class\="product-title product-title--{{ section.settings.title\_size }}"\>
 {{ product.name }}
 </h1\>

 {'%' if section.settings.show\_price '%'}
 <div class\="product-price"\>
 {{ product.price | money }}
 </div\>
 {'%' endif '%'}
 </div\>
</div\>

{'%' schema '%'}
{
 "name": "Product Header",
 "settings": \[
 {
 "type": "checkbox",
 "id": "show\_vendor",
 "label": "Show vendor",
 "default": true
 },
 {
 "type": "checkbox", 
 "id": "show\_price",
 "label": "Show price",
 "default": true
 },
 {
 "type": "select",
 "id": "title\_size",
 "label": "Title size",
 "options": \[
 {"value": "small", "label": "Small"},
 {"value": "medium", "label": "Medium"},
 {"value": "large", "label": "Large"}
 \],
 "default": "medium"
 },
 {
 "type": "color",
 "id": "background\_color", 
 "label": "Background color",
 "default": "#ffffff"
 }
 \]
}
{'%' endschema '%'}

### Using Sections in Templates

{'%' section 'main\_product', id: 'main\_product' '%'}
{'%' section 'related\_products', id: 'related\_products' '%'}

{'%' schema '%'}
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
 "heading": "Products",
 "heading\_size": "h1"
 }
 }
 }
}
{'%' endschema '%'}

### Section Processing

1. **Section Resolution**: Each section `type` maps to a Liquid template
2. **Settings Injection**: Section settings merged with schema defaults
3. **Block Processing**: Dynamic blocks processed with individual settings
4. **Template Rendering**: Section template rendered with populated variables

---

## Blocks

Blocks are dynamic content elements within sections that can be added, removed, and reordered. They provide flexibility for content creators to customize sections.

### Block Definition in Schema

{
 "name": "Testimonials Section",
 "settings": \[
 {
 "type": "text",
 "id": "section\_title",
 "label": "Section Title",
 "default": "What Our Customers Say"
 }
 \],
 "blocks": \[
 {
 "type": "testimonial",
 "name": "Customer Testimonial",
 "limit": 10,
 "settings": \[
 {
 "type": "text",
 "id": "customer\_name",
 "label": "Customer Name"
 },
 {
 "type": "textarea",
 "id": "testimonial\_text",
 "label": "Testimonial"
 },
 {
 "type": "image\_picker",
 "id": "customer\_photo",
 "label": "Customer Photo"
 }
 \]
 }
 \]
}

### Using Blocks in Templates

{
 "sections": {
 "testimonials": {
 "type": "testimonials\_section",
 "settings": {
 "section\_title": "Customer Reviews"
 },
 "blocks": {
 "testimonial\_1": {
 "type": "testimonial",
 "settings": {
 "customer\_name": "John Doe",
 "testimonial\_text": "Great product!",
 "customer\_photo": "photo.jpg"
 }
 }
 },
 "block\_order": \["testimonial\_1"\]
 }
 }
}

### Block Rendering in Liquid

<!-- sections/testimonials\_section.liquid -->
<div class\="testimonials"\>
 <h2\>{{ section.settings.section\_title }}</h2\>
 
 {'%' for block in section.blocks '%'}
 <div class\="testimonial" 
 data-fluid-section-block-id\="{{ block.id }}"
 data-fluid-section-block-type\="{{ block.type }}"\>
 {'%' case block.type '%'}
 {'%' when 'testimonial' '%'}
 <blockquote\>{{ block.settings.testimonial\_text }}</blockquote\>
 <cite\>{{ block.settings.customer\_name }}</cite\>
 {'%' when 'divider' '%'}
 <hr class\="testimonial-divider"\>
 {'%' endcase '%'}
 </div\>
 {'%' endfor '%'}
</div\>

### Block Characteristics

- **Dynamic Quantity**: Add/remove blocks as needed
- **Reorderable**: Drag blocks to change sequence
- **Type-Specific**: Different block types have different settings
- **Limited**: Optional `limit` property restricts maximum blocks

---

## Settings

Settings define configurable options for themes, sections, and blocks. They automatically generate form interfaces for customization.

### Setting Types

| Type | Description | Example |
| --- | --- | --- |
| `text` | Single-line text input | Product title, headings |
| `plaintext` | Single-line plain text input (no rich-text formatting) | SKUs, codes, plain labels |
| `textarea` | Multi-line text input | Descriptions, long content |
| `select` | Dropdown with options | Layout styles, sizes |
| `checkbox` | Boolean toggle | Show/hide elements |
| `color` | Color picker | Brand colors, backgrounds |
| `range` | Slider with min/max | Font sizes, spacing |
| `number` | Numeric input | Quantities, dimensions |
| `url` | URL input with validation | Links, external resources |
| `image_picker` | Asset selection | Images, icons |

### Theme-Level Settings (CSS Variables)

{
 "primary\_color": "#2c5aa0",
 "secondary\_color": "#f8f9fa", 
 "font\_family": "Inter, sans-serif",
 "heading\_font": "Playfair Display, serif",
 "border\_radius": "8px",
 "container\_width": "1200px"
}

### Section Settings Example

{
 "name": "Hero Section",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Hero Heading",
 "default": "Welcome to Our Store"
 },
 {
 "type": "textarea", 
 "id": "description",
 "label": "Hero Description",
 "default": "Discover amazing products"
 },
 {
 "type": "select",
 "id": "layout",
 "label": "Layout Style",
 "options": \[
 {"value": "centered", "label": "Centered"},
 {"value": "left", "label": "Left Aligned"},
 {"value": "right", "label": "Right Aligned"}
 \],
 "default": "centered"
 },
 {
 "type": "color",
 "id": "background\_color",
 "label": "Background Color", 
 "default": "#ffffff"
 },
 {
 "type": "range",
 "id": "padding",
 "label": "Section Padding",
 "min": 0,
 "max": 100,
 "step": 10,
 "unit": "px",
 "default": 40
 },
 {
 "type": "checkbox",
 "id": "show\_button",
 "label": "Show Call-to-Action Button",
 "default": true
 },
 {
 "type": "image\_picker",
 "id": "background\_image",
 "label": "Background Image"
 }
 \]
}

### Option Groups (Reusable Options)

When the same set of `select`, `font_picker` and `radio` options is needed across many sections or blocks, you can define the options once in `settings_schema.json` and reference them by ID instead of repeating the array inline.

**Without option groups** — options are declared inline on every setting that needs them:

{
 "type": "select",
 "id": "tagline\_font\_size",
 "label": "Tagline Font Size",
 "options": \[
 { "label": "XS", "value": "text-xs" },
 { "label": "SM", "value": "text-sm" },
 { "label": "Base", "value": "text-base" },
 { "label": "LG", "value": "text-lg" }
 \],
 "default": "text-base"
}

**With option groups** — pass the group ID as a plain string:

{
 "type": "select",
 "id": "tagline\_font\_size",
 "label": "Tagline Font Size",
 "options": "font\_sizes",
 "default": "text-base"
}

The `options` field on any `select`, `font_picker` and `radio` setting accepts either an inline array **or** a string that matches an `option_group.id` defined in `settings_schema.json`. Both forms are equivalent at render time.

---

### Using Settings in Templates

<section class\="hero hero--{{ section.settings.layout }}"
 style\="background-color: {{ section.settings.background\_color }};
 padding: {{ section.settings.padding }}px 0;"\>
 {'%' if section.settings.background\_image '%'}
 <div class\="hero-background"\>
 <img src\="{{ section.settings.background\_image | img\_url: '1920x1080' }}" 
 alt\="Hero background" />
 </div\>
 {'%' endif '%'}
 
 <div class\="hero-content"\>
 <h1\>{{ section.settings.heading }}</h1\>
 <p\>{{ section.settings.description }}</p\>
 
 {'%' if section.settings.show\_button '%'}
 <a href\="#" class\="btn btn-primary"\>Learn More</a\>
 {'%' endif '%'}
 </div\>
</section\>

---

## Config

Configuration templates define global theme settings and provide structured data for theme customization.

### Theme Configuration Structure

{
 "name": "Fluid Theme Configuration",
 "settings": \[
 {
 "type": "header",
 "content": "Colors"
 },
 {
 "type": "color",
 "id": "primary\_color",
 "label": "Primary Color",
 "default": "#2c5aa0"
 },
 {
 "type": "color", 
 "id": "secondary\_color",
 "label": "Secondary Color",
 "default": "#6c757d"
 },
 {
 "type": "header",
 "content": "Typography"
 },
 {
 "type": "font\_picker",
 "id": "font\_family",
 "label": "Body Font",
 "default": "Inter, sans-serif"
 },
 {
 "type": "font\_picker",
 "id": "heading\_font", 
 "label": "Heading Font",
 "default": "Playfair Display, serif"
 },
 {
 "type": "header",
 "content": "Layout"
 },
 {
 "type": "range",
 "id": "container\_width",
 "label": "Container Max Width",
 "min": 960,
 "max": 1600,
 "step": 40,
 "unit": "px",
 "default": 1200
 },
 {
 "type": "select",
 "id": "border\_radius",
 "label": "Border Radius Style",
 "options": \[
 {"value": "0px", "label": "Square"},
 {"value": "4px", "label": "Slightly Rounded"},
 {"value": "8px", "label": "Rounded"},
 {"value": "16px", "label": "Very Rounded"}
 \],
 "default": "8px"
 }
 \]
}

### Reusable Option Groups in settings\_schema.json

`settings_schema.json` is where you define option groups that can be shared across any section or block in the theme. Any setting entry that includes an `option_group` key contributes one option to the named group.

\[
 {
 "name": "typography",
 "settings": \[
 {
 "type": "range",
 "id": "text\_xs",
 "label": "Extra Small Font Size",
 "min": 1, "max": 100, "step": 1, "default": 12,
 "option\_group": {
 "id": "font\_sizes",
 "label": "XS",
 "value": "text-xs"
 }
 },
 {
 "type": "range",
 "id": "text\_sm",
 "label": "Small Font Size",
 "min": 1, "max": 100, "step": 1, "default": 14,
 "option\_group": {
 "id": "font\_sizes",
 "label": "SM",
 "value": "text-sm"
 }
 },
 {
 "type": "range",
 "id": "text\_base",
 "label": "Base Font Size",
 "min": 1, "max": 100, "step": 1, "default": 16,
 "option\_group": {
 "id": "font\_sizes",
 "label": "Base",
 "value": "text-base"
 }
 }
 \]
 }
\]

Each `option_group` object has three fields:

| Field | Description |
| --- | --- |
| `id` | The group identifier — used as the `"options"` value in section/block settings |
| `label` | The human-readable label shown in the dropdown |
| `value` | The value stored when this option is selected |

Multiple settings can share the same `option_group.id` — the resolver collects them all into a single ordered options array, maintaining the order they appear in `settings_schema.json`.

### Global Settings Access

<!-- Access theme variables in any template -->
<div class\="container" style\="max-width: {{ settings.container\_width }};"\>
 <h1 style\="font-family: {{ settings.heading\_font }}; 
 color: {{ settings.primary\_color }};"\>
 {{ page.title }}
 </h1\>
</div\>

### CSS Variable Integration

<!DOCTYPE html\>
<html\>
 <head\>
 {{ content\_for\_header }}
 <style\>
 :root {
 --primary-color: {{ settings.primary\_color }};
 --secondary-color: {{ settings.secondary\_color }};
 --font-family: {{ settings.font\_family }};
 --heading-font: {{ settings.heading\_font }};
 --border-radius: {{settings.border\_radius }};
 --container-width: {{ settings.container\_width }};
 }
 </style\>
 </head\>
 <body\>
 {{ content\_for\_layout }}
 </body\>
</html\>

.container {
 max-width: var(\--container-width);
 font-family: var(\--font-family);
}

.btn-primary {
 background-color: var(\--primary-color);
 border-radius: var(\--border-radius);
}

---

## Locales

Localization support enables themes to display content in multiple languages and adapt to different regional preferences.

### Translation Files Structure

Fluid themes use JSON locale files organized by components and sections for better maintainability:

// locales/en.json
{
 "cart\_page": {
 "checkout": "Checkout",
 "order\_summary": "Order Summary",
 "shipping": "Shipping estimate",
 "ships\_in": "Ships in 3-5 business days",
 "shopping\_cart": "Shopping Cart",
 "subtotal": "Subtotal",
 "tax": "Tax estimate",
 "total": "Order total"
 },
 "components": {
 "comment\_form": {
 "email": "Email",
 "full\_name": "Full Name",
 "leave\_info": "Please leave your information so {{ company }} will know who you are. You only need to do this once.",
 "placeholder": "Add your comment",
 "post": "Post",
 "want\_to\_leave\_comment": "Want to leave a comment?"
 }
 },
 "navbar": {
 "button\_1": "Button 1",
 "button\_2": "Button 2",
 "countries": "Countries",
 "country": "Country",
 "language": "Language",
 "shop": "Shop",
 "enrollments": "Enrollments",
 "view\_my\_site": "View MySite"
 },
 "product": {
 "add\_to\_cart": "Add to Cart",
 "buy\_now": "Buy Now",
 "details": "Details",
 "free\_shipping": "Free Shipping over {{amount}}",
 "quantity": "Quantity",
 "reviews": "{{reviews}} reviews",
 "stars": "{{stars}} stars",
 "one\_time\_purchase": "One time purchase",
 "subscribe": "Subscribe"
 },
 "shared": {
 "button": "Button",
 "medium\_heading": "Medium length heading goes here",
 "short\_heading": "Short heading here",
 "tagline": "Tagline",
 "view\_all": "View All"
 }
}

// locales/es.json 
{
 "cart\_page": {
 "checkout": "Finalizar Compra",
 "order\_summary": "Resumen del Pedido",
 "shipping": "Estimación de envío",
 "ships\_in": "Se envía en 3-5 días hábiles",
 "shopping\_cart": "Carrito de Compras",
 "subtotal": "Subtotal",
 "tax": "Estimación de impuestos",
 "total": "Total del pedido"
 },
 "components": {
 "comment\_form": {
 "email": "Correo electrónico",
 "full\_name": "Nombre completo",
 "leave\_info": "Por favor deja tu información para que {{ company }} sepa quién eres. Solo necesitas hacer esto una vez.",
 "placeholder": "Añade tu comentario",
 "post": "Publicar",
 "want\_to\_leave\_comment": "¿Quieres dejar un comentario?"
 }
 },
 "navbar": {
 "button\_1": "Botón 1",
 "button\_2": "Botón 2",
 "countries": "Países",
 "country": "País",
 "language": "Idioma",
 "shop": "Tienda",
 "enrollments": "Inscripciones",
 "view\_my\_site": "Ver Mi Sitio"
 },
 "product": {
 "add\_to\_cart": "Añadir al Carrito",
 "buy\_now": "Comprar Ahora",
 "details": "Detalles",
 "free\_shipping": "Envío gratis por encima de {{amount}}",
 "quantity": "Cantidad",
 "reviews": "{{reviews}} reseñas",
 "stars": "{{stars}} estrellas",
 "one\_time\_purchase": "Compra única",
 "subscribe": "Suscribirse"
 },
 "shared": {
 "button": "Botón",
 "medium\_heading": "Encabezado de longitud media aquí",
 "short\_heading": "Encabezado corto aquí",
 "tagline": "Eslogan",
 "view\_all": "Ver Todo"
 }
}

### Using Translations in Templates

<!-- Basic translation -->
<button class\="btn-primary"\>{{ 'product.add\_to\_cart' | t }}</button\>

<!-- Translation with variables -->
<p class\="price"\>{{ 'product.free\_shipping' | t: amount: '$50' }}</p\>

<!-- Component-based translations -->
<div class\="comment-form"\>
 <label\>{{ 'components.comment\_form.email' | t }}</label\>
 <input type\="email" placeholder\="{{ 'components.comment\_form.placeholder' | t }}"\>
 <button\>{{ 'components.comment\_form.post' | t }}</button\>
</div\>

<!-- Navigation translations -->
<nav class\="navbar"\>
 <a href\="/shop"\>{{ 'navbar.shop' | t }}</a\>
 <a href\="/enrollments"\>{{ 'navbar.enrollments' | t }}</a\>
 <button\>{{ 'navbar.button\_1' | t }}</button\>
</nav\>

<!-- Cart page translations -->
<div class\="cart-summary"\>
 <h2\>{{ 'cart\_page.shopping\_cart' | t }}</h2\>
 <div class\="total"\>{{ 'cart\_page.total' | t }}: ${{ cart.total }}</div\>
 <button\>{{ 'cart\_page.checkout' | t }}</button\>
</div\>

## Assets

Theme assets are stored in the theme's `assets` folder. These files are specific to the theme and support its functionality and appearance.

### Asset Folder Structure

theme/
├── assets/
│ ├── custom.js
│ ├── banner.jpg
│ ├──logo.png
│ └──...
│

> **Note**: For files that need to be accessed across multiple themes, you can use global files stored in Sidenav > Website > Files.

### File Types

Assets can be categorized into two types:

1. **Binary Files**
 
 - Can only be uploaded, not edited
 - Examples: images, fonts, videos
 - Upload only through file selection
2. **Non-Binary Files**
 
 - Can be uploaded or created from scratch
 - Can be edited after creation
 - Examples:
 - CSS files
 - JavaScript files
 - JSON files
 - Creation options:
 - Upload existing file
 - Create blank file with proper extension

### Referencing Assets

All files uploaded to the assets folder are automatically processed through Filestack. A CDN URL is generated for each file.These URLs can be referenced throughout your theme

To reference assets in your theme files, use the `asset_url` filter:

{{ 'filename.ext' | asset\_url }}

#### Example

<link rel\="stylesheet" href\="{{ 'custom.css' | asset\_url }}"\>
<script src\="{{ 'custom.js' | asset\_url }}"\></script\>
<img src\="{{ 'banner.jpg' | asset\_url }}" alt\="Banner"\>

---

## Best Practices

### Performance Optimization

**Image Optimization:**

<!-- Responsive images with proper sizing -->
<img src\="{{ image.url | img\_url: '600x400' }}" 
 srcset\="{{ image.url | img\_url: '300x200' }} 300w,
 {{ image.url | img\_url: '600x400' }} 600w,
 {{ image.url | img\_url: '1200x800' }} 1200w"
 sizes\="(max-width: 768px) 300px, (max-width: 1200px) 600px, 1200px"
 alt\="{{ image.alt }}" 
 loading\="lazy" />

**CSS and JavaScript Loading:**

<!-- Critical CSS inline, non-critical async -->
<style\>
 /\* Critical above-the-fold styles \*/
 .header, .hero { /\* styles \*/ }
</style\>

<link rel\="preload" href\="{{ 'theme.css' | asset\_url }}" as\="style" onload\="this.onload=null;this.rel='stylesheet'"\>

<!-- Defer non-critical JavaScript -->
<script src\="{{ 'theme.js' | asset\_url }}" defer\></script\>

### Accessibility

**Semantic HTML:**

<article class\="product" role\="main"\>
 <header\>
 <h1\>{{ product.name }}</h1\>
 </header\>
 
 <nav aria-label\="Product images"\>
 <!-- Image gallery -->
 </nav\>
 
 <section aria-label\="Product information"\>
 <p\>{{ product.description }}</p\>
 </section\>
</article\>

**ARIA Labels and Screen Reader Support:**

<button class\="btn-cart" 
 aria-label\="{{ 'products.add\_to\_cart' | t }}: {{ product.name }}"
 data-product-id\="{{ product.id }}"\>
 <span aria-hidden\="true"\>🛒</span\>
 {{ 'products.add\_to\_cart' | t }}
</button\>

<div class\="price" aria-label\="{{ 'products.price' | t }}"\>
 <span class\="sr-only"\>{{ 'products.price' | t }}: </span\>
 {{ product.price | money }}
</div\>

### Mobile-First Design

<!-- Mobile-optimized navigation -->
<nav class\="navbar"\>
 <div class\="navbar-brand"\>
 <a href\="/"\>{{ company.name }}</a\>
 </div\>
 
 <button class\="navbar-toggle" 
 aria-label\="{{ 'general.menu' | t }}"
 data-toggle\="mobile-menu"\>
 <span class\="hamburger-icon"\></span\>
 </button\>
 
 <div class\="navbar-menu" id\="mobile-menu"\>
 <!-- Navigation items -->
 </div\>
</nav\>

### Version Control

**Theme Development Workflow:**

1. Create feature branches for template changes
2. Test changes in preview mode before publishing
3. Use version control for template content
4. Maintain audit trails for customizations

**Template Organization:**

themes/
├── layouts/
│ ├── theme.liquid
│ └── minimal.liquid
├── templates/
│ ├── product.liquid
│ ├── home\_page.json
│ └── cart\_page.liquid
├── sections/
│ ├── product\_header.liquid
│ ├── product\_gallery.liquid
│ └── testimonials.liquid
└── config/
 └── settings\_schema.json

### Liquid Filters Reference

Fluid themes provide powerful Liquid filters for processing and formatting data:

**Image Processing:**

{{ image.url | img\_url: '300x200' }} <!-- Resize to 300x200 -->
{{ image.url | img\_url: '300x200', crop: 'center' }} <!-- Crop from center -->

**Text Processing:**

{{ text | truncate: 150 }} <!-- Limit to 150 characters -->
{{ text | strip\_html }} <!-- Remove HTML tags -->
{{ text | capitalize }} <!-- Capitalize first letter -->

**Formatting:**

{{ price | money }} <!-- Format as currency -->
{{ date | date: '%B %d, %Y' }} <!-- Format date -->
{{ number | number }} <!-- Format number with commas -->

**Arrays and Objects:**

{{ array | join: ', ' }} <!-- Join array elements -->
{{ array | first }} <!-- Get first element -->
{{ array | size }} <!-- Get array length -->

**Asset Management:**

{{ 'theme.css' | asset\_url }} <!-- Get theme asset URL -->
{{ 'logo.png' | asset\_url }} <!-- Get image asset URL -->

**JSON:**

{{ object | json }} <!-- Convert object to JSON string -->

**Translation:**

{{ 'general.welcome' | t }} <!-- Translate using locale file -->
{{ 'cart.items\_count' | t: count: 3 }} <!-- Pass variables to translation -->

## See also

- [Theme Available Variables](https://docs.fluid.app/docs/themes/theme-variables)
- [Base Theme Configuation](https://docs.fluid.app/docs/themes/base_theme_configuration)
- [Fluid Theme Configuation](https://docs.fluid.app/docs/themes/fluid_theme_configuration)
- [Vox Theme Configuation](https://docs.fluid.app/docs/themes/vox_theme_configuration)