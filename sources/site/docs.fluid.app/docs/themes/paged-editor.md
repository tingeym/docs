# Source: https://docs.fluid.app/docs/themes/paged-editor

# Fluid Page Editor - User Guide

## What is the Page Editor?

The Fluid Page Editor is your visual workspace for customizing website pages. It's designed for theme engineers who want to create beautiful, professional pages without complex coding.

With the Page Editor, you can:

- **Edit pages visually** by clicking and selecting sections
- **See changes instantly** as you make them
- **Customize content easily** using simple settings panels
- **Choose from three professional themes** (Base, Vox, Fluid)
- **Access advanced options** when you need more control

## Getting Started

### Choosing Your Theme

Fluid provides three professionally designed themes to choose from:

- **Base Theme** - Clean and minimal design perfect for any business
- **Vox Theme** - Modern and bold styling ideal for creative businesses
- **Fluid Theme** - Versatile design that adapts well to your brand

Each theme comes with pre-built sections and layouts that you can customize to match your needs.

### Accessing the Page Editor

1. Go to **Sidebar > Website > Pages** in your Fluid dashboard
2. Click **"Edit"** on any existing page, or **"Create New Page"**
3. The Page Editor opens with your page ready to customize

### Your Page Editor Interface

The editor has three main areas:

- **Left panel** with Templates, Sections, and Layers tabs
- **Center preview** showing your page as visitors will see it
- **Right panel** with settings to customize selected sections

## How Pages Work

Think of your pages like building blocks that stack together:

**Your Page** uses a **Template** which contains **Sections** that have **Blocks**

### Pages and Templates

In Fluid, **all pages are templates**. This means:

- Every page you create can serve as a template for other pages
- You can create new templates from the left sidebar menu
- You can turn any existing page into a reusable template

Common types of templates you might create:

- **Product Page Template** - For showcasing individual products
- **Home Page Template** - Your main website layout
- **Collection Template** - For displaying groups of products
- **About Page Template** - For company information pages
- **Landing Page Template** - For marketing campaigns

### Sections

Sections are content areas you can create for your pages. Examples of sections you might build:

- **Hero Section** - Large banner with headline and call-to-action
- **Gallery Section** - Showcase images or videos
- **Text Section** - Written content and descriptions
- **Form Section** - Contact forms and data collection
- **Testimonial Section** - Customer reviews and quotes

Each section you create can have its own customizable settings.

### Blocks

Blocks are elements you can add within your sections. Examples include:

- **Text blocks** for headings and paragraphs
- **Image blocks** for photos and graphics
- **Button blocks** for links and calls-to-action
- **Video blocks** for embedded media

## Using the Page Editor

### Step 1: Choose Your Approach

**Visual Editing (For everyone):**

- Point and click to edit your pages
- No coding knowledge needed
- Perfect for customizing content, images, colors, and layout

**Code Editing (For more customization):**

- Edit the underlying code when you need deeper control
- Access advanced styling and structure options
- Requires HTML and Liquid knowledge

### Step 2: Navigate the Interface

**Left Panel - Three Tabs:**

- **Templates:** Browse existing templates or create new ones
- **Sections:** View sections you've created for your theme
- **Layers:** See which sections are active on the current page

**Center Preview:**

- Shows your page exactly as visitors will see it
- Click on any section to select it
- Changes appear instantly as you make them
- **Add sections** by clicking the "Add Section" button - opens a modal with available sections
- **Delete sections** by selecting them and using the delete option

**Right Panel - Settings:**

- Automatically shows options for the selected section
- Simple controls for text, images, colors, and layout
- Settings change based on which section you've selected

### Step 3: Customize Your Content

**Editing Sections:**

1. Click on any section in the preview or Layers tab
2. Settings for that section appear in the right panel
3. Make your changes using the simple controls
4. Watch your changes appear instantly in the preview

**Adding New Sections:**

1. Click the **"Add Section"** button in the center preview area
2. A modal opens showing all available sections for your template
3. Browse through the section options and click to select one
4. The new section is added to your template and appears in the preview
5. Customize the new section using the settings panel

**Removing Sections:**

1. Click on the section you want to remove in the preview
2. Use the delete option to remove it from your template
3. The section disappears from both the preview and the Layers tab

**Common Settings You'll See:**

- **Text content** - Edit headlines, descriptions, button text
- **Images** - Upload photos, logos, background images
- **Colors** - Change text colors, background colors, button colors
- **Layout** - Adjust spacing, alignment, and positioning

**Translations** Fluid’s Page Editor supports multi-language content editing. This feature allows you to manage and translate your page text across all languages available in your company.

**How to Access Text Translation:**

**Translating Individual Text Elements:**

- Click on any text element within your page preview to open its settings.
- In the right settings panel, you’ll see a field to update the text content.
- On the top right corner of that settings area, you’ll find a “Translation” button.
- Click on this button to open the Translation Panel.
- The panel will display a list of text fields for each available language in your company.
- Enter or edit translations for each language as needed.
- When finished, click Save — your translations will automatically be linked to the selected text element.

