# Source: https://docs.fluid.app/docs/guides/themes

# Themes

## Introduction

Themes allow companies to customize the visual appearance and layout of their key customer-facing pages. Each company can have multiple themes installed, though only one theme can be active (published) at a time.

Themes heavily rely on the [Liquid gem](https://github.com/Shopify/liquid) built by Shopify.

[Liquid Documentation](https://shopify.dev/docs/api/liquid)

[Liquid Wiki](https://github.com/Shopify/liquid/wiki)

[Liquid Website](https://shopify.github.io/liquid/)

It is crucial to understand the basics of Liquid to create a theme.

## Customizable Pages

With themes, you can customize the appearance of the following pages:

- Home/Landing page
- Shop page
- Product page
- Medium page
- Playlist page
- Cart page
- Collection page
- Enrollment pack page

## Accessing Themes

Themes can be accessed from `Sidebar > Website > Themes` or directly navigating to `/application_themes` in your browser

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

To use a different layout from the default theme.liquid, add the `{% layout "custom_layout" %}` tag at on your template file:

`{%​ layout "custom_layout" ​%}`

`<h1>Welcome to the Dashboard</h1>`

#### Disabling layout

To render a template without any layout, use `{% layout none %}` on your template file:

`{%​ layout none ​%}`

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

`{% section 'navbar' %}`

`{% section 'footer' %}`

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

`{% render 'header' %}`

You can pass variables to the component as follows:

`{% render 'header', variable: 'value' %}`

## Variables

Variables are used to make your theme dynamic. For each template, specific variables are accessible. For example, the `product` template has access to the `product` variable that contains information about the product.

### Global Variables

Global variables are available to all templates.

There are a system-defined variables that are available to all templates:

#### request variable:

contains information about the request

{
 "request": {
 "path": "Request path",
 "host": "Request host",
 "page\_type": "Page type, eg: shop, enrollment\_pack, library, media"
 }
}

#### company variable:

contains information about the company

{
 "company": {
 "id": "Company id",
 "name": "Company name",
 "logo\_url": "Company logo url",
 "shop\_page\_url": "Shop page url",
 "checkout\_url": "Checkout url",
 "subdomain": "Company subdomain"
 }
}

#### localization variable

contains information about the localization

{
 "localization": {
 "available\_countries <Array>": {
 "currency": {
 "iso\_code": "Currency iso code",
 "name": "Currency name",
 "symbol": "Currency symbol"
 },
 "iso\_code": "Country iso code",
 "name": "Country name"
 },
 "available\_languages <Array>": {
 "iso\_code": "Language iso code",
 "name": "Language name"
 },
 "country": "Current Country object",
 "language": "Current Language object"
 }
}

#### affiliate variable

contains information about the affiliate

{
 "affiliate": {
 "web\_rep\_store\_enabled": "Should show login affiliate login bannar",
 "logged\_in\_rep\_for\_store": "Is affiliate logged in?",
 "name": "Affiliate name",
 "avatar": "Affiliate avatar url",
 "initials": "Affiliate initials",
 "my\_site\_url": "Affiliate my site url",
 "sign\_in\_url": "Sign in url",
 "sign\_out\_url": "Sign out url"
 }
}

You can add additional global variables in the `variables.json` file.

theme/
├── variables.json

### Template Variables

Template variables are specific to each template. You can see the list of available variables for each template in the theme editor under the 'Variables' dropdown.

## How to customize a theme

The theme editor is a tool that allows you to customize the appearance of your theme.

- Navigate to `Sidebar > Website > Themes`
 
- Click 'Customize' on the theme you want to customize.
 
- Navigate to the template you want to customize.
 
- Make your changes and click 'Save' to save your changes.
 
- Click 'Preview' to preview your changes.
 
- Click 'Publish' to publish your changes.
 

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
 

## Creating Custom Web Pages

### Overview

Custom web pages can be created programmatically through the admin dashboard. These pages support custom HTML and CSS, with optional theme style integration.

### Access Path

1. Navigate to `Sidebar > Sharing > Pages`
2. Select `Create Page > With Code`

### Page Structure

Each custom page consists of two main components:

- HTML section for markup
- CSS section for page-specific styles

### Theme Integration

- Toggle `Use Theme Styles` to inherit global styles from the currently active theme
- Theme styles are injected before page-specific CSS, allowing for style overrides

### URL Structure

Custom pages follow this URL pattern:

/:credit/page/:slug

Example: `example.com/mycredit/page/custom-landing`

### Page Availability

Pages can be region-restricted through country selection:

- Select specific countries where the page should be accessible
- Page visibility is automatically managed based on user geolocation
- Unselected countries will not have access to the page

### Development Workflow

1. Create page structure in HTML section
2. Add page-specific styles in CSS section
3. Configure country availability
4. Save changes
5. Use 'Preview Link' to validate appearance and functionality
6. Publish when ready by setting the Status to 'Active'

### Preview and Testing

- Use the 'Preview Link' functionality after saving to validate changes
- Preview mode shows the page exactly as it will appear to end users

## FAQs

### How do I edit the Shop page for my theme?

- Navigate to `Sidebar > Website > Themes`
 
- Click 'Customize' on the theme you want to customize.
 
- Navigate to the Shop page in the theme editor sidebar.
 
- Make your changes and click 'Save' to save your changes.
 
- Click 'Preview' to preview your changes.
 
- Click 'Publish' to publish your changes.
 

### How are images stored?

Theme specific images are stored in the `assets` folder.

- Navigate to Assets folder in the theme editor sidebar.
 
- Click New Asset button
 
- Upload your image
 
- Click Save Changes
 

### How are images served up?

- You can reference the image in your theme files using the `asset_url` filter.

<img src\="{{ 'banner.jpg' | asset\_url }}" alt\="Banner"\>

### What is the easiest way to get started with building a new Theme?

You can directly start customizing the Fluid base theme.

- Navigate to `Sidebar > Website > Themes`
- Click on 'Customize' on the Fluid base theme

Or you can clone the Fluid base theme and start customizing from there so that base theme remains intact.

- Navigate to `Sidebar > Website > Themes`
- Download the Fluid base theme. A zip file will be downloaded.
- Click on 'Import' button and select the downloaded zip file.
- Customize the imported theme.

Note: Direct cloning of the Fluid base theme without having to download it will be available soon.

### Do you have a base theme I can work off of?

- Yes, Fluid app comes with a base theme that you can work off of directly or clone to create a new theme.

### What considerations should I have when naming buttons, classes, etc.?

### If I change themes will my templates be lost?

- No, your templates will not be lost.

### What if I make an error and I want to roll back?

- Click on the 'Versions' dropdown on the theme editor.
 
- Select the version you want to revert to.
 
- Click on 'Preview' to preview the version to make sure it is what you want.
 
- Click on 'Publish' to publish the version.
 

### How do I preview before publishing?

- Click on 'Preview' to preview the theme.

### How is the routing handled in the theme?

- Internally, Fluid has its own routing which determines which template is displayed based on the URL.

Here's an overview of which template is rendered based on the URL:

/ → home\_page
/cart → cart\_page
/:credit/shop → shop\_page
/:credit/shop/:product\_slug → product
/:credit/collections → collection\_page
/:credit/enrollments/:token → enrollments\_pack
/:credit/library/:slug → library

### What is the hierarchy of Themes > Templates > Resources? Who gets priority?

- Styles are applied in the following order: Theme > Template > Resource.

### How does an app from the integration marketplace add JavaScript to the site?

- They are added to the layout or the head section of the individual template/page