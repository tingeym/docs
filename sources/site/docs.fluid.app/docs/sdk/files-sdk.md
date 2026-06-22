# Source: https://docs.fluid.app/docs/sdk/files-sdk

# Files SDK

The Files SDK provides tools for integrating Fluid's Digital Asset Management (DAM) capabilities into your applications.

## Installation

npm install @fluid-app/dam-picker

## Quick Start

import { DamPicker } from '@fluid-app/dam-picker';

const picker \= new DamPicker({
 token: 'pub-your-public-token',
 onSelect: (result) \=> {
 console.log('Selected:', result.asset.url);
 },
});

picker.open();

## Authentication

The SDK requires a **public token** (`pub-` prefix) created via the [Public Tokens API](https://docs.fluid.app/api-reference/admin-v2025-06#tag/tokens). Server-side tokens (`C-`, `PT-`, `dit-`, `cdt-`, `U-`) are blocked for security.

Public tokens without a domain allowlist must have an expiration and should be generated server-side on each page load. Tokens with a domain allowlist can live without expiration. See the [Authentication Guide](https://docs.fluid.app/docs/guides/authentication-guide#public-tokens-sdk--embeds) for details on token expiration, domain allowlists, and available scopes.

## Configuration

### Full Options Reference

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `token` | `string` | — | **Required.** Public token (`pub-` prefix) or JWT |
| `accept` | `string[]` | All types | File type restrictions — MIME types or extensions |
| `fromSources` | `SourceId[]` | All sources | Which source tabs to show |
| `transformations` | `object | false` | All enabled | Transformation tools to enable, or `false` to skip |
| `maxFiles` | `number` | `1` | Maximum files the user can select |
| `minFiles` | `number` | `1` | Minimum files required before confirming |
| `maxSize` | `number` | No limit | Maximum file size in MB |
| `imageDim` | `[width, height]` | — | Force exact image dimensions |
| `imageMax` | `[width, height]` | — | Maximum image dimensions |
| `imageMin` | `[width, height]` | — | Minimum image dimensions |
| `asset` | `AssetInput` | — | Pre-load a single asset — skips picker, opens transforms |
| `assets` | `AssetInput[]` | — | Pre-load multiple assets for transformation |
| `zIndex` | `number` | `10000` | Z-index of the modal overlay |
| `onSelect` | `(result) => void` | — | **Required.** Called when user confirms selection |
| `onCancel` | `() => void` | — | Called when user closes without selecting |
| `onError` | `(error) => void` | — | Called when an error occurs |
| `onReady` | `() => void` | — | Called when the picker has loaded and is ready |

### File Type Filtering (`accept`)

Control which file types users can upload and browse using MIME types or file extensions:

// Images only
{ accept: \['image/\*'\] }

// Videos only
{ accept: \['video/\*'\] }

// PDF documents only
{ accept: \['application/pdf'\] }

// Images and PDFs
{ accept: \['image/\*', 'application/pdf'\] }

// SVG files only
{ accept: \['.svg'\] }

// Audio files only
{ accept: \['audio/\*'\] }

### Source Tabs (`fromSources`)

Control which acquisition sources appear as tabs. Available sources:

| Source ID | Tab Label | Description |
| --- | --- | --- |
| `local_file_system` | Upload | Upload from the user's computer |
| `url` | URL | Import from a URL |
| `dam_library` | Library | Browse the company's DAM library |
| `unsplash` | Stock | Search Unsplash stock photos |
| `ai_generate` | Generate | AI image generation |

// Upload and library only
{ fromSources: \['local\_file\_system', 'dam\_library'\] }

// External sources only
{ fromSources: \['url', 'unsplash'\] }

// Single file upload (no browsing)
{ fromSources: \['local\_file\_system'\] }

// All sources (default)
{ fromSources: \['local\_file\_system', 'url', 'dam\_library', 'unsplash', 'ai\_generate'\] }

### Transformations

The picker includes built-in image transformation tools. Control which tools appear, or disable transformations entirely:

| Tool | Key | Description |
| --- | --- | --- |
| Crop | `crop` | Crop with aspect ratio presets |
| Rotate | `rotate` | Rotate left/right |
| Quality | `quality` | Quality selector (Very Low to Full) |
| Remove Background | `removeBackground` | AI-powered background removal |
| Change Background | `changeBackground` | AI: Replace background with a text prompt |
| Upscale | `upscale` | AI: Increase image resolution |
| Retouch | `retouch` | AI: Enhance and retouch image |
| Drop Shadow | `dropShadow` | AI: Add realistic drop shadow |
| Smart Crop | `smartCrop` | AI: Auto-focus crop on subject |
| Edit with AI | `editWithAi` | AI: Edit image with a text prompt |

// All transformations enabled (default)
{ transformations: { crop: true, rotate: true, quality: true, removeBackground: true, changeBackground: true, upscale: true, retouch: true, dropShadow: true, smartCrop: true, editWithAi: true } }

// Only crop and rotate
{ transformations: { crop: true, rotate: true, quality: false, removeBackground: false, changeBackground: false, upscale: false, retouch: false, dropShadow: false, smartCrop: false, editWithAi: false } }

// Only AI tools (no manual crop/rotate)
{ transformations: { crop: false, rotate: false, quality: false } }

// Skip transformations entirely
{ transformations: false }

### Selection Limits

// Single select (default)
{ maxFiles: 1 }

// Multi-select up to 5
{ maxFiles: 5 }

// Require at least 2, up to 4
{ minFiles: 2, maxFiles: 4 }

### File Size Limits

// Max 5 MB
{ maxSize: 5 }

// Max 1 MB, images only
{ maxSize: 1, accept: \['image/\*'\] }

### Image Dimension Constraints

// Exact dimensions (800x600)
{ imageDim: \[800, 600\] }

// Maximum dimensions (1920x1080)
{ imageMax: \[1920, 1080\] }

// Minimum dimensions (400x400)
{ imageMin: \[400, 400\] }

## Common Presets

### Hero Image

const picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['image/\*'\],
 maxSize: 10,
 imageMin: \[1200, 600\],
 maxFiles: 1,
 onSelect: (result) \=> setHeroImage(result.asset.url),
});

