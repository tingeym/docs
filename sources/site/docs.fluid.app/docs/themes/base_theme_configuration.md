# Source: https://docs.fluid.app/docs/themes/base_theme_configuration

# Base Theme Configuration System Documentation

## Overview

The Base theme uses a comprehensive configuration system that allows users to customize the theme's appearance and behavior through a structured JSON-based settings system. This document explains how the configuration files work together to create a customizable, maintainable theme.

## Architecture Overview

The Base theme configuration system consists of four main components:

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
│ theme.liquid │ Converts settings to root CSS variables
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
 "default": "Roboto",
 "label": "Body Font"
 },
 {
 "type": "range",
 "id": "font\_size\_body",
 "min": 1,
 "max": 100,
 "step": 1,
 "label": "Body Font Size",
 "default": 16
 }
 \]
}

### Configuration Groups in Base Theme

1. **`typography`** - Font families, sizes, weights
2. **`color_schema`** - Color palette (primary, secondary, neutral)
3. **`spacing`** - Spacing scale for gaps (xs, sm, md, lg, xl, 2xl-10xl)
4. **`padding`** - Padding scale (0, xs, sm, md, lg, xl, 2xl-10xl)
5. **`margin`** - Margin scale (0, xs, sm, md, lg, xl, 2xl-10xl)
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
- `padding_xs` → `settings.padding_xs` in Liquid

### Example Settings Data

{
 "current": {
 "font\_family\_body": "Roboto",
 "font\_size\_body": 16,
 "color\_primary": "#000000",
 "space\_xs": 4,
 "padding\_xs": 4,
 "margin\_xs": 4
 }
}

## 3\. Theme Layout (`layouts/theme.liquid`)

The theme layout file is responsible for converting settings from `settings_data.json` into CSS custom properties (CSS variables) that are injected into the page's `:root` element.

### CSS Variable Injection

In `theme.liquid`, settings are accessed via the `settings` object and converted to CSS variables:

{'%' style '%'}
 :root {
 /\* Typography \*/
 --ff-body: {{ settings.font\_family\_body | font\_family | default: "Roboto, sans-serif" }};
 --fs-body: {{ settings.font\_size\_body | append: 'px' }};
 
 /\* Colors \*/
 --clr-primary: {{ settings.color\_primary }};
 
 /\* Spacing \*/
 --space-xs: {{ settings.space\_xs | append: 'px' }};
 --padding-xs: {{ settings.padding\_xs | append: 'px' }};
 --margin-xs: {{ settings.margin\_xs | append: 'px' }};
 }
{'%' endstyle '%'}

### CSS Variable Naming Convention

The theme uses a consistent naming pattern for CSS variables:

- **Font Families**: `--ff-*` (e.g., `--ff-body`, `--ff-heading`)
- **Font Sizes**: `--fs-*` or `--text-*` (e.g., `--fs-body`, `--text-xs`)
- **Colors**: `--clr-*` (e.g., `--clr-primary`, `--clr-black`)
- **Spacing**: `--space-*` (e.g., `--space-xs`, `--space-lg`)
- **Padding**: `--padding-*` (e.g., `--padding-xs`, `--padding-lg`)
- **Margin**: `--margin-*` (e.g., `--margin-xs`, `--margin-lg`)
- **Border Radius**: `--rounded-*` (e.g., `--rounded-sm`, `--rounded-lg`)

### Complete Variable Mapping

The theme.liquid file maps all settings to CSS variables:

