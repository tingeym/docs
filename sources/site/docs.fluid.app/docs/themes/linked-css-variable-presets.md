# Source: https://docs.fluid.app/docs/themes/linked-css-variable-presets

# Linked CSS Variable Presets

Several inputs in the page builder support **linked CSS variable presets** — instead of hardcoding values like `font-size: 32px` or `border-radius: 8px`, the builder inserts CSS variable references. This keeps content in sync with theme settings: when a company changes their heading size, brand color, spacing, or corner radius, all linked values update automatically.

**How values are stored:** When a linked preset is used in the page builder, the persisted value is the full **`var()`** form — for example `border-radius: var(--corner_radius_md)` or `color: var(--color_primary)`. It is **not** stored as a bare custom property token like `--corner_radius_md` alone (that would not be valid in a CSS property value). In `theme.liquid` you still **declare** properties as `--name: …`; the storefront and saved builder content **reference** them as `var(--name)`.

Linked presets are available in:

- **Rich text editor** — text size, font family, and color presets
- **Corner radius inputs** — corner radius presets
- **Padding inputs** — spacing/padding presets

---

## Table of Contents

- [Linked CSS Variable Presets](https://docs.fluid.app/#linked-css-variable-presets)
 - [Table of Contents](https://docs.fluid.app/#table-of-contents)
 - [How It Works](https://docs.fluid.app/#how-it-works)
 - [Setup](https://docs.fluid.app/#setup)
 - [Step 1: Define Settings in `settings_schema.json`](https://docs.fluid.app/#step-1-define-settings-in-settings_schemajson)
 - [Step 2: Wire Settings to CSS Variables in `theme.liquid`](https://docs.fluid.app/#step-2-wire-settings-to-css-variables-in-themeliquid)
 - [Corner Radius Presets](https://docs.fluid.app/#corner-radius-presets)
 - [Padding Presets](https://docs.fluid.app/#padding-presets)
 - [How the Mapping Works](https://docs.fluid.app/#how-the-mapping-works)
 - [Naming Convention](https://docs.fluid.app/#naming-convention)
 - [What Gets Linked and What Doesn't](https://docs.fluid.app/#what-gets-linked-and-what-doesnt)
 - [Default Behavior (Linked Presets)](https://docs.fluid.app/#default-behavior-linked-presets)
 - [Unlinking](https://docs.fluid.app/#unlinking)

---

## How It Works

When a company selects a linked preset in the rich text editor, the output uses CSS variable references wrapped in **`var()`**:

<span style\="font-size: var(--font\_size\_h1); font-family: var(--font\_family\_heading); font-weight: bold;"\>
 Welcome to our store
</span\>
<span style\="color: var(--color\_primary);"\>
 Shop now
</span\>

If the company **unlinks** and switches to manual controls, the resolved values are applied directly instead:

<span style\="font-size: 32px; font-family: Montserrat; font-weight: bold;"\>
 Welcome to our store
</span\>
<span style\="color: #2563eb;"\>
 Shop now
</span\>

For this to work, your theme needs two things: settings defined in `settings_schema.json` and CSS variables wired in `theme.liquid`.

---

## Setup

### Step 1: Define Settings in `settings_schema.json`

The builder looks for settings in specific groups by name. Settings in these groups become available as presets:

| Group Name | What It Powers |
| --- | --- |
| `typography` | Font size presets in the rich text editor |
| `color_schema` | Color presets in the rich text editor |
| `corner_radius` | Corner radius presets |
| `padding` | Padding/spacing presets |
| `custom_font_sizes` | Additional custom text size presets (optional) |
| `custom_colors` | Additional custom color presets (optional) |

Font family settings (`font_family_heading`, `font_family_body`) can be in any group — they are resolved by setting ID, not group name.

**Example typography settings:**

{
 "name": "typography",
 "settings": \[
 {
 "type": "range",
 "id": "font\_size\_h1",
 "label": "Heading 1 Size",
 "min": 16,
 "max": 80,
 "step": 1,
 "default": 40,
 "unit": "px"
 },
 {
 "type": "range",
 "id": "font\_size\_h2",
 "label": "Heading 2 Size",
 "min": 16,
 "max": 72,
 "step": 1,
 "default": 32,
 "unit": "px"
 },
 {
 "type": "font\_picker",
 "id": "font\_family\_heading",
 "label": "Heading Font",
 "default": "Montserrat"
 },
 {
 "type": "font\_picker",
 "id": "font\_family\_body",
 "label": "Body Font",
 "default": "Inter"
 }
 \]
}

**Example color settings:**

{
 "name": "color\_schema",
 "settings": \[
 {
 "type": "color",
 "id": "color\_primary",
 "label": "Primary Color",
 "default": "#2563eb"
 },
 {
 "type": "color",
 "id": "color\_body",
 "label": "Body Text Color",
 "default": "#1a1a2e"
 }
 \]
}

### Step 2: Wire Settings to CSS Variables in `theme.liquid`

Inside a `{%- style -%}...{%- endstyle -%}` or `<style>...</style>` block in `layouts/theme.liquid`, define CSS custom properties that reference your settings:

{'%' style '%'}
 :root {
 /\* Typography \*/
 --font\_size\_h1: {{ settings.font\_size\_h1 | append: 'px' }};
 --font\_size\_h2: {{ settings.font\_size\_h2 | append: 'px' }};
 --font\_size\_h3: {{ settings.font\_size\_h3 | append: 'px' }};
 --font\_family\_heading: {{ settings.font\_family\_heading | font\_family }};
 --font\_family\_body: {{ settings.font\_family\_body | font\_family }};

 /\* Colors \*/
 --color\_primary: {{ settings.color\_primary }};
 --color\_body: {{ settings.color\_body }};
 --color\_heading: {{ settings.color\_heading }};

 /\* Corner Radius \*/
 --corner\_radius\_sm: {{ settings.corner\_radius\_sm | append: 'px' }};
 --corner\_radius\_md: {{ settings.corner\_radius\_md | append: 'px' }};
 --corner\_radius\_lg: {{ settings.corner\_radius\_lg | append: 'px' }};

 /\* Padding / Spacing \*/
 --spacing\_sm: {{ settings.spacing\_sm | append: 'px' }};
 --spacing\_md: {{ settings.spacing\_md | append: 'px' }};
 --spacing\_lg: {{ settings.spacing\_lg | append: 'px' }};
 }
{'%' endstyle '%'}

If a setting in a recognized group has a matching CSS variable in `theme.liquid`, the preset becomes **linkable** in the builder.

---

## Corner Radius Presets

Corner radius inputs support linked CSS variable presets. Define corner radius settings in the `corner_radius` group and map them to CSS variables in `theme.liquid`.

**`settings_schema.json`:**

{
 "name": "corner\_radius",
 "settings": \[
 {
 "type": "range",
 "id": "corner\_radius\_sm",
 "label": "Small",
 "min": 0,
 "max": 24,
 "step": 1,
 "default": 4,
 "unit": "px"
 },
 {
 "type": "range",
 "id": "corner\_radius\_md",
 "label": "Medium",
 "min": 0,
 "max": 32,
 "step": 1,
 "default": 8,
 "unit": "px"
 },
 {
 "type": "range",
 "id": "corner\_radius\_lg",
 "label": "Large",
 "min": 0,
 "max": 48,
 "step": 1,
 "default": 16,
 "unit": "px"
 }
 \]
}

When a preset is selected, the saved value is the **`var()`** reference (e.g. `var(--corner_radius_md)`), not the bare name `--corner_radius_md`. The input shows the preset label in read-only mode. When corners are linked, the preset applies to all four corners.

---

## Padding Presets

Padding inputs support the same linked preset pattern using the `padding` group name.

**`settings_schema.json`:**

{
 "name": "padding",
 "settings": \[
 {
 "type": "range",
 "id": "spacing\_sm",
 "label": "Small",
 "min": 0,
 "max": 48,
 "step": 1,
 "default": 8,
 "unit": "px"
 },
 {
 "type": "range",
 "id": "spacing\_md",
 "label": "Medium",
 "min": 0,
 "max": 64,
 "step": 1,
 "default": 16,
 "unit": "px"
 },
 {
 "type": "range",
 "id": "spacing\_lg",
 "label": "Large",
 "min": 0,
 "max": 96,
 "step": 1,
 "default": 32,
 "unit": "px"
 }
 \]
}

When a preset is selected, the saved value uses **`var(--token)`** on the selected side(s) (for example `padding-top: var(--spacing_md)`), not bare `--spacing_md`. When sides are linked, the preset applies to all four sides.

---

## How the Mapping Works

The admin parses `theme.liquid` and matches this pattern:

\--{css\_var\_name}: {{ settings.{setting\_id} ...

It builds a map of `setting_id` to `css_var_name` (the `--…` name from the left-hand side of the declaration). When the builder saves a linked preset, it writes that token inside **`var()`** in the stored style (e.g. mapping `color_primary` → `--color_primary` results in `color: var(--color_primary)` in saved content).

| Line in `theme.liquid` | Detected Mapping |
| --- | --- |
| `--font_size_h1: {{ settings.font_size_h1 | append: 'px' }};` | `font_size_h1` → `--font_size_h1` |
| `--color_primary: {{ settings.color_primary }};` | `color_primary` → `--color_primary` |
| `--font_family_heading: {{ settings.font_family_heading | font_family }};` | `font_family_heading` → `--font_family_heading` |

---

## Naming Convention

The CSS variable name does **not** need to match the setting ID. All of these work:

\--font\_size\_h1: {{ settings.font\_size\_h1 | append: 'px' }};
\--heading\-size\-1: {{ settings.font\_size\_h1 | append: 'px' }};
\--h1\-size: {{ settings.font\_size\_h1 | append: 'px' }};

The parser matches on the `settings.{id}` part, not the variable name. However, keeping names consistent (e.g., `--font_size_h1` for `settings.font_size_h1`) is recommended for clarity.

---

## What Gets Linked and What Doesn't

Not everything becomes a linked preset:

- **Custom presets** (from `custom_font_sizes` / `custom_colors` groups) — these are user-added values that don't map to CSS variables in `theme.liquid`. They always apply resolved values.
- **Settings without a CSS variable in `theme.liquid`** — if a setting exists in `settings_schema.json` but has no corresponding `--var: {{ settings.id }}` line in `theme.liquid`, it will still appear as a preset but will apply the resolved value directly.
- **Font weight** — always applied as a resolved value (`bold` / `normal`), not as a CSS variable. There is no theme setting for font weight.

---

### Default Behavior (Linked Presets)

The toolbar shows two preset pickers: **Text Presets** and **Color Presets**. Each preset in the dropdown corresponds to a theme setting. Selecting a preset writes inline styles using **`var(--setting_token)`** (always the `var()` wrapper), matching the rich text, corner radius, and padding behavior above.

### Unlinking

Each preset picker has an **"Unlink Variables"** option in its footer. Clicking it:

1. Resolves any active CSS variable values back to their current concrete values
2. Switches the toolbar to manual mode showing individual controls: **Font Family picker**, **Font Size picker**, and **Color picker**

The toolbar has a **"Link Variables"** option to switch back to preset mode.