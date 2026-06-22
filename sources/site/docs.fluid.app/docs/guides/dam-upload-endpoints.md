# Source: https://docs.fluid.app/docs/guides/dam-upload-endpoints

# DAM Upload Endpoints Developer Guide

This guide provides comprehensive documentation for the Digital Asset Management (DAM) file upload endpoints that use multipart form-data.

## Authentication Requirements

Both DAM upload endpoints require Bearer token authentication:

Authorization: Bearer <company\_token>

The token must be a valid company token that provides access to the DAM system. Authentication is handled by the `Api::BaseController` which validates the Bearer token and sets the current company context.

## Endpoint Details

### Asset Creation Endpoint

**Endpoint:** `POST /api/dam/assets`

Creates a new digital asset with file upload. This endpoint creates both the asset record and its default variant in a single operation.

**Required Parameters:**

- `asset[file]`: Binary file upload (required) - Maximum file size: 100MB
- `asset[name]`: Display name for the asset (required)

**Optional Parameters:**

- `asset[description]`: Asset description
- `asset[tags]`: Comma-separated list of tags (e.g., "en,en-us,product")

**Response:** Returns the created asset with its default variant and metadata.

### Variant Creation Endpoint

**Endpoint:** `POST /api/dam/assets/{code}/variants`

Creates multiple variants for an existing digital asset. This endpoint allows batch upload of multiple files as variants of the same asset.

**Path Parameters:**

- `code`: Unique asset code identifier (required)

**Request Body:** Accepts an array of variant data with file uploads and optional tags. Each variant can have different tags and files.

## Request Examples

### Asset Creation Example

curl \-X POST "https://api.example.com/api/dam/assets" \\
 \-H "Authorization: Bearer <company\_token>" \\
 \-H "Content-Type: multipart/form-data" \\
 \-F "asset\[file\]=@/path/to/image.png" \\
 \-F "asset\[name\]=My Product Image" \\
 \-F "asset\[description\]=High-resolution product image" \\
 \-F "asset\[tags\]=en,en-us,product,featured"

**Ruby Example:**

\# Using Faraday or similar HTTP client
conn \= Faraday.new do |f|
 f.request :multipart
end

response \= conn.post("/api/dam/assets") do |req|
 req.headers\["Authorization"\] \= "Bearer #{company\_token}"
 req.body \= {
 "asset\[file\]" \=> Faraday::UploadIO.new("/path/to/image.png", "image/png"),
 "asset\[name\]" \=> "My Product Image",
 "asset\[description\]" \=> "High-resolution product image",
 "asset\[tags\]" \=> "en,en-us,product,featured"
 }
end

### Variant Creation Example

curl \-X POST "https://api.example.com/api/dam/assets/ABC123/variants" \\
 \-H "Authorization: Bearer <company\_token>" \\
 \-H "Content-Type: multipart/form-data" \\
 \-F "variants\[0\]\[file\]=@/path/to/variant1.png" \\
 \-F "variants\[0\]\[tags\]=en,en-us" \\
 \-F "variants\[1\]\[file\]=@/path/to/variant2.png" \\
 \-F "variants\[1\]\[tags\]=es,es-mx,draft"

**Ruby Example:**

\# Test pattern from the codebase
variants\_data \= {
 variants: \[
 { 
 file: File.open("path/to/variant1.png"), 
 tags: "en,en-us" 
 },
 { 
 file: File.open("path/to/variant2.png"), 
 tags: "es,es-mx,draft" 
 }
 \]
}

## Error Handling

The system provides detailed error responses for various failure scenarios:

### Asset Creation Errors

#### File Read Errors

- **Status:** 422 Unprocessable Entity
- **Error:** `{ "errors": { "asset": { "file": ["Unable to read file"] } } }`
- **Cause:** File cannot be read or accessed

#### Variant Build Errors

- **Status:** 422 Unprocessable Entity
- **Error:** `{ "errors": { "asset": { "file": ["Failed to create variant"] } } }`
- **Cause:** Variant creation failed during upload or processing

#### Asset Build Errors

- **Status:** 422 Unprocessable Entity
- **Error:** `{ "errors": { "asset": { "base": ["Failed to save asset"] } } }`
- **Cause:** Asset record could not be saved to database

#### Asset Path Build Errors

- **Status:** 422 Unprocessable Entity
- **Error:** `{ "errors": { "asset": { "base": ["Failed to create asset path"] } } }`
- **Cause:** Asset path record creation failed

### Variant Creation Errors

#### File Size Errors

- **Status:** 422 Unprocessable Entity (when all variants fail) or 201 with error objects (partial failure)
- **Error:** `{ "errors": { "files": ["File size exceeds maximum limit of 100MB"] } }`
- **Cause:** File exceeds the 100MB size limit

#### File Type Errors