**Managing All Translations from the Top Navigation:**

- On the top navigation bar of the Page Editor, click the “Translations” button.
- This opens a drawer containing all text content from the current page, grouped by section.
- Each section lists its text fields with columns for every available language — displayed in a table format.
- You can edit translations for multiple sections and languages all in one place.
- Once finished, click Save — all updates will instantly sync with the relevant text elements in your page.

**Tips:**

- Translations are stored per text field, so each phrase or section can have unique localized content.
- Default text (usually English) will display if a translation is missing for the user’s selected language.

## Global Variables

Fluid provides global variables that you can use throughout your themes to access important information:

### Company Information

- `{{ company.name }}` - Your business name
- `{{ company.logo }}` - Your company logo
- `{{ company.description }}` - Business description
- `{{ company.email }}` - Contact email address
- `{{ company.phone }}` - Phone number
- `{{ company.address }}` - Business address

### Page Information

- `{{ page.title }}` - Current page title
- `{{ page.content }}` - Page content
- `{{ page.url }}` - Page web address
- `{{ page.featured_image }}` - Main page image

### Product Information (for product pages)

- `{{ product.name }}` - Product name
- `{{ product.price }}` - Product price
- `{{ product.description }}` - Product description
- `{{ product.images }}` - Product photos
- `{{ product.available }}` - Whether product is in stock

### Formatting Helpers

- `{{ product.price | money }}` - Format price as currency
- `{{ image.url | img_url: '300x200' }}` - Resize images
- `{{ product.created_at | date: '%B %d, %Y' }}` - Format dates

## Advanced Options (Code Mode)

For theme engineers who need more control:

**Code Editor Features:**

- **HTML Tab:** Edit page structure and add Liquid variables
- **CSS Tab:** Create custom styles and layouts
- **Variables Tab:** Set theme-wide settings and colors
- **Preview Tab:** Test your changes before publishing

## Creating Custom Sections (Advanced)

### What are Schemas?

Schemas are simple instructions that tell the Page Editor what settings to show. When you create a section, you add a schema at the end that defines what options users can customize.

### Simple Schema Example

<div class\="my-section"\>
 <h2\>{{ section.settings.title }}</h2\>
 <p\>{{ section.settings.description }}</p\>
</div\>

{'%' schema '%'}
{
 "name": "My Custom Section",
 "settings": \[
 {
 "type": "text",
 "id": "title",
 "label": "Section Title"
 },
 {
 "type": "textarea",
 "id": "description", 
 "label": "Description"
 },
 {
 "type": "color",
 "id": "bg\_color",
 "label": "Background Color"
 }
 \]
}
{'%' endschema '%'}

### Common Setting Types

- `text` - Single line text input
- `textarea` - Multi-line text
- `color` - Color picker
- `image_picker` - Image upload
- `select` - Dropdown menu
- `checkbox` - True/false toggle

When users select your section, these settings automatically appear in the right panel for easy customization.

## Working with the Page Editor

### Understanding the Interface

The Page Editor provides a user-friendly visual editing interface with three main areas:

Page Editor Interface

Left Panel: Navigation

Center Panel: Live Page Preview

Right Panel: Settings

Top Bar: Editor Controls

Templates List

Sections List

Page Elements

Live Page Preview

Interactive Elements

Real-time Updates

Element Settings

Section Settings

Page Settings

Visual Editor Mode

Code Editor Mode

Save & Publish

HTML Editor

CSS Editor

Variables Editor

**Left Panel - Navigation:**

- **Templates Tab:** Create new templates or browse existing ones
- **Sections Tab:** View and manage sections you've built for your theme
- **Layers Tab:** See which sections are currently used in this template
- Switch between tabs to manage your templates, sections, and page structure

**Center Panel - Live Page Preview:**

- Interactive preview of your actual page
- Real-time updates as you make changes
- Click on any element to select and edit it
- See exactly how your page will look to visitors

**Right Panel - Settings:**

- **Schema-driven settings** for the selected section
- Settings are automatically generated from each section's schema
- Customize content, styling, and functionality through these controls
- Settings change based on which section you have selected

**Top Bar - Editor Controls:**

- Toggle between Visual Editor and Code Editor modes
- Save your changes as drafts
- Publish your page to make it live
- Preview your page in different device sizes

### Working with Content

The Page Editor makes it easy to add and manage content without needing to know code:

**Adding Text Content:**

- Click on any text element to edit it directly
- Use the text formatting toolbar for bold, italics, lists, and links
- Change fonts, colors, and sizes using the settings panel

**Working with Images:**

- Click on image elements to select and replace them
- Upload new images through the settings panel
- Adjust image settings like size, alignment, and alt text
- Configure image display options and styling

**Managing Links and Buttons:**

- Click on buttons or links to change their destination
- Link to other pages on your site, external websites, or email addresses
- Customize button colors, sizes, and styles
- Add hover effects and animations

### Building Your Page Layout

