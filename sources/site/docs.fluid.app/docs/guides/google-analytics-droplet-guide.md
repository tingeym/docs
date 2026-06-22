# Source: https://docs.fluid.app/docs/guides/google-analytics-droplet-guide

# Creating A Google Analytics Droplet for the Marketplace Full Guide

This guide explains how to create a public droplet that allows clients to easily install Google Analytics tracking on their Fluid-powered websites. The droplet provides a simple interface for admins to input their Google Analytics ID and automatically creates or updates a Global Embed that injects the tracking script into all public theme pages.

## Overview

A Google Analytics droplet consists of:

1. **Droplet Interface**: A simple form where admins enter their Google Analytics ID
2. **API Integration**: Backend logic that creates/updates Global Embeds via the Fluid API
3. **Global Embed Management**: Automatic script injection into all public theme pages

## Understanding Global Embeds

Global Embeds are a powerful Fluid feature that allows you to inject custom HTML content (typically JavaScript) into all public theme pages for a client. They work by:

### How Global Embeds Work

1. **Script Placement**: Global Embeds can be placed in either the `<head>` or `<body>` section of pages
2. **Automatic Injection**: Once created and activated, the scripts are automatically injected into all public theme pages
3. **Company-Specific**: Each company has their own set of Global Embeds
4. **Caching**: Global Embeds are cached for performance and invalidated when updated

### Global Embed Properties

- **name**: A descriptive name for the embed (e.g., "Google Analytics Tracking")
- **content**: The HTML/JavaScript content to inject
- **status**: Either "draft" or "active" (only active embeds are injected)
- **placement**: Either "head" or "body" (determines where the script is placed)

## API Reference

### Global Embeds API Endpoints

The Fluid API provides full CRUD operations for Global Embeds:

- `GET /api/global_embeds` - List all Global Embeds
- `GET /api/global_embeds/:id` - Get a specific Global Embed
- `POST /api/global_embeds` - Create a new Global Embed
- `PUT /api/global_embeds/:id` - Update an existing Global Embed
- `DELETE /api/global_embeds/:id` - Delete a Global Embed

### Creating a Global Embed

curl \-X POST "https://api.fluid.app/api/global\_embeds" \\
 \-H "Authorization: Bearer dit\_xyz789..." \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "global\_embed": {
 "name": "Google Analytics Tracking",
 "content": "<script async src=\\"https://www.googletagmanager.com/gtag/js?id=GA\_MEASUREMENT\_ID\\"></script><script>window.dataLayer = window.dataLayer || \[\]; function gtag(){dataLayer.push(arguments);} gtag(\\"js\\", new Date()); gtag(\\"config\\", \\"GA\_MEASUREMENT\_ID\\");</script>",
 "status": "active",
 "placement": "head"
 }
 }'

### Updating a Global Embed

curl \-X PUT "https://api.fluid.app/api/global\_embeds/:id" \\
 \-H "Authorization: Bearer dit\_xyz789..." \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "global\_embed": {
 "name": "Google Analytics Tracking",
 "content": "<script async src=\\"https://www.googletagmanager.com/gtag/js?id=NEW\_GA\_ID\\"></script><script>window.dataLayer = window.dataLayer || \[\]; function gtag(){dataLayer.push(arguments);} gtag(\\"js\\", new Date()); gtag(\\"config\\", \\"NEW\_GA\_ID\\");</script>",
 "status": "active"
 }
 }'

## Droplet Implementation

### 1\. Droplet Setup

First, create your droplet using the Fluid API:

