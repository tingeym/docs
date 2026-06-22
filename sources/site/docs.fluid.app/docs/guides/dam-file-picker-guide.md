# Source: https://docs.fluid.app/docs/guides/dam-file-picker-guide

# DAM File Picker Component Guide

This guide covers the FilePicker component, a sophisticated file management system that integrates with the DAM (Digital Asset Management) system. This component replaces Filestack implementations and provides multiple upload methods with advanced features.

## Overview

The FilePicker is a modal-based component that provides a unified interface for file selection, upload, and management. It supports multiple upload methods, integrates with the DAM system, and includes advanced features like shareable media creation.

## Core Features

The FilePicker component provides four main tabs for different file management needs:

1. **Upload Tab** - Computer upload and URL download
2. **Library Tab** - Browse existing DAM assets
3. **Media Tab** - Browse existing shareable media (optional)
4. **Unsplash Tab** - Search and select from Unsplash images

## Upload Methods & Tabs

### Upload Tab

The Upload tab provides two primary methods for adding files:

#### Computer Upload

- **Drag & Drop Functionality**: Users can drag files directly onto the upload area
- **File Browser**: Click to browse and select files from the device
- **File Validation**: Automatic validation of file types, sizes, and formats
- **Progress Tracking**: Real-time upload progress indication
- **Automatic File Routing**:
 - Text files use traditional upload
 - All other files use ImageKit direct upload for better performance

#### URL Upload

- **Web URL Download**: Download files directly from web URLs
- **Automatic Processing**: Files are processed and uploaded to the DAM system
- **Validation**: URL validation and file type detection

### Library Tab (DAM)

The Library tab provides access to existing DAM assets:

- **Hierarchical Navigation**: Browse assets using ltree navigation with dot notation
- **Search and Filter**: Advanced search and filtering capabilities
- **Variant Selection**: Select from multiple formats/sizes for assets with variants
- **No Upload Functionality**: Selection only - no new uploads
- **Asset Organization**: Navigate through organized asset categories

#### DAM Navigation Examples

company\_id.images.asset\_code // Specific asset
company\_id.\* // All items under company
company\_id.images.\* // All images under company

### Media Tab (Optional)

The Media tab allows browsing existing shareable media:

- **Media Browser**: Browse existing shareable media
- **Search by Title**: Search functionality with pagination
- **Visual Previews**: Image previews for media items
- **Domain Integration**: Used for attaching media to domain areas like events
- **Reuse Content**: Select from previously created media

### Unsplash Tab

The Unsplash tab provides integration with Unsplash's image library:

- **Image Search**: Search and select from Unsplash images
- **API Integration**: Direct integration with Unsplash API
- **High-Quality Images**: Access to professional stock photography
- **Direct Download**: Images are downloaded and processed automatically

## Component Props & Configuration

### FilePickerProps Interface

interface FilePickerProps {
 isOpen: boolean
 onClose: () \=\> void
 onFilesSelected: (files: FilePickerResult\[\]) \=\> void
 config?: FilePickerConfig
 showMediaTab?: boolean
 showUnsplashTab?: boolean
 allowShareableMediaCreation?: boolean
}

### FilePickerConfig Interface

interface FilePickerConfig {
 accept?: string\[\] // MIME types or file extensions
 maxFiles?: number // Maximum number of files
 maxSize?: number // Maximum file size in bytes
 multiple?: boolean // Allow multiple file selection
}

### Configuration Examples

#### Single Image Only

const config: FilePickerConfig \= {
 accept: \["image/\*"\],
 maxFiles: 1,
 maxSize: 5 \* 1024 \* 1024 // 5MB
}

#### Multiple Documents

const config: FilePickerConfig \= {
 accept: \["application/pdf", ".doc", ".docx"\],
 maxFiles: 10,
 maxSize: 10 \* 1024 \* 1024 // 10MB
}

#### No Restrictions

const config: FilePickerConfig \= {
 maxFiles: undefined,
 accept: undefined,
 maxSize: undefined
}

## Return Value Structure

### FilePickerResult Interface

interface FilePickerResult {
 id: string
 name: string
 url: string
 type: 'asset' | 'media' | 'unsplash'
 metadata?: {
 size?: number
 mimeType?: string
 dimensions?: { width: number; height: number }
 alt?: string
 }
 shareableMediaOptions?: ShareableMediaOptions
}

### ShareableMediaOptions Interface

interface ShareableMediaOptions {
 title: string
 description?: string
 productIds?: string\[\]
 themeTemplateId?: string
 countryTargeting?: string\[\]
 ctaType?: 'button' | 'cart'
 ctaText?: string
}

## Usage Examples

### Basic Usage

import { FilePicker } from '@/components/FilePicker'

