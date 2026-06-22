# Source: https://docs.fluid.app/docs/themes/api-reference

# Theme API Reference

This guide covers the API endpoints for managing themes and templates programmatically.

---

## Theme Management API

### Get Company Themes

Returns list of all themes available to the company (root + company-specific).

GET /api/application\_themes

### Create Company Theme

Creates a new theme for the company.

POST /api/application\_themes

**Request Body:**

{
 "name": "Custom Company Theme",
 "description": "Customized theme for our brand",
 "clone\_from\_id": 123,
 "variables": {
 "primary\_color": "#ff6b6b",
 "secondary\_color": "#4ecdc4"
 }
}

| Field | Type | Description |
| --- | --- | --- |
| `name` | string | Theme name (required) |
| `description` | string | Theme description |
| `clone_from_id` | integer | ID of theme to clone from |
| `variables` | object | CSS variables as key-value pairs |

### Update Theme

Updates theme properties.

PUT /api/application\_themes/{theme\_id}

**Request Body:**

{
 "name": "Updated Theme Name",
 "variables": {
 "primary\_color": "#e74c3c",
 "font\_family": "'Open Sans', sans-serif"
 },
 "custom\_stylesheet": ".custom-class { margin: 20px; }"
}

### Publish Theme

Activates the theme for the company, deactivating the current active theme.

POST /api/application\_themes/{theme\_id}/publish

---

## Template Management API

### Get Theme Templates

Returns templates for a specific theme.

GET /api/application\_themes/{theme\_id}/templates

**Query Parameters:**

| Parameter | Type | Description |
| --- | --- | --- |
| `themeable_type` | string | Filter by template type (product, home\_page, etc.) |
| `status` | string | Filter by status (draft, active) |

### Create Template

Creates a new template within a theme.

POST /api/application\_themes/{theme\_id}/templates

**Request Body:**

{
 "name": "Custom Product Template",
 "themeable\_type": "product",
 "content": "<div>{{ product.name }}</div>",
 "format": "liquid",
 "applicable": "specific"
}

| Field | Type | Description |
| --- | --- | --- |
| `name` | string | Template name (required) |
| `themeable_type` | string | Template type (required) |
| `content` | string | Template code (Liquid or JSON) |
| `format` | string | `liquid` or `json` |
| `applicable` | string | `everything` or `specific` |

### Update Template

Updates an existing template.

PUT /api/application\_theme\_templates/{template\_id}

**Request Body:**

{
 "content": "{\`%\` layout 'theme' \`%\`}<h1>{{ product.name }}</h1>",
 "head": "<meta name='description' content='{{ product.description }}' />",
 "stylesheet": ".product-title { color: red; }"
}

### Make Template Default

Sets this template as the default for its `themeable_type` within the theme.

POST /api/application\_theme\_templates/{template\_id}/make\_default

---

## Template Preview API

### Preview Template

Returns rendered HTML of the template with sample or specified content.

GET /api/application\_theme\_templates/{template\_id}/preview

**Query Parameters:**

| Parameter | Type | Description |
| --- | --- | --- |
| `version` | integer | Specific version to preview |
| `record_id` | integer | ID of content item to preview with |

---

## Themeable Types

Available values for `themeable_type`:

| Type | Description |
| --- | --- |
| `product` | Individual product pages |
| `medium` | Media post pages |
| `enrollment_pack` | Course/enrollment pages |
| `shop_page` | Shop/catalog pages |
| `navbar` | Navigation header |
| `library` | Library/playlist pages |
| `page` | Custom pages |
| `components` | Reusable components |
| `sections` | Template sections |
| `locales` | Localization templates |
| `footer` | Page footer |
| `layouts` | Page wrapper layouts |
| `category_page` | Category browsing |
| `collection_page` | Collection browsing |
| `cart_page` | Shopping cart |
| `config` | Theme configuration |
| `home_page` | Homepage layout |
| `mysite` | MySite pages |
| `join_page` | Registration pages |

---

## Error Responses

### Theme Validation Errors (422)

{
 "status": "unprocessable\_entity",
 "errors": {
 "variables": \["Variables must be valid JSON"\],
 "name": \["Name cannot be blank"\]
 }
}

### Template Validation Errors (422)

{
 "status": "unprocessable\_entity",
 "errors": {
 "content": \["Content cannot be blank"\],
 "themeable\_type": \["Themeable type is not included in the list"\]
 }
}

### Theme Not Found (404)

{
 "status": "not\_found",
 "errors": {
 "base": \["Theme not found"\]
 }
}

### Active Theme Deletion Error (422)

{
 "status": "unprocessable\_entity",
 "errors": {
 "base": \["Cannot delete a published theme"\]
 }
}

---

## Ruby Model Examples

### Find Company's Active Theme

company \= Company.find(123)
active\_theme \= company.current\_theme

### Clone a Root Theme

root\_theme \= ApplicationTheme.root.find\_by(name: 'fluid')
company\_theme \= root\_theme.deep\_clone(company.id)
company\_theme.name \= "Custom Fluid Theme"
company\_theme.save!

### Customize Theme Variables

company\_theme.variables \= {
 primary\_color: '#ff6b6b',
 secondary\_color: '#4ecdc4',
 font\_family: 'Inter, sans-serif'
}.to\_json
company\_theme.save!

### Create a Custom Template

custom\_template \= company\_theme.application\_theme\_templates.create!(
 name: 'Custom Product Layout',
 themeable\_type: :product,
 content: '{\`%\` layout "theme" \`%\`}<div class="custom-product">{{ product.name }}</div>',
 format: :liquid
)

\# Make it the default
custom\_template.make\_default

### Assign Template to Specific Product

product \= Product.find(456)
product.application\_theme\_template \= custom\_template
product.save!

### Publish the Theme

company\_theme.publish

### Check Upgrade Status

\# Check if theme can be auto-upgraded
company\_theme.auto\_upgradeable?
company\_theme.outdated?

### Access Version History

version\_history \= active\_theme.versions
\# => \[{ version: 1, created\_at: "...", author\_name: "John Doe", published: true }\]

---

## Caching

The theme system uses multi-level caching:

| Cache Type | Duration | Invalidation |
| --- | --- | --- |
| Stylesheet Cache | 1 hour | When variables or CSS changes |
| Global Embeds | 1 hour | When embed settings change |
| Rendered Templates | 24 hours | When template or variables change |

### Cache Control Parameters

Use query parameters to control caching:

- `?clear_cache=true` - Clear cache for current template/variables combination
- `?clear_all_cache=true` - Clear all cached versions of the template