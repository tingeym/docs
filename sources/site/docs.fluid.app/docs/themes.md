# Source: https://docs.fluid.app/docs/themes

# Themes System

Welcome to the Fluid Themes documentation. The Themes System provides a flexible, multi-layered architecture for customizing the appearance and behavior of pages across the platform.

## Quick Links

- [Developer Guide](https://docs.fluid.app/docs/themes/developer-guide) - Build and customize themes with Liquid templating
- [Page Editor Guide](https://docs.fluid.app/docs/themes/paged-editor) - Visual page builder interface
- [CLI Tools](https://docs.fluid.app/docs/themes/themes-cli) - Command-line tools for theme development
- [Template Variables](https://docs.fluid.app/docs/themes/theme-variables) - Available variables for each page type
- [Blocks and Components](https://docs.fluid.app/docs/themes/template-types) - When to use blocks vs components in your theme
- [Schema Components](https://docs.fluid.app/docs/themes/schema-components) - Settings, blocks, and presets reference
- [Linked CSS Variable Presets](https://docs.fluid.app/docs/themes/linked-css-variable-presets) - Enable rich text presets that stay in sync with theme settings
- [API Reference](https://docs.fluid.app/docs/themes/api-reference) - Theme management API endpoints
- [Theme Marketplace](https://docs.fluid.app/docs/themes/theme-marketplace) - Explore, preview and choose a theme
- [Github Integration](https://docs.fluid.app/docs/themes/github_integration) - Connect theme with github

---

## Overview

The Themes System separates theme management from template content, enabling companies to customize their user-facing pages through a combination of theme selection, template customization, and variable configuration. The system supports both traditional Liquid templating and modern JSON-based section components.

## Core Concepts

### Application Themes

**Application Themes** represent complete theme packages that define the visual appearance and layout structure for a company's pages.

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

### Application Theme Templates

**Application Theme Templates** are individual page templates within a theme that define the layout and content structure for specific page types.

**Template Types:**

- **Content Templates**: `product`, `medium`, `enrollment_pack`, `library`, `collection`
- **Page Templates**: `home_page`, `shop_page`, `cart_page`, `category_page`, `collection_page`, `join_page`
- **Layout Components**: `navbar`, `footer`, `layouts`
- **Structural Components**: `sections`, `components`, `config`
- **Localization Templates**: `locales`

### Template Processing

Templates can be created using either **Liquid** or **JSON** format:

**Liquid Templates:**

- Traditional server-side templating using Liquid syntax
- Variables injected as `{{ variable_name }}` expressions
- Control flow through `{`%`tag`%`}` blocks
- Support for layouts, includes, and custom filters

**JSON Templates:**

- Modern, component-based approach
- Schema-driven configuration with settings, blocks, and presets
- Visual page builder integration
- Drag-and-drop section management

---

## Theme Architecture

A theme is distributed as a zip file containing template files organized in a specific folder structure:

theme/
├── layout/
│ └── theme.liquid
├── assets/
│ ├── custom.js
│ ├── custom.css
│ └── logo.png
├── components/
│ └── header/
│ └── index.liquid
├── sections/
│ └── hero/
│ └── index.liquid
├── product/
│ └── default/
│ ├── index.liquid
│ └── styles.css
├── navbar/
│ └── default/
│ └── index.liquid
├── footer/
│ └── default/
│ └── index.liquid
└── locales/
 └── en.json

### Layout Files

The layout file (`theme.liquid`) contains common elements shared across pages:

<!DOCTYPE html\>
<html\>
 <head\>
 {{ content\_for\_header }}
 </head\>
 <body\>
 {\`%\` section 'navbar' \`%\`}
 {{ content\_for\_layout }}
 {\`%\` section 'footer' \`%\`}
 </body\>
</html\>

### Asset Management

Assets are stored in the `assets` folder and referenced using the `asset_url` filter:

<link rel\="stylesheet" href\="{{ 'custom.css' | asset\_url }}"\>
<script src\="{{ 'custom.js' | asset\_url }}"\></script\>
<img src\="{{ 'banner.jpg' | asset\_url }}" alt\="Banner"\>

---

## Theme Customization Levels

The system provides multiple levels of customization:

| Level | Description | Auto-Upgrade Compatible |
| --- | --- | --- |
| **Level 1** | Theme Selection & Cloning | ✅ Yes |
| **Level 2** | CSS Variable Customization | ✅ Yes |
| **Level 3** | Custom Stylesheet Overrides | ✅ Yes |
| **Level 4** | Template Customization | ❌ Manual Review |
| **Level 5** | Content-Specific Assignment | ✅ Yes |

### CSS Variables

Customize themes by modifying CSS variables:

{
 "primary\_color": "#007bff",
 "secondary\_color": "#6c757d",
 "font\_family": "Helvetica, Arial, sans-serif",
 "heading\_font": "'Playfair Display', serif",
 "border\_radius": "4px",
 "container\_width": "1200px"
}

Variables are substituted in stylesheets:

:root {
 \--primary-color: $primary\_color;
 \--secondary-color: $secondary\_color;
 \--font-family: $font\_family;
}

---

## Available Root Themes

Fluid provides three professionally designed root themes:

- **[Base Theme](https://docs.fluid.app/docs/themes/base_theme_configuration)** - Clean and minimal design perfect for any business
- **[Vox Theme](https://docs.fluid.app/docs/themes/vox_theme_configuration)** - Modern and bold styling ideal for creative businesses
- **[Fluid Theme](https://docs.fluid.app/docs/themes/fluid_theme_configuration)** - Versatile design that adapts well to your brand

---

## Next Steps

Ready to build? Start with the [Developer Guide](https://docs.fluid.app/docs/themes/developer-guide) for a comprehensive walkthrough of theme development.

Need to customize pages visually? Check out the [Page Editor Guide](https://docs.fluid.app/docs/themes/paged-editor).

Looking for available template data? See the [Template Variables](https://docs.fluid.app/docs/themes/theme-variables) reference.