function MyComponent() {
 const \[isPickerOpen, setIsPickerOpen\] \= useState(false)
 const \[selectedFiles, setSelectedFiles\] \= useState<FilePickerResult\[\]\>(\[\])

 const handleFilesSelected \= (files: FilePickerResult\[\]) \=\> {
 setSelectedFiles(files)
 setIsPickerOpen(false)
 }

 return (
 <\>
 <button onClick\={() \=\> setIsPickerOpen(true)}\>
 Select Files
 </button\>
 
 <FilePicker
 isOpen\={isPickerOpen}
 onClose\={() \=\> setIsPickerOpen(false)}
 onFilesSelected\={handleFilesSelected}
 config\={{
 accept: \["image/\*"\],
 maxFiles: 5,
 maxSize: 10 \* 1024 \* 1024
 }}
 /\>
 </\>
 )
}

### With React Hook Form Integration

import { useForm, Controller } from 'react-hook-form'
import { FilePicker } from '@/components/FilePicker'

function ProductForm() {
 const { control, handleSubmit } \= useForm()

 return (
 <form onSubmit\={handleSubmit(onSubmit)}\>
 <Controller
 name\="productImages"
 control\={control}
 render\={({ field }) \=\> (
 <FilePicker
 isOpen\={field.value?.isOpen || false}
 onClose\={() \=\> field.onChange({ ...field.value, isOpen: false })}
 onFilesSelected\={(files) \=\> {
 field.onChange({ 
 ...field.value, 
 files,
 isOpen: false 
 })
 }}
 config\={{
 accept: \["image/\*"\],
 maxFiles: 10
 }}
 /\>
 )}
 /\>
 </form\>
 )
}

### With Media Tab

<FilePicker
 isOpen\={isPickerOpen}
 onClose\={() \=\> setIsPickerOpen(false)}
 onFilesSelected\={handleFilesSelected}
 showMediaTab\={true}
 config\={{
 accept: \["image/\*", "video/\*"\],
 maxFiles: 1
 }}
/\>

### With Shareable Media Creation

<FilePicker
 isOpen\={isPickerOpen}
 onClose\={() \=\> setIsPickerOpen(false)}
 onFilesSelected\={handleFilesSelected}
 allowShareableMediaCreation\={true}
 showMediaTab\={true}
 config\={{
 accept: \["image/\*"\],
 maxFiles: 1
 }}
/\>

## Advanced Features

### DAM Integration

The FilePicker integrates deeply with the DAM system:

#### Asset Storage Structure

{company\_id}.{category}.{asset\_code}

#### Ltree Navigation

- **Dot Notation**: `company_id.images.asset_code`
- **Wildcard Support**: `company_id.*` (all items under company)
- **Hierarchical Browsing**: Navigate through organized asset categories

#### Variant Selection

- Assets with multiple formats/sizes show variant options
- Users can select the most appropriate variant for their use case
- Automatic optimization based on context

### Shareable Media Creation

The component supports a two-step process for creating shareable media:

1. **File Selection**: Choose files from any tab
2. **Media Details**: Configure media options including:
 - Title and description
 - Product associations
 - Theme template selection
 - Country targeting
 - CTA configuration (button/cart types)

### File Routing

The component intelligently routes files based on type:

- **Text Files**: Use traditional upload for better compatibility
- **All Other Files**: Use ImageKit direct upload for better performance and features

## Environment Variables

The following environment variables are required:

\# ImageKit Configuration
NEXT\_PUBLIC\_IMAGEKIT\_URL\_ENDPOINT\=your\_imagekit\_url\_endpoint
NEXT\_PUBLIC\_IMAGEKIT\_PUBLIC\_KEY\=your\_imagekit\_public\_key
NEXT\_PUBLIC\_IMAGEKIT\_PRIVATE\_KEY\=your\_imagekit\_private\_key

\# Unsplash Integration
NEXT\_PUBLIC\_UNSPLASH\_ACCESS\_KEY\=your\_unsplash\_access\_key

## API Endpoints

The FilePicker component uses these API endpoints:

### DAM Endpoints

- `POST /api/dam/assets` - Upload new assets
- `POST /api/dam/query` - Browse DAM library with ltree queries

### External APIs

- **Unsplash API** - Search and download images
- **ImageKit API** - Direct file uploads and transformations

## Testing & Development

### POC Page

A proof-of-concept page is available at `/poc/file-picker` for testing different configurations and features.

### Component Architecture

The component uses separate hooks for each upload method:

- `useUploadTab` - Handles computer and URL uploads
- `useLibraryTab` - Manages DAM asset browsing
- `useMediaTab` - Handles shareable media selection
- `useUnsplashTab` - Manages Unsplash integration

### Error Handling

