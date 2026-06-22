# Source: https://docs.fluid.app/docs/themes/theme-marketplace

# Theme Marketplace

## Overview

The Theme Marketplace enables theme developers to build, submit, and distribute themes globally across the platform. This system provides a structured workflow for theme development, submission, review, and approval, ensuring quality and consistency across all available themes.

**Key Features:**

- Theme developers can build themes using the theme builder in admin
- Submit themes as global themes for platform-wide distribution
- Super admin review and approval process
- Version management for theme updates
- Public marketplace for theme discovery

**Workflow Summary:**

1. **Development**: Theme developers build themes in admin with theme builder access
2. **Submission**: Developers export and submit themes as global themes via `/themes` routes
3. **Review**: Super admins review submitted themes in superadmin/root admin settings
4. **Approval**: Super admins approve or reject theme submissions
5. **Publishing**: Approved themes become available in the marketplace

## Theme Developer Workflow

### Step 1: Building Your Theme

Theme developers with access to the theme builder can create themes in the admin interface:

1. **Access Theme Builder**: Navigate to `/themes` in the admin panel
2. **Create New Theme**: Use the theme builder to design and develop your theme
3. **Configure Templates**: Set up templates for different page types (product, home\_page, etc.)
4. **Add Assets**: Upload theme assets (images, fonts, stylesheets, JavaScript files)
5. **Test Theme**: Preview and test your theme before submission

**Theme Builder Access:**

- Theme developers need appropriate permissions to access the theme builder
- The builder provides visual editing tools for templates, sections, and components
- Supports both Liquid and JSON template formats

### Step 2: Exporting Your Theme

Once your theme is complete and tested:

1. **Export Theme**: Use the export functionality to create a ZIP file of your theme
2. **Verify Contents**: Ensure all templates, assets, and configuration files are included
3. **Test Export**: Verify the exported theme can be imported successfully

**Export Structure:** The exported ZIP file should contain:

- All template files (Liquid/JSON)
- Theme assets (images, fonts, CSS, JavaScript)
- Configuration files
- Schema definitions for sections and components

### Step 3: Submitting as Global Theme

Submit your theme for global distribution:

1. **Navigate to Themes**: Go to `/themes` in the admin panel
2. **Submit Global Theme**: Use the submission interface to upload your theme ZIP file
3. **Provide Metadata**:
 - Theme name
 - Description
 - Demo URL (optional): You can copy the preview URL from the same theme you're trying to globalize. The preview URL option is available in the theme library, and this URL serves as the demo URL for the marketplace.
 - Changelog (for updates)
4. **Submit for Review**: The theme is submitted with `draft` status for super admin review

**Submission Process:**

- Theme is created as a `root_theme` (ApplicationTheme with `company_id: nil`)
- A new `ApplicationThemeVersion` is created with status `draft`
- Theme becomes visible to super admins for review
- Theme developer receives confirmation of submission

**API Endpoint:**

POST /api/root\_themes

**Request Body:**

{
 "name": "My Custom Theme",
 "description": "A beautiful theme for e-commerce",
 "demo\_url": "https://demo.example.com",
 "zip\_file\_url": "https://cdn.example.com/themes/my-theme.zip",
 "changelog": "Initial release with product and home page templates"
}

### Step 4: Updating Existing Themes

For theme updates:

1. **Select Theme**: Choose the existing global theme you want to update
2. **Upload New Version**: Submit a new ZIP file with updated theme files
3. **Update Metadata**: Modify description, demo URL, or changelog as needed
4. **Submit Update**: New version is created with `draft` status

**Update Process:**

- New `ApplicationThemeVersion` is created with incremented version number
- Previous published version remains active until new version is approved
- Version history is maintained for tracking changes

**API Endpoint:**

PUT /api/root\_themes/:id

**Request Body:**

{
 "zip\_file\_url": "https://cdn.example.com/themes/my-theme-v2.zip",
 "changelog": "Added new product gallery section and improved mobile responsiveness",
 "description": "Updated theme description",
 "demo\_url": "https://demo.example.com/v2"
}

## Super Admin Review Process

### Accessing Theme Submissions

Super admins can review submitted themes in the superadmin/root admin settings:

1. **Navigate to Admin Settings**: Access superadmin/root admin panel
2. **View Theme Submissions**: Go to the themes review section
3. **Filter by Status**: View themes by status (draft, publishing, published, rejected)

**Review Interface:**

- Lists all submitted themes awaiting review
- Shows theme metadata (name, description, creator, submission date)
- Displays theme version information
- Provides preview and download options

### Reviewing Theme Submissions

When reviewing a theme submission:

1. **View Theme Details**: Review theme name, description, and metadata
2. **Preview Theme**: Test the theme in a preview environment
3. **Check Theme Structure**: Verify templates, assets, and configuration
4. **Review Code Quality**: Ensure templates follow best practices
5. **Test Functionality**: Verify all features work correctly
6. **Check Compatibility**: Ensure theme is compatible with platform requirements

**Review Checklist:**

- ✅ Theme structure is correct
- ✅ All required templates are present
- ✅ Assets are properly referenced
- ✅ No security vulnerabilities
- ✅ Follows theme development guidelines
- ✅ Responsive design implemented
- ✅ Performance optimizations applied
- ✅ Accessibility standards met

### Approval or Rejection

After reviewing the theme:

**Approve Theme:**

1. Click "Approve" on the theme submission
2. Theme version status changes to `publishing`
3. Theme import job is queued
4. Once imported, status becomes `published`
5. Theme becomes available in the marketplace

**Reject Theme:**

1. Click "Reject" on the theme submission
2. Theme version status changes to `rejected`
3. Theme developer is notified (if notification system is configured)
4. Theme remains in rejected state for reference

**API Endpoint:**

POST /api/root\_themes/:id/status

**Request Body:**

{
 "theme\_version\_id": 123,
 "approved": true
}

**Response (Approved):**

{
 "status": "ok",
 "resource": {
 "message": "Theme version is being published"
 }
}

**Response (Rejected):**

{
 "status": "ok",
 "resource": {
 "message": "Theme version has been rejected"
 }
}

## Theme Submission Requirements

### Required Components

A complete theme submission must include:

1. **Layout Templates**: Main layout file (`theme.liquid`)
2. **Page Templates**: Templates for different page types
 - Product pages
 - Home page
 - Shop/category pages
 - Cart page
 - Collection pages
3. **Navigation Components**: Navbar and footer templates
4. **Section Templates**: Reusable section components
5. **Stylesheets**: Global and template-specific CSS
6. **Assets**: Images, fonts, JavaScript files
7. **Configuration**: Theme variables and settings

### Theme Structure

The theme ZIP file should follow this structure:

theme\-name/
├── layout/
│ └── theme.liquid
├── product/
│ └── default/
│ ├── index.liquid (or index.json)
│ └── styles.css
├── home\_page/
│ └── default/
│ ├── index.liquid (or index.json)
│ └── styles.css
├── navbar/
│ └── default/
│ ├── index.liquid
│ └── styles.css
├── footer/
│ └── default/
│ ├── index.liquid
│ └── styles.css
├── sections/
│ └── \[section\-name\]/
│ ├── index.liquid
│ └── styles.css
├── assets/
│ ├── images/
│ ├── fonts/
│ ├── scripts/
│ └── styles/
└── config/
 └── variables.json

### Quality Standards

Themes must meet these quality standards:

**Code Quality:**

- Clean, well-commented code
- Follows Liquid/JSON best practices
- Proper error handling
- No deprecated features

**Performance:**

- Optimized asset loading
- Efficient template rendering
- Minimal external dependencies
- Fast page load times

**Design:**

- Responsive design (mobile, tablet, desktop)
- Accessible UI components
- Consistent styling
- Modern, professional appearance

**Compatibility:**

- Works with all supported browsers
- Compatible with platform features
- No breaking changes to core functionality

## Approval and Publishing

### Publishing Workflow

When a theme is approved:

1. **Status Change**: Theme version status changes from `draft` to `publishing`
2. **Import Job**: `Themes::ImportJob` is queued to process the theme
3. **Theme Import**: Theme files are extracted and processed
4. **Template Creation**: Templates are created in the database
5. **Asset Processing**: Assets are uploaded and processed
6. **Status Update**: Status changes to `published` upon successful import
7. **Marketplace Availability**: Theme becomes available in the marketplace