- **Status:** 422 Unprocessable Entity (when all variants fail) or 201 with error objects (partial failure)
- **Error:** `{ "errors": { "file": ["Unsupported file type"] } }`
- **Cause:** File type is not supported or cannot be detected

#### Upload Errors

- **Status:** 422 Unprocessable Entity (when all variants fail) or 201 with error objects (partial failure)
- **Error:** `{ "errors": { "file": ["Upload failed"] } }`
- **Cause:** ImageKit upload failed or network issues

#### Variant Build Errors

- **Status:** 422 Unprocessable Entity (when all variants fail) or 201 with error objects (partial failure)
- **Error:** `{ "errors": { "file": ["Failed to create variant"] } }`
- **Cause:** Variant record creation failed

### Authentication Errors

#### Unauthorized Access

- **Status:** 401 Unauthorized
- **Error:** `{ "error_message": "unauthorized", "errors": { "customer": ["Not authorized."] } }`
- **Cause:** Invalid or missing Bearer token

#### Asset Not Found

- **Status:** 404 Not Found
- **Error:** `{ "status": "error", "data": { "error": "Asset not found" } }`
- **Cause:** Asset with specified code does not exist (variants endpoint only)

## Tag Processing

Tags are processed with the following rules and validation:

### Tag Format

- **Input Format:** Comma-separated string (e.g., "en,en-us,product")
- **Validation:** No square brackets `[]` or quotes `"` allowed
- **Processing:** Tags are split by comma, trimmed, and normalized

### Tag Normalization Rules

The `Dam::Tag.normalize_name` method applies these transformations:

1. Convert to lowercase
2. Remove non-word characters except hyphens
3. Replace underscores with hyphens
4. Remove duplicate hyphens (squeeze)

**Examples:**

- `"Product_Tag!"` becomes `"product-tag"`
- `"EN-US"` becomes `"en-us"`
- `"test__tag"` becomes `"test-tag"`

### Tag Validation

\# Invalid tag strings (will be ignored)
"\[en,us\]" \# Contains square brackets
'"en,us"' \# Contains quotes
"" \# Empty string
" " \# Whitespace only

\# Valid tag strings
"en,en-us" \# Basic comma-separated
"product,featured,new" \# Multiple tags
"en-us" \# Single tag with hyphen

## Implementation Flow

The upload process follows this architectural flow:

### Asset Creation Flow

1. **API Request** → `Api::Dam::AssetsController#create`
2. **Action Processing** → `Api::Dam::Assets::CreateAction.call`
3. **Parameter Validation** → Dry-validation schema validation
4. **Tag Processing** → `tags_from_params` method normalizes tags
5. **Asset Building** → `Dam::AssetBuilder.build` coordinates creation
6. **Database Transaction** → Creates partial asset and asset path records
7. **Variant Creation** → `Dam::VariantBuilder.build` handles file upload
8. **File Validation** → Size and type validation
9. **ImageKit Upload** → File uploaded to ImageKit storage
10. **Metadata Storage** → Variant updated with upload metadata
11. **Response Serialization** → `Dam::AssetBlueprint` formats response

### Variant Creation Flow

1. **API Request** → `Api::Dam::VariantsController#create`
2. **Asset Lookup** → Find asset by code or return 404
3. **Action Processing** → `Api::Dam::Variants::CreateAction.call`
4. **Batch Processing** → Process each variant in the array
5. **Individual Variant Flow** → For each variant:
 - Tag normalization
 - `Dam::VariantBuilder.build` call
 - File validation and upload
 - Database persistence
6. **Error Handling** → Collect successes and failures
7. **Response Assembly** → Mix of successful variants and error objects

### Detailed Asset Builder Process

\# From Dam::AssetBuilder.build
def build(company:, name:, description: nil, file:, tags: \[\])
 \# 1. Parameter validation
 raise ArgumentError unless company && name.present? && file
 
 \# 2. MIME type detection
 mime\_type \= Marcel::MimeType.for(file)
 
 \# 3. Database transaction
 asset \= ActiveRecord::Base.transaction do
 \# Create partial asset record
 asset \= build\_partial\_asset(company:, name:, description:, mime\_type:)
 \# Create canonical asset path
 asset.canonical\_asset\_path \= build\_partial\_asset\_path(asset:)
 asset
 end
 
 \# 4. Variant creation (outside transaction for API calls)
 asset.default\_variant \= Dam::VariantBuilder.build(asset:, file:, tags:)
 asset.save!
 
 asset
end

### Detailed Variant Builder Process

\# From Dam::VariantBuilder.build
def build(asset:, file:, tags: \[\])
 \# 1. File size validation
 raise FileSizeError unless valid\_file\_size?(file)
 
 \# 2. File type validation
 mime\_type, extension \= validate\_file\_type!(file:)
 
 \# 3. Create partial variant record
 variant \= build\_partial\_variant(asset:)
 
 \# 4. Upload and finalize
 upload\_and\_finalize\_variant(variant:, file:, mime\_type:, extension:, tags:)