- Comprehensive validation for file types and sizes
- Network error handling with retry mechanisms
- User-friendly error messages
- Graceful fallbacks for failed operations

## Component Architecture

### File Structure

components/
├── FilePicker/
│ ├── index.tsx # Main component
│ ├── FilePickerModal.tsx # Modal wrapper
│ ├── tabs/
│ │ ├── UploadTab.tsx # Upload functionality
│ │ ├── LibraryTab.tsx # DAM browsing
│ │ ├── MediaTab.tsx # Shareable media
│ │ └── UnsplashTab.tsx # Unsplash integration
│ ├── hooks/
│ │ ├── useUploadTab.ts # Upload logic
│ │ ├── useLibraryTab.ts # DAM logic
│ │ ├── useMediaTab.ts # Media logic
│ │ └── useUnsplashTab.ts # Unsplash logic
│ └── types.ts # TypeScript interfaces

## Dependencies

### Key Dependencies

- `@tanstack/react-query` - Data fetching and caching
- `react-hook-form` - Form handling
- `@hookform/resolvers/zod` - Form validation
- `zod` - Schema validation
- `react-dropzone` - Drag-and-drop functionality
- `@fluid-app/fluid-ui` - UI components
- `@fortawesome/react-fontawesome` - Icons

## Best Practices

### Implementation

- Always handle the `onFilesSelected` callback
- Use appropriate configuration for file type restrictions
- Implement proper error handling
- Consider file size limits for better UX

### User Experience

- Use the media tab for reusing existing content
- Leverage DAM library for organized asset management
- Provide clear feedback during uploads
- Use appropriate file size limits

### Performance

- Use ImageKit direct upload for non-text files
- Implement proper caching for DAM queries
- Optimize image uploads with appropriate compression
- Use pagination for large media libraries

## Common Use Cases

### Profile Pictures

<FilePicker
 config\={{
 accept: \["image/\*"\],
 maxFiles: 1,
 maxSize: 2 \* 1024 \* 1024 // 2MB
 }}
/\>

### Document Attachments

<FilePicker
 config\={{
 accept: \["application/pdf", ".doc", ".docx"\],
 maxFiles: 10,
 maxSize: 10 \* 1024 \* 1024 // 10MB
 }}
/\>

### Media Galleries

<FilePicker
 showMediaTab\={true}
 config\={{
 accept: \["image/\*", "video/\*"\],
 maxFiles: 20
 }}
/\>

### Event Media

<FilePicker
 showMediaTab\={true}
 config\={{
 accept: \["image/\*"\],
 maxFiles: 1
 }}
/\>

### Content Creation

<FilePicker
 allowShareableMediaCreation\={true}
 showMediaTab\={true}
 showUnsplashTab\={true}
 config\={{
 accept: \["image/\*"\],
 maxFiles: 1
 }}
/\>

## Troubleshooting

### Common Issues

#### File Upload Failures

- **Check file size limits**: Ensure files don't exceed configured limits
- **Verify file types**: Check that file types are in the accepted list
- **Network connectivity**: Ensure stable internet connection
- **ImageKit configuration**: Verify ImageKit credentials and endpoints

#### DAM Navigation Issues

- **Ltree syntax**: Ensure proper dot notation for navigation
- **Permissions**: Check user permissions for DAM access
- **Asset existence**: Verify assets exist in the specified path

#### Unsplash API Errors

- **API key**: Verify `NEXT_PUBLIC_UNSPLASH_ACCESS_KEY` is set
- **Rate limits**: Check Unsplash API rate limits
- **Network issues**: Ensure external API access is available

#### ImageKit Upload Problems

- **Credentials**: Verify ImageKit public and private keys
- **Endpoint**: Check ImageKit URL endpoint configuration
- **File routing**: Ensure proper file type routing logic

#### Form Validation Errors

- **Required fields**: Check that all required fields are provided
- **Data types**: Verify data types match expected schemas
- **Validation rules**: Review Zod validation schemas

### Debug Information

Enable browser developer tools to monitor:

- Network requests to DAM and ImageKit APIs
- Form submission data and validation
- JavaScript console errors
- React Query cache and state
- Component state and props

### Development Tools

- **POC Page**: Use `/poc/file-picker` for testing
- **React DevTools**: Monitor component state and props
- **Network Tab**: Track API requests and responses
- **Console Logs**: Check for JavaScript errors and warnings

## API Integration

The FilePicker component integrates with:

- **DAM System**: For asset storage and management
- **ImageKit**: For file uploads and transformations
- **Unsplash API**: For stock image access
- **Fluid API**: For shareable media management

For API-level integration details, refer to the [DAM Upload Endpoints Guide](https://docs.fluid.app/docs/guides/dam-upload-endpoints).