### Version Management

Themes support version management:

**Version Numbering:**

- Initial version: `1.0`
- Major updates: `2.0`, `3.0`, etc.
- Version increments automatically on updates

**Version States:**

- `draft`: Submitted and awaiting review
- `publishing`: Approved and being imported
- `published`: Successfully imported and available
- `rejected`: Rejected by super admin

**Version History:**

- All versions are maintained in the database
- Previous versions remain accessible
- Version changelog tracks changes

### Marketplace Availability

Once published, themes are:

- **Publicly Visible**: Available in the theme marketplace
- **Searchable**: Can be found by name or description
- **Importable**: Companies can import themes to their accounts
- **Previewable**: Demo URLs allow preview before import

## Marketplace Features

### Theme Discovery

The marketplace provides several ways to discover themes:

1. **Browse All Themes**: View all available published themes
2. **Search**: Search themes by name or description
3. **Filter**: Filter by category, features, or tags
4. **Trending**: View popular or trending themes

### Theme Preview

Before importing a theme:

1. **View Details**: See theme description, screenshots, and features
2. **Preview Demo**: Visit demo URL to see theme in action
3. **Check Requirements**: Verify theme compatibility
4. **Read Changelog**: Review version history and updates

**Note for Theme Developers**: When submitting a theme for globalization, you can find and copy the preview URL directly from the theme in the theme library. This preview URL is what you'll use as the demo URL in your submission.

### Theme Import

Companies can import themes from the marketplace:

1. **Select Theme**: Choose a theme from the marketplace
2. **Import to Company**: Theme is cloned to company account
3. **Customize**: Company can customize the imported theme
4. **Activate**: Set as active theme for the company

**Import Process:**

- Theme is cloned with `company_id` set
- All templates and assets are copied
- Company can customize without affecting original
- Original theme remains available for others

## Best Practices

### For Theme Developers

**Before Submission:**

- ✅ Thoroughly test your theme
- ✅ Ensure all templates are complete
- ✅ Optimize assets for performance
- ✅ Write clear documentation
- ✅ Include comprehensive changelog

**Theme Design:**

- Use consistent naming conventions
- Follow platform design patterns
- Implement responsive design
- Ensure accessibility compliance
- Optimize for performance

**Code Quality:**

- Write clean, maintainable code
- Add comments for complex logic
- Follow Liquid/JSON best practices
- Test edge cases
- Handle errors gracefully

### For Super Admins

**Review Process:**

- Test theme thoroughly before approval
- Check code quality and security
- Verify theme meets quality standards
- Test on multiple devices/browsers
- Review changelog for changes

**Approval Criteria:**

- Theme is complete and functional
- Code quality meets standards
- No security vulnerabilities
- Performance is acceptable
- Documentation is adequate

## Troubleshooting

### Common Issues

**Theme Submission Fails:**

- **Issue**: ZIP file upload fails
- **Solution**: Verify file size and format, ensure valid ZIP structure

**Theme Import Errors:**

- **Issue**: Theme import job fails
- **Solution**: Check theme structure, verify all required files are present

**Template Errors:**

- **Issue**: Templates fail to render
- **Solution**: Verify Liquid/JSON syntax, check variable references

**Asset Loading Issues:**

- **Issue**: Assets don't load correctly
- **Solution**: Verify asset paths, ensure files are included in ZIP

**Version Conflicts:**

- **Issue**: Version numbering conflicts
- **Solution**: Ensure version numbers increment correctly

### Getting Help

**For Theme Developers:**

- Review theme development documentation
- Check theme structure examples
- Test theme locally before submission
- Contact support for technical issues

**For Super Admins:**

- Review theme submission guidelines
- Test themes in staging environment
- Consult with development team for complex issues

## Related Documentation

- [Theme Development Guide](https://docs.fluid.app/docs/themes/developer-guide)
- [Theme Architecture](https://docs.fluid.app/docs/themes/themes#system-architecture)
- [Template Development Patterns](https://docs.fluid.app/docs/themes/themes#template-development-patterns)
- [Theme Variables](https://docs.fluid.app/docs/themes/theme-variables)
- [Page Editor Guide](https://docs.fluid.app/docs/themes/paged-editor)