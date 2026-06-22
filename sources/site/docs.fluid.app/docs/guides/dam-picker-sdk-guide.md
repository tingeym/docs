# Source: https://docs.fluid.app/docs/guides/dam-picker-sdk-guide

# DAM Picker SDK Integration Guide

This guide covers setting up and using the `@fluid-app/dam-picker` npm package to embed Fluid's Digital Asset Management (DAM) file picker into your application. The SDK provides a drop-in modal for browsing, searching, and uploading assets, with support for React and vanilla JavaScript.

## Overview

The DAM Picker SDK lets you add a full-featured asset picker to any web application. Users can browse the DAM library, upload files from their computer, import from URLs, and search Unsplash — all within a single modal. When a user makes a selection, your application receives structured asset data including the public URL, asset code, MIME type, and optional metadata.

The SDK works with any frontend framework. First-class React bindings are included, and the vanilla JavaScript API integrates cleanly with Vue, Angular, Svelte, or plain HTML pages.

## Installation

Install the package from npm:

npm install @fluid-app/dam-picker

pnpm add @fluid-app/dam-picker

yarn add @fluid-app/dam-picker

## Prerequisites

You need a **Fluid API token** to authenticate the picker. This is the same token used for other Fluid API integrations. See the [Authentication Guide](https://docs.fluid.app/docs/guides/authentication-guide) for details on obtaining a token.

## Quick Start

### Vanilla JavaScript

import { DamPicker } from '@fluid-app/dam-picker';

const picker \= new DamPicker({
 token: 'your-fluid-api-token',
 onSelect: (assets) \=> {
 console.log('Selected:', assets\[0\].url);
 },
 onCancel: () \=> {
 console.log('User cancelled');
 },
 onError: (error) \=> {
 console.error('Picker error:', error.message);
 },
});

document.getElementById('choose-file').addEventListener('click', () \=> {
 picker.open();
});

### React (Hook)

The `useDamPicker` hook provides an imperative `open()` function that you call with callbacks:

import { useState } from 'react';
import { useDamPicker } from '@fluid-app/dam-picker/react';

function ImageSelector() {
 const \[imageUrl, setImageUrl\] \= useState<string | null\>(null);
 const { open, isOpen } \= useDamPicker({ token: 'your-fluid-api-token' });

 const handleChooseImage \= () \=\> {
 open({
 onSelect: (assets) \=\> setImageUrl(assets\[0\].url),
 onCancel: () \=\> console.log('Cancelled'),
 });
 };

 return (
 <div\>
 <button onClick\={handleChooseImage} disabled\={isOpen}\>
 Choose Image
 </button\>
 {imageUrl && <img src\={imageUrl} alt\="Selected" /\>}
 </div\>
 );
}

### React (Controlled Component)

The `DamPickerModal` component gives you declarative control over the picker's open/close state:

import { useState } from 'react';
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
 token\="your-fluid-api-token"
 onSelect\={(assets) \=\> {
 setImageUrl(assets\[0\].url);
 setIsOpen(false);
 }}
 onCancel\={() \=\> setIsOpen(false)}
 /\>
 </div\>
 );
}

## Configuration

### DamPicker Options

The `DamPicker` constructor and `DamPickerModal` component accept the following options:

| Option | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `token` | `string` | Yes | — | Fluid API token for authentication |
| `onSelect` | `(assets: SelectedAsset[]) => void` | Yes | — | Called when the user confirms their selection |
| `onCancel` | `() => void` | No | — | Called when the user closes the picker without selecting |
| `onError` | `(error: DamPickerError) => void` | No | — | Called when an error occurs |
| `onReady` | `() => void` | No | — | Called when the picker iframe has loaded and is ready |
| `maxSelectable` | `number` | No | `1` | Maximum number of assets the user can select (minimum: 1) |
| `filters.assetTypes` | `AssetType[]` | No | — | Restrict which asset types are shown |
| `baseUrl` | `string` | No | Production URL | Override the picker app URL (for local development) |
| `zIndex` | `number` | No | `10000` | Z-index of the modal overlay |

### Asset Type Filtering

Restrict the picker to specific asset types using the `filters.assetTypes` option:

const picker \= new DamPicker({
 token: 'your-token',
 filters: {
 assetTypes: \['images', 'videos'\],
 },
 onSelect: (assets) \=> console.log(assets),
});

Available asset types:

| Type | Description |
| --- | --- |
| `'images'` | Image files (jpg, png, gif, webp, svg, etc.) |
| `'videos'` | Video files (mp4, webm, mov, etc.) |
| `'documents'` | Documents (pdf, doc, docx, xls, xlsx, etc.) |
| `'audio'` | Audio files (mp3, wav, ogg, etc.) |

## Multi-Select