### Product Gallery

const picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['image/\*'\],
 maxSize: 5,
 imageMin: \[400, 400\],
 minFiles: 3,
 maxFiles: 8,
 onSelect: (result) \=> setGalleryImages(result.assets.map(a \=> a.url)),
});

### Avatar / Profile Picture

const picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['image/\*'\],
 maxSize: 2,
 imageMin: \[200, 200\],
 fromSources: \['local\_file\_system', 'url'\],
 maxFiles: 1,
 onSelect: (result) \=> setAvatar(result.asset.url),
});

### Document Upload

const picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['application/pdf'\],
 maxSize: 25,
 maxFiles: 5,
 fromSources: \['local\_file\_system', 'url'\],
 transformations: false,
 onSelect: (result) \=> attachDocuments(result.assets),
});

### Background Removal Only

const picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['image/\*'\],
 transformations: {
 crop: true,
 rotate: false,
 quality: false,
 removeBackground: true,
 changeBackground: false,
 upscale: false,
 retouch: false,
 dropShadow: false,
 smartCrop: false,
 editWithAi: false,
 },
 onSelect: (result) \=> setProductImage(result.asset.url),
});

## Transform Mode

Skip the picker entirely and open an existing asset directly in the transformation editor:

const picker \= new DamPicker({
 token: 'pub-xxxxx',
 asset: { url: 'https://ik.imagekit.io/fluid/your-company/image.jpg' },
 onSelect: (result) \=> {
 console.log('Transformed:', result.asset.url);
 },
 onCancel: () \=> console.log('Cancelled'),
});

picker.open();

You can also pass an asset code instead of a URL:

{ asset: { assetCode: 'ABC123' } }

Or both:

{ asset: { url: 'https://...', assetCode: 'ABC123', name: 'My Image' } }

## Result Object

When a user makes a selection, `onSelect` receives a `DamPickerResult`:

interface DamPickerResult {
 /\*\* The first (or only) selected asset \*/
 asset: SelectedAsset;
 /\*\* All selected assets (always an array, even for single-select) \*/
 assets: SelectedAsset\[\];
}

interface SelectedAsset {
 url: string; // Public URL
 assetCode: string; // Unique asset identifier
 name: string; // Display name
 mimeType: string; // e.g. "image/png", "video/mp4"
 variantId?: string; // ID of the selected variant
 metadata?: {
 width?: number;
 height?: number;
 fileSize?: number;
 };
}

### Example: Using the Result