curl \-X POST "https://api.fluid.app/api/droplets" \\
 \-H "Authorization: Bearer YOUR\_OWNER\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "droplet": {
 "name": "Google Analytics Integration",
 "embed\_url": "https://your-droplet.com/embed",
 "active": true,
 "settings": {
 "marketplace\_page": {
 "title": "Google Analytics Integration",
 "summary": "Easily add Google Analytics tracking to your Fluid website",
 "logo\_url": "https://your-droplet.com/ga-logo.svg"
 },
 "details\_page": {
 "title": "Google Analytics Integration",
 "summary": "Add Google Analytics tracking with just your measurement ID",
 "logo": "https://your-droplet.com/ga-logo-large.svg",
 "features": \[
 {
 "name": "Easy Setup",
 "summary": "Just enter your GA measurement ID",
 "details": "No technical knowledge required. Simply paste your Google Analytics measurement ID and we'll handle the rest."
 },
 {
 "name": "Automatic Installation",
 "summary": "Scripts injected into all pages",
 "details": "Your tracking code is automatically added to all public pages of your Fluid website."
 }
 \]
 }
 }
 }
 }'

### 2\. Webhook Registration

Register for installation webhooks to receive company authentication tokens:

curl \-X POST "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer YOUR\_OWNER\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "webhook": {
 "resource": "droplet",
 "event": "installed",
 "url": "https://your-droplet.com/api/webhooks",
 "http\_method": "post"
 }
 }'

### 3\. Droplet Interface (HTML/JavaScript)

Create a simple interface for admins to input their Google Analytics ID:

<!DOCTYPE html\>
<html lang\="en"\>
<head\>
 <meta charset\="UTF-8"\>
 <meta name\="viewport" content\="width=device-width, initial-scale=1.0"\>
 <title\>Google Analytics Setup</title\>
 <style\>
 body {
 font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
 max-width: 600px;
 margin: 0 auto;
 padding: 20px;
 background: #f8f9fa;
 }
 .container {
 background: white;
 padding: 30px;
 border-radius: 8px;
 box-shadow: 0 2px 10px rgba(0,0,0,0.1);
 }
 .form-group {
 margin-bottom: 20px;
 }
 label {
 display: block;
 margin-bottom: 8px;
 font-weight: 600;
 color: #333;
 }
 input\[type="text"\] {
 width: 100%;
 padding: 12px;
 border: 2px solid #e1e5e9;
 border-radius: 6px;
 font-size: 16px;
 transition: border-color 0.2s;
 }
 input\[type="text"\]:focus {
 outline: none;
 border-color: #007bff;
 }
 .help-text {
 font-size: 14px;
 color: #666;
 margin-top: 5px;
 }
 .btn {
 background: #007bff;
 color: white;
 border: none;
 padding: 12px 24px;
 border-radius: 6px;
 font-size: 16px;
 cursor: pointer;
 transition: background-color 0.2s;
 }
 .btn:hover {
 background: #0056b3;
 }
 .btn:disabled {
 background: #6c757d;
 cursor: not-allowed;
 }
 .status {
 margin-top: 20px;
 padding: 12px;
 border-radius: 6px;
 display: none;
 }
 .status.success {
 background: #d4edda;
 color: #155724;
 border: 1px solid #c3e6cb;
 }
 .status.error {
 background: #f8d7da;
 color: #721c24;
 border: 1px solid #f5c6cb;
 }
 .loading {
 display: none;
 margin-left: 10px;
 }
 .welcome-message {
 background: #e3f2fd;
 color: #1565c0;
 padding: 12px 16px;
 border-radius: 6px;
 margin-bottom: 20px;
 border-left: 4px solid #2196f3;
 font-weight: 500;
 }
 </style\>
