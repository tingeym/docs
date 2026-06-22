# Source: https://docs.fluid.app/docs/themes/template-types

# Blocks and Components

When building a Fluid theme, you'll work with two types of reusable templates: **blocks** and **components**. This guide explains what each is, when to use them, and how they differ.

---

## Table of Contents

1. [Components](https://docs.fluid.app/#components)
2. [Blocks](https://docs.fluid.app/#blocks)
 - [Section blocks (inline)](https://docs.fluid.app/#section-blocks-inline)
 - [Theme blocks (standalone)](https://docs.fluid.app/#theme-blocks-standalone)
 - [Private blocks](https://docs.fluid.app/#private-blocks)
 - [The @theme wildcard](https://docs.fluid.app/#the-theme-wildcard)
 - [Named block references](https://docs.fluid.app/#named-block-references)
 - [Rendering blocks with content\_for](https://docs.fluid.app/#rendering-blocks-with-content_for)
 - [Nesting blocks](https://docs.fluid.app/#nesting-blocks)
 - [Presets](https://docs.fluid.app/#presets)
 - [Static blocks](https://docs.fluid.app/#static-blocks)
3. [Blocks vs Components](https://docs.fluid.app/#blocks-vs-components)

---

## Components

Components are reusable Liquid partials. They don't have a schema or settings — instead, they accept props when you render them. Think of them like functions you call from your sections or other templates.

### Folder structure

theme/
 components/
 button/
 index.liquid
 product\_card/
 index.liquid

### Example: Button component

{'%' comment '%'}
 Props:
 - tag: string (a or button, default: button)
 - type: string (button, submit, reset; for button tag)
 - href: string (for link buttons)
 - variant: string (primary, secondary, outline, link, white-primary, white-outline)
 - size: string (sm, md, lg)
 - text: string (button label)
 - icon: string (icon name or svg)
 - icon\_position: string (left, right, default: left)
 - disabled: boolean
 - attr: string (additional attributes)
 - class: string
{'%' endcomment '%'}

{'%' liquid
 assign tag = tag | default: 'button'
 if href != blank
 assign tag = 'a'
 endif
 assign variant = variant | default: 'primary'
 assign size = size | default: 'md'
 assign icon\_position = icon\_position | default: 'left'
 assign type = type | default: 'button'

 case variant
 when 'primary'
 assign variant\_class = 'btn-bg-primary'
 when 'secondary'
 assign variant\_class = 'btn-bg-secondary'
 when 'outline'
 assign variant\_class = 'btn-outline'
 else
 assign variant\_class = variant
 endcase

 assign size\_class = 'button--medium'
 case size
 when 'sm'
 assign size\_class = 'button--small'
 when 'lg'
 assign size\_class = 'button--large'
 endcase
'%'}

<{{ tag }}
 {'%' if tag == 'a' '%'}href="{{ href }}"{'%' else '%'}type="{{ type }}"{'%' endif '%'}
 class="button {{ variant\_class }} {{ size\_class }} {{ class }}"
 {'%' if disabled '%'}disabled{'%' endif '%'}
 {'%' if disabled and tag == 'a' '%'}style="pointer-events: none; opacity: 0.5;"{'%' endif '%'}
 {{ attr }}
>
 {'%' if icon and icon\_position == 'left' '%'}
 <span class="fluid-btn\_\_icon leading-none flex items-center justify-center">{{ icon }}</span>
 {'%' endif '%'}
 <span class="fluid-btn\_\_text">{{ text }}</span>
 {'%' if icon and icon\_position == 'right' '%'}
 <span class="fluid-btn\_\_icon leading-none flex items-center justify-center">{{ icon }}</span>
 {'%' endif '%'}
</{{ tag }}>

### How to use a component

Call a component from any section, block, or other component using `{'%' render '%'}`:

{'%' render 'button',
 text: 'Buy Now',
 href: product.url,
 variant: 'primary',
 size: 'md'
'%'}

### When to create a component

- You have a piece of UI that shows up in multiple places (buttons, cards, pagination, icons)
- The template doesn't need configurable settings in the visual builder — it just takes props and outputs HTML
- You want to keep your section templates clean by extracting repeated markup

### Tips

- Document your props in a `{'%' comment '%'}` block at the top of the file
- Use `| default:` filters to set fallback values for optional props
- Components can render other components — nesting is fine

---

## Blocks

Blocks are dynamic content elements that let users add, remove, and reorder content within a section through the visual builder. There are two kinds of blocks:

- **Section blocks** — defined inline in a section's `{'%' schema '%'}` tag
- **Theme blocks** — standalone `.liquid` files in the `blocks/` directory, reusable across sections

### Section blocks (inline)

The traditional approach. Sections define their available block types directly in the `"blocks"` array of their schema. The section template renders blocks inline using `{'%' for block in section.blocks '%'}`.

#### Example: A section with inline blocks

{'%' for block in section.blocks '%'}
 {'%' case block.type '%'}
 {'%' when 'heading' '%'}
 <h2 class="rte" {{ block.fluid\_attributes }}>{{ block.settings.text }}</h2>
 {'%' when 'text' '%'}
 <p class="rte" {{ block.fluid\_attributes }}>{{ block.settings.content }}</p>
 {'%' endcase '%'}
{'%' endfor '%'}

{'%' schema '%'}
{
 "name": "Promo Banner",
 "blocks": \[
 {
 "type": "heading",
 "name": "Heading",
 "limit": 1,
 "settings": \[
 { "type": "richtext", "id": "text", "label": "Text", "default": "<p>Welcome</p>" }
 \]
 },
 {
 "type": "text",
 "name": "Text",
 "settings": \[
 { "type": "richtext", "id": "content", "label": "Content" }
 \]
 }
 \],
 "presets": \[
 {
 "name": "Promo Banner",
 "blocks": \[
 { "type": "heading" },
 { "type": "text" }
 \]
 }
 \]
}
{'%' endschema '%'}

Inline blocks work well when block types are specific to a single section. But if you find yourself repeating the same block definitions across many sections, theme blocks are a better fit.

---

### Theme blocks (standalone)

Theme blocks are standalone `.liquid` files in the `blocks/` directory. Each file contains its own markup, schema, and settings. Any section can use them without redefining the block's settings.

#### Folder structure

theme/
 blocks/
 heading/
 index.liquid
 description/
 index.liquid
 button/
 index.liquid
 group/
 index.liquid
 \_slide/
 index.liquid <\-- private block (underscore prefix)

#### Example: Heading block

<div class="rte" {{ block.fluid\_attributes }}>{{ block.settings.text }}</div>

{'%' schema '%'}
{
 "name": "Heading",
 "settings": \[
 {
 "type": "richtext",
 "id": "text",
 "label": "Text",
 "default": "<p>Medium length heading goes here</p>"
 }
 \],
 "presets": \[
 { "name": "Heading" }
 \]
}
{'%' endschema '%'}

#### Example: Button block

{'%' assign cr = block.settings.border\_radius '%'}
{'%' assign \_btn\_attr = block.fluid\_attributes '%'}
{'%' if cr '%'}
 {'%' capture cr\_tl '%'}{{ cr.tl }}{'%' endcapture '%'}
 {'%' capture cr\_tr '%'}{{ cr.tr }}{'%' endcapture '%'}
 {'%' capture cr\_br '%'}{{ cr.br }}{'%' endcapture '%'}
 {'%' capture cr\_bl '%'}{{ cr.bl }}{'%' endcapture '%'}
 {'%' capture \_br\_val '%'}{{ cr\_tl }}px {{ cr\_tr }}px {{ cr\_br }}px {{ cr\_bl }}px{'%' endcapture '%'}
 {'%' assign \_btn\_attr = 'style="border-radius: ' | append: \_br\_val | append: ';" ' | append: \_btn\_attr '%'}
{'%' endif '%'}
{'%' if block.settings.text != blank '%'}
 {'%' render 'button',
 href: block.settings.link | default: '#',
 text: block.settings.text,
 variant: block.settings.style | default: 'btn-bg-primary',
 attr: \_btn\_attr
 '%'}
{'%' endif '%'}

{'%' schema '%'}
{
 "name": "Button",
 "settings": \[
 { "type": "text", "id": "text", "label": "Text", "default": "Button" },
 { "type": "url", "id": "link", "label": "Link" },
 {
 "type": "select",
 "id": "style",
 "label": "Style",
 "options": \[
 { "value": "btn-bg-white", "label": "White" },
 { "value": "btn-bg-primary", "label": "Primary" },
 { "value": "btn-border-primary", "label": "Primary Border" }
 \],
 "default": "btn-bg-primary"
 },
 { "type": "corner\_radius", "id": "border\_radius", "label": "Border Radius" }
 \],
 "presets": \[
 { "name": "Button" },
 { "name": "White Button", "settings": { "style": "btn-bg-white" } }
 \]
}
{'%' endschema '%'}

**Key differences from section blocks:**

- Theme blocks have their own `.liquid` file with markup — sections don't need to render them manually
- Settings are defined once and reused everywhere
- Multiple presets let a single block file appear as different variants in the block picker
- Theme blocks can contain nested child blocks (see [Nesting blocks](https://docs.fluid.app/#nesting-blocks))

---

### Private blocks

Blocks with names starting with an underscore (`_`) are **private blocks**. They are excluded from the `@theme` wildcard and only available when a section explicitly references them by name.

Use private blocks for section-specific block types that shouldn't appear in the block picker of other sections.

theme/
 blocks/
 heading/
 index.liquid <\-- public, available via @theme
 \_slide/
 index.liquid <\-- private, must be explicitly referenced
 \_product\_card/
 index.liquid <\-- private

A slideshow section can reference the private `_slide` block explicitly:

{
 "name": "Slideshow",
 "blocks": \[{ "type": "\_slide" }\]
}

But a generic section using `@theme` will NOT see `_slide` in its block picker — only public blocks like `heading`, `description`, `button`, etc.

---

### The @theme wildcard

Sections can accept **all public theme blocks** by declaring `{"type": "@theme"}` in their blocks array. This is the most flexible pattern — users can add any combination of theme blocks to the section.

<section class="custom-section section-{{ section.id }}">
 <div class="container">
 {'%' content\_for 'blocks' '%'}
 </div>
</section>

{'%' schema '%'}
{
 "name": "Custom Section",
 "blocks": \[{ "type": "@theme" }\],
 "settings": \[\],
 "presets": \[
 {
 "name": "Custom Section",
 "blocks": \[
 { "type": "heading", "settings": { "text": "<h2>Hello World</h2>" } },
 { "type": "description", "settings": { "text": "<p>Add any blocks you want.</p>" } }
 \]
 }
 \]
}
{'%' endschema '%'}

`@theme` expands to all public (non-underscore) blocks in the `blocks/` directory. The visual builder shows them in the "Add block" picker.

You can combine `@theme` with `@app` to also accept extension blocks:

"blocks": \[{ "type": "@theme" }, { "type": "@app" }\]

You can mix all three approaches in the same section — inline definitions, `@theme`, and named references:

"blocks": \[
 { "type": "@theme" },
 { "type": "\_slide" },
 { "type": "custom\_inline", "name": "Custom Inline", "settings": \[
 { "type": "text", "id": "label", "label": "Label" }
 \]}
\]

In this example:

- `@theme` makes all public standalone blocks available
- `_slide` explicitly adds the private slide block (excluded from `@theme`)
- `custom_inline` defines a section-specific block with inline settings

When resolving a block, the engine checks inline definitions first, then falls back to standalone block templates via `@theme` or named references.

---

### Named block references

Instead of `@theme` (which accepts all blocks), you can reference specific standalone blocks by name:

{
 "name": "Slideshow",
 "blocks": \[{ "type": "\_slide" }\]
}

This restricts the section to only accept the `_slide` block. The block's settings come from the standalone template file — you don't need to redefine them in the section schema.

You can also reference multiple specific blocks:

"blocks": \[
 { "type": "heading" },
 { "type": "description" },
 { "type": "button" }
\]

When a block entry has no `settings` key, the engine resolves the settings from the matching standalone block template in the `blocks/` directory.

---

### Rendering blocks with content\_for

When using standalone theme blocks, use `{'%' content_for 'blocks' '%'}` to render them. Each block renders using its own `.liquid` template from the `blocks/` directory.

<div class="my-section">
 {'%' content\_for 'blocks' '%'}
</div>

This is different from the inline approach where you manually iterate with `{'%' for block in section.blocks '%'}`. With `content_for`, each block's own template handles the rendering — the section doesn't need to know how each block looks.

#### Rendering a single static block

You can render a specific block by type and ID:

{'%' content\_for 'block', type: 'heading', id: 'section-header' '%'}

This is useful for pinned/static blocks that always appear in a fixed position (see [Static blocks](https://docs.fluid.app/#static-blocks)).

---

### Nesting blocks

Theme blocks can contain child blocks. This is useful for container blocks like groups, columns, or cards.

#### Example: Group block

A group block that accepts any theme block as children:

<div class="block-group" {{ block.fluid\_attributes }}
 style="background-color: {{ block.settings.background\_color }};">
 {'%' content\_for 'blocks' '%'}
</div>

{'%' schema '%'}
{
 "name": "Group",
 "blocks": \[{ "type": "@theme" }\],
 "settings": \[
 {
 "type": "color\_background",
 "id": "background\_color",
 "label": "Background Color"
 }
 \],
 "presets": \[
 { "name": "Group" },
 {
 "name": "Card",
 "settings": { "background\_color": "#f8f8f8" },
 "blocks": \[
 { "type": "heading", "settings": { "text": "<h3>Card Title</h3>" } },
 { "type": "description", "settings": { "text": "<p>Card description.</p>" } }
 \]
 }
 \]
}
{'%' endschema '%'}

The `{'%' content_for 'blocks' '%'}` inside a block template renders that block's children, not the section's blocks.

**Nesting depth limit:** Blocks can be nested up to 2 levels deep.

---

### Presets

Presets define the default blocks and settings when a section is first added to a page. They serve two purposes:

1. **Provide starter content** so the section isn't blank when added
2. **Offer variants** — multiple presets let the same section appear as different options in the section picker

#### Basic preset

"presets": \[
 {
 "name": "Hero Banner",
 "blocks": \[
 { "type": "heading", "settings": { "text": "<h1>Welcome</h1>" } },
 { "type": "button", "settings": { "text": "Shop Now", "style": "btn-bg-white" } }
 \]
 }
\]

#### Preset with nested blocks

Presets can include nested blocks for container block types:

"presets": \[
 {
 "name": "Two Column Cards",
 "blocks": \[
 {
 "type": "group",
 "settings": { "background\_color": "#f0f4f8" },
 "blocks": \[
 { "type": "heading", "settings": { "text": "<h3>Column One</h3>" } },
 { "type": "description", "settings": { "text": "<p>First column content.</p>" } }
 \]
 },
 {
 "type": "group",
 "settings": { "background\_color": "#f8f0f4" },
 "blocks": \[
 { "type": "heading", "settings": { "text": "<h3>Column Two</h3>" } },
 { "type": "description", "settings": { "text": "<p>Second column content.</p>" } }
 \]
 }
 \]
 }
\]

#### Block presets (in standalone blocks)

Standalone block templates can also have presets. These control how the block appears in the block picker and provide pre-configured variants:

{
 "name": "Button",
 "settings": \[...\],
 "presets": \[
 { "name": "Button" },
 { "name": "White Button", "settings": { "style": "btn-bg-white" } },
 { "name": "Outline Button", "settings": { "style": "btn-border-primary" } }
 \]
}

A block without presets will not appear in the block picker.

---

### Static blocks

Static blocks are pinned to a specific location in the section and cannot be removed or reordered by users. They are excluded from the normal block flow rendered by `{'%' content_for 'blocks' '%'}`.

Mark a block as static in the preset:

"presets": \[
 {
 "name": "Content Columns",
 "blocks": \[
 {
 "type": "heading",
 "id": "section-header",
 "static": true,
 "settings": { "text": "<h2>Section Title</h2>" }
 },
 { "type": "group", "blocks": \[...\] },
 { "type": "group", "blocks": \[...\] }
 \]
 }
\]

Render the static block explicitly:

{'%' content\_for 'block', type: 'heading', id: 'section-header' '%'}

<div class="grid">
 {'%' content\_for 'blocks' '%'}
</div>

The static heading always appears above the grid. The dynamic blocks (groups) render inside the grid. Users can add, remove, and reorder the dynamic blocks but not the static heading.

---

### Block schema properties

| Property | Description |
| --- | --- |
| `type` | Unique identifier for the block type. Use `@theme` for all public blocks, `@app` for extension blocks. |
| `name` | Display name shown in the visual builder. Not needed for `@theme`, `@app`, or named references. |
| `limit` | Maximum number of this block type allowed (optional). |
| `settings` | Array of configurable settings. Not needed for named references — resolved from the standalone block template. |
| `blocks` | Child block definitions for nested blocks (standalone blocks only). |

---

## Blocks vs Components

| | Components | Section Blocks | Theme Blocks |
| --- | --- | --- | --- |
| Has schema? | No | Yes (inline in section) | Yes (own file) |
| Configurable in visual builder? | No | Yes | Yes |
| Can be added/removed/reordered? | No | Yes | Yes |
| How to render | `{'%' render 'name' '%'}` | `{'%' for block in section.blocks '%'}` | `{'%' content_for 'blocks' '%'}` |
| Reusable across sections? | Yes | No (per-section) | Yes (via `@theme` or named ref) |
| Supports nesting? | Via render calls | No | Yes (up to 2 levels) |
| Folder | `components/` | Inline in section schema | `blocks/` |
| Use case | Simple partials (button, card) | Section-specific content units | Reusable, configurable content units |

### Which should I use?

- **Use a component** when you need a reusable piece of markup that just takes props — no visual builder settings, no user controls.
- **Use section blocks (inline)** when block types are unique to a single section and you want full control over how they render within that section's template.
- **Use theme blocks (standalone)** when the same block types appear across many sections. Define once in `blocks/`, reference everywhere with `@theme` or by name. Use `{'%' content_for 'blocks' '%'}` to let each block render itself.
- Components and blocks work together — a standalone button block might call the button component internally to handle the actual HTML rendering.