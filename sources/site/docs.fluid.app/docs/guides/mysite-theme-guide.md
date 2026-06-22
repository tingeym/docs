# Source: https://docs.fluid.app/docs/guides/mysite-theme-guide

# MySite Theme Development Guide

## Overview

MySite themes customize affiliate-facing pages. After deployment, themes become available in the admin interface for further customization and publishing.

## Prerequisites

- Fluid development environment access
- Liquid templating knowledge
- Admin privileges for rake task execution

## Development Workflow

### 1\. Create Theme Structure

mkdir \-p app/themes/templates/mysite/YOUR\_THEME\_NAME/
touch app/themes/templates/mysite/YOUR\_THEME\_NAME/index.liquid
touch app/themes/templates/mysite/YOUR\_THEME\_NAME/styles.css
touch app/themes/templates/mysite/YOUR\_THEME\_NAME/script.js \# optional

### 2\. Template Requirements

**Critical**: Root container must include `my-site` class for styles to load.

<link rel\="stylesheet" href\="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" integrity\="XXXXX-XXXXX" crossorigin\="anonymous" referrerpolicy\="no-referrer" />

<div class\="themes-wrapper\_default my-site"\>
 <!-- Theme content -->
</div\>

### 3\. Required CSS Classes

| Element | Required Classes | Purpose |
| --- | --- | --- |
| Root container | `themes-wrapper_default my-site` | **CRITICAL** - Enables style loading |
| Navigation | `themes-nav` or `themes-nav_default` | Navigation styling |
| Body content | `themes-body` or `themes-body_default` | Main content area |
| Footer | `themes-footer` or `themes-footer_default` | Footer styling |

### 4\. Standard Components

- **Avatar**: `ui image circular tiny` with `id="image-swap"`
- **Social Icons**: Facebook, Twitter, Instagram, YouTube, Pinterest, LinkedIn, TikTok, WeChat, WhatsApp
- **Links**: Iterate through `fluid_affiliate.links`
- **Bio**: Include expand/collapse functionality

### 5\. Deploy Theme

\# Deploy to all companies
rake "mysite\_themes:load\_theme\[YOUR\_THEME\_NAME\]"

\# Deploy to specific company
rake "mysite\_themes:load\_default\[COMPANY\_ID,YOUR\_THEME\_NAME\]"

\# Deploy all themes
rake mysite\_themes:load\_all

### 6\. Post-Deployment Management

After deployment, themes are available in the admin interface:

**Navigation**: `Sidebar > Website > MySite Theme`

**Available Actions**:

- **Customize**: Edit Liquid template and CSS directly in the admin interface
- **Preview**: Test changes before publishing
- **Publish**: Deploy customized theme to production
- **Revert**: Restore to original theme version

**Admin Interface Features**:

- Live preview of template changes
- Syntax highlighting for Liquid and CSS
- Version control and rollback capabilities
- Company-specific theme customization

## Liquid Variables Reference

| Variable | Properties | Description |
| --- | --- | --- |
| `fluid_affiliate` | `name`, `bio`, `image`, `links` | Affiliate profile data |
| `fluid_affiliate` | Social media URLs | Facebook, Twitter, Instagram, etc. |
| `company` | Various properties | Company information |
| `request` | Context data | Request-specific data |

## Common Issues & Solutions

| Issue | Solution |
| --- | --- |
| Styles not loading | Ensure root container has `my-site` class |
| Theme not visible | Run deployment rake task |
| JavaScript errors | Include script in `script.js` file |
| Missing content | Verify Liquid variable usage |

## Project Structure

app/themes/templates/mysite/
├── YOUR\_THEME\_NAME/
│ ├── index.liquid # Main template
│ ├── styles.css # Theme styles
│ └── script.js # Optional JavaScript
├── canvas/ # Existing theme
└── default/ # Default theme

## Best Practices

- **Test locally** before deployment
- **Use semantic CSS classes** for maintainability
- **Follow Liquid conventions** for variable access
- **Optimize images** for performance
- **Validate HTML** structure