By default, users can select one asset at a time. Set `maxSelectable` to allow multiple selections:

const picker \= new DamPicker({
 token: 'your-token',
 maxSelectable: 10,
 onSelect: (assets) \=> {
 console.log(\`Selected ${assets.length} assets\`);
 assets.forEach((asset) \=> console.log(asset.url));
 },
});

With the React component:

<DamPickerModal
 isOpen\={isOpen}
 token\="your-token"
 maxSelectable\={5}
 onSelect\={(assets) \=\> {
 setSelectedImages(assets.map((a) \=\> a.url));
 setIsOpen(false);
 }}
 onCancel\={() \=\> setIsOpen(false)}
/\>

`onSelect` always receives an array of `SelectedAsset[]`, even when `maxSelectable` is `1`. For single-select, access the asset with `assets[0]`.

## Selected Asset Structure

When a user makes a selection, `onSelect` receives an array of `SelectedAsset` objects:

interface SelectedAsset {
 /\*\* Public URL to access the asset \*/
 url: string;

 /\*\* Unique asset identifier \*/
 assetCode: string;

 /\*\* Display name of the asset \*/
 name: string;

 /\*\* MIME type (e.g., "image/png", "video/mp4") \*/
 mimeType: string;

 /\*\* Optional metadata (dimensions, file size) \*/
 metadata?: {
 width?: number;
 height?: number;
 fileSize?: number;
 };

 /\*\* ID of the selected variant, if applicable \*/
 variantId?: string;
}

### Example: Using the Selected Asset

onSelect: (assets) \=\> {
 const asset \= assets\[0\];

 // Use the URL directly
 document.querySelector('img').src \= asset.url;

 // Check asset type
 if (asset.mimeType.startsWith('image/')) {
 console.log(\`Image: ${asset.metadata?.width}x${asset.metadata?.height}\`);
 }

 // Store the asset code for API references
 saveToDatabase({ assetCode: asset.assetCode, url: asset.url });
}

## Error Handling

The `onError` callback receives a `DamPickerError` with a machine-readable code and a human-readable message:

interface DamPickerError {
 code: 'AUTHENTICATION\_ERROR' | 'NETWORK\_ERROR' | 'TIMEOUT' | 'IFRAME\_LOAD\_ERROR' | 'UNKNOWN';
 message: string;
}

| Error Code | Cause |
| --- | --- |
| `AUTHENTICATION_ERROR` | Invalid or expired API token |
| `NETWORK_ERROR` | Network connectivity issue |
| `TIMEOUT` | Picker failed to load within timeout period (10s) |
| `IFRAME_LOAD_ERROR` | Picker iframe failed to load |
| `UNKNOWN` | Unexpected error |

const picker \= new DamPicker({
 token: 'your-token',
 onSelect: (assets) \=> handleSelection(assets),
 onError: (error) \=> {
 switch (error.code) {
 case 'AUTHENTICATION\_ERROR':
 refreshToken().then(() \=> picker.open());
 break;
 case 'TIMEOUT':
 case 'NETWORK\_ERROR':
 showRetryMessage();
 break;
 default:
 console.error('Picker error:', error.message);
 }
 },
});

## Instance Methods

The `DamPicker` class exposes these methods:

| Method | Return | Description |
| --- | --- | --- |
| `open()` | `void` | Opens the picker modal |
| `close()` | `void` | Closes the picker modal |
| `destroy()` | `void` | Cleans up the picker instance and removes all DOM elements |
| `isOpen()` | `boolean` | Returns whether the picker is currently open |
| `getState()` | `DamPickerState` | Returns the current state: `'idle'`, `'loading'`, `'ready'`, `'open'`, or `'closed'` |

Always call `destroy()` when you're done with the picker to prevent memory leaks:

const picker \= new DamPicker({ token, onSelect });

// Later, when no longer needed:
picker.destroy();

## Upload Behavior

When users upload a file (either from their computer or via URL), the file is uploaded to Fluid's DAM. Once the upload completes, the `onSelect` callback fires with the new asset — no separate upload handling is needed. The picker automatically closes after a successful upload.

## Framework Integrations

### Vue 3

<script setup\>
import { ref, onMounted, onUnmounted } from 'vue';
import { DamPicker } from '@fluid-app/dam-picker';

const imageUrl \= ref(null);
let picker \= null;

onMounted(() \=\> {
 picker \= new DamPicker({
 token: 'your-fluid-api-token',
 onSelect: (assets) \=\> {
 imageUrl.value \= assets\[0\].url;
 },
 onError: (error) \=\> {
 console.error('Picker error:', error.message);
 },
 });
});

onUnmounted(() \=\> {
 picker?.destroy();
});

const openPicker \= () \=\> picker?.open();
</script\>

<template\>
 <div\>
 <button @click\="openPicker"\>Choose Image</button\>
 <img v\-if\="imageUrl" :src\="imageUrl" alt\="Selected" /\>
 </div\>
</template\>

### Angular

import { Component, OnDestroy } from '@angular/core';
import { DamPicker, SelectedAsset } from '@fluid-app/dam-picker';

@Component({
 selector: 'app-image-selector',
 template: \`
    <button (click)\="openPicker()"\>Choose Image</button\>
    <img \*ngIf\="imageUrl" \[src\]\="imageUrl" alt\="Selected" /\>
  \`,
})
export class ImageSelectorComponent implements OnDestroy {
 imageUrl: string | null \= null;
 private picker: DamPicker;

 constructor() {
 this.picker \= new DamPicker({
 token: 'your-fluid-api-token',
 onSelect: (assets: SelectedAsset\[\]) \=\> {
 this.imageUrl \= assets\[0\].url;
 },
 });
 }

 openPicker() {
 this.picker.open();
 }

 ngOnDestroy() {
 this.picker.destroy();
 }
}

### Svelte

<script\>
 import { onMount, onDestroy } from 'svelte';
 import { DamPicker } from '@fluid-app/dam-picker';

 let imageUrl \= null;
 let picker;

 onMount(() \=\> {
 picker \= new DamPicker({
 token: 'your-fluid-api-token',
 onSelect: (assets) \=\> {
 imageUrl \= assets\[0\].url;
 },
 });
 });

 onDestroy(() \=\> {
 picker?.destroy();
 });
</script\>

<button on:click\={() \=\> picker?.open()}\>Choose Image</button\>
{#if imageUrl}
 <img src\={imageUrl} alt\="Selected" /\>
{/if}

## Common Use Cases

### Product Image Selector

const picker \= new DamPicker({
 token: 'your-token',
 filters: { assetTypes: \['images'\] },
 maxSelectable: 1,
 onSelect: (assets) \=\> {
 updateProductImage(productId, assets\[0\].assetCode);
 },
});

### Gallery Builder

<DamPickerModal
 isOpen\={isOpen}
 token\="your-token"
 filters\={{ assetTypes: \['images', 'videos'\] }}
 maxSelectable\={20}
 onSelect\={(assets) \=\> {
 setGalleryItems((prev) \=\> \[...prev, ...assets\]);
 setIsOpen(false);
 }}
 onCancel\={() \=\> setIsOpen(false)}
/\>

### Document Attachment

const picker \= new DamPicker({
 token: 'your-token',
 filters: { assetTypes: \['documents'\] },
 maxSelectable: 5,
 onSelect: (assets) \=\> {
 assets.forEach((doc) \=\> attachDocument(doc.url, doc.name));
 },
});

## Local Development

For local development, point the SDK to your local `fluid-admin` instance:

const picker \= new DamPicker({
 token: 'your-token',
 baseUrl: 'http://localhost:3007/dam-picker',
 onSelect: (assets) \=> console.log(assets),
});

## Troubleshooting

### Picker does not open

- Verify your API token is valid and not expired. An invalid token triggers the `onError` callback with `AUTHENTICATION_ERROR`.
- Check the browser console for JavaScript errors.
- Ensure the `@fluid-app/dam-picker` package is installed and imported correctly.

### Picker opens but shows a blank screen

- Check network connectivity. The picker loads inside an iframe from `admin.fluid.app`.
- If using a corporate network or VPN, ensure `admin.fluid.app` is not blocked.
- The `onReady` callback should fire when the picker is loaded. If it doesn't, there may be a network issue.

### Timeout errors

- The picker has a 10-second load timeout. On slow connections, this may not be enough.
- Check that `admin.fluid.app` is reachable from the browser.

### `onSelect` returns an empty array

- This should not happen under normal operation. If it does, check the browser console for errors and ensure you're on the latest SDK version.

### Multi-select not working

- Confirm you're passing `maxSelectable` with a value greater than 1.
- The picker UI will show a different selection mode when `maxSelectable > 1`.

### Content Security Policy (CSP) issues

If your application uses a strict CSP, you need to allow the picker iframe:

frame\-src https://admin.fluid.app;

For local development:

frame\-src https://admin.fluid.app http://localhost:3007;

## Related Guides

- [DAM File Picker Component Guide](https://docs.fluid.app/docs/guides/dam-file-picker-guide) — Internal FilePicker component used within fluid-admin
- [DAM Upload Endpoints Guide](https://docs.fluid.app/docs/guides/dam-upload-endpoints) — API endpoints for programmatic asset uploads
- [Authentication Guide](https://docs.fluid.app/docs/guides/authentication-guide) — How to obtain and manage API tokens