The Page Editor provides a flexible system for creating beautiful page layouts:

Start Editing

Select Section

Click on Elements

Customize Settings

Hero Section

Gallery Section

Text Section

Contact Form

Text Elements

Image Elements

Button Elements

Video Elements

Colors & Fonts

Layout & Spacing

Content Settings

Style Options

Edit Background

Change Headlines

Update Buttons

**Examples of Sections You Can Create:**

- **Hero Sections** - Large banners with headlines and call-to-action buttons
- **Gallery Sections** - Image showcases with various layout options
- **Text Sections** - Rich content areas with headings and paragraphs
- **Form Sections** - Contact forms and data collection
- **Testimonial Sections** - Customer reviews and social proof

**Customization Options:** When you select any element, you'll see relevant settings in the right panel:

- **Text Settings** - Font family, size, color, alignment, line spacing
- **Image Settings** - Size, crop, filters, overlay effects, alt text
- **Layout Settings** - Margins, padding, column layouts, responsive behavior
- **Style Settings** - Background colors, borders, shadows, animations

## Best Practices

### Choosing the Right Theme

**Base Theme** - Perfect for:

- Professional service businesses
- Clean, minimal designs
- When you want content to be the focus
- B2B companies and consultants

**Vox Theme** - Great for:

- Creative agencies and studios
- Bold, modern aesthetics
- Showcasing visual portfolios
- Businesses that want to stand out

**Fluid Theme** - Ideal for:

- E-commerce stores
- Versatile businesses that need flexibility
- When you want to customize extensively
- Multi-purpose websites

### Getting Started Tips

1. **Pick your theme first** - Choose Base, Vox, or Fluid based on your business needs
2. **Start with existing sections** - Use the pre-built sections before creating custom ones
3. **Make small changes** - Edit one section at a time and preview your changes
4. **Keep it simple** - Don't try to customize everything at once

### Content Guidelines

- **Write clear headlines** - Make your message easy to understand
- **Use quality images** - High-resolution photos make a big difference
- **Keep text scannable** - Use short paragraphs and bullet points
- **Include clear calls-to-action** - Tell visitors what you want them to do

### Design Tips

- **Stay consistent** - Use the same fonts and colors throughout your site
- **Leave breathing room** - Don't cram too much content into one section
- **Test on mobile** - Most visitors will view your site on their phones
- **Make navigation easy** - Keep menus simple and logical

## Common Workflows

### Working with Templates and Sections

Yes

No

Open Page in Editor

Select Template

View Layers Tab

See Active Sections

Click on Section

Settings Panel Opens

Modify Section Settings

See Live Preview Update

Need Different Section?

Browse Sections Tab

Continue Editing

Select New Section

Add to Template

Save Changes

Publish Page

### Customizing Page Content

1. **Open your page** in the Page Editor
2. **Use the Layers tab** to see which sections are active in your template
3. **Click on a section** in the Layers tab or preview to select it
4. **Customize using the settings panel** that appears on the right:
 - Change text content, colors, and styling
 - Upload images and adjust display options
 - Configure section-specific functionality
5. **See changes update** in real-time in the preview
6. **Save and publish** when satisfied

### Creating and Using Templates

1. **Create a new template** from the Templates tab in the left sidebar
2. **Or start with an existing page** and turn it into a template
3. **Add sections** to your template by building them with schemas
4. **Use the Layers tab** to see which sections are in your template
5. **Apply your template** to new pages or modify existing ones

### Making Pages into Templates

1. **Open any page** you want to turn into a template
2. **Use the left sidebar menu** to access template creation options
3. **Save as template** to make it reusable for other pages
4. **Name your template** so you can easily find it later

## Quick Start

### Getting Started (5 Simple Steps)

1. **Choose your theme** - Start with Base, Vox, or Fluid based on your needs
2. **Open the Page Editor** - Click "Edit" on any page
3. **Click on sections** - Select what you want to customize
4. **Use the settings panel** - Simple controls appear automatically on the right
5. **Save and publish** - Your changes go live instantly

### For Theme Engineers

When building themes, focus on creating reusable sections with schemas, using global variables for dynamic content, and designing flexible templates that work across different page types.

## Getting Help

### Common Questions

**"Which theme should I choose?"**

- **Base:** Clean and professional (great for service businesses)
- **Vox:** Bold and creative (perfect for agencies and portfolios)
- **Fluid:** Flexible and versatile (ideal for e-commerce and multi-purpose sites)

**"How do I change text and images?"**

1. Click on the section you want to edit
2. Look at the right panel for settings
3. Edit text directly or upload new images
4. Changes appear instantly in the preview

**"Can I undo changes?"** Yes! Fluid automatically saves drafts, so you can always revert to previous versions.

**"My page looks different on mobile"** Use the device preview buttons to see how your page appears on phones and tablets. Most sections automatically adapt to smaller screens.

**Remember:** The Page Editor is designed to be simple and intuitive. Start with visual editing and explore code mode only when you need more advanced customization options.