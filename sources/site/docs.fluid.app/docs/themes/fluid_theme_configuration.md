# Source: https://docs.fluid.app/docs/themes/fluid_theme_configuration

# Fluid Theme Configuration System Documentation

## Overview

The Fluid theme uses a comprehensive configuration system that allows users to customize the theme's appearance and behavior through a structured JSON-based settings system. This document explains how the configuration files work together to create a customizable, maintainable theme.

## Architecture Overview

The Fluid theme configuration system consists of four main components:

1. **`settings_schema.json`** - Defines the configuration UI and available settings
2. **`settings_data.json`** - Stores the actual configuration values
3. **`theme.liquid`** - Converts settings to CSS custom properties
4. **`global_styles.css`** - Uses CSS variables to style the theme
5. **Section Liquid Templates** - Use settings for dynamic content

┌─────────────────────┐
│ settings\_schema.json│ Defines what can be configured
│ │ (UI form fields, types, defaults)
└──────────┬──────────┘
 │
 ▼
┌─────────────────────┐
│ settings\_data.json │ Stores actual values
│ │ (user's customizations)
└──────────┬──────────┘
 │
 ▼
┌─────────────────────┐
│ theme.liquid │ Converts settings to CSS variables
│ │ (injects into :root)
└──────────┬──────────┘
 │
 ▼
┌─────────────────────┐
│ global\_styles.css │ Uses CSS variables for styling
│ │ (utility classes, components)
└──────────┬──────────┘
 │
 ▼
┌─────────────────────┐
│ Section Templates │ Use settings for dynamic content
│ (sections/\*.liquid) │ (colors, spacing, typography)
└─────────────────────┘

## 1\. Settings Schema (`config/settings_schema.json`)

The settings schema defines the structure of the theme's configuration interface. It's an array of configuration groups, each containing settings that control different aspects of the theme.

### Structure

\[
 {
 "name": "group\_name",
 "settings": \[
 {
 "type": "setting\_type",
 "id": "setting\_id",
 "label": "Display Label",
 "default": "default\_value"
 }
 \]
 }
\]

### Setting Types

The schema supports various setting types:

#### Typography Settings

- **`font_picker`** - Font family selection
- **`range`** - Numeric values with min/max/step (for font sizes, weights)

#### Color Settings

- **`color_background`** - Color picker (supports hex, rgba, gradients)

#### Spacing Settings

- **`range`** - Numeric spacing values

#### Other Settings

- **`text`** - Single-line text input
- **`textarea`** - Multi-line text input
- **`richtext`** - Multi-line text format
- **`select`** - Dropdown selection
- **`checkbox`** - Boolean toggle
- **`url`** - web url
- **`image_picker`** - Image upload
- **`video`** - Video upload
- **`header`** - Section divider (not a setting, just UI)

### Example Schema Section

{
 "name": "typography",
 "settings": \[
 {
 "type": "header",
 "content": "Body Typography"
 },
 {
 "type": "font\_picker",
 "id": "font\_family\_body",
 "default": "Inter",
 "label": "Font Family"
 },
 {
 "type": "range",
 "id": "text\_body",
 "min": 10,
 "max": 40,
 "step": 2,
 "label": "Body Font Size",
 "default": 14
 }
 \]
}

### Configuration Groups in Fluid Theme

1. **`typography`** - Font families, sizes, weights
2. **`spacing`** - Spacing scale (xs, sm, md, lg, xl, 2xl-10xl)
3. **`color_schema`** - Color palette (primary, neutral, grays)
4. **`shadows`** - Box shadow definitions
5. **`Button`** - Button styling (sizes, spacing, colors)
6. **`border_radius`** - Border radius scale

## 2\. Settings Data (`config/settings_data.json`)

The settings data file stores the actual configuration values. It uses a nested structure with a `current` object containing all active settings.

### Data Structure

{
 "current": {
 "setting\_id": "value",
 "another\_setting": "value"
 }
}

### Naming Convention

Settings use snake\_case naming:

- `font_family_body` → `settings.font_family_body` in Liquid
- `color_primary` → `settings.color_primary` in Liquid
- `text_body` → `settings.text_body` in Liquid

### Example Settings Data

{
 "current": {
 "font\_family\_body": "Inter",
 "font\_weight\_body": 400,
 "text\_body": 14,
 "color\_primary": "#2365ea",
 "space\_xs": 4,
 "space\_sm": 8
 }
}

## 3\. Theme Layout (`layouts/theme.liquid`)

The theme layout file is responsible for converting settings from `settings_data.json` into CSS custom properties (CSS variables) that are injected into the page's `:root` element.

### CSS Variable Injection

In `theme.liquid`, settings are accessed via the `settings` object and converted to CSS variables:

{'%' style '%'}
 :root {
 /\* Typography \*/
 --ff-body: {{ settings.font\_family\_body | font\_family | default: "Inter, sans-serif" }};
 --text-body: {{ settings.text\_body | append: 'px' }};
 
 /\* Colors \*/
 --clr-primary: {{ settings.color\_primary }};
 
 /\* Spacing \*/
 --space-xs: {{ settings.space\_xs | append: 'px' }};
 }
{'%' endstyle '%'}

### CSS Variable Naming Convention

The theme uses a consistent naming pattern for CSS variables:

- **Font Families**: `--ff-*` (e.g., `--ff-body`, `--ff-heading`)
- **Font Sizes**: `--text-*` or `--fs-*` (e.g., `--text-body`, `--fs-h1`)
- **Colors**: `--clr-*` (e.g., `--clr-primary`, `--clr-black`)
- **Spacing**: `--space-*` (e.g., `--space-xs`, `--space-lg`)
- **Shadows**: `--shadow-*` (e.g., `--shadow-card`, `--shadow-dropdown`)
- **Buttons**: `--btn-*` (e.g., `--btn-text`, `--btn-rounded`)
- **Border Radius**: `--rounded-*` (e.g., `--rounded-sm`, `--rounded-lg`)

### Complete Variable Mapping

The theme.liquid file maps all settings to CSS variables:

:root {
 /\* Typography \*/
 --ff-body: {{ settings.font\_family\_body | font\_family }};
 --ff-heading: {{ settings.font\_family\_heading | font\_family }};
 --fw-body: {{ settings.font\_weight\_body }};
 
 /\* Font Sizes \*/
 --text-body: {{ settings.text\_body | append: 'px' }};
 --text-xs: {{ settings.text\_xs | append: 'px' }};
 --text-sm: {{ settings.text\_sm | append: 'px' }};
 /\* ... up to text-10xl \*/
 
 /\* Heading Sizes \*/
 --fs-h1: {{ settings.font\_size\_h1 | append: 'px' }};
 --fs-h2: {{ settings.font\_size\_h2 | append: 'px' }};
 /\* ... up to h6 \*/
 
 /\* Colors \*/
 --clr-primary: {{ settings.color\_primary }};
 --clr-primary-50: {{ settings.color\_primary\_50 }};
 --clr-primary-100: {{ settings.color\_primary\_100 }};
 --clr-primary-800: {{ settings.color\_primary\_800 }};
 --clr-body: {{ settings.color\_body }};
 --clr-black: {{ settings.color\_black }};
 /\* ... all color variants \*/
 
 /\* Spacing \*/
 --space-xs: {{ settings.space\_xs | append: 'px' }};
 --space-sm: {{ settings.space\_sm | append: 'px' }};
 /\* ... up to space-10xl \*/
 
 /\* Buttons \*/
 --btn-text: {{ settings.btn\_text | append: 'px' }};
 --btn-rounded: {{ settings.btn\_rounded | append: 'px' }};
 /\* ... all button variants \*/
 
 /\* Border Radius \*/
 --rounded: {{ settings.rounded | append: 'px' }};
 --rounded-sm: {{ settings.rounded\_sm | append: 'px' }};
 /\* ... up to rounded-5xl \*/
}

### Font Loading

The Fluid theme loads fonts using the `font_face` filter:

{{ settings.font\_family\_body | font\_face: font\_display: 'swap' }}
{{ settings.font\_family\_semibold | font\_face: font\_display: 'swap' }}

## 4\. Global Styles (`global_styles.css`)

The global stylesheet uses the CSS variables defined in `theme.liquid` to create utility classes and component styles.

### Utility Classes

The theme provides utility classes that use CSS variables:

/\* Text Colors \*/
.text-primary {
 color: var(\--clr-primary);
}

.text-primary-50 {
 color: var(\--clr-primary-50);
}

.text-primary-100 {
 color: var(\--clr-primary-100);
}

.text-primary-800 {
 color: var(\--clr-primary-800);
}

.text-black {
 color: var(\--clr-black);
}

/\* Background Colors \*/
.bg-primary {
 background-color: var(\--clr-primary);
}

.bg-primary-50 {
 background-color: var(\--clr-primary-50);
}

/\* Border Colors \*/
.border-primary {
 border-color: var(\--clr-primary);
}

/\* Font Sizes \*/
.text-xs {
 font-size: var(\--text-xs);
}

.text-sm {
 font-size: var(\--text-sm);
}

/\* Spacing \*/
.m-xs {
 margin: var(\--space-xs);
}

.p-lg {
 padding: var(\--space-lg);
}

/\* Border Radius \*/
.rounded {
 border-radius: var(\--rounded);
}

.rounded-lg {
 border-radius: var(\--rounded-lg);
}

### Component Styles

Components also use CSS variables for consistent theming:

.btn {
 font-size: var(\--btn-text);
 padding: var(\--btn-vertical-space) var(\--btn-horizontal-space);
 border-radius: var(\--btn-rounded);
 background-color: var(\--clr-primary);
 color: var(\--clr-white);
}

### Responsive Utilities

The theme includes responsive variants using media queries:

@media (min-width: 1024px) {
 .lg\\:text-h1 {
 font-size: var(\--fs-h1);
 }
 
 .lg\\:m-xl {
 margin: var(\--space-xl);
 }
}

## 5\. Section Templates (`sections/*.liquid`)

Section templates use settings in two ways:

1. **Section-level settings** - Defined in the section's `{'%' schema '%'}` block
2. **Global theme settings** - Accessed via the `settings` object

### Section Schema

Each section can define its own settings in a `{'%' schema '%'}` block:

{'%' schema '%'}
{
 "name": "Shop Collections",
 "settings": \[
 {
 "type": "text",
 "id": "heading",
 "label": "Heading",
 "default": "Shop Collections"
 },
 {
 "type": "select",
 "id": "heading\_color",
 "label": "Heading Color",
 "options": \[
 { "value": "text-primary", "label": "Primary" },
 { "value": "text-black", "label": "Black" }
 \],
 "default": "text-black"
 }
 \],
 "blocks": \[
 {
 "name": "Collection Card",
 "type": "collection\_card",
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
{'%' endschema '%'}

### Using Settings in Sections

Section settings are accessed via `section.settings`:

<div class\="{{ section.settings.heading\_color }}"\>
 {{ section.settings.heading }}
</div\>

Block settings are accessed via `block.settings`:

{'%' for block in section.blocks '%'}
 <div\>
 <h3\>{{ block.settings.title }}</h3\>
 </div\>
{'%' endfor '%'}

### Class Composition Pattern

The Fluid theme uses a pattern of composing classes using Liquid `assign` statements for better maintainability:

{'%' assign heading\_classes =
 section.settings.heading\_size | append: " " |
 append: section.settings.heading\_font\_weight | append: " " |
 append: section.settings.heading\_color 
'%'}

<div class\="{{ heading\_classes }}"\>
 {{ section.settings.heading }}
</div\>

This pattern allows for:

- Cleaner template code
- Easier maintenance
- Reusable class combinations
- Better readability

### Using Global Settings

Global theme settings can be accessed directly:

<div style\="color: var(--clr-primary);"\>
 This uses the global primary color
</div\>

Or through utility classes that use CSS variables:

<div class\="text-primary"\>
 This also uses the global primary color
</div\>

### Dynamic Styles in Sections

Sections can generate dynamic CSS using section settings:

{'%' style '%'}
 .section-{{ section.id }} {
 padding-top: {{ section.settings.padding\_top }}px;
 background-color: {{ section.settings.background\_color }};
 }
{'%' endstyle '%'}

## Configuration Flow Example

Let's trace how a color setting flows through the system:

### Step 1: Define in Schema

// settings\_schema.json
{
 "type": "color\_background",
 "id": "color\_primary",
 "label": "Primary Color",
 "default": "#2365ea"
}

### Step 2: Store in Data

// settings\_data.json
{
 "current": {
 "color\_primary": "#2365ea"
 }
}

### Step 3: Convert to CSS Variable

// theme.liquid
:root {
 --clr-primary: {{ settings.color\_primary }};
}

### Step 4: Use in Styles

/\* global\_styles.css \*/
.text-primary {
 color: var(\--clr-primary);
}

.btn-primary {
 background-color: var(\--clr-primary);
}

### Step 5: Use in Sections

<!-- sections/shop\_collections/index.liquid -->
<div class\="text-primary"\>
 Primary colored text
</div\>

<button class\="btn-primary"\>
 Primary button
</button\>

## Best Practices

### 1\. Naming Conventions

- **Settings IDs**: Use snake\_case (e.g., `font_family_body`)
- **CSS Variables**: Use kebab-case with prefixes (e.g., `--clr-primary`)
- **Utility Classes**: Use kebab-case (e.g., `.text-primary`)

### 2\. Setting Organization

Group related settings together in the schema:

- Typography settings together
- Color settings together
- Spacing settings together

### 3\. Default Values

Always provide sensible defaults in the schema:

{
 "type": "range",
 "id": "text\_body",
 "default": 14,
 "min": 10,
 "max": 40
}

### 4\. CSS Variable Usage

Always use CSS variables in `global_styles.css` instead of hardcoded values:

/\* Good \*/
.text-primary {
 color: var(\--clr-primary);
}

/\* Bad \*/
.text-primary {
 color: #2365ea;
}

### 5\. Section Settings

Use section settings for section-specific customization:

- Colors, spacing, typography specific to that section
- Content that changes per section instance

Use global settings for theme-wide consistency:

- Brand colors
- Base typography
- Spacing scale

### 6\. Class Composition

Use Liquid `assign` statements to compose classes for better maintainability:

{'%' assign button\_classes =
 section.settings.button\_size | append: " " |
 append: section.settings.button\_color | append: " " |
 append: section.settings.button\_style
'%'}

<button class\="btn {{ button\_classes }}"\>
 Click me
</button\>

## Adding New Settings

To add a new setting to the theme:

### 1\. Add to Schema

// config/settings\_schema.json
{
 "name": "color\_schema",
 "settings": \[
 {
 "type": "color\_background",
 "id": "color\_accent",
 "label": "Accent Color",
 "default": "#00ff00"
 }
 \]
}

### 2\. Add to Settings Data

// config/settings\_data.json
{
 "current": {
 "color\_accent": "#00ff00"
 }
}

### 3\. Add CSS Variable

// layouts/theme.liquid
:root {
 --clr-accent: {{ settings.color\_accent }};
}

### 4\. Create Utility Classes

/\* global\_styles.css \*/
.text-accent {
 color: var(\--clr-accent);
}

.bg-accent {
 background-color: var(\--clr-accent);
}

### 5\. Use in Sections

<!-- sections/example/index.liquid -->
<div class\="text-accent"\>
 Accent colored text
</div\>

## Common Patterns

### Color Variations

The Fluid theme uses a consistent pattern for color variations:

- Base: `color_primary` → `--clr-primary`
- Light shades: `color_primary_50`, `color_primary_100` → `--clr-primary-50`, `--clr-primary-100`
- Dark shades: `color_primary_800` → `--clr-primary-800`

### Gray Scale

The Fluid theme uses a numbered gray scale:

- `color_gray_100` → `--clr-gray-100`
- `color_gray_200` → `--clr-gray-200`
- `color_gray_300` → `--clr-gray-300`
- `color_gray_400` → `--clr-gray-400`

### Spacing Scale

The spacing system uses a consistent scale:

- `space_xs` (4px) → `--space-xs`
- `space_sm` (8px) → `--space-sm`
- `space_md` (12px) → `--space-md`
- Up to `space_10xl` (72px)

### Typography Scale

Typography follows a similar pattern:

- Body: `text_body` → `--text-body`
- Headings: `font_size_h1` → `--fs-h1`
- Utilities: `text_xs` through `text_10xl`

### Font Families

The Fluid theme supports multiple font families:

- Body: `font_family_body` (default: "Inter")
- Heading: `font_family_heading` (default: "Eina03-SemiBold")
- Semibold: `font_family_semibold` (for specific use cases)

## Differences from Vox Theme

While both themes share the same configuration architecture, there are some key differences:

### Color System Differences

- **Fluid**: Uses primary color variations (50, 100, 800) and a numbered gray scale (100-400)
- **Vox**: Uses primary color variations (400) and a more extensive gray scale (300, 500, 600, 700, 800, 900)

### Default Colors Differences

- **Fluid**: Primary color is `#2365ea` (blue)
- **Vox**: Primary color is `#ff0c00` (red)

### Fonts Differences

- **Fluid**: Default body font is "Inter", heading font is "Eina03-SemiBold"
- **Vox**: Default body and heading fonts are "Roboto"

### Gradients Differences

- **Fluid**: Does not include gradient color settings in the schema
- **Vox**: Includes gradient colors (mental, bg, hero, testimonial, join\_vox)

### Section Patterns Differences

- **Fluid**: Uses more class composition with `assign` statements
- **Vox**: Uses more direct class application

## Troubleshooting

### Settings Not Appearing

1. Check that the setting is in `settings_schema.json`
2. Verify the setting ID matches in `settings_data.json`
3. Ensure the setting is in the correct schema group

### CSS Variables Not Working

1. Verify the variable is defined in `theme.liquid`
2. Check that the variable name matches (case-sensitive)
3. Ensure the variable is in the `:root` selector

### Section Settings Not Accessible

1. Verify the setting is in the section's `{'%' schema '%'}` block
2. Check that you're using `section.settings.setting_id`
3. Ensure the setting has a default value

### Class Composition Issues

1. Ensure all classes are separated by spaces in the `append` chain
2. Check that all settings exist before using them
3. Verify the final class string doesn't have extra spaces

## Summary

The Fluid theme configuration system provides a powerful, flexible way to customize the theme:

1. **Schema** defines what can be configured
2. **Data** stores the actual values
3. **Theme Layout** converts settings to CSS variables
4. **Global Styles** use variables for consistent theming
5. **Sections** use settings for dynamic content

This architecture allows for:

- Easy customization through a UI
- Consistent theming across the site
- Maintainable code with CSS variables
- Flexible section-level customization
- Clean class composition patterns

By following this system, you can create customizable, maintainable themes that are easy for users to configure and for developers to extend.