:root {
 /\* Typography \*/
 --ff-body: {{ settings.font\_family\_body | font\_family }};
 --ff-heading: {{ settings.font\_family\_heading | font\_family }};
 --fw-body: {{ settings.font\_weight\_400 }};
 
 /\* Font Sizes \*/
 --fs-body: {{ settings.font\_size\_body | append: 'px' }};
 --fs-sm: {{ settings.font\_size\_small | append: 'px' }};
 --fs-md: {{ settings.font\_size\_medium | append: 'px' }};
 --fs-lg: {{ settings.font\_size\_large | append: 'px' }};
 --fs-h1: {{ settings.font\_size\_h1 | append: 'px' }};
 --fs-h2: {{ settings.font\_size\_h2 | append: 'px' }};
 /\* ... up to h6 \*/
 --fs-3xl: {{ settings.font\_size\_3xl | append: 'px' }};
 
 /\* Text Sizes \*/
 --text-xs: {{ settings.text\_xs | append: 'px' }};
 --text-sm: {{ settings.text\_sm | append: 'px' }};
 /\* ... up to text-5xl \*/
 
 /\* Colors \*/
 --clr-primary: {{ settings.color\_primary }};
 --clr-secondary: {{ settings.color\_secondary }};
 --clr-white: {{ settings.color\_white | default: '#fff' }};
 --clr-gray: {{ settings.color\_gray }};
 --clr-body: {{ settings.color\_body }};
 --clr-black: {{ settings.color\_black | default: '#000000' }};
 
 /\* Spacing (for gaps) \*/
 --space-0: {{ settings.space\_0 | append: 'px' }};
 --space-xs: {{ settings.space\_xs | append: 'px' }};
 /\* ... up to space-10xl \*/
 
 /\* Padding \*/
 --padding-0: {{ settings.padding\_0 | append: 'px' }};
 --padding-xs: {{ settings.padding\_xs | append: 'px' }};
 /\* ... up to padding-10xl \*/
 
 /\* Margin \*/
 --margin-0: {{ settings.margin\_0 | append: 'px' }};
 --margin-xs: {{ settings.margin\_xs | append: 'px' }};
 /\* ... up to margin-10xl \*/
 
 /\* Border Radius \*/
 --rounded: {{ settings.rounded | append: 'px' }};
 --rounded-sm: {{ settings.rounded\_sm | append: 'px' }};
 /\* ... up to rounded-5xl \*/
}

### Font Loading

The Base theme loads fonts using the `font_face` filter:

{{ settings.font\_family\_body | font\_face: font\_display: 'swap' }}
{{ settings.font\_family\_heading | font\_face: font\_display: 'swap' }}

### Background Image Handling

The Base theme includes JavaScript to handle background images via the `data-bg` attribute:

