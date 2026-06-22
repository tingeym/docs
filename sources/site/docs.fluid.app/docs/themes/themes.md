# Source: https://docs.fluid.app/docs/themes/themes

# Themes System Architecture

#### Table of Contents

---

> 1. [Overview](https://docs.fluid.app/#overview)
> 2. [Core Concepts](https://docs.fluid.app/#core-concepts)
> 3. [System Architecture](https://docs.fluid.app/#system-architecture)
> 4. [Request Flow Architecture](https://docs.fluid.app/#request-flow-architecture)
> 5. [Understanding Schema Components](https://docs.fluid.app/#understanding-schema-components-settings-blocks-and-presets)
> 6. [Implementation Details](https://docs.fluid.app/#implementation-details)
> 7. [Template Development Patterns](https://docs.fluid.app/#template-development-patterns)
> 8. [Reserved Section IDs](https://docs.fluid.app/#reserved-section-ids)
> 9. [Theme Customization Architecture](https://docs.fluid.app/#theme-customization-architecture)
> 10. [Company Theme Switching](https://docs.fluid.app/#company-theme-switching)
> 11. [API Design and Usage](https://docs.fluid.app/#api-design-and-usage)
> 12. [Error Handling](https://docs.fluid.app/#error-handling)
> 13. [Model Usage Examples](https://docs.fluid.app/#model-usage-examples)
> 14. [Use Cases](https://docs.fluid.app/#use-cases)
> 15. [Benefits](https://docs.fluid.app/#benefits)

---

## Overview

The Themes System provides a flexible, multi-layered architecture for customizing the appearance and behavior of pages across the platform. It separates theme management from template content, enabling companies to customize their user-facing pages through a combination of theme selection, template customization, and variable configuration. The system supports both traditional Liquid templating and modern JSON-based section components.

- [Page Editor Guide](https://docs.fluid.app/docs/themes/paged-editor)
- [Theme Development Guide](https://docs.fluid.app/docs/themes/developer-guide)
- [Theme Available Variables](https://docs.fluid.app/docs/themes/theme-variables)
- [Theme CLI Guide](https://docs.fluid.app/docs/themes/themes-cli)

## Core Concepts

### Themes vs Application Themes

The system maintains two distinct theme models representing different generations of theming functionality:

**Legacy Theme Model (`Theme`):**

- Simple theme selection system with predefined options
- Used for basic theme switching in older parts of the system
- Contains hardcoded theme names like `fashion`, `cosmetics`, `minimalistic`, etc.
- Primarily used by `UserCompany` associations for individual user theme preferences

**Modern Application Theme Model (`ApplicationTheme`):**

- Full-featured theme system with template management, versioning, and customization
- Supports company-specific theme customization and template overrides
- Includes CSS variable injection, custom stylesheets, and rich media support
- Primary system for all current theme functionality

### Application Themes

**Application Themes** represent complete theme packages that define the visual appearance and layout structure for a company's pages. Each theme can exist as either a root theme (shared across the platform) or a company-specific theme (customized for individual companies).

**Key characteristics:**

- Themes contain collections of templates for different page types
- Support CSS variable injection for dynamic styling
- Include versioning and publishing workflows
- Can be cloned and customized per company
- Maintain audit trails for change tracking

**Theme Hierarchy:**

- **Root Themes**: Platform-provided base themes (`fluid`, `vox`, `base`) that serve as templates
- **Company Themes**: Company-specific themes that clone and customize root themes
- **Template Inheritance**: Company themes can override specific templates while inheriting others

**Root Theme Definition:** A theme becomes a "root theme" when it has `company_id: nil` in the database. Root themes are platform-wide resources that:

- Are created and maintained by the platform administrators
- Serve as the foundation for all company-specific theme customization
- Cannot be directly modified by individual companies
- Provide the base templates and styling that companies clone and customize
- Are accessible to all companies for cloning purposes
- Include complete sets of templates for all major page types (product, home\_page, etc.)

### Application Theme Templates

**Application Theme Templates** are individual page templates within a theme that define the layout and content structure for specific page types. Each template can render different types of content and supports both Liquid and JSON formats.

**Key characteristics:**

- Templates belong to specific themes and handle particular content types
- Support both Liquid templating and JSON-based section systems
- Include schema definitions for dynamic content configuration
- Can be assigned to specific content items (products, media, pages)
- Maintain status tracking (draft/active) and versioning
- Support template-specific variables via variables.json for customizable data unique to each template

**Template Types:**

- **Content Templates**: `product`, `medium`, `enrollment_pack`, `library`, `collection` - Render specific content items
- **Page Templates**: `home_page`, `shop_page`, `cart_page`, `category_page`, `collection_page`, `join_page` - Handle page types
- **Layout Components**: `navbar`, `footer`, `layouts` - Provide page structure and navigation
- **Structural Components**: `sections`, `components`, `config` - Reusable template parts and configuration
- **Localization Templates**: `locales` - Contain language-specific JSON files that define translatable text and labels used across templates.

### Template Processing System

The system processes templates through a sophisticated pipeline that combines content resolution, variable injection, and rendering:

**Liquid Template Processing:**

- Traditional server-side templating using Liquid syntax
- Variables injected as `{{ variable_name }}` expressions
- Control flow through `{'%' tag '%'}` blocks
- Support for layouts, includes, and custom filters

**Custom Liquid Filters:** The system provides specialized filters for common theming tasks:

- `{{ product.price | money }}` - Formats currency values with proper symbols and localization
- `{{ image.url | img_url: '300x200' }}` - Resizes images to specified dimensions via ImageKit
- `{{ 'common.add_to_cart' | t }}` - Translates text based on current locale
- `{{ product.created_at | date: '%B %d, %Y' }}` - Formats dates with custom patterns
- `{{ content | truncate: 150 }}` - Truncates text to specified character length
- `{{ product.tags | join: ', ' }}` - Joins array elements with specified separator

**JSON Section Processing:** JSON templates represent a modern, component-based approach that separates content structure from presentation logic:

- **Schema-Driven Configuration**: Templates define sections with JSON schemas that specify three key components:
 - **Settings**: Individual configuration options that control section appearance and behavior (colors, text, toggles, etc.)
 - **Blocks**: Repeatable content elements within a section that can be added, removed, and reordered dynamically
 - **Presets**: Pre-configured combinations of settings and blocks that provide starting templates for common use cases
- **Component-Based Architecture**: Each section type corresponds to a reusable Liquid component template
- **Dynamic Content Blocks**: Sections can contain configurable blocks that are rendered with context-specific data
- **Visual Page Builder Integration**: JSON structure enables drag-and-drop page building interfaces
- **Settings Management**: Schema definitions automatically generate form interfaces for template customization

**JSON Template Structure:**

{
 "layout": "theme",
 "sections": {
 "header": {
 "type": "product\_header",
 "settings": {
 "show\_vendor": true,
 "show\_price": true,
 "title\_size": "large"
 }
 },
 "gallery": {
 "type": "product\_gallery",
 "settings": {
 "gallery\_type": "slideshow",
 "show\_thumbnails": true
 },
 "blocks": {
 "block\_1": {
 "type": "image\_slide",
 "settings": {
 "image": "{{ product.featured\_image }}",
 "caption": "Main product image"
 }
 }
 },
 "block\_order": \["block\_1"\]
 }
 },
 "order": \["header", "gallery"\]
}

**Section Processing Flow:**

1. **Section Resolution**: Each section `type` maps to a Liquid template in the `sections` themeable\_type
2. **Settings Injection**: Section settings are merged with schema defaults and injected as variables
3. **Block Processing**: Dynamic blocks are processed with their individual settings and rendered within sections
4. **Schema Validation**: Settings are validated against the section's schema definition
5. **Template Rendering**: The resolved section template is rendered with populated variables

### Understanding Schema Components: Settings, Blocks, and Presets

**Settings: Individual Configuration Options** Settings are individual form fields that control specific aspects of a section's appearance or behavior. Each setting has a type, default value, and user-facing label.

{
 "name": "Product Gallery",
 "settings": \[
 {
 "type": "header",
 "id": "content\_header",
 "label": "Content Settings"
 },
 {
 "type": "text",
 "id": "heading\_text",
 "label": "Section heading",
 "default": "Product Images"
 },
 {
 "type": "textarea",
 "id": "description",
 "label": "Gallery Description",
 "default": "Browse our product images"
 },
 {
 "type": "header",
 "id": "layout\_header",
 "label": "Layout Settings"
 },
 {
 "type": "select",
 "id": "gallery\_layout",
 "label": "Gallery Layout",
 "options": \[
 { "value": "grid", "label": "Grid" },
 { "value": "carousel", "label": "Carousel" },
 { "value": "stacked", "label": "Stacked" }
 \],
 "default": "grid"
 },
 {
 "type": "range",
 "id": "columns\_count",
 "label": "Columns per row",
 "min": 2,
 "max": 6,
 "step": 1,
 "default": 4
 },
 {
 "type": "checkbox",
 "id": "show\_thumbnails",
 "label": "Show thumbnail navigation",
 "default": true
 },
 {
 "type": "header",
 "id": "style\_header",
 "label": "Style Settings"
 },
 {
 "type": "color\_background",
 "id": "background\_color",
 "label": "Background color",
 "default": "#ffffff"
 },
 {
 "type": "color",
 "id": "border\_color",
 "label": "Border color",
 "default": "#e0e0e0"
 },
 {
 "type": "text\_alignment",
 "id": "text\_align",
 "label": "Text alignment",
 "default": "left"
 },
 {
 "type": "header",
 "id": "content\_selection\_header",
 "label": "Content Selection"
 },
 {
 "type": "product",
 "id": "featured\_product",
 "label": "Featured Product",
 "default": null
 },
 {
 "type": "product\_list",
 "id": "related\_products",
 "label": "Related Products",
 "default": \[\],
 "limit": 10
 },
 {
 "type": "image\_picker",
 "id": "banner\_image",
 "label": "Banner Image",
 "default": ""
 },
 {
 "type": "link\_list",
 "id": "navigation\_links",
 "label": "Navigation Menu",
 "default": \[\]
 }
 \]
}

**Available Setting Types:**

Settings control how your sections look and behave. Each setting type provides a different interface and stores data in a specific format.

**Text & Content:**

- `text`: Single-line text input for short text (e.g., headings, labels)
- `textarea`: Rich text editor with formatting options (bold, italic, links, etc.)
- `html`: Raw HTML code editor for custom HTML

**Layout & Style:**

- `select`: Dropdown menu to choose from predefined options
- `radio`: Radio button tabs for single-choice selection
- `checkbox`: Toggle switch for yes/no options
- `range`: Slider control for numeric values with min/max limits
- `text_alignment`: Quick picker for text alignment (left, center, right, justify)
- `color_background`: Color picker that supports both solid colors and gradients
 - **Important**: When using gradients, use the CSS `background` property instead of `background-color`

**Media & Assets:**

- `image_picker`: Choose images from your media library
- `video_picker`: Select videos from your media library
- `media_picker`: Choose an image **or** a video from your media library, plus embed options — unlike the image and video pickers, this stores a media **object**, not a plain URL string
- `font_picker`: Choose fonts with live preview

**Navigation & Links:**

- `url`: URL input field with validation
- `link_list`: Manage a list of navigation links

**Single Resource Selectors:**

- `product`: Select one product
- `collection`: Select one collection
- `posts`: Select one blog post
- `category`: Select one category
- `enrollment_pack`: Select one enrollment pack

**Multiple Resource Selectors:**

- `product_list`, `products_list`: Select multiple products
- `collections_list`: Select multiple collections
- `posts_list`: Select multiple blog posts
- `categories`: Select multiple categories
- `enrollment_list`: Select multiple enrollments

**Organization:**

- `header`: Creates collapsible sections to organize your settings (doesn't store data)

**Important Things to Know:**

1. **Using Color Gradients**: The `color_background` setting can hold both solid colors and gradients. When you use gradients in your template, make sure to use `background` instead of `background-color`:
 
 <!-- ✅ Works with both solid colors and gradients -->
 <div style="background: {{ section.settings.background\_color }}">
 
 <!-- ❌ Only works with solid colors, gradients won't show -->
 <div style="background-color: {{ section.settings.background\_color }}">
 
2. **Always Provide Default Values**: Every setting needs a default value that matches what type of data it stores:
 
 - Text settings: Use `""` (empty) or a sample text like `"Enter text here"`
 - Number settings: Use `0` or a reasonable starting number
 - Toggle switches: Use `true` or `false`
 - Single resource selectors: Use `null` or a resource ID number
 - Multiple resource selectors: Use `[]` (empty array) or an array with resource IDs
 - Link lists: Use `[]` (empty array) or an array with sample URLs

**What Data Does Each Setting Store?**

Understanding what data format each setting uses helps you work with the values in your templates:

| Setting Type | Stores | Example |
| --- | --- | --- |
| Text inputs (`text`, `textarea`, `html`) | Plain text or HTML | `"Welcome"` or `"<p>Hello</p>"` |
| Dropdowns (`select`, `radio`) | Selected option value | `"center"` or `"grid"` |
| Alignment picker (`text_alignment`) | Alignment value | `"left"`, `"center"`, `"right"`, or `"justify"` |
| Font picker (`font_picker`) | Font name | `"Arial"` |
| URL input (`url`) | Web address | `"https://example.com"` |
| Toggle switch (`checkbox`) | True or false | `true` or `false` |
| Slider (`range`) | Number | `24` |
| Color picker (`color_background`) | Color code or gradient | `"#ff0000"` or `"linear-gradient(...)"` |
| Link list (`link_list`) | Array of URLs | `["/home", "/products"]` |
| Single resource selectors (`product`, `collection`, etc.) | Resource ID (as number) | `123` |
| Multiple resource selectors (`product_list`, `collections_list`, etc.) | Array of resource IDs (as numbers) | `[123, 456, 789]` |
| Image/video pickers (`image_picker`, `video_picker`) | File URL | `"https://cdn.example.com/image.jpg"` |
| Media picker (`media_picker`) | Media object (URL plus optional embed settings) | `{ "url": "https://cdn.example.com/clip.mp4", "fluid_media_id": 42, "embed_type": "default" }` |
| Section headers (`header`) | Nothing - just for organization | \- |

> **Heads up - `media_picker` stores an object, not a string.** The `image_picker` and `video_picker` settings both store a plain URL string, so you can use them directly (`{{ section.settings.banner_image }}`). The `media_picker` setting stores a media **object**, so you read its `url` property (`{{ section.settings.banner_media.url }}`) and may branch on a **positive** `fluid_media_id` to embed a Fluid media widget (a value of `0` means a plain uploaded image). The editor also auto-manages a companion `<id>_alt` setting (e.g. `banner_media_alt`) for alt text and translations - you do not declare it yourself. See the [Media Picker](https://docs.fluid.app/docs/themes/schema-components#media-picker) reference for the full object shape and a Liquid example.

**Blocks: Repeatable Content Elements**

Blocks are dynamic content items that can be added, removed, and reordered within a section. Each block has its own settings and can represent different content types.

{
 "name": "Testimonials Section",
 "settings": \[
 {
 "type": "text",
 "id": "section\_title",
 "label": "Section Title",
 "default": "What Our Customers Say"
 },
 {
 "type": "textarea",
 "id": "section\_subtitle",
 "label": "Section Subtitle",
 "default": "<p>Hear from our satisfied customers</p>"
 },
 {
 "type": "color\_background",
 "id": "section\_background",
 "label": "Section Background",
 "default": "#f8f9fa"
 },
 {
 "type": "text\_alignment",
 "id": "content\_alignment",
 "label": "Content Alignment",
 "default": "center"
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
 "label": "Customer Name",
 "default": ""
 },
 {
 "type": "textarea",
 "id": "testimonial\_text",
 "label": "Testimonial",
 "default": ""
 },
 {
 "type": "image\_picker",
 "id": "customer\_photo",
 "label": "Customer Photo",
 "default": ""
 },
 {
 "type": "text",
 "id": "customer\_title",
 "label": "Customer Title/Company",
 "default": ""
 },
 {
 "type": "range",
 "id": "star\_rating",
 "label": "Star Rating",
 "min": 1,
 "max": 5,
 "step": 1,
 "default": 5
 },
 {
 "type": "checkbox",
 "id": "show\_photo",
 "label": "Show Customer Photo",
 "default": true
 },
 {
 "type": "url",
 "id": "company\_link",
 "label": "Company Website",
 "default": ""
 }
 \]
 },
 {
 "type": "video\_testimonial",
 "name": "Video Testimonial",
 "limit": 3,
 "settings": \[
 {
 "type": "text",
 "id": "customer\_name",
 "label": "Customer Name",
 "default": ""
 },
 {
 "type": "video\_picker",
 "id": "testimonial\_video",
 "label": "Testimonial Video",
 "default": ""
 },
 {
 "type": "checkbox",
 "id": "autoplay",
 "label": "Autoplay Video",
 "default": false
 }
 \]
 },
 {
 "type": "divider",
 "name": "Section Divider",
 "settings": \[
 {
 "type": "select",
 "id": "divider\_style",
 "label": "Divider Style",
 "options": \[
 { "value": "line", "label": "Line" },
 { "value": "dots", "label": "Dots" },
 { "value": "stars", "label": "Stars" }
 \],
 "default": "line"
 },
 {
 "type": "color\_background",
 "id": "divider\_color",
 "label": "Divider Color",
 "default": "#e0e0e0"
 }
 \]
 }
 \]
}

**Block Characteristics:**

- **Dynamic Quantity**: Users can add/remove blocks as needed
- **Reorderable**: Blocks can be dragged to change their sequence
- **Type-Specific**: Different block types have different settings schemas
- **Limited**: Optional `limit` property restricts maximum number of blocks

**Presets: Pre-Configured Starting Points** Presets provide ready-to-use configurations that combine section settings with a predefined set of blocks, giving users a starting point for common use cases.

{
 "name": "Feature Showcase",
 "settings": \[
 {
 "type": "header",
 "id": "content\_header",
 "label": "Content"
 },
 {
 "type": "text",
 "id": "section\_heading",
 "label": "Section Heading",
 "default": "Key Features"
 },
 {
 "type": "textarea",
 "id": "section\_description",
 "label": "Section Description",
 "default": ""
 },
 {
 "type": "header",
 "id": "layout\_header",
 "label": "Layout"
 },
 {
 "type": "select",
 "id": "layout\_style",
 "label": "Layout Style",
 "options": \[
 { "value": "grid", "label": "Grid" },
 { "value": "list", "label": "List" },
 { "value": "carousel", "label": "Carousel" }
 \],
 "default": "grid"
 },
 {
 "type": "range",
 "id": "columns\_desktop",
 "label": "Columns (Desktop)",
 "min": 2,
 "max": 4,
 "step": 1,
 "default": 3
 },
 {
 "type": "header",
 "id": "style\_header",
 "label": "Style"
 },
 {
 "type": "color\_background",
 "id": "background\_color",
 "label": "Background Color",
 "default": "#ffffff"
 },
 {
 "type": "text\_alignment",
 "id": "content\_alignment",
 "label": "Content Alignment",
 "default": "center"
 }
 \],
 "blocks": \[
 {
 "type": "feature\_item",
 "name": "Feature",
 "settings": \[
 {
 "type": "text",
 "id": "feature\_title",
 "label": "Feature Title",
 "default": ""
 },
 {
 "type": "textarea",
 "id": "feature\_description",
 "label": "Feature Description",
 "default": ""
 },
 {
 "type": "image\_picker",
 "id": "feature\_icon",
 "label": "Feature Icon",
 "default": ""
 },
 {
 "type": "url",
 "id": "feature\_link",
 "label": "Link URL",
 "default": ""
 },
 {
 "type": "checkbox",
 "id": "show\_icon",
 "label": "Show Icon",
 "default": true
 }
 \]
 }
 \],
 "presets": \[
 {
 "name": "Basic Features (3 items)",
 "settings": {
 "section\_heading": "Why Choose Us"
 },
 "blocks": \[
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Fast Delivery",
 "feature\_description": "Get your order in 24 hours or less"
 }
 },
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Quality Guarantee",
 "feature\_description": "100% satisfaction or your money back"
 }
 },
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Expert Support",
 "feature\_description": "24/7 customer service from our team"
 }
 }
 \]
 },
 {
 "name": "Product Benefits (5 items)",
 "settings": {
 "section\_heading": "Product Benefits"
 },
 "blocks": \[
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Eco-Friendly",
 "feature\_description": "Made from sustainable materials"
 }
 },
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Durable Design",
 "feature\_description": "Built to last for years of use"
 }
 },
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Easy to Use",
 "feature\_description": "Simple setup in under 5 minutes"
 }
 },
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Versatile",
 "feature\_description": "Works in multiple environments"
 }
 },
 {
 "type": "feature\_item",
 "settings": {
 "feature\_title": "Cost Effective",
 "feature\_description": "Saves money compared to alternatives"
 }
 }
 \]
 }
 \]
}

**Preset Benefits:**

- **Quick Setup**: Users can start with pre-configured content instead of building from scratch
- **Best Practices**: Presets demonstrate optimal configurations for common scenarios
- **Inspiration**: Show users what's possible with the section
- **Consistency**: Ensure common use cases follow established patterns

**How They Work Together in Practice:**

1. **User Selects Preset**: When adding a section, user chooses "Product Benefits (5 items)" preset
2. **System Applies Configuration**: Section gets the preset's settings and 5 pre-configured feature blocks
3. **User Customizes Settings**: User modifies section heading, colors, layout options through the settings
4. **User Manages Blocks**: User can edit individual features, reorder them, add more, or remove some
5. **Final Rendering**: Section template renders with the customized settings and blocks

This three-tier system (settings + blocks + presets) provides maximum flexibility while maintaining usability for non-technical users.

User Experience Flow

Preset Components

Block Components

Settings Components

JSON Schema Structure

generates forms for

populates

populates

Select Preset

User adds section

Apply preset settings & blocks

Customize Settings

Add/Remove/Reorder Blocks

Final Rendering

Preset 1: Basic Setup

Default Settings Values

Pre-configured Blocks

Preset 2: Advanced Setup

Default Settings Values

Pre-configured Blocks

Block Type Definition

Block Settings

Block Limit

Another Block Type

Block Settings

Text Input

Select Dropdown

Color Picker

Range Slider

Checkbox Toggle

Section Schema

Settings Array

Blocks Array

Presets Array

**Layout Resolution:**

- Templates can specify layout wrappers using `{'%' layout 'theme' '%'}` tags
- Layouts are separate templates with `themeable_type: "layouts"`
- Content injection through `{{ content_for_layout }}` variable
- Supports nested layouts and template inheritance

### Variable System

The themes system includes a comprehensive variable injection system that populates templates with dynamic content:

**Variable Categories:**

- **Global Variables**: Company information, site settings, localization data
- **Content Variables**: Product details, page content, media information
- **Request Variables**: URL paths, query parameters, user session data
- **Theme Variables**: CSS custom properties, color schemes, typography settings

**Variable Processing:**

- Variables are resolved by specialized service classes in `app/services/themes/templates/variables/`
- Different content types have dedicated variable providers
- Caching mechanisms prevent N+1 queries and improve performance
- Variables support both simple values and complex objects (drops)

## Theme Architecture

A theme is distributed as a zip file containing various template files organized in a specific folder structure. Here's a detailed breakdown of the theme architecture:

### Layout File

The layout file (`theme.liquid`) serves as the foundation of your theme and is located in the `layout` folder. This file contains:

- Common elements (headers, footers)
- Shared JavaScript files
- Global CSS styles
- Other repeated theme elements

#### Layout Folder Structure

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

#### Minimal Layout Example

<!DOCTYPE html\>
<html\>
 <head\>
 {{ content\_for\_header }}
 </head\>
 <body\>
 {{ content\_for\_layout }}
 </body\>
</html\>

#### Changing layouts

To use a different layout from the default theme.liquid, add the `{'%' layout "custom_layout" '%'}` tag at on your template file:

`{'%'​ layout "custom_layout" ​'%'}`

`<h1>Welcome to the Dashboard</h1>`

#### Disabling layout

To render a template without any layout, use `{'%' layout none '%'}` on your template file:

`{'%'​ layout none ​'%'}`

`<!DOCTYPE html>`

`<html>`

`<head>`

`{{​ content_for_header ​}}`

`</head>`

`<body>`

`<h1>Welcome to the Dashboard</h1>`

`</body>`

`</html>`

You will still have access to `content_for_header` variable which includes all necessary metadata, scripts and stylesheets required to run the page smoothly.

### Template Files

Template files are organized in page-specific folders. Each page type has its own dedicated folder.

#### Template Example

theme/
:
├── product/
│ ├── default/
│ │ ├── index.liquid
│ │ └── styles.css
│ └── new\_temp/
│ ├── index.json
│ └── styles.css
├── shop\_page/
├── medium/
└── ...
:

### Navbar and Footer templates

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

#### Multiple Templates

- Each page type can have multiple templates
- Example for product page:
 - `product/default/`
 - `product/new_temp/`
- Only one template can be published at a time
- A `default` template is always included

#### Template Structure

Each template consists of two main files:

1. Main template file (one of the following):
 - `index.liquid`
 - `index.json`
2. Styles file:
 - `styles.css`

**Note**: Templates can be created using either Liquid or JSON format. Detailed information about these formats will be covered in later sections.

## Asset Files

Theme assets are stored in the theme's `assets` folder. These files are specific to the theme and support its functionality and appearance.

### Asset Folder Structure

theme/
├── assets/
│ ├── custom.js
│ ├── banner.jpg
│ ├──logo.png
│ └──...
│

> **Note**: For files that need to be accessed across multiple themes, you can use global files stored in Sidenav > Website > Files. Global files will be discussed in detail in later sections.

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

Reference: https://shopify.dev/docs/api/liquid/filters/asset\_url

### Components

Components are reusable UI elements that can be used across multiple pages. They are located in the `components` folder.

#### Component Folder Structure

theme/
├── components/
│ ├── header/
│ │ ├── index.liquid
│ │ └── styles.css

#### Component Example

`{'%' render 'header' '%'}`

You can pass variables to the component as follows:

`{'%' render 'header', variable: 'value' '%'}`

## System Architecture

### Theme-Company Relationships

Companies can have multiple themes but only one active theme at a time:

CompanyintidPKstringnamestringsubdomainApplicationThemeintidPKintcompany\_idFKNULL for root themesstringnametextvariablesJSON CSS variablestextcustom\_stylesheettextglobal\_stylesheetstringversionintstatus0=draft, 1=activeApplicationThemeTemplateintidPKintcompany\_idFKintapplication\_theme\_idFKintparent\_idFKfor nested templatesstringnameintthemeable\_typeenum: product, medium, page, etctextcontentLiquid or JSON templatestringformatliquid or jsonintstatus0=draft, 1=activebooleandefaultdefault for themeable\_typeImageFileResourceFileReferenceProductMediumLibraryEnrollmentPackPageThemeUserCompanyhas many themeshas one current\_themehas template overridescontains templateshas cover imageshas assetshas nested templates (parent\_id)references fileshas imageslinks tocan have specific templatecan have specific templatecan have specific templatecan have specific templatecan have specific templatelegacy belongs toused by

### Template Hierarchy and Inheritance

Templates follow a hierarchical resolution system:

Yes

No

Yes

No

Yes

No

Yes

No

Request for Product Page

URL template\_id parameter?

Use ApplicationThemeTemplate.find template\_id

Product has specific template from current company theme assigned?

Use product.application\_theme\_template

Company has active theme with default product template?

Use company.current\_theme.default\_product\_template

Root theme has default product template?

Use root\_theme.default\_product\_template

Error: No template found

Render Template

### File and Asset Management

Theme assets are managed through the broader file resource system:

ApplicationTheme
├── has\_many :images (cover images, theme previews)
├── has\_many :file\_resources (CSS files, JavaScript, fonts)
└── has\_many :ordered\_images (positioned theme gallery images)

ApplicationThemeTemplate
├── has\_many :images (template preview images)
└── content field contains template code (Liquid/JSON)

## Request Flow Architecture

UserRouterControllerThemeableTemplateResolverVariableServicePageBuilderLiquidEngineResponsePriority-based template resolutionalt\[Liquid Template\]\[JSON Template\]GET /products/123PublicController or SharesControllerinclude Themeable concernset\_theme\_template\_and\_variables()theme\_template\_for(:product, product)Check URL paramsCheck content-specific assignmentCheck company theme defaultsCheck root theme fallbackApplicationThemeTemplatetheme\_template\_variables\_for(template, product)Build global variablesBuild content variablesBuild request variablesVariables hashtemplate + variablesrender\_template(template, variables)parse(content, variables)Rendered HTMLprocess\_sections(json, variables)Rendered HTMLresolve\_layout(template)apply\_stylesheets()Final HTMLHTTP ResponseRendered PageUserRouterControllerThemeableTemplateResolverVariableServicePageBuilderLiquidEngineResponse

### Step 1: Request Routing and Controller Setup

**Request Entry Points:**

- `PublicController` - Handles company homepage, join pages, privacy policy
- `SharesController` - Handles content pages (products, media, libraries, custom pages)

**Controller Processing:** Both controllers include the `Themeable` concern which provides theme resolution functionality:

- `set_theme_template_and_variables()` - Main orchestration method
- `theme_template_for()` - Template resolution logic
- `theme_template_variables_for()` - Variable population

### Step 2: Theme and Template Resolution

**Theme Resolution:**

\# In Themeable concern
def current\_theme\_for\_company
 @current\_theme ||= @company&.current\_theme || default\_system\_theme
end

def theme\_template\_for(content\_type, content\_item \= nil)
 \# 1. Check URL parameters for template override
 return find\_template\_by\_id(params\[:template\_id\]) if params\[:template\_id\]

 \# 2. Check content-specific template assignment
 return content\_item.application\_theme\_template if content\_item&.application\_theme\_template

 \# 3. Find default template for content type in company's active theme
 ApplicationThemeTemplate.default\_for\_company\_themes(
 company: @company,
 themeable\_type: content\_type
 ).first
end

**Template Selection Logic:**

- Templates are resolved based on `themeable_type` (product, medium, home\_page, etc.)
- Default templates are marked with `default: true` within each theme
- Only `active` templates are considered for rendering
- Falls back to root theme defaults if company theme lacks specific templates

### Step 3: Variable Population and Context Building

**Variable Service Orchestration:**

\# Variables are created by specialized service classes
def theme\_template\_variables\_for(template, content\_item \= nil)
 Themes::Templates::Variable.new(
 template: template,
 request: request,
 company: @company,
 record: content\_item,
 params: params
 ).call
end

**Variable Categories and Sources:**

- **Base Variables**: URLs, paths, request info, company details
- **Content Variables**: Product data, page content, media info (based on content\_item)
- **Global Variables**: Site settings, localization, social media links
- **Theme Variables**: CSS custom properties, layout settings from theme configuration

### Step 4: Template Rendering Pipeline

**Rendering Orchestration:**

\# PageBuilder handles the core rendering logic
def render\_template(template, variables, layout \= nil)
 if template.json?
 render\_json\_template(template, variables)
 else
 render\_liquid\_template(template, variables)
 end
end

**Liquid Template Processing:**

- Templates processed through `LiquidTemplate.parse(template.content, variables)`
- Variables accessible as `{{ variable_name }}` in templates
- Support for control flow: `{'%' if condition '%'}`, `{'%' for item in collection '%'}`
- Custom filters available for formatting, localization, and image processing
- Layout injection via `{{ content_for_layout }}` if layout specified

**JSON Template Processing:** JSON templates use a sophisticated section-based rendering system that separates content structure from presentation:

- **Section-by-Section Rendering**: The JSON structure defines discrete sections that are processed and rendered individually
- **Schema-Based Configuration**: Each section type has a corresponding schema that defines available settings, blocks, and presets
- **Dynamic Block Management**: Sections can contain configurable blocks with their own settings and rendering logic
- **Settings Inheritance**: Block settings inherit defaults from their section schema and can be overridden per instance
- **Fluid Attributes**: Blocks receive special `fluid_attributes` for frontend editing integration (section IDs, block types, etc.)

**Processing Steps:**

1. **JSON Parsing**: Template content is parsed as JSON structure
2. **Section Iteration**: Each section in the `sections` object is processed according to the `order` array
3. **Schema Loading**: Section schema is loaded from corresponding `sections` themeable\_type templates
4. **Settings Merging**: Section settings are merged with schema defaults
5. **Block Processing**: Dynamic blocks are processed with individual settings and fluid attributes
6. **Template Resolution**: Each section type maps to a Liquid template that renders the final HTML
7. **Variable Injection**: Processed settings become available as `{{ section.settings.setting_name }}` variables

### Step 5: Layout Resolution and Content Wrapping

**Layout Processing:**

def resolve\_theme\_layout(template)
 return nil unless template.templateable\_type?

 layout\_name \= template.layout\_name || 'theme'
 theme \= template.theme

 theme&.application\_theme\_templates&.find\_by(
 name: layout\_name,
 themeable\_type: 'layouts'
 )&.published
end

**Layout Integration:**

- Templates can specify layouts via `{'%' layout 'theme' '%'}` tags or JSON configuration
- Layout templates wrap content using `{{ content_for_layout }}` variable
- Supports nested layouts and template inheritance
- Layout resolution respects theme hierarchy (company themes inherit root layouts)

### Step 6: Stylesheet and Asset Integration

**CSS Processing:**

\# ApplicationTheme methods for stylesheet processing
def global\_stylesheet\_with\_variables
 cache\_key \= "theme\_stylesheet:#{id}:global:#{cache\_key\_suffix}"
 Rails.cache.fetch(cache\_key, expires\_in: 1.hour) do
 stylesheet\_with\_variables(global\_stylesheet)
 end
end

def stylesheet\_with\_variables(stylesheet)
 variables \= safe\_parse\_json(self.variables)
 variables.each { |key, value| stylesheet.gsub!("$#{key}", value) }
 stylesheet
end

**Asset Integration:**

- Global stylesheets with CSS variable substitution
- Custom stylesheets for theme-specific overrides
- File resources (fonts, images, scripts) linked to themes and templates
- Caching with variable-based cache invalidation

## Implementation Details

### Data Model Structure

\-- ApplicationTheme: Main theme container
CREATE TABLE application\_themes (
 id SERIAL PRIMARY KEY,
 company\_id INTEGER REFERENCES companies(id), \-- NULL for root themes
 name VARCHAR NOT NULL,
 description TEXT,
 variables TEXT, \-- JSON string with CSS variables
 custom\_stylesheet TEXT,
 global\_stylesheet TEXT,
 version VARCHAR DEFAULT '1.0',
 status INTEGER DEFAULT 0, \-- 0: draft, 1: active
 created\_at TIMESTAMP DEFAULT NOW(),
 updated\_at TIMESTAMP DEFAULT NOW()
);

\-- Only one active theme per company
CREATE UNIQUE INDEX idx\_app\_themes\_company\_active
ON application\_themes (company\_id)
WHERE status \= 1;

\-- ApplicationThemeTemplate: Individual templates within themes
CREATE TABLE application\_theme\_templates (
 id SERIAL PRIMARY KEY,
 company\_id INTEGER REFERENCES companies(id),
 application\_theme\_id INTEGER REFERENCES application\_themes(id),
 parent\_id INTEGER REFERENCES application\_theme\_templates(id), \-- For nested templates
 name VARCHAR NOT NULL,
 themeable\_type INTEGER NOT NULL, \-- Enum: product, medium, home\_page, etc.
 content TEXT, \-- Template code (Liquid or JSON)
 head TEXT, \-- HTML head content
 stylesheet TEXT, \-- Template-specific CSS
 format VARCHAR DEFAULT 'liquid', \-- 'liquid' or 'json'
 status INTEGER DEFAULT 0, \-- 0: draft, 1: active
 applicable INTEGER DEFAULT 0, \-- 0: everything, 1: specific
 "default" BOOLEAN DEFAULT FALSE, \-- Default template for this themeable\_type
 fluid BOOLEAN DEFAULT FALSE, \-- System template flag
 created\_at TIMESTAMP DEFAULT NOW(),
 updated\_at TIMESTAMP DEFAULT NOW()
);

\-- Index for finding default templates by company and type
CREATE INDEX idx\_app\_theme\_templates\_theme\_company\_type\_default
ON application\_theme\_templates (application\_theme\_id, company\_id, themeable\_type, "default")
WHERE status \= 1;

\-- Legacy Theme model (maintained for backwards compatibility)
CREATE TABLE themes (
 id SERIAL PRIMARY KEY,
 company\_id INTEGER REFERENCES companies(id),
 application\_theme\_template\_id INTEGER REFERENCES application\_theme\_templates(id),
 name VARCHAR NOT NULL,
 public BOOLEAN DEFAULT TRUE,
 created\_at TIMESTAMP DEFAULT NOW(),
 updated\_at TIMESTAMP DEFAULT NOW()
);

**Key Schema Features:**

- **Company Isolation**: Templates and themes are scoped by `company_id`
- **Root Theme Support**: Root themes have `company_id: NULL` and are shared across the platform
- **Template Hierarchy**: Templates can have parent-child relationships via `parent_id`
- **Status Management**: Draft/active status for both themes and templates
- **Default Template Resolution**: Index optimized for finding default templates by type
- **Format Flexibility**: Templates can be either Liquid or JSON format

### Themeable Types Enumeration

\# ApplicationThemeTemplate themeable\_type enum
enum :themeable\_type, {
 product: 0, \# Individual product pages
 medium: 1, \# Media post pages
 enrollment\_pack: 2, \# Course/enrollment pages
 shop\_page: 3, \# Shop/catalog pages
 navbar: 4, \# Navigation header
 library: 5, \# Library/playlist pages
 page: 6, \# Custom pages
 components: 7, \# Reusable components
 library\_navbar: 8, \# Library-specific navigation
 sections: 9, \# Template sections
 locales: 10, \# Localization templates
 footer: 11, \# Page footer
 layouts: 12, \# Page wrapper layouts
 category\_page: 13, \# Category browsing
 collection\_page: 14, \# Collection browsing
 cart\_page: 15, \# Shopping cart
 config: 16, \# Theme configuration
 home\_page: 17, \# Homepage layout
 mysite: 18, \# MySite pages
 join\_page: 19 \# Registration pages
}

### Caching Architecture

**Multi-Level Caching Strategy:**

1. **Stylesheet Caching** (1 hour expiration):
 
 cache\_key \= "theme\_stylesheet:#{theme\_id}:global:#{updated\_at.to\_i}:#{variables\_hash}"
 Rails.cache.fetch(cache\_key, expires\_in: 1.hour) do
 process\_stylesheet\_with\_variables(stylesheet)
 end
 
2. **Global Embeds Caching** (1 hour / 5 seconds in development):
 
 cache\_key \= "global\_embeds:#{company\_id}:#{global\_embeds\_updated\_at}"
 Rails.cache.fetch(cache\_key, expires\_in: cache\_duration) do
 fetch\_and\_process\_global\_embeds
 end
 
3. **Rendered Template Caching** (24 hours expiration):
 
 variables\_digest \= Digest::SHA256.hexdigest(
 JSON.dump(normalize\_for\_cache(@theme\_template\_variables.except("content\_for\_header")))
 )
 cache\_key \= "page\_builder/#{@theme\_template.cache\_key\_with\_version}/#{variables\_digest}"
 
 Rails.cache.delete(cache\_key) if params\[:clear\_cache\].present?
 
 if params\[:clear\_all\_cache\].present?
 Rails.cache.delete\_matched("page\_builder/#{@theme\_template.cache\_key\_with\_version}/\*")
 end
 
 Rails.cache.fetch(cache\_key, expires\_in: 24.hours) do
 PageBuilder.render\_template(@theme\_template, variables: @theme\_template\_variables)
 end
 
4. **Variable Optimization**:
 
 - Language and country data memoized to prevent N+1 queries
 - Base URL resolution cached per request
 - Template resolution cached within request scope

**Cache Invalidation:**

- Stylesheet cache invalidated when theme variables or CSS content changes
- Global embeds cache invalidated when company embed settings change
- Rendered template cache invalidated when any variable is changed, or template updated
- Theme template cache invalidated on publish/unpublish actions

### Publishing and Versioning System

**Publishing Workflow:**

\# ApplicationTheme publishing
def publish
 update!(status: :active)
 application\_theme\_templates.each(&:publish) \# Publish all templates
end

\# ApplicationThemeTemplate publishing
def make\_default
 ApplicationRecord.transaction do
 \# Set this as the active default template
 active!
 update!(default: true)

 \# Deactivate other default templates of same type
 application\_theme.application\_theme\_templates
 .where(themeable\_type: themeable\_type)
 .where.not(id: id)
 .update\_all(default: false)
 end
end

**Theme Version Management:**

- Themes use semantic versioning (major.minor format)
- Minor versions automatically increment on template changes
- Major versions increment on root themes updates
- Audit trail maintains complete change history
- Auto-upgrade system for compatible theme versions

**Theme Template Version Management:**

## Theme Template Versioning

When you make changes to a template, a new version is created.

![Screenshot 2025-01-22 at 1 38 48 PM](https://cdn.filestackcontent.com/N3wOBC9KSwedJRRFtrvG)

#### You can revert to a previous version by:

- Click on the 'Versions' dropdown on the theme editor.
 
- Select the version you want to revert to.
 
- Click on 'Preview' to preview the version to make sure it is what you want.
 
- Click on 'Publish' to publish the version.
 

#### How to compare changes with a previous version

- Click on the 'Versions' dropdown on the theme editor.
 
- Click on Compare icon on the right to compare the changes.
 

## Template Development Patterns

### Liquid Template Structure

**Basic Template Format:**

{'%' comment '%'}
 Product template with layout, filters, and sections
{'%' endcomment '%'}

{'%' layout 'theme' '%'}

<article class\="product-page"\>
 <header class\="product-header"\>
 <h1\>{{ product.name }}</h1\>
 <div class\="product-price"\>
 {{ product.price | money }}
 </div\>
 <div class\="product-meta"\>
 <span class\="product-date"\>{{ product.created\_at | date: '%B %d, %Y' }}</span\>
 {'%' if product.tags.size > 0 '%'}
 <div class\="product-tags"\>{{ product.tags | join: ', ' }}</div\>
 {'%' endif '%'}
 </div\>
 </header\>

 <div class\="product-content"\>
 <div class\="product-images"\>
 {'%' for image in product.images '%'}
 <img src\="{{ image.url | img\_url: '400x300' }}"
 alt\="{{ image.alt }}" />
 {'%' endfor '%'}
 </div\>

 <div class\="product-description"\>
 {{ product.description | truncate: 250 }}
 </div\>
 </div\>

 {'%' if product.available '%'}
 <div class\="product-purchase"\>
 <button class\="btn-purchase" data-product-id\="{{ product.id }}"\>
 {{ 'products.add\_to\_cart' | t }}
 </button\>
 </div\>
 {'%' endif '%'}
</article\>

**Complete JSON Template Example:**

{
 "name": "Product Template",
 "layout": "theme", 
 "sections": {
 "product-header": {
 "type": "product\_header",
 "settings": {
 "show\_vendor": true,
 "show\_price": true,
 "title\_size": "large",
 "background\_color": "linear-gradient(135deg, #667eea 0%, #764ba2 100%)",
 "text\_alignment": "center",
 "padding": 24
 }
 },
 "product-gallery": {
 "type": "product\_gallery",
 "settings": {
 "gallery\_type": "slideshow",
 "show\_thumbnails": true,
 "thumbnail\_position": "bottom"
 },
 "blocks": {
 "image\_1": {
 "type": "product\_image",
 "settings": {
 "image\_size": "large",
 "show\_zoom": true
 }
 },
 "video\_1": {
 "type": "product\_video",
 "settings": {
 "video\_url": "{{ product.featured\_video.url }}",
 "autoplay": false
 }
 }
 },
 "block\_order": \["image\_1", "video\_1"\]
 },
 "product-info": {
 "type": "product\_information",
 "settings": {
 "show\_description": true,
 "show\_features": true,
 "description\_length": 300
 }
 }
 },
 "order": \["product-header", "product-gallery", "product-info"\]
}

**Corresponding Section Template (sections/product\_header.liquid):**

{'%' comment '%'}
 Product Header Section
 Note: Using 'background' CSS property instead of 'background-color' 
 to support both solid colors and gradients from color\_background setting
{'%' endcomment '%'}

<div class\="product-header"
 style\="background: {{ section.settings.background\_color }}"\>
 <div class\="product-header-content"\>
 {'%' if section.settings.show\_vendor and product.vendor '%'}
 <div class\="product-vendor"\>{{ product.vendor }}</div\>
 {'%' endif '%'}

 <h1 class\="product-title product-title--{{ section.settings.title\_size }}"\>
 {{ product.name }}
 </h1\>

 {'%' if section.settings.show\_price '%'}
 <div class\="product-price"\>
 <span class\="price"\>{{ product.price | money }}</span\>
 {'%' if product.compare\_at\_price '%'}
 <span class\="compare-price"\>{{ product.compare\_at\_price | money }}</span\>
 {'%' endif '%'}
 </div\>
 {'%' endif '%'}
 </div\>
</div\>

{'%' schema '%'}
{
 "name": "Product Header",
 "settings": \[
 {
 "type": "header",
 "id": "content\_group",
 "label": "Content Settings"
 },
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
 "type": "header",
 "id": "style\_group",
 "label": "Style Settings"
 },
 {
 "type": "select",
 "id": "title\_size",
 "label": "Title size",
 "options": \[
 { "value": "small", "label": "Small" },
 { "value": "medium", "label": "Medium" },
 { "value": "large", "label": "Large" }
 \],
 "default": "medium"
 },
 {
 "type": "color\_background",
 "id": "background\_color",
 "label": "Background color",
 "default": "#ffffff"
 },
 {
 "type": "text\_alignment",
 "id": "text\_alignment",
 "label": "Text alignment",
 "default": "left"
 },
 {
 "type": "range",
 "id": "padding",
 "label": "Section padding",
 "min": 0,
 "max": 100,
 "step": 4,
 "default": 20
 }
 \]
}
{'%' endschema '%'}

**Rendered HTML Output Example:**

When the above JSON template and section template are processed together, here's what the final HTML output would look like:

<!-- Product template with product-header section -->
<!DOCTYPE html\>
<html\>
<head\>
 <title\>Wireless Bluetooth Headphones</title\>
 <!-- Theme stylesheets with CSS variables applied -->
 <style\>
 :root {
 --primary-color: #2c5aa0;
 --secondary-color: #f8f9fa;
 --font-family: Inter, sans-serif;
 --heading-font: Playfair Display, serif;
 }
 .product-header { background-color: var(--primary-color); }
 .product-title--large { font-size: 2.5rem; font-family: var(--heading-font); }
 </style\>
</head\>
<body\>
 <!-- Layout wrapper from theme.liquid -->
 <div class\="theme-container"\>
 
 <!-- Content from product template sections -->
 
 <!-- product-header section rendered -->
 <!-- Note: Using 'background' property (not background-color) to support gradient -->
 <div class\="product-header" 
 style\="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); 
 text-align: center; 
 padding: 24px;"\>
 <div class\="product-header-content"\>
 <div class\="product-vendor"\>AudioTech</div\>
 
 <h1 class\="product-title product-title--large"\>
 Wireless Bluetooth Headphones
 </h1\>
 
 <div class\="product-price"\>
 <span class\="price"\>$199.99</span\>
 <span class\="compare-price"\>$249.99</span\>
 </div\>
 </div\>
 </div\>
 
 <!-- product-gallery section rendered -->
 <div class\="product-gallery" data-gallery-type\="slideshow"\>
 <div class\="main-gallery"\>
 <!-- image\_1 block rendered -->
 <div class\="gallery-item" 
 data-fluid-section-block-id\="image\_1"
 data-fluid-section-id\="product-gallery"
 data-fluid-section-block-type\="product\_image"\>
 <img src\="https://cdn.example.com/headphones-main-600x400.jpg" 
 alt\="Wireless Bluetooth Headphones - Main View"
 class\="product-image product-image--large" />
 <div class\="image-zoom-overlay" style\="display: block;"\></div\>
 </div\>
 
 <!-- video\_1 block rendered -->
 <div class\="gallery-item"
 data-fluid-section-block-id\="video\_1" 
 data-fluid-section-id\="product-gallery"
 data-fluid-section-block-type\="product\_video"\>
 <video controls preload\="metadata"\>
 <source src\="https://cdn.example.com/headphones-demo.mp4" type\="video/mp4"\>
 Your browser does not support the video tag.
 </video\>
 </div\>
 </div\>
 
 <div class\="thumbnail-navigation" style\="position: bottom;"\>
 <button class\="thumbnail-btn active" data-target\="image\_1"\></button\>
 <button class\="thumbnail-btn" data-target\="video\_1"\></button\>
 </div\>
 </div\>
 
 <!-- product-info section rendered -->
 <div class\="product-information"\>
 <div class\="product-description"\>
 <p\>Experience premium sound quality with our latest wireless Bluetooth headphones. 
 Featuring active noise cancellation, 30-hour battery life, and comfortable over-ear design.</p\>
 </div\>
 
 <div class\="feature-highlights"\>
 <h3\>Key Features</h3\>
 <ul\>
 <li\>Active Noise Cancellation</li\>
 <li\>30-hour battery life</li\>
 <li\>Quick charge technology</li\>
 <li\>Premium leather ear cushions</li\>
 </ul\>
 </div\>
 </div\>
 
 </div\>
 
 <!-- JavaScript for section interactions -->
 <script\>
 // Gallery navigation functionality
 document.querySelectorAll('.thumbnail-btn').forEach(btn => {
 btn.addEventListener('click', function() {
 const targetId = this.dataset.target;
 // Switch gallery view logic
 });
 });
 </script\>
</body\>
</html\>

**Key Points About the Rendered Output:**

1. **CSS Variables Applied**: The `$primary_color` variable from the JSON became `#ffffff` in the final CSS
2. **Settings Injection**: `section.settings.title_size` became `product-title--large` CSS class
3. **Block Processing**: Each block got `data-fluid-*` attributes for frontend editing
4. **Layout Wrapping**: Content was wrapped in the theme layout structure
5. **Dynamic Content**: Product data (`product.name`, `product.price`) was injected from variables
6. **Schema Validation**: All settings matched their schema definitions (show\_vendor: true, etc.)
7. **Type Safety**: All default values matched their expected data types (booleans, strings, numbers)

**Processing Flow Summary:**

JSON Template → Section Resolution → Settings Merge → Block Processing → Liquid Rendering → Layout Application → Final HTML

This demonstrates how the abstract JSON configuration becomes concrete, interactive HTML that users see in their browsers.

### Tips for Creating Better Section Schemas

Follow these tips to create sections that are easy for users to customize:

**1\. Organize Settings into Groups** Use `header` settings to group related options together. This creates collapsible sections that make the editor easier to navigate.

{
 "settings": \[
 {
 "type": "header",
 "id": "content\_group",
 "label": "Content Settings"
 },
 // ... content-related settings ...
 {
 "type": "header",
 "id": "style\_group",
 "label": "Style Settings"
 },
 // ... style-related settings ...
 \]
}

**2\. Always Set Good Defaults** Every setting should have a default value so your section looks good right away. Users can then customize from there instead of starting from scratch.

**3\. Pick the Right Setting Type** Choose the setting type that makes the most sense:

- Use `text_alignment` for text alignment (gives a visual picker instead of a dropdown)
- Use `color_background` when you want to allow both solid colors and gradients
- Use `textarea` when users need formatting options like bold, italic, or links
- Use `range` for numbers where there's a clear minimum and maximum value

**4\. Handle Colors Correctly** When using `color_background` settings (which can be gradients), always use the `background` CSS property:

<!-- ✅ Correct -->
<div style="background: {{ section.settings.background\_color }}">

<!-- ❌ Wrong - gradients won't render -->
<div style="background-color: {{ section.settings.background\_color }}">

**5\. Use the Right Data Format** Make sure your default values match what the setting expects:

- Single resource selectors (like `product`) use number IDs: `123`
- Multiple resource selectors (like `product_list`) use number arrays: `[123, 456]`
- Link lists use text arrays: `["/home", "/about"]`

**6\. Limit Selection When Needed** For settings that let users select multiple items, set a reasonable limit to keep your pages fast:

{
 "type": "product\_list",
 "id": "featured\_products",
 "label": "Featured Products",
 "default": \[\],
 "limit": 10
}

**7\. Write Clear Labels** Use simple, descriptive labels that tell users exactly what each setting does. Good labels help users customize without confusion.

## Reserved Section IDs

When creating new sections or templates, certain section IDs are reserved by the system and cannot be used for custom sections.

**⚠️ Reserved IDs You Cannot Use:**

- `main_navbar` - Reserved for the main navigation bar
- `main_footer` - Reserved for the main footer

**Why These IDs Are Reserved:**

Reserved sections are special system-level sections that provide critical functionality:

- **`main_navbar`**: The main navigation bar that appears at the top of pages
- **`main_footer`**: The main footer that appears at the bottom of pages

These sections are automatically included in templates and managed by the platform. Attempting to create sections with these IDs will cause conflicts and errors.

**What You Can Do With Reserved Sections:**

- ✅ Edit their settings (colors, fonts, spacing, content)
- ✅ Customize their appearance through the visual editor
- ✅ Configure their content (text, links, menu items)

**What You Cannot Do:**

- ❌ Delete reserved sections from templates
- ❌ Create new sections with reserved IDs
- ❌ Clone or duplicate sections with reserved IDs
- ❌ Rename their IDs

**Creating Custom Navigation or Footer Sections:**

If you need additional navigation or footer sections, use any unique IDs (except the reserved ones):

{
 "sections": {
 "custom\_navbar": {
 "type": "navbar",
 "settings": { }
 },
 "secondary\_navbar": {
 "type": "navbar",
 "settings": { }
 },
 "page\_footer": {
 "type": "footer",
 "settings": { }
 },
 "custom\_footer": {
 "type": "footer",
 "settings": { }
 }
 }
}

**Section ID Requirements:**

When creating section IDs, ensure they:

1. Are not `main_navbar` or `main_footer`
2. Are unique within your template

**Valid Section ID Examples:**

"hero\_section" // ✅ Valid
"product\_grid" // ✅ Valid
"section1" // ✅ Valid
"MySection" // ✅ Valid
"custom-nav" // ✅ Valid

**Invalid Section ID Examples:**

"main\_navbar" // ❌ Reserved ID
"main\_footer" // ❌ Reserved ID

**Troubleshooting ID Conflicts:**

If you encounter a "Section ID conflict" error:

1. Verify your section ID is not `main_navbar` or `main_footer`
2. Check that the ID is unique within your template
3. Try using a different ID name

**Why This Matters:**

Using reserved IDs causes several issues:

- **ID Conflicts**: The system already uses these IDs, causing database conflicts
- **Unexpected Behavior**: The visual editor may not handle duplicate IDs correctly
- **Template Errors**: Your template may fail to render or save properly
- **Data Loss**: Settings for reserved sections may be overwritten or lost

### CSS Variable Integration

**Theme Variables Definition:**

{
 "primary\_color": "#007bff",
 "secondary\_color": "#6c757d",
 "font\_family": "Helvetica, Arial, sans-serif",
 "heading\_font": "'Playfair Display', serif",
 "border\_radius": "4px",
 "container\_width": "1200px"
}

**Stylesheet with Variable Substitution:**

/\* Global stylesheet with CSS variables \*/
:root {
 \--primary-color: $primary\_color;
 \--secondary-color: $secondary\_color;
 \--font-family: $font\_family;
 \--heading-font: $heading\_font;
 \--border-radius: $border\_radius;
 \--container-width: $container\_width;
}

.product-page {
 font-family: var(\--font-family);
 max-width: var(\--container-width);
 margin: 0 auto;
}

.product-header h1 {
 font-family: var(\--heading-font);
 color: var(\--primary-color);
}

.btn-purchase {
 /\* Use background instead of background-color to support gradients \*/
 background: var(\--primary-color);
 border-radius: var(\--border-radius);
}

/\* Example of section with color\_background setting that might be a gradient \*/
.hero-section {
 /\* ✅ Correct - works with both solid colors and gradients \*/
 background: var(\--hero-background);
 
 /\* ❌ Wrong - would not render gradients correctly \*/
 /\* background-color: var(--hero-background); \*/
}

### Component and Section System

**Section Template (sections/product\_gallery.liquid):**

{'%' comment '%'}
 Reusable product gallery section
{'%' endcomment '%'}

<div class\="product-gallery" data-gallery-type\="{{ section.settings.gallery\_type }}"\>
 <div class\="main-image"\>
 <img src\="{{ product.featured\_image.url }}"
 alt\="{{ product.featured\_image.alt }}"
 id\="main-product-image" />
 </div\>

 {'%' if section.settings.show\_thumbnails and product.images.size > 1 '%'}
 <div class\="thumbnail-list"\>
 {'%' for image in product.images '%'}
 <img src\="{{ image.url | img\_url: '100x100' }}" 
 alt\="{{ image.alt }}"
 class\="thumbnail {'%' if forloop.first '%'}active{'%' endif '%'}"
 data-main-image\="{{ image.url }}" />
 {'%' endfor '%'}
 </div\>
 {'%' endif '%'}
</div\>

{'%' schema '%'}
{
 "name": "Product Gallery",
 "settings": \[
 {
 "type": "header",
 "id": "layout\_header",
 "label": "Layout Settings"
 },
 {
 "type": "select",
 "id": "gallery\_type",
 "label": "Gallery Type",
 "options": \[
 { "value": "slideshow", "label": "Slideshow" },
 { "value": "grid", "label": "Grid" },
 { "value": "carousel", "label": "Carousel" }
 \],
 "default": "slideshow"
 },
 {
 "type": "checkbox",
 "id": "show\_thumbnails",
 "label": "Show thumbnails",
 "default": true
 },
 {
 "type": "range",
 "id": "images\_per\_row",
 "label": "Images per row",
 "min": 2,
 "max": 6,
 "step": 1,
 "default": 3
 },
 {
 "type": "header",
 "id": "style\_header",
 "label": "Style Settings"
 },
 {
 "type": "color\_background",
 "id": "gallery\_background",
 "label": "Gallery background",
 "default": "#ffffff"
 },
 {
 "type": "range",
 "id": "image\_spacing",
 "label": "Image spacing (px)",
 "min": 0,
 "max": 40,
 "step": 4,
 "default": 8
 }
 \],
 "blocks": \[
 {
 "type": "image",
 "name": "Gallery Image",
 "settings": \[
 {
 "type": "image\_picker",
 "id": "image",
 "label": "Image",
 "default": ""
 },
 {
 "type": "text",
 "id": "caption",
 "label": "Caption",
 "default": ""
 },
 {
 "type": "checkbox",
 "id": "enable\_zoom",
 "label": "Enable zoom on hover",
 "default": true
 }
 \]
 }
 \]
}
{'%' endschema '%'}

## Theme Customization Architecture

The themes system provides multiple levels of customization, allowing companies to progressively tailor their user experience from high-level branding changes down to individual template modifications. Each level builds upon the previous one, creating a flexible hierarchy that balances ease of use with customization depth.

### Customization Hierarchy

\[object Object\]

**Level 1: Theme Selection and Cloning** Companies begin customization by selecting and cloning a root theme:

\# Find available root themes
root\_themes \= ApplicationTheme.root.where(name: \['fluid', 'vox', 'base'\])

\# Clone a root theme for company customization
root\_theme \= ApplicationTheme.root.find\_by(name: 'fluid')
company\_theme \= root\_theme.deep\_clone(company.id)
company\_theme.name \= "Acme Corp Custom Theme"
company\_theme.save!

**What gets cloned:**

- All application theme templates (product, home\_page, sections, etc.)
- Theme-level settings and variables
- File resources and assets
- Template relationships and hierarchy

**What remains linked to root:**

- Template inheritance for future updates
- Version compatibility for auto-upgrades
- Schema definitions for sections and components

**Level 2: CSS Variable Customization** The most common customization approach involves modifying CSS variables to change colors, fonts, and layout properties:

\# Update theme variables
company\_theme.variables \= {
 primary\_color: '#2c5aa0', \# Brand primary color
 secondary\_color: '#f8f9fa', \# Accent color
 font\_family: 'Inter, sans-serif', \# Primary font
 heading\_font: 'Playfair Display, serif', \# Header font
 border\_radius: '8px', \# Rounded corner style
 container\_width: '1440px', \# Maximum content width
 spacing\_unit: '16px' \# Base spacing measurement
}.to\_json

company\_theme.save!

**Variable Processing:** Variables are injected into stylesheets during rendering:

/\* Before processing \*/
.primary-button {
 background-color: $primary\_color;
 font-family: $font\_family;
 border-radius: $border\_radius;
}

/\* After processing \*/
.primary-button {
 background-color: #2c5aa0;
 font-family: Inter, sans-serif;
 border-radius: 8px;
}

**Level 3: Custom Stylesheet Overrides** For advanced styling needs, companies can add custom CSS that supplements or overrides base theme styles:

company\_theme.custom\_stylesheet \= <<~CSS
 /\* Brand-specific component styling \*/
 .product-card {
 box-shadow: 0 4px 12px rgba(44, 90, 160, 0.15);
 transition: transform 0.2s ease;
 }
 
 .product-card:hover {
 transform: translateY(-4px);
 }
 
 /\* Custom responsive breakpoints \*/
 @media (max-width: 768px) {
 .container {
 padding: 0 12px;
 }
 }
CSS

company\_theme.save!

**Level 4: Template Customization** Companies can override specific templates while inheriting others from the parent theme:

\# Create a custom product template
custom\_product\_template \= company\_theme.application\_theme\_templates.create!(
 name: 'Premium Product Layout',
 themeable\_type: :product,
 content: custom\_liquid\_content,
 format: :liquid
)

\# Make it the default for all products
custom\_product\_template.make\_default

\# Or assign to specific products
premium\_product \= Product.find(123)
premium\_product.application\_theme\_template \= custom\_product\_template
premium\_product.save!

**Level 5: Content-Specific Template Assignment** The most granular level allows assigning specific templates to individual content items:

\# Create a special template for featured products
featured\_template \= company\_theme.application\_theme\_templates.create!(
 name: 'Featured Product Showcase',
 themeable\_type: :product,
 applicable: :specific, \# Only for specifically assigned items
 content: featured\_product\_liquid,
 format: :liquid
)

\# Assign to featured products
featured\_products \= Product.where(featured: true)
featured\_products.update\_all(application\_theme\_template\_id: featured\_template.id)

### Template Resolution Process

Understanding how the system chooses which template to render is crucial for effective customization. The system follows a priority-based hierarchy, checking each level until it finds a suitable template:

**Priority 1: URL Parameter Override** (Highest Priority)

/products/123?template\_id\=456
# Uses ApplicationThemeTemplate.find(456) regardless of other settings
# Use case: Testing, previewing, or temporary template switches

**Priority 2: Content-Specific Assignment**

\# Product has specific template assigned
product.application\_theme\_template\_id \= 789
\# Uses template 789 for this product specifically
\# Use case: Featured products, premium content, special campaigns

**Priority 3: Company Theme Default**

\# Find default template for this content type in company's active theme
ApplicationThemeTemplate.default\_for\_company\_themes(
 company: product.company,
 themeable\_type: :product
).first
\# Use case: Standard company branding applied to all content of this type

**Priority 4: Root Theme Fallback** (Lowest Priority)

\# If company theme lacks this template type, inherit from root theme
root\_theme \= ApplicationTheme.root.find\_by(name: company\_theme.name)
root\_theme.application\_theme\_templates.product.find\_by(default: true)
\# Use case: Ensures pages always render even with incomplete customization

### Recommended Customization Workflows

**Workflow 1: Basic Company Branding** (Most Common) _Goal: Apply consistent brand colors, fonts, and basic styling_

1. **Clone Base Theme**: Select and clone appropriate root theme (`fluid`, `vox`, or `base`)
2. **Configure Variables**: Update CSS variables for brand colors, fonts, spacing
3. **Add Custom CSS**: Include brand-specific styling that can't be achieved through variables
4. **Test Preview**: Use preview URLs to test changes before going live
5. **Publish**: Activate the theme for all company pages

_Timeline: 1-2 hours | Skill Level: Basic_

**Workflow 2: Advanced Template Customization** (Advanced Users) _Goal: Modify page layouts and add custom functionality_

1. **Complete Basic Branding**: Ensure foundation is solid before template changes
2. **Audit Current Templates**: Identify which page types need layout modifications
3. **Create Custom Templates**: Build modified versions of key templates (product, home\_page, etc.)
4. **Set Template Defaults**: Make custom templates the default for their content types
5. **Comprehensive Testing**: Test all page types and responsive behavior
6. **Gradual Rollout**: Consider phased publishing for major changes

_Timeline: 1-2 weeks | Skill Level: Advanced_

**Workflow 3: Content-Specific Customization** (Enterprise) _Goal: Different layouts for different content categories or campaigns_

1. **Establish Template Foundation**: Complete advanced template customization first
2. **Define Content Categories**: Identify which content needs special treatment
3. **Create Specialized Templates**: Build templates with `applicable: :specific` setting
4. **Implement Assignment Strategy**: Systematically assign templates to appropriate content
5. **Monitor and Optimize**: Track performance and user engagement by template type
6. **Maintain Consistency**: Ensure specialized templates maintain brand coherence

_Timeline: 2-4 weeks | Skill Level: Expert_

### Theme Inheritance and Upgrade System

**How Inheritance Works:** Company themes maintain a connection to their root themes, allowing them to receive updates while preserving customizations:

\# Check upgrade eligibility
company\_theme.auto\_upgradeable?
\# => true if minor version is 0 (no template modifications)

\# Check for available updates
company\_theme.outdated?
\# => true if root theme has newer version

\# Automatic upgrade (safe customizations only)
if company\_theme.auto\_upgradeable? && company\_theme.outdated?
 CompanyThemeUpgradeJob.perform\_later(company\_theme)
end

**Impact of Customization Types on Upgrades:**

| Customization Type | Version Impact | Auto-Upgrade | Notes |
| --- | --- | --- | --- |
| CSS Variables | None | ✅ Compatible | Variables preserved during upgrade |
| Custom Stylesheets | None | ✅ Compatible | Custom CSS appended to upgraded styles |
| New Templates | None | ✅ Compatible | Custom templates remain untouched |
| Modified Templates | Minor +1 | ❌ Manual Review | Prevents automatic upgrades |
| Asset Changes | None | ✅ Compatible | Company assets preserved |

**Upgrade Strategies:**

**Automatic Upgrades** (Recommended for most companies)

- Stick to CSS variables and custom stylesheets for brand customization
- Create new templates rather than modifying existing ones
- Benefits: Always get latest features and bug fixes automatically

**Manual Upgrade Control** (For complex customizations)

- Modify existing templates when layout changes are needed
- Accept that upgrades require manual review and testing
- Benefits: Maximum customization flexibility with controlled update process

### Asset and File Management

**Theme-Level Assets:**

\# Add theme-specific assets
company\_theme.file\_resources.create!(
 filename: 'brand-logo.svg',
 url: 'https://cdn.example.com/logo.svg',
 content\_type: 'image/svg+xml'
)

\# Add cover images for theme preview
company\_theme.images.create!(
 image\_url: 'https://cdn.example.com/theme-preview.jpg',
 position: 1
)

**Template-Level Assets:**

\# Create template-specific JavaScript
product\_template.assets.create!(
 filename: 'product\_interactions.js',
 content: javascript\_code,
 content\_type: 'application/javascript'
)

\# Reference in template
\# => <script>{{ 'product\_interactions.js' | asset\_url }}</script>

### Testing and Validation

**Preview Before Publishing:**

\# Preview template changes before making them live
preview\_url \= ApplicationThemeTemplates::TemplateUrls.new(template).preview\_url
\# => /templates/123/preview?version=draft

**Validation Checks:**

- **Liquid Syntax**: Templates are parsed for syntax errors before saving
- **Schema Validation**: JSON templates validated against section schemas
- **Asset References**: Broken asset links detected and reported
- **Variable Dependencies**: Missing variables identified in stylesheets

**Rollback Capabilities:**

\# Access version history
template.versions
\# => \[{ version: 1, author: "Jane Doe", published: true, created\_at: "..." }\]

\# Revert to previous version
template.audits.find\_by(version: 2).restore
template.publish

### Practical Implementation Example

**Complete Company Branding Implementation:** Here's a real-world example of customizing a theme for "Acme Corp":

\# 1. Clone root theme
root\_theme \= ApplicationTheme.root.find\_by(name: 'fluid')
acme\_theme \= root\_theme.deep\_clone(company.id)
acme\_theme.update!(
 name: "Acme Corp Theme",
 description: "Custom theme for Acme Corp branding"
)

\# 2. Configure brand variables
acme\_theme.variables \= {
 \# Brand Colors
 primary\_color: '#c8102e', \# Acme red
 secondary\_color: '#f5f5f5', \# Light gray
 accent\_color: '#1f4e79', \# Navy blue
 
 \# Typography
 font\_family: 'Inter, sans-serif',
 heading\_font: 'Montserrat, sans-serif',
 font\_size\_base: '16px',
 
 \# Layout
 container\_width: '1200px',
 border\_radius: '6px',
 spacing\_unit: '20px'
}.to\_json

\# 3. Add custom brand styling
acme\_theme.custom\_stylesheet \= <<~CSS
 /\* Acme-specific component styling \*/
 .hero-section {
 background: linear-gradient(135deg, var(--primary-color), var(--accent-color));
 }
 
 .product-card {
 border: 2px solid var(--primary-color);
 box-shadow: 0 4px 8px rgba(200, 16, 46, 0.1);
 }
 
 .btn-primary {
 background: var(--primary-color);
 font-weight: 600;
 text-transform: uppercase;
 letter-spacing: 0.5px;
 }
 
 .navbar {
 border-bottom: 3px solid var(--primary-color);
 }
CSS

\# 4. Create custom product template for featured items
featured\_template \= acme\_theme.application\_theme\_templates.create!(
 name: 'Acme Featured Product',
 themeable\_type: :product,
 applicable: :specific,
 format: :liquid,
 content: <<~LIQUID
 {'%' layout 'theme' '%'}
 
 <div class="featured-product-hero">
 <div class="hero-badge">Featured Product</div>
 <h1 class="hero-title">{{ product.name }}</h1>
 <div class="hero-price">{{ product.price | money }}</div>
 </div>
 
 <div class="product-gallery-featured">
 {'%' for image in product.images limit: 5 '%'}
 <img src="{{ image.url | img\_url: '600x400' }}" 
 alt="{{ image.alt }}" 
 class="gallery-image" />
 {'%' endfor '%'}
 </div>
 
 <div class="product-description-enhanced">
 {{ product.description }}
 
 {'%' if product.features '%'}
 <div class="feature-highlights">
 <h3>Key Features</h3>
 <ul>
 {'%' for feature in product.features '%'}
 <li>{{ feature }}</li>
 {'%' endfor '%'}
 </ul>
 </div>
 {'%' endif '%'}
 </div>
 
 <div class="cta-section">
 <button class="btn-primary btn-large" data-product-id="{{ product.id }}">
 {{ 'products.add\_to\_cart' | t }}
 </button>
 </div>
 LIQUID
)

\# 5. Assign featured template to premium products
premium\_products \= Product.where(company: company, featured: true)
premium\_products.update\_all(application\_theme\_template\_id: featured\_template.id)

\# 6. Publish theme
acme\_theme.publish

**Result:**

- All pages use Acme's brand colors and fonts automatically
- Standard products use the default template with brand styling
- Featured products get the enhanced layout with special CTAs
- Theme can receive automatic updates since only safe customizations were used
- Consistent branding across all customer touchpoints

This approach demonstrates how the multi-layered customization system enables sophisticated branding while maintaining system stability and upgrade compatibility.

## API Design and Usage

### Theme Management API

**Get Company Themes:**

GET /api/application\_themes

Returns list of all themes available to the company (root + company-specific).

**Create Company Theme:**

POST /api/application\_themes

Creates a new theme for the company. Request body:

{
 "name": "Custom Company Theme",
 "description": "Customized theme for our brand",
 "clone\_from\_id": 123, // Optional: clone from existing theme
 "variables": {
 "primary\_color": "#ff6b6b",
 "secondary\_color": "#4ecdc4"
 }
}

**Update Theme:**

PUT /api/application\_themes/{theme\_id}

Updates theme properties. Request body:

{
 "name": "Updated Theme Name",
 "variables": {
 "primary\_color": "#e74c3c",
 "font\_family": "'Open Sans', sans-serif"
 },
 "custom\_stylesheet": ".custom-class { margin: 20px; }"
}

**Publish Theme:**

POST /api/application\_themes/{theme\_id}/publish

Activates the theme for the company, deactivating the current active theme.

### Template Management API

**Get Theme Templates:**

GET /api/application\_themes/{theme\_id}/templates

Query parameters:

- `themeable_type`: Filter by template type (product, home\_page, etc.)
- `status`: Filter by status (draft, active)

**Create Template:**

POST /api/application\_themes/{theme\_id}/templates

Request body:

{
 "name": "Custom Product Template",
 "themeable\_type": "product",
 "content": "<div>{{ product.name }}</div>",
 "format": "liquid",
 "applicable": "specific" // or "everything"
}

**Update Template:**

PUT /api/application\_theme\_templates/{template\_id}

Request body:

{
 "content": "{'%' layout 'theme' '%'}<h1>{{ product.name }}</h1>",
 "head": "<meta name='description' content='{{ product.description }}' />",
 "stylesheet": ".product-title { color: red; }"
}

**Make Template Default:**

POST /api/application\_theme\_templates/{template\_id}/make\_default

Sets this template as the default for its `themeable_type` within the theme.

### Template Preview API

**Preview Template:**

GET /api/application\_theme\_templates/{template\_id}/preview

Query parameters:

- `version`: Specific version to preview (defaults to current)
- `record_id`: ID of content item to preview with (for product/medium templates)

Returns rendered HTML of the template with sample or specified content.

## Error Handling

**Theme Validation Errors (422 Unprocessable Entity):**

{
 "status": "unprocessable\_entity",
 "errors": {
 "variables": \["Variables must be valid JSON"\],
 "name": \["Name cannot be blank"\]
 }
}

**Template Validation Errors (422 Unprocessable Entity):**

{
 "status": "unprocessable\_entity",
 "errors": {
 "content": \["Content cannot be blank"\],
 "themeable\_type": \["Themeable type is not included in the list"\]
 }
}

**Theme Not Found (404 Not Found):**

{
 "status": "not\_found",
 "errors": {
 "base": \["Theme not found"\]
 }
}

**Active Theme Deletion (422 Unprocessable Entity):**

{
 "status": "unprocessable\_entity",
 "errors": {
 "base": \["Cannot delete a published theme"\]
 }
}

## Model Usage Examples

\# Find company's active theme
company \= Company.find(123)
active\_theme \= company.current\_theme
\# => ApplicationTheme with status: :active

\# Get all templates for a theme
product\_templates \= active\_theme.application\_theme\_templates.product
default\_product\_template \= product\_templates.find\_by(default: true)

\# Create a new company theme by cloning a root theme
root\_theme \= ApplicationTheme.root.find\_by(name: 'fluid')
company\_theme \= root\_theme.deep\_clone(company.id)
company\_theme.name \= "Custom Fluid Theme"
company\_theme.save!

\# Customize theme variables
company\_theme.variables \= {
 primary\_color: '#ff6b6b',
 secondary\_color: '#4ecdc4',
 font\_family: 'Inter, sans-serif'
}.to\_json
company\_theme.save!

\# Create a custom template
custom\_template \= company\_theme.application\_theme\_templates.create!(
 name: 'Custom Product Layout',
 themeable\_type: :product,
 content: '{'%' layout "theme" '%'}<div class="custom-product">{{ product.name }}</div>',
 format: :liquid
)

\# Make it the default template for products
custom\_template.make\_default

\# Assign template to specific product
product \= Product.find(456)
product.application\_theme\_template \= custom\_template
product.save!

\# Publish the theme to make it active
company\_theme.publish

\# Find template for rendering a product
product \= Product.find(456)
template \= ApplicationThemeTemplate.default\_for\_company\_themes(
 company: product.company,
 themeable\_type: :product
).first

\# Get template with all variables for rendering
variables \= Themes::Templates::Variable.new(
 template: template,
 record: product,
 company: product.company,
 request: request
).call

\# Render the template
html \= PageBuilder.render\_template(template, variables)

\# Working with layouts
layout\_template \= active\_theme.application\_theme\_templates.layouts.find\_by(name: 'theme')
content\_with\_layout \= PageBuilder.render\_with\_layout(content, layout\_template, variables)

\# Theme versioning
active\_theme.update\_version(:minor) \# Increment minor version
active\_theme.update\_version(:major) \# Increment major version

\# Check if theme can be auto-upgraded
active\_theme.auto\_upgradeable? \# => true if minor version is 0 and outdated
active\_theme.outdated? \# => true if root theme has newer version

\# Get theme audit history
version\_history \= active\_theme.versions
\# => \[{ version: 1, created\_at: "...", author\_name: "John Doe", published: true }\]

## Use Cases

### Company Theme Customization

- Clone root themes to create company-specific versions
- Customize color schemes, typography, and layout through CSS variables
- Override specific templates (product pages, homepage) while inheriting others
- Maintain brand consistency across all customer-facing pages

### Content-Specific Template Assignment

- Assign premium templates to high-value products or featured content
- Create landing page templates for marketing campaigns
- Develop specialized layouts for different content categories
- Support A/B testing through template variations

### Multi-Brand Support

- Different themes for different product lines or brands
- Template inheritance for consistent base functionality
- Brand-specific customizations through variable overrides
- Centralized template management with distributed customization

### Theme Development and Deployment

- Version control for theme changes and rollbacks
- Staged deployment through draft/active status system
- Template preview and testing before going live
- Audit trails for change tracking and compliance

## Benefits

**Flexibility**: Companies can customize themes at multiple levels without affecting core functionality

**Maintainability**: Clear separation between theme structure and content enables safe updates

**Performance**: Multi-level caching and optimized variable resolution ensure fast page loads

**Scalability**: Template inheritance and variable systems support growing customization needs

**Developer Experience**: Comprehensive API and model interfaces for theme manipulation

**User Experience**: Consistent theming across all page types with flexible customization options