end

## File Constraints and Validation

### File Size Limits

- **Maximum file size:** 200MB for images, 2GB for videos (`Dam::VariantBuilder::MAX_IMAGE_FILE_SIZE`, `Dam::VariantBuilder::MAX_VIDEO_FILE_SIZE`)
- **Validation method:** `valid_file_size?` checks file size before upload
- **Error handling:** `FileSizeError` raised if limit exceeded

### File Type Support

- **Detection method:** Marcel gem for MIME type detection
- **Extension mapping:** MIME::Types gem for file extensions
- **Validation:** Both MIME type and extension must be valid
- **Error handling:** `FileTypeError` raised for unsupported types

### Supported Categories

Assets are automatically categorized based on MIME type:

- **Images:** `image/*` → "images" category
- **Videos:** `video/*` → "videos" category
- **Audio:** `audio/*` → "audio" category
- **Documents:** `text/*`, `*word*`, `*pdf*`, `*rtf*` → "documents" category
- **Other:** All other types → "other" category

## Response Schemas

### Asset Creation Response (201 Created)

{
 "asset": {
 "id": "string",
 "code": "ABC123",
 "name": "My Product Image",
 "description": "High-resolution product image",
 "category": "images",
 "created\_at": "2023-01-01T00:00:00Z",
 "updated\_at": "2023-01-01T00:00:00Z",
 "canonical\_path": "123.images.ABC123",
 "company": "Company Name",
 "tree\_paths": \["123.images.ABC123"\],
 "default\_variant\_id": "variant-id",
 "variants": \[
 {
 "id": "variant-id",
 "file\_name": "ABC123.png",
 "mime\_type": "image/png",
 "tags": \["en", "en-us", "product", "featured"\],
 "default": true,
 "url": "https://imagekit.io/path/to/file.png"
 }
 \]
 },
 "meta": {
 "request\_id": "123e4567-e89b-12d3-a456-426614174000",
 "timestamp": "2023-01-01T00:00:00Z"
 }
}

### Variant Creation Response (201 Created)

{
 "variants": \[
 {
 "id": "variant-id-1",
 "file\_name": "ABC123-variant1.png",
 "mime\_type": "image/png",
 "tags": \["en", "en-us"\],
 "default": false,
 "url": "https://imagekit.io/path/to/variant1.png"
 },
 {
 "errors": {
 "file": \["File size exceeds maximum limit of 100MB"\]
 }
 }
 \],
 "meta": {
 "request\_id": "123e4567-e89b-12d3-a456-426614174000",
 "timestamp": "2023-01-01T00:00:00Z"
 }
}

### Error Response (422 Unprocessable Entity)

{
 "error\_message": "Validation failed",
 "errors": {
 "asset": {
 "file": \["Unable to read file"\],
 "name": \["can't be blank"\]
 }
 },
 "meta": {
 "request\_id": "123e4567-e89b-12d3-a456-426614174000",
 "timestamp": "2023-01-01T00:00:00Z"
 }
}

## Storage and Organization

### ImageKit Integration

- **Storage Provider:** ImageKit.io for file storage and CDN
- **Upload Method:** `ImageKitIo::Client#upload_file`
- **Folder Structure:** Organized by company, category, and asset code
- **Metadata:** Upload response stored in variant metadata field

### Database Schema

- **Assets:** Core asset records with company association
- **Variants:** File variants with ImageKit metadata
- **Asset Paths:** Tree-structured paths for organization
- **Tags:** Normalized tag system with many-to-many relationships

### Path Structure

Storage Path: {company\_id}/{category}/{asset\_code}
Tree Path: {company\_id}.{category}.{asset\_code}

Example:
Storage: "123/images/ABC123"
Tree: "123.images.ABC123"

## Security Considerations

### Authentication

- Bearer token authentication required for all endpoints
- Company-scoped access - users can only access their company's assets
- Token validation handled by `Api::BaseController`

### File Validation

- File size limits prevent abuse
- MIME type validation prevents malicious uploads
- File content validation through Marcel gem

### Error Handling

- Detailed error messages for debugging
- Sensitive information not exposed in error responses
- Proper HTTP status codes for different error types

## Testing and Development

### Running Tests

\# Test DAM API conformance
bin/rails test test/integration/api/dam\_v0

\# Validate OpenAPI documentation
bin/redocly-lint

\# Run linting
bin/rubocop

### Test Patterns

The test suite includes examples of:

- Successful asset and variant creation
- Error handling for various failure scenarios
- Authentication testing
- Multipart form-data request formatting
- Response validation

### Mock Setup

Tests use ImageKit client mocking to avoid external API calls:

client \= instance\_double(ImageKitIo::Client, upload\_file: mock\_response)
allow(::Dam::VariantBuilder).to receive(:client).and\_return(client)