document.addEventListener("DOMContentLoaded", () \=> {
 const elements \= document.querySelectorAll("\[data-bg\]");
 elements.forEach((element) \=> {
 const bgImage \= element.getAttribute("data-bg");
 if (bgImage) {
 element.style.backgroundImage \= \`url(${bgImage})\`;
 }
 });
});

This allows sections to use background images like:

<section data-bg\="{{ section.settings.background\_image | image\_url }}"\>
 <!-- content -->
</section\>

## 4\. Global Styles (`global_styles.css`)

The global stylesheet uses the CSS variables defined in `theme.liquid` to create utility classes and component styles.

### Utility Classes

The theme provides utility classes that use CSS variables:

/\* Text Colors \*/
.text-primary {
 color: var(\--clr-primary);
}

.text-secondary {
 color: var(\--clr-secondary);
}

.text-white {
 color: var(\--clr-white);
}

.text-black {
 color: var(\--clr-black);
}

.text-gray {
 color: var(\--clr-gray);
}

/\* Font Sizes \*/
.text-xs {
 font-size: var(\--text-xs);
}

.text-sm {
 font-size: var(\--text-sm);
}

.fs-body {
 font-size: var(\--fs-body);
}

.fs-sm {
 font-size: var(\--fs-sm);
}

/\* Spacing (Gaps) \*/
.gap-0 {
 gap: var(\--space-0);
}

.gap-xs {
 gap: var(\--space-xs);
}

/\* Padding \*/
.p-0 {
 padding: var(\--padding-0);
}

.p-xs {
 padding: var(\--padding-xs);
}

/\* Margin \*/
.m-0 {
 margin: var(\--margin-0);
}

.m-xs {
 margin: var(\--margin-xs);
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

body {
 font-family: var(\--ff-body);
 font-size: var(\--fs-body);
 font-weight: var(\--fw-body);
 color: var(\--clr-body);
 background-color: var(\--clr-white);
}

h1, h2, h3, h4, h5, h6 {
 font-family: var(\--ff-heading);
}

### Responsive Utilities

The theme includes responsive variants using media queries:

@media (min-width: 1024px) {
 .lg\\:text-h1 {
 font-size: var(\--fs-h1);
 }
 
 .lg\\:p-xl {
 padding: var(\--padding-xl);
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
 "name": "Hero Section",
 "settings": \[
 {
 "type": "text",
 "id": "headline",
 "label": "Main Headline",
 "default": "Medium length hero headline goes here"
 },
 {
 "type": "color\_background",
 "id": "background\_color",
 "label": "Background Color",
 "default": "#000000"
 }
 \]
}
{'%' endschema '%'}

### Using Settings in Sections

Section settings are accessed via `section.settings`:

<div style\="background-color: {{ section.settings.background\_color | default: '#000000' }};"\>
 {{ section.settings.headline }}
</div\>

Block settings are accessed via `block.settings`:

{'%' for block in section.blocks '%'}
 <div\>
 <h3\>{{ block.settings.title }}</h3\>
 </div\>
{'%' endfor '%'}

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
 .hero-section-{{ section.id }} {
 background-color: {{ section.settings.background\_color | default: '#000000' }};
 }

 @media (min-width: 1280px) {
 .hero-section-{{ section.id }} {
 padding-top: {{ section.settings.section\_padding\_top\_desktop | default: 80 }}px;
 padding-bottom: {{ section.settings.section\_padding\_bottom\_desktop | default: 80 }}px;
 }
 }
{'%' endstyle '%'}

### Background Images

Sections can use background images via the `data-bg` attribute:

<section 
 class\="hero-section" 
 data-bg\="{{ section.settings.background\_image | image\_url }}"
 style\="background-color: {{ section.settings.background\_color | default: '#000000' }};"
\>
 <!-- content -->
</section\>

The JavaScript in `theme.liquid` will automatically apply the background image.

## Configuration Flow Example

Let's trace how a color setting flows through the system:

### Step 1: Define in Schema

// settings\_schema.json
{
 "type": "color\_background",
 "id": "color\_primary",
 "label": "Primary Color",
 "default": "#000000"
}

### Step 2: Store in Data

// settings\_data.json
{
 "current": {
 "color\_primary": "#000000"
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

### Step 5: Use in Sections

<!-- sections/hero\_section/index.liquid -->
<div class\="text-primary"\>
 Primary colored text
</div\>

## Best Practices

### 1\. Naming Conventions

- **Settings IDs**: Use snake\_case (e.g., `font_family_body`)
- **CSS Variables**: Use kebab-case with prefixes (e.g., `--clr-primary`)
- **Utility Classes**: Use kebab-case (e.g., `.text-primary`)

### 2\. Setting Organization

Group related settings together in the schema:

- Typography settings together
- Color settings together
- Spacing settings together (space, padding, margin)

### 3\. Default Values

Always provide sensible defaults in the schema:

{
 "type": "range",
 "id": "font\_size\_body",
 "default": 16,
 "min": 1,
 "max": 100
}

### 4\. CSS Variable Usage

Always use CSS variables in `global_styles.css` instead of hardcoded values:

/\* Good \*/
.text-primary {
 color: var(\--clr-primary);
}

/\* Bad \*/
.text-primary {
 color: #000000;
}

### 5\. Section Settings

Use section settings for section-specific customization:

- Colors, spacing, typography specific to that section
- Content that changes per section instance

Use global settings for theme-wide consistency:

- Brand colors
- Base typography
- Spacing scale

### 6\. Spacing System

The Base theme has three separate spacing systems:

- **`space-*`**: For gaps between elements (flexbox gap, grid gap)
- **`padding-*`**: For internal spacing within elements
- **`margin-*`**: For external spacing between elements

Use the appropriate system for your use case:

<!-- Gap between items -->
<div class\="flex gap-lg"\>
 <div\>Item 1</div\>
 <div\>Item 2</div\>
</div\>

<!-- Internal spacing -->
<div class\="p-lg"\>
 Content with padding
</div\>

<!-- External spacing -->
<div class\="m-lg"\>
 Content with margin
</div\>

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

### Color System

The Base theme uses a simple color system:

- `color_primary` → `--clr-primary`
- `color_secondary` → `--clr-secondary`
- `color_white` → `--clr-white`
- `color_gray` → `--clr-gray`
- `color_black` → `--clr-black`
- `color_body` → `--clr-body`

### Spacing Scales

The Base theme has three separate spacing scales:

**Space (for gaps):**

- `space_0` (0px) → `--space-0`
- `space_xs` (4px) → `--space-xs`
- Up to `space_10xl` (72px)

**Padding:**

- `padding_0` (0px) → `--padding-0`
- `padding_xs` (4px) → `--padding-xs`
- Up to `padding_10xl` (72px)

**Margin:**

- `margin_0` (0px) → `--margin-0`
- `margin_xs` (4px) → `--margin-xs`
- Up to `margin_10xl` (72px)

### Typography Scale

Typography includes multiple size systems:

**Font Sizes:**

- `font_size_body` → `--fs-body`
- `font_size_small` → `--fs-sm`
- `font_size_medium` → `--fs-md`
- `font_size_large` → `--fs-lg`
- `font_size_h1` through `font_size_h6` → `--fs-h1` through `--fs-h6`
- `font_size_3xl` → `--fs-3xl`

**Text Sizes:**

- `text_xs` → `--text-xs`
- `text_sm` → `--text-sm`
- `text_base` → `--text-base`
- `text_lg` → `--text-lg`
- `text_xl` → `--text-xl`
- `text_2xl` through `text_5xl` → `--text-2xl` through `--text-5xl`

### Border Radius Scale

- `rounded` (4px) → `--rounded`
- `rounded_sm` (8px) → `--rounded-sm`
- `rounded_md` (12px) → `--rounded-md`
- `rounded_lg` (16px) → `--rounded-lg`
- `rounded_xl` (20px) → `--rounded-xl`
- `rounded_2xl` through `rounded_5xl` → `--rounded-2xl` through `--rounded-5xl`

## Differences from Vox and Fluid Themes

While all themes share the same configuration architecture, the Base theme has some key differences:

### Spacing System Differences

- **Base**: Has separate `space-*`, `padding-*`, and `margin-*` scales
- **Vox/Fluid**: Only have `space-*` scale

### Color System Differences

- **Base**: Simple color system (primary, secondary, white, gray, black, body)
- **Vox**: More extensive color system with variations (primary-400, black-500-800, gray-300-900, gradients)
- **Fluid**: Color system with primary variations (50, 100, 800) and numbered grays (100-400)

### Typography Differences

- **Base**: Has both `font_size_*` and `text_*` systems, plus `font_size_small/medium/large`
- **Vox/Fluid**: Primarily use `text_*` system with heading sizes

### Button Settings Differences

- **Base**: Does not include button-specific settings in the schema
- **Vox/Fluid**: Include comprehensive button settings (sizes, spacing, colors, border radius)

### Shadow Settings Differences

- **Base**: Does not include shadow settings in the schema
- **Vox**: Includes shadow settings (dropdown, card, header, join\_vox)
- **Fluid**: Includes shadow settings (dropdown, card, header)

### Background Images Differences

- **Base**: Uses `data-bg` attribute with JavaScript for background images
- **Vox/Fluid**: Use inline styles or CSS classes for background images

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

### Background Images Not Showing

1. Verify the `data-bg` attribute is set correctly
2. Check that the JavaScript in `theme.liquid` is loading
3. Ensure the image URL is valid

### Spacing Not Working

1. Verify you're using the correct spacing system:
 
 - `space-*` for gaps
 - `padding-*` for internal spacing
 - `margin-*` for external spacing
2. Check that the CSS variable exists in `theme.liquid`
 
3. Ensure the utility class exists in `global_styles.css`
 

## Summary

The Base theme configuration system provides a powerful, flexible way to customize the theme:

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
- Separate spacing systems for different use cases

By following this system, you can create customizable, maintainable themes that are easy for users to configure and for developers to extend.