</head\>
<body\>
 <div class\="container"\>
 <div id\="welcomeMessage" class\="welcome-message" style\="display: none;"\>
 Welcome, <span id\="companyName"\></span\>!
 </div\>
 <h1\>Google Analytics Setup</h1\>
 <p\>Enter your Google Analytics measurement ID to start tracking visitors on your Fluid website.</p\>
 
 <form id\="gaForm"\>
 <div class\="form-group"\>
 <label for\="gaId"\>Google Analytics Measurement ID</label\>
 <input 
 type\="text" 
 id\="gaId" 
 name\="gaId" 
 placeholder\="G-XXXXXXXXXX"
 pattern\="G-\[A-Z0-9\]{10}"
 required
 \>
 <div class\="help-text"\>
 Find your measurement ID in your Google Analytics account under Admin > Data Streams
 </div\>
 </div\>
 
 <button type\="submit" class\="btn" id\="submitBtn"\>
 Save Google Analytics
 <span class\="loading" id\="loading"\>⏳</span\>
 </button\>
 </form\>
 
 <div class\="status" id\="status"\></div\>
 </div\>

 <script\>
 // Extract company context from URL parameters
 const urlParams = new URLSearchParams(window.location.search);
 const dri = urlParams.get('dri');
 
 if (!dri) {
 document.getElementById('status').textContent = 'Error: Missing company context. Please access this droplet through the Fluid interface.';
 document.getElementById('status').className = 'status error';
 document.getElementById('status').style.display = 'block';
 document.getElementById('gaForm').style.display = 'none';
 } else {
 // Load company information and existing configuration
 loadCompanyInfo();
 loadExistingConfig();
 }

 document.getElementById('gaForm').addEventListener('submit', async function(e) {
 e.preventDefault();
 
 const gaId = document.getElementById('gaId').value.trim();
 const submitBtn = document.getElementById('submitBtn');
 const loading = document.getElementById('loading');
 const status = document.getElementById('status');
 
 if (!gaId.match(/^G-\[A-Z0-9\]{10}$/)) {
 status.textContent = 'Please enter a valid Google Analytics measurement ID (format: G-XXXXXXXXXX)';
 status.className = 'status error';
 status.style.display = 'block';
 return;
 }
 
 submitBtn.disabled = true;
 loading.style.display = 'inline';
 status.style.display = 'none';
 
 try {
 await saveGoogleAnalytics(gaId);
 status.textContent = 'Google Analytics has been successfully configured! Your tracking code is now active on all pages.';
 status.className = 'status success';
 status.style.display = 'block';
 } catch (error) {
 status.textContent = 'Error: ' + error.message;
 status.className = 'status error';
 status.style.display = 'block';
 } finally {
 submitBtn.disabled = false;
 loading.style.display = 'none';
 }
 });

 async function loadCompanyInfo() {
 try {
 const response = await fetch(\`/api/companies/${dri}/info\`);
 if (response.ok) {
 const data = await response.json();
 if (data.companyName) {
 document.getElementById('companyName').textContent = data.companyName;
 document.getElementById('welcomeMessage').style.display = 'block';
 }
 }
 } catch (error) {
 console.log('Could not load company information');
 }
 }

 async function loadExistingConfig() {
 try {
 const response = await fetch(\`/api/companies/${dri}/google-analytics\`);
 if (response.ok) {
 const data = await response.json();
 if (data.gaId) {
 document.getElementById('gaId').value = data.gaId;
 }
 }
 } catch (error) {
 console.log('No existing configuration found');
 }
 }

 async function saveGoogleAnalytics(gaId) {
 const response = await fetch(\`/api/companies/${dri}/google-analytics\`, {
 method: 'POST',
 headers: {
 'Content-Type': 'application/json',
 },
 body: JSON.stringify({ gaId })
 });
 
 if (!response.ok) {
 const error = await response.json();
 throw new Error(error.message || 'Failed to save Google Analytics configuration');
 }
 }
 </script\>
</body\>
</html\>

### 4\. Backend Implementation (Node.js/Express Example)

const express \= require('express');
const axios \= require('axios');
const app \= express();

app.use(express.json());

// Store company authentication tokens
const companyTokens \= new Map();

// Webhook endpoint for droplet installations
app.post('/api/webhooks', async (req, res) \=> {
 const { resource, event, company } \= req.body;
 
 if (resource \=== 'droplet' && event \=== 'installed') {
 // Store the company's authentication token
 companyTokens.set(company.droplet\_uuid, {
 companyId: company.fluid\_company\_id,
 token: company.authentication\_token,
 name: company.name
 });
 
 console.log(\`Company ${company.name} installed the droplet\`);
 res.status(200).send('OK');
 } else if (resource \=== 'droplet' && event \=== 'uninstalled') {
 // Remove company data
 companyTokens.delete(company.droplet\_uuid);
 console.log(\`Company ${company.name} uninstalled the droplet\`);
 res.status(200).send('OK');
 } else {
 res.status(400).send('Unknown webhook');
 }
});

// Get company information
app.get('/api/companies/:dri/info', async (req, res) \=> {
 const { dri } \= req.params;
 const companyData \= companyTokens.get(dri);
 
 if (!companyData) {
 return res.status(404).json({ error: 'Company not found' });
 }
 
 res.json({ 
 companyName: companyData.name,
 companyId: companyData.companyId 
 });
});

// Get existing Google Analytics configuration
app.get('/api/companies/:dri/google-analytics', async (req, res) \=> {
 const { dri } \= req.params;
 const companyData \= companyTokens.get(dri);
 
 if (!companyData) {
 return res.status(404).json({ error: 'Company not found' });
 }
 
 try {
 // Check if Google Analytics embed already exists
 const response \= await axios.get('https://api.fluid.app/api/global\_embeds', {
 headers: {
 'Authorization': \`Bearer ${companyData.token}\`,
 'Content-Type': 'application/json'
 }
 });
 
 const gaEmbed \= response.data.global\_embeds.find(embed \=> 
 embed.name \=== 'Google Analytics Tracking'
 );
 
 if (gaEmbed) {
 // Extract GA ID from the script content
 const match \= gaEmbed.content.match(/gtag\\("config", "(\[^"\]+)"/);
 res.json({ gaId: match ? match\[1\] : null });
 } else {
 res.json({ gaId: null });
 }
 } catch (error) {
 console.error('Error fetching GA config:', error);
 res.status(500).json({ error: 'Failed to fetch configuration' });
 }
});

// Save Google Analytics configuration
app.post('/api/companies/:dri/google-analytics', async (req, res) \=> {
 const { dri } \= req.params;
 const { gaId } \= req.body;
 const companyData \= companyTokens.get(dri);
 
 if (!companyData) {
 return res.status(404).json({ error: 'Company not found' });
 }
 
 if (!gaId || !gaId.match(/^G-\[A-Z0-9\]{10}$/)) {
 return res.status(400).json({ error: 'Invalid Google Analytics ID' });
 }
 
 try {
 // Check if Google Analytics embed already exists
 const listResponse \= await axios.get('https://api.fluid.app/api/global\_embeds', {
 headers: {
 'Authorization': \`Bearer ${companyData.token}\`,
 'Content-Type': 'application/json'
 }
 });
 
 const existingEmbed \= listResponse.data.global\_embeds.find(embed \=> 
 embed.name \=== 'Google Analytics Tracking'
 );
 
 const gaScript \= generateGoogleAnalyticsScript(gaId);
 
 if (existingEmbed) {
 // Update existing embed
 await axios.put(\`https://api.fluid.app/api/global\_embeds/${existingEmbed.id}\`, {
 global\_embed: {
 name: 'Google Analytics Tracking',
 content: gaScript,
 status: 'active',
 placement: 'head'
 }
 }, {
 headers: {
 'Authorization': \`Bearer ${companyData.token}\`,
 'Content-Type': 'application/json'
 }
 });
 } else {
 // Create new embed
 await axios.post('https://api.fluid.app/api/global\_embeds', {
 global\_embed: {
 name: 'Google Analytics Tracking',
 content: gaScript,
 status: 'active',
 placement: 'head'
 }
 }, {
 headers: {
 'Authorization': \`Bearer ${companyData.token}\`,
 'Content-Type': 'application/json'
 }
 });
 }
 
 res.json({ success: true });
 } catch (error) {
 console.error('Error saving GA config:', error);
 res.status(500).json({ error: 'Failed to save Google Analytics configuration' });
 }
});

function generateGoogleAnalyticsScript(gaId) {
 return \`<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=${gaId}"></script>
<script>
  window.dataLayer = window.dataLayer || \[\];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', '${gaId}');
</script>\`;
}

// Serve the droplet interface
app.get('/embed', (req, res) \=> {
 res.sendFile(\_\_dirname + '/public/embed.html');
});

const PORT \= process.env.PORT || 3000;
app.listen(PORT, () \=> {
 console.log(\`Droplet server running on port ${PORT}\`);
});

### 5\. Package.json Dependencies

{
 "name": "google-analytics-droplet",
 "version": "1.0.0",
 "description": "Google Analytics integration droplet for Fluid",
 "main": "server.js",
 "scripts": {
 "start": "node server.js",
 "dev": "nodemon server.js"
 },
 "dependencies": {
 "express": "^4.18.2",
 "axios": "^1.6.0"
 },
 "devDependencies": {
 "nodemon": "^3.0.1"
 }
}

## Testing Your Droplet

### 1\. Local Development with ngrok

\# Install ngrok
npm install \-g ngrok

\# Start your droplet server
npm run dev

\# In another terminal, start ngrok
ngrok http 3000

\# Register webhook with ngrok URL
curl \-X POST "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer YOUR\_OWNER\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "webhook": {
 "resource": "droplet",
 "event": "installed",
 "url": "https://abc123.ngrok.io/api/webhooks",
 "http\_method": "post"
 }
 }'

### 2\. Test Installation Flow

1. Install your droplet through the Fluid interface
2. Verify the webhook is received
3. Test the embed interface with a valid GA measurement ID
4. Check that the Global Embed is created in the company's account
5. Visit a public page to verify the script is injected

## Best Practices

### Security

- **Validate GA IDs**: Ensure the measurement ID format is correct
- **Sanitize Input**: Clean any user input before using it in scripts
- **Secure Tokens**: Store authentication tokens securely and encrypted

### Error Handling

- **Graceful Degradation**: Handle API failures gracefully
- **User Feedback**: Provide clear error messages to users
- **Retry Logic**: Implement retry with exponential backoff for API calls

### Performance

- **Cache Responses**: Cache Global Embed data when possible
- **Minimize API Calls**: Batch operations when possible
- **Async Operations**: Use async/await for better performance

### Monitoring

- **Log Events**: Log all important events (installations, updates, errors)
- **Health Checks**: Implement health check endpoints
- **Metrics**: Track usage and performance metrics

## Troubleshooting

### Common Issues

1. **Script Not Appearing**: Check that the Global Embed status is "active"
2. **Wrong Placement**: Verify the placement is set to "head" for GA scripts
3. **Authentication Errors**: Ensure you're using the correct company token
4. **Cache Issues**: Global Embeds are cached; changes may take a few minutes to appear

### Debug Steps

1. Check the company's Global Embeds via API:

curl \-X GET "https://api.fluid.app/api/global\_embeds" \\
 \-H "Authorization: Bearer dit\_xyz789..."

2. Verify the script content is correct
3. Check browser developer tools for any JavaScript errors
4. Test with a simple script first before adding complex tracking

## Conclusion

This guide provides everything needed to create a Google Analytics droplet for Fluid. The key concepts are:

- **Global Embeds** automatically inject scripts into all public theme pages
- **Droplet Installation** provides company-specific authentication tokens
- **API Integration** allows programmatic management of Global Embeds
- **Simple Interface** makes it easy for non-technical users to configure tracking

By following this guide, you can create a professional, user-friendly droplet that seamlessly integrates Google Analytics with Fluid websites.