onSelect: (result) \=> {
 // Single select — use result.asset
 const { url, assetCode, mimeType } \= result.asset;
 document.querySelector('img').src \= url;

 // Multi-select — iterate result.assets
 result.assets.forEach((asset) \=> {
 console.log(\`${asset.name}: ${asset.url}\`);
 });

 // Check dimensions
 if (result.asset.metadata?.width) {
 console.log(\`${result.asset.metadata.width}x${result.asset.metadata.height}\`);
 }
}

## Error Handling

interface DamPickerError {
 code: 'AUTHENTICATION\_ERROR' | 'NETWORK\_ERROR' | 'TIMEOUT' |
 'VALIDATION\_ERROR' | 'UPLOAD\_ERROR' | 'SCOPE\_ERROR' | 'UNKNOWN';
 message: string;
}

| Error Code | Cause |
| --- | --- |
| `AUTHENTICATION_ERROR` | Invalid, expired, or blocked token |
| `NETWORK_ERROR` | Network connectivity issue |
| `TIMEOUT` | Picker failed to load within timeout |
| `VALIDATION_ERROR` | Invalid configuration options |
| `UPLOAD_ERROR` | File upload failed |
| `SCOPE_ERROR` | Token missing required scope |
| `UNKNOWN` | Unexpected error |

## Instance Methods

| Method | Return | Description |
| --- | --- | --- |
| `open()` | `void` | Opens the picker modal |
| `close()` | `void` | Closes the picker modal |
| `destroy()` | `void` | Cleans up the instance and removes all DOM elements |

Always call `destroy()` when you're done with the picker to prevent memory leaks.

## Framework Examples

### React (Hook)

import { useDamPicker } from '@fluid-app/dam-picker/react';

function ImageSelector() {
 const \[imageUrl, setImageUrl\] \= useState<string | null\>(null);
 const { open, isOpen } \= useDamPicker({ token: 'pub-xxxxx' });

 return (
 <div\>
 <button onClick\={() \=\> open({
 accept: \['image/\*'\],
 maxSize: 5,
 onSelect: (result) \=\> setImageUrl(result.asset.url),
 })} disabled\={isOpen}\>
 Choose Image
 </button\>
 {imageUrl && <img src\={imageUrl} alt\="Selected" /\>}
 </div\>
 );
}

### React (Controlled Component)

import { DamPickerModal } from '@fluid-app/dam-picker/react';

function ImageSelector() {
 const \[isOpen, setIsOpen\] \= useState(false);
 const \[imageUrl, setImageUrl\] \= useState<string | null\>(null);

 return (
 <div\>
 <button onClick\={() \=\> setIsOpen(true)}\>Choose Image</button\>
 {imageUrl && <img src\={imageUrl} alt\="Selected" /\>}

 <DamPickerModal
 isOpen\={isOpen}
 token\="pub-xxxxx"
 accept\={\['image/\*'\]}
 maxFiles\={1}
 maxSize\={10}
 onSelect\={(result) \=\> {
 setImageUrl(result.asset.url);
 setIsOpen(false);
 }}
 onCancel\={() \=\> setIsOpen(false)}
 /\>
 </div\>
 );
}

### Vue 3

<script setup\>
import { ref, onMounted, onUnmounted } from 'vue';
import { DamPicker } from '@fluid-app/dam-picker';

const imageUrl \= ref(null);
let picker \= null;

onMounted(() \=\> {
 picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['image/\*'\],
 onSelect: (result) \=\> { imageUrl.value \= result.asset.url },
 });
});

onUnmounted(() \=\> picker?.destroy());
</script\>

<template\>
 <button @click\="picker?.open()"\>Choose Image</button\>
 <img v\-if\="imageUrl" :src\="imageUrl" alt\="Selected" /\>
</template\>

### Angular

import { Component, OnDestroy } from '@angular/core';
import { DamPicker } from '@fluid-app/dam-picker';

@Component({
 selector: 'app-image-selector',
 template: \`
    <button (click)\="openPicker()"\>Choose Image</button\>
    <img \*ngIf\="imageUrl" \[src\]\="imageUrl" alt\="Selected" /\>
  \`,
})
export class ImageSelectorComponent implements OnDestroy {
 imageUrl: string | null \= null;
 private picker \= new DamPicker({
 token: 'pub-xxxxx',
 accept: \['image/\*'\],
 onSelect: (result) \=\> { this.imageUrl \= result.asset.url },
 });

 openPicker() { this.picker.open() }
 ngOnDestroy() { this.picker.destroy() }
}

## Content Security Policy

If your application uses a strict CSP, allow the picker iframe:

frame\-src https://admin.fluid.app;

## Additional Resources

- [DAM Picker SDK Integration Guide](https://docs.fluid.app/docs/guides/dam-picker-sdk-guide) — Detailed setup walkthrough
- [DAM File Picker Component Guide](https://docs.fluid.app/docs/guides/dam-file-picker-guide) — Internal FilePicker component used within fluid-admin
- [DAM Upload Endpoints](https://docs.fluid.app/docs/guides/dam-upload-endpoints) — API endpoints for programmatic file uploads