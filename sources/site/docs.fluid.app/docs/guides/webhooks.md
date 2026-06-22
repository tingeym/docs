# Source: https://docs.fluid.app/docs/guides/webhooks

# Webhooks Guide

Welcome to the comprehensive guide for using webhooks in the Fluid platform. Webhooks provide a powerful way to receive real-time notifications when events occur in your Fluid commerce environment, enabling you to build responsive integrations and automate business processes.

## Introduction to Webhooks in Fluid

### What are Webhooks?

Webhooks are HTTP callbacks that Fluid sends to your application when specific events occur. Instead of continuously polling our API for changes, webhooks allow your system to receive instant notifications, making your integrations more efficient and responsive.

### Why Use Webhooks?

- **Real-time Updates**: Receive instant notifications when events occur
- **Reduced API Calls**: Eliminate the need for constant polling
- **Automated Workflows**: Trigger business processes automatically
- **Better User Experience**: Provide immediate feedback to your customers
- **Resource Efficiency**: Lower server load and improved performance

### Common Use Cases

- **Order Processing**: Automatically fulfill orders, send confirmation emails, or update inventory
- **Customer Management**: Sync customer data with CRM systems or trigger marketing campaigns
- **Inventory Tracking**: Update stock levels across multiple sales channels
- **Analytics**: Feed real-time data into business intelligence tools
- **Notifications**: Send SMS, email, or push notifications to customers and staff

### How Webhooks Work in Fluid

Event SourceFluid PlatformWebhook EndpointYour ApplicationEvent occurs (e.g., order created)Process eventHTTP POST with payloadProcess webhook dataHTTP 200 responseConfirm receiptEvent SourceFluid PlatformWebhook EndpointYour Application

When an event occurs in your Fluid environment (such as a new order being created), the platform automatically sends an HTTP request to your configured webhook endpoint with relevant data about the event.

## Creating and Configuring Webhooks

### Setting Up Webhooks via API

Webhooks are managed through the Fluid Company API. Here's how to create and configure them:

#### Creating a Webhook

curl \-X POST "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "resource": "order",
 "event": "created",
 "url": "https://your-app.com/webhooks/orders"
 }'

#### Required Parameters

- **resource**: The type of resource to monitor (e.g., "order", "cart", "customer")
- **event**: The specific event to listen for (e.g., "created", "updated", "completed")
- **url**: Your endpoint URL that will receive the webhook calls

#### Optional Parameters

- **http\_method**: HTTP method to use (default: "post")
- **auth\_token**: Custom authentication token for webhook verification
- **synchronous**: Whether to call webhook synchronously (default: false)

#### Response

{
 "webhook": {
 "id": 123,
 "company\_id": 1,
 "resource": "order",
 "event": "created",
 "event\_identifier": "order\_created",
 "url": "https://your-app.com/webhooks/orders",
 "http\_method": "post",
 "active": true,
 "auth\_token": "abc123def456",
 "created\_at": "2024-01-01T00:00:00Z",
 "updated\_at": "2024-01-01T00:00:00Z"
 }
}

### Managing Existing Webhooks

#### List All Webhooks

curl \-X GET "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN"

#### Update a Webhook

curl \-X PUT "https://api.fluid.app/api/company/webhooks/123" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "webhook": {
 "url": "https://your-app.com/new-webhook-endpoint",
 "auth\_token": "new-auth-token"
 }
 }'

#### Delete a Webhook

curl \-X DELETE "https://api.fluid.app/api/company/webhooks/123" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN"

### Configuration Best Practices

- **Use HTTPS**: Always use secure HTTPS endpoints for webhook URLs
- **Validate URLs**: Ensure your webhook endpoints are accessible and return proper HTTP status codes
- **Set Timeouts**: Configure appropriate timeout values for your webhook endpoints
- **Handle Failures**: Implement proper error handling and retry logic

## Available Webhook Events

Fluid supports webhooks for multiple resource types. Each resource has specific events that can trigger webhook calls.

### Complete Event Reference

Webhook Events

Commerce Events

Customer Events

System Events

Cart Events

Order Events

Product Events

Subscription Events

cart\_updated

cart\_abandoned

cart\_update\_address

cart\_update\_cart\_email

cart\_add\_items

cart\_remove\_items

order\_created

order\_updated

order\_completed

order\_shipped

order\_cancelled

order\_refunded

product\_created

product\_updated

product\_destroyed

subscription\_started

subscription\_paused

subscription\_cancelled

customer\_created

customer\_updated

### Commerce Events

#### Cart Events

- **cart\_updated**: Triggered when cart contents or details are modified. Uses CartBlueprinter with :for\_webhook\_payload view
- **cart\_abandoned**: Fired when a cart is considered abandoned based on company settings
- **cart\_update\_address**: Called when shipping or billing address is updated
- **cart\_update\_cart\_email**: Triggered when the cart email is changed
- **cart\_add\_items**: Fired when items are added to the cart
- **cart\_remove\_items**: Called when items are removed from the cart

#### Order Events

- **order\_created**: Triggered when a new order is placed. Uses OrderBlueprinter with :for\_webhook\_payload view
- **order\_updated**: Fired when order details are modified. Uses OrderBlueprinter with :for\_webhook\_payload view
- **order\_completed**: Called when an order is marked as delivered/completed
- **order\_shipped**: Triggered when an order is marked as shipped
- **order\_cancelled**: Fired when an order is cancelled
- **order\_refunded**: Called when a refund is processed for an order

#### Product Events

- **product\_created**: Triggered when a new product is added. Uses default Product serialization
- **product\_updated**: Fired when product details are modified. Uses default Product serialization
- **product\_destroyed**: Called when a product is deleted. Uses default Product serialization

#### Subscription Events

- **subscription\_started**: Triggered when a new subscription begins. Uses OrderSerializer with custom\_key "subscription\_order"
- **subscription\_paused**: Fired when a subscription is paused. Uses OrderSerializer with custom\_key "subscription\_order"
- **subscription\_cancelled**: Called when a subscription is cancelled. Uses OrderSerializer with custom\_key "subscription\_order"

### Customer & Contact Events

- **customer\_created**: New customer account created
- **customer\_updated**: Customer information modified
- **contact\_created**: New contact/lead added
- **contact\_updated**: Contact information updated

### System Events

- **user\_created**: New user account created
- **user\_updated**: User information modified
- **user\_deactivated**: User account deactivated
- **enrollment\_completed**: User enrollment process finished
- **event\_created**: Custom event created
- **event\_updated**: Custom event modified
- **event\_deleted**: Custom event removed

### Integration Events

- **droplet\_installed**: Droplet/app installed
- **droplet\_uninstalled**: Droplet/app removed
- **bot\_message\_created**: Bot message sent
- **popup\_submitted**: Popup form submitted
- **webchat\_submitted**: Web chat form submitted

### Event Naming Convention

All webhook events follow the pattern: `{resource}_{event}`

- Resource names are singular (e.g., "order", not "orders")
- Event names use past tense (e.g., "created", not "create")
- Multiple words are separated by underscores

## Sample Payloads

### Order Event Payloads

When order events are triggered, Fluid sends detailed information about the order. Here are examples of the JSON payloads you'll receive:

#### Order Created/Updated Payload

{
 "id": "whe\_1234567890abcdef",
 "identifier": "whe\_unique\_identifier",
 "name": "order\_created",
 "payload": {
 "event\_name": "order\_created",
 "company\_id": 1,
 "resource\_name": "Commerce::Order",
 "resource": "order",
 "event": "created",
 "order": {
 "subtotal": 99.99,
 "tax": 8.50,
 "discount": 10.00,
 "shipping": 5.99,
 "external\_id": "ext\_order\_123",
 "metadata": {
 "source": "web",
 "campaign": "summer\_sale"
 },
 "discount\_codes": \["SUMMER10", "WELCOME5"\],
 "order\_class": "customer\_order",
 "rep": {
 "id": 456,
 "external\_id": "rep\_789"
 },
 "items": \[
 {
 "id": 1,
 "product\_id": 100,
 "variant\_id": 200,
 "quantity": 2,
 "price": 49.99,
 "total": 99.98,
 "sku": "PROD-001",
 "title": "Premium Widget",
 "variant\_title": "Blue - Large"
 }
 \],
 "ship\_to": {
 "id": 301,
 "first\_name": "John",
 "last\_name": "Doe",
 "address1": "123 Main St",
 "address2": "Apt 4B",
 "city": "New York",
 "state": "NY",
 "zip": "10001",
 "country": "US",
 "phone": "+1-555-123-4567"
 },
 "bill\_to": {
 "id": 302,
 "first\_name": "John",
 "last\_name": "Doe",
 "address1": "123 Main St",
 "city": "New York",
 "state": "NY",
 "zip": "10001",
 "country": "US"
 }
 }
 },
 "timestamp": "2024-01-01T12:00:00Z"
}

#### Order Status Change Payloads

When an order status changes, specific event identifiers are used:

**Order Shipped** (`order_shipped`):

{
 "payload": {
 "event\_name": "order\_shipped",
 "order": {
 "id": 12345,
 "status": "shipped",
 "tracking\_number": "1Z999AA1234567890",
 "shipped\_at": "2024-01-02T10:30:00Z"
 }
 }
}

**Order Completed** (`order_completed`):

{
 "payload": {
 "event\_name": "order\_completed",
 "order": {
 "id": 12345,
 "status": "delivered",
 "delivered\_at": "2024-01-05T14:20:00Z"
 }
 }
}

**Order Cancelled** (`order_cancelled`):

{
 "payload": {
 "event\_name": "order\_cancelled",
 "order": {
 "id": 12345,
 "status": "cancelled",
 "cancelled\_at": "2024-01-01T15:45:00Z",
 "cancellation\_reason": "Customer request"
 }
 }
}

### Cart Event Payloads

Cart webhooks provide information about shopping cart changes based on the CartBlueprinter :for\_webhook\_payload view:

#### Cart Updated Payload

{
 "id": "whe\_1234567890abcdef",
 "identifier": "whe\_unique\_identifier",
 "name": "cart\_updated",
 "payload": {
 "event\_name": "cart\_updated",
 "company\_id": 1,
 "resource\_name": "Commerce::Cart",
 "resource": "cart",
 "event": "updated",
 "data": {
 "id": 67890,
 "state": "start",
 "payment\_method": null,
 "amount\_total": 170.72,
 "sub\_total": 149.98,
 "tax\_total": 12.75,
 "shipping\_total": 7.99,
 "discount\_total": 0.00,
 "currency\_code": "USD",
 "email": "customer@example.com",
 "first\_name": "John",
 "last\_name": "Doe",
 "phone": "+1-555-123-4567",
 "items": \[
 {
 "id": 1,
 "title": "Premium Widget",
 "quantity": 2,
 "price": 74.99,
 "total": 149.98,
 "sku": "WIDGET-001",
 "tax": 6.38,
 "cv": 50.0,
 "qv": 40.0,
 "product": {
 "id": 100,
 "title": "Premium Widget",
 "sku": "WIDGET-001",
 "price": 74.99
 },
 "variant": {
 "id": 200,
 "title": "Blue - Large",
 "sku": "WIDGET-001-BL"
 }
 }
 \],
 "ship\_to": {
 "id": 301,
 "first\_name": "John",
 "last\_name": "Doe",
 "address1": "123 Main St",
 "city": "New York",
 "state": "NY",
 "zip": "10001",
 "country": "US"
 },
 "bill\_to": {
 "id": 302,
 "first\_name": "John",
 "last\_name": "Doe",
 "address1": "123 Main St",
 "city": "New York",
 "state": "NY",
 "zip": "10001",
 "country": "US"
 }
 }
 },
 "timestamp": "2024-01-01T11:30:00Z"
}

#### Cart Abandoned Payload

{
 "payload": {
 "event\_name": "cart\_abandoned",
 "data": {
 "id": 67890,
 "email": "customer@example.com",
 "amount\_total": 170.72,
 "items\_count": 2,
 "abandoned\_at": "2024-01-01T11:30:00Z"
 }
 }
}

#### Cart Add Items Payload

{
 "payload": {
 "event\_name": "cart\_add\_items",
 "data": {
 "id": 67890,
 "items\_added": \[
 {
 "id": 2,
 "title": "New Product",
 "quantity": 1,
 "price": 25.00,
 "sku": "NEW-001"
 }
 \],
 "amount\_total": 195.72,
 "items\_count": 3
 }
 }
}

#### Cart Remove Items Payload

{
 "payload": {
 "event\_name": "cart\_remove\_items",
 "data": {
 "id": 67890,
 "items\_removed": \[
 {
 "id": 1,
 "title": "Removed Product",
 "quantity": 1,
 "sku": "REM-001"
 }
 \],
 "amount\_total": 145.72,
 "items\_count": 2
 }
 }
}

### Customer Event Payloads

Customer webhooks include comprehensive customer profile information based on the CustomerBlueprinter:

#### Customer Created Payload

{
 "id": "whe\_1234567890abcdef",
 "identifier": "whe\_unique\_identifier",
 "name": "customer\_created",
 "payload": {
 "event\_name": "customer\_created",
 "company\_id": 1,
 "resource\_name": "Customer",
 "resource": "customer",
 "event": "created",
 "data": {
 "id": 789,
 "email": "newcustomer@example.com",
 "first\_name": "Jane",
 "last\_name": "Smith",
 "phone": "+1-555-987-6543",
 "orders\_count": 0,
 "total\_spent": 0.0,
 "subscription\_count": 0,
 "is\_rep": false,
 "created\_at": "2024-01-01T09:15:00Z",
 "updated\_at": "2024-01-01T09:15:00Z",
 "addresses": \[
 {
 "id": 401,
 "first\_name": "Jane",
 "last\_name": "Smith",
 "address1": "456 Oak Ave",
 "city": "Los Angeles",
 "state": "CA",
 "zip": "90210",
 "country": "US",
 "phone": "+1-555-987-6543"
 }
 \],
 "customer\_notes": \[\],
 "payment\_methods": \[\]
 }
 },
 "timestamp": "2024-01-01T09:15:00Z"
}

#### Customer Updated Payload

{
 "payload": {
 "event\_name": "customer\_updated",
 "data": {
 "id": 789,
 "email": "jane.smith.updated@example.com",
 "first\_name": "Jane",
 "last\_name": "Smith-Johnson",
 "phone": "+1-555-987-6543",
 "orders\_count": 3,
 "total\_spent": 299.97,
 "subscription\_count": 1,
 "updated\_at": "2024-01-02T14:30:00Z"
 }
 }
}

### Subscription Event Payloads

Subscription webhooks provide information about subscription lifecycle events using OrderSerializer with custom\_key "subscription\_order":

#### Subscription Started Payload

{
 "id": "whe\_1234567890abcdef",
 "identifier": "whe\_unique\_identifier",
 "name": "subscription\_started",
 "payload": {
 "event\_name": "subscription\_started",
 "company\_id": 1,
 "resource\_name": "SubscriptionOrder",
 "resource": "subscription",
 "event": "started",
 "subscription\_order": {
 "id": 12345,
 "status": "active",
 "next\_bill\_date": "2024-02-01T00:00:00Z",
 "last\_bill\_date": null,
 "next\_ship\_date": "2024-02-01T00:00:00Z",
 "last\_ship\_date": null,
 "quantity": 1,
 "price": 29.99,
 "original\_price": 34.99,
 "attempts": 0,
 "skipped\_count": 0,
 "max\_skips": 3,
 "trial\_ends\_at": "2024-01-15T00:00:00Z",
 "in\_trial": true,
 "disabled": false,
 "notes": "Premium subscription for customer",
 "created\_at": "2024-01-01T12:00:00Z",
 "updated\_at": "2024-01-01T12:00:00Z",
 "subscription\_plan": {
 "id": 10,
 "name": "Monthly Premium",
 "billing\_interval": 1,
 "billing\_interval\_unit": "month",
 "price\_adjustment\_amount": 5.00,
 "price\_adjustment\_type": "discount"
 },
 "customer": {
 "id": 789,
 "email": "customer@example.com",
 "first\_name": "John",
 "last\_name": "Doe"
 },
 "variant": {
 "id": 200,
 "title": "Premium Subscription",
 "sku": "SUB-PREM-001"
 },
 "address": {
 "id": 301,
 "first\_name": "John",
 "last\_name": "Doe",
 "address1": "123 Main St",
 "city": "New York",
 "state": "NY",
 "zip": "10001",
 "country": "US"
 }
 }
 },
 "timestamp": "2024-01-01T12:00:00Z"
}

#### Subscription Paused Payload

{
 "payload": {
 "event\_name": "subscription\_paused",
 "subscription\_order": {
 "id": 12345,
 "status": "paused",
 "next\_bill\_date": "2024-03-01T00:00:00Z",
 "last\_bill\_date": "2024-01-01T00:00:00Z",
 "paused\_at": "2024-01-15T10:30:00Z",
 "updated\_at": "2024-01-15T10:30:00Z"
 }
 }
}

#### Subscription Cancelled Payload

{
 "payload": {
 "event\_name": "subscription\_cancelled",
 "subscription\_order": {
 "id": 12345,
 "status": "cancelled",
 "cancelled\_at": "2024-01-20T16:45:00Z",
 "updated\_at": "2024-01-20T16:45:00Z",
 "cancellation\_reason": "customer\_request"
 }
 }
}

### Product Event Payloads

Product webhooks provide information about product lifecycle events using default Product serialization:

#### Product Created Payload

{
 "id": "whe\_1234567890abcdef",
 "identifier": "whe\_unique\_identifier",
 "name": "product\_created",
 "payload": {
 "event\_name": "product\_created",
 "company\_id": 1,
 "resource\_name": "Product",
 "resource": "product",
 "event": "created",
 "data": {
 "id": 100,
 "title": "Premium Widget",
 "description": "High-quality widget for premium customers",
 "introduction": "Introducing our latest premium widget",
 "feature\_text": "Advanced features for professional use",
 "slug": "premium-widget",
 "status": "active",
 "sku": "WIDGET-001",
 "upc": "123456789012",
 "hs\_code": "8543709099",
 "image\_url": "https://cdn.fluid.app/products/widget.jpg",
 "compressed\_image\_url": "https://cdn.fluid.app/products/widget\_compressed.jpg",
 "image\_path": "/products/widget.jpg",
 "external\_url": "https://example.com/widget",
 "tax\_category\_id": 1,
 "public": true,
 "in\_stock": true,
 "keep\_selling": true,
 "track\_quantity": true,
 "no\_index": false,
 "commission": 10.0,
 "affiliate\_commission": 5.0,
 "integration\_id": "shopify\_123",
 "external\_id": "ext\_prod\_123",
 "last\_synced\_at": "2024-01-01T10:00:00Z",
 "metadata": {
 "source": "manual",
 "category": "widgets"
 },
 "publish\_to\_retail\_store": true,
 "publish\_to\_mobile\_store": true,
 "publish\_to\_share\_tab": true,
 "show\_reviews": true,
 "international\_tax\_type": "standard",
 "created\_at": "2024-01-01T10:00:00Z",
 "updated\_at": "2024-01-01T10:00:00Z",
 "category": {
 "id": 5,
 "title": "Widgets",
 "description": "Premium widget collection",
 "image\_url": "https://cdn.fluid.app/categories/widgets.jpg",
 "position": 1,
 "public": true
 },
 "collections": \[
 {
 "id": 10,
 "title": "Premium Collection",
 "description": "Our premium product line",
 "position": 1,
 "public": true
 }
 \],
 "tags": \[
 {
 "id": 1,
 "name": "premium"
 },
 {
 "id": 2,
 "name": "widget"
 }
 \],
 "label": {
 "id": 1,
 "title": "New",
 "background": "#ff0000",
 "icon": "star",
 "color": "#ffffff"
 },
 "images": \[
 {
 "id": 1,
 "image\_url": "https://cdn.fluid.app/products/widget\_1.jpg",
 "image\_path": "/products/widget\_1.jpg",
 "position": 1
 }
 \],
 "variants": \[
 {
 "id": 200,
 "title": "Blue - Large",
 "sku": "WIDGET-001-BL",
 "price": 74.99,
 "position": 1,
 "is\_master": true
 }
 \]
 }
 },
 "timestamp": "2024-01-01T10:00:00Z"
}

#### Product Updated Payload

{
 "payload": {
 "event\_name": "product\_updated",
 "data": {
 "id": 100,
 "title": "Premium Widget - Updated",
 "description": "Updated high-quality widget for premium customers",
 "price": 79.99,
 "status": "active",
 "updated\_at": "2024-01-02T15:30:00Z"
 }
 }
}

#### Product Destroyed Payload

{
 "payload": {
 "event\_name": "product\_destroyed",
 "data": {
 "id": 100,
 "title": "Premium Widget",
 "sku": "WIDGET-001",
 "status": "discarded",
 "destroyed\_at": "2024-01-03T09:15:00Z"
 }
 }
}

### Understanding Payload Structure

All webhook payloads follow a consistent structure:

- **id**: Unique webhook event ID
- **identifier**: Unique event identifier for tracking
- **name**: Event name (matches event\_identifier)
- **payload**: Contains the actual event data
 - **event\_name**: The specific event that occurred
 - **company\_id**: Your company ID
 - **resource\_name**: Full class name of the resource
 - **resource**: Resource type (order, cart, customer, etc.)
 - **event**: Event type (created, updated, etc.)
 - **\[resource\]**: The actual resource data
- **timestamp**: ISO 8601 timestamp when the event occurred

## Authentication and Security

### Webhook Authentication

Fluid uses token-based authentication to secure webhook calls. Every webhook request includes authentication headers that you should verify to ensure the request is legitimate.

### Authentication Headers

Each webhook request includes the following headers:

POST /your-webhook-endpoint HTTP/1.1
Host: your-app.com
Content-Type: application/json
AUTH\_TOKEN: your\_webhook\_auth\_token
X-Fluid-Shop: your-company-subdomain

#### Header Descriptions

- **AUTH\_TOKEN**: Your webhook verification token
- **X-Fluid-Shop**: Your company's Fluid subdomain identifier
- **Content-Type**: Always `application/json`

### Verification Methods

#### Method 1: Token Verification

Compare the `AUTH_TOKEN` header with your stored webhook token:

require 'openssl'

def verify\_webhook(request)
 received\_token \= request.headers\['AUTH\_TOKEN'\]
 expected\_token \= 'your\_stored\_webhook\_token'
 
 \# Use secure comparison to prevent timing attacks
 ActiveSupport::SecurityUtils.secure\_compare(received\_token, expected\_token)
end

#### Method 2: Company Verification

Verify the `X-Fluid-Shop` header matches your expected company:

def verify\_company(request)
 received\_shop \= request.headers\['X-Fluid-Shop'\]
 expected\_shop \= 'your-company-subdomain'
 
 received\_shop \== expected\_shop
end

### Token Management

#### Retrieving Your Auth Token

Your webhook auth token is returned when you create a webhook:

curl \-X POST "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "resource": "order",
 "event": "created",
 "url": "https://your-app.com/webhooks/orders"
 }'

Response includes the `auth_token`:

{
 "webhook": {
 "id": 123,
 "auth\_token": "abc123def456",
 ...
 }
}

#### Custom Auth Tokens

You can specify a custom auth token when creating webhooks:

curl \-X POST "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "resource": "order",
 "event": "created",
 "url": "https://your-app.com/webhooks/orders",
 "auth\_token": "your\_custom\_token\_here"
 }'

#### Rotating Tokens

Update your webhook's auth token periodically for security:

curl \-X PUT "https://api.fluid.app/api/company/webhooks/123" \\
 \-H "Authorization: Bearer YOUR\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "webhook": {
 "auth\_token": "new\_secure\_token\_here"
 }
 }'

### Security Best Practices

#### Endpoint Security

1. **Use HTTPS Only**: Never use HTTP endpoints for webhooks
2. **Validate All Requests**: Always verify the AUTH\_TOKEN header
3. **Implement Rate Limiting**: Protect against potential abuse
4. **Log Webhook Calls**: Monitor for suspicious activity

#### Token Security

1. **Store Tokens Securely**: Use environment variables or secure key management
2. **Rotate Regularly**: Change tokens periodically
3. **Use Strong Tokens**: Generate cryptographically secure random tokens
4. **Limit Token Scope**: Use different tokens for different webhook types if possible

#### Example Secure Webhook Handler

class WebhooksController < ApplicationController
 skip\_before\_action :verify\_authenticity\_token
 before\_action :verify\_webhook\_auth
 
 def orders
 payload \= JSON.parse(request.body.read)
 process\_order\_event(payload)
 
 head :ok
 rescue JSON::ParserError
 head :bad\_request
 end
 
 private
 
 def verify\_webhook\_auth
 received\_token \= request.headers\['AUTH\_TOKEN'\]
 expected\_token \= ENV\['WEBHOOK\_AUTH\_TOKEN'\]
 received\_shop \= request.headers\['X-Fluid-Shop'\]
 expected\_shop \= ENV\['COMPANY\_SUBDOMAIN'\]
 
 unless received\_token && expected\_token
 head :unauthorized and return
 end
 
 unless received\_shop \== expected\_shop
 head :unauthorized and return
 end
 
 unless ActiveSupport::SecurityUtils.secure\_compare(received\_token, expected\_token)
 head :unauthorized and return
 end
 end
 
 def process\_order\_event(payload)
 event\_name \= payload.dig('payload', 'event\_name')
 order\_data \= payload.dig('payload', 'order')
 
 case event\_name
 when 'order\_created'
 handle\_new\_order(order\_data)
 when 'order\_shipped'
 handle\_order\_shipped(order\_data)
 \# ... handle other events
 end
 end
 
 def handle\_new\_order(order\_data)
 \# Process new order
 Rails.logger.info "Processing new order: #{order\_data\['id'\]}"
 end
 
 def handle\_order\_shipped(order\_data)
 \# Process shipped order
 Rails.logger.info "Order shipped: #{order\_data\['id'\]}"
 end
end

### IP Allowlisting

For additional security, you may want to restrict webhook calls to specific IP addresses. Contact Fluid support for current webhook IP ranges if you need to implement IP allowlisting.

## Best Practices

### Implementation Patterns

#### Idempotency

Webhooks may be delivered more than once. Implement idempotency to handle duplicate events:

class WebhookProcessor
 def self.handle\_webhook(payload)
 event\_id \= payload\['id'\]
 
 \# Check if we've already processed this event
 return { status: 'already\_processed' } if already\_processed?(event\_id)
 
 \# Process the event
 result \= process\_event(payload)
 
 \# Mark as processed
 mark\_as\_processed(event\_id)
 
 result
 end
 
 private
 
 def self.already\_processed?(event\_id)
 ProcessedWebhook.exists?(event\_id: event\_id)
 end
 
 def self.mark\_as\_processed(event\_id)
 ProcessedWebhook.create!(event\_id: event\_id, processed\_at: Time.current)
 end
 
 def self.process\_event(payload)
 \# Event processing logic here
 { status: 'success' }
 end
end

#### Asynchronous Processing

Process webhooks asynchronously to avoid timeouts:

class WebhooksController < ApplicationController
 def receive\_webhook
 payload \= JSON.parse(request.body.read)
 
 \# Queue for background processing
 WebhookProcessorJob.perform\_later(payload)
 
 head :ok
 end
end

class WebhookProcessorJob < ApplicationJob
 queue\_as :webhooks
 
 def perform(payload)
 \# Heavy processing here
 process\_order\_fulfillment(payload)
 end
 
 private
 
 def process\_order\_fulfillment(payload)
 event\_name \= payload.dig('payload', 'event\_name')
 order\_data \= payload.dig('payload', 'order')
 
 case event\_name
 when 'order\_created'
 OrderFulfillmentService.new(order\_data).process
 when 'order\_shipped'
 ShippingNotificationService.new(order\_data).send\_notification
 end
 end
end

#### Response Handling

Always return appropriate HTTP status codes:

class WebhooksController < ApplicationController
 def webhook\_handler
 payload \= JSON.parse(request.body.read)
 
 unless validate\_payload(payload)
 head :bad\_request and return
 end
 
 process\_webhook(payload)
 head :ok
 
 rescue JSON::ParserError, ValidationError
 head :bad\_request
 rescue ProcessingError
 head :unprocessable\_entity
 rescue StandardError \=> e
 Rails.logger.error "Webhook processing error: #{e.message}"
 head :internal\_server\_error
 end
 
 private
 
 def validate\_payload(payload)
 payload.present? && payload\['payload'\].present?
 end
 
 def process\_webhook(payload)
 WebhookProcessor.handle\_webhook(payload)
 end
end

### Error Handling and Retry Strategies

#### Webhook Delivery Behavior

- Fluid will retry failed webhook deliveries
- Retries use exponential backoff
- Maximum retry attempts: 5
- Timeout per request: 10 seconds

#### Handling Failures

Implement proper error handling in your webhook endpoints:

class WebhookProcessor
 def self.process\_webhook(payload)
 \# Main processing logic
 result \= handle\_event(payload)
 Rails.logger.info "Successfully processed webhook: #{payload\['id'\]}"
 result
 
 rescue ValidationError \=> e
 Rails.logger.error "Validation error: #{e.message}"
 raise \# Re-raise to return 400 status
 
 rescue ExternalServiceError \=> e
 Rails.logger.error "External service error: #{e.message}"
 \# Don't re-raise - return success to prevent retries
 { status: 'deferred' }
 
 rescue StandardError \=> e
 Rails.logger.error "Unexpected error: #{e.message}"
 raise \# Re-raise to trigger retry
 end
 
 private
 
 def self.handle\_event(payload)
 event\_name \= payload.dig('payload', 'event\_name')
 
 case event\_name
 when 'order\_created'
 OrderProcessor.new(payload.dig('payload', 'order')).process
 when 'cart\_updated'
 CartProcessor.new(payload.dig('payload', 'cart')).process
 else
 Rails.logger.warn "Unknown event type: #{event\_name}"
 end
 
 { status: 'success' }
 end
end

#### Retry Logic

Implement your own retry logic for external service calls:

class ExternalServiceCaller
 MAX\_RETRIES \= 3
 
 def self.call\_with\_retry(data)
 (0...MAX\_RETRIES).each do |attempt|
 begin
 return external\_service\_call(data)
 rescue ExternalServiceError \=> e
 raise if attempt \== MAX\_RETRIES \- 1
 
 \# Exponential backoff with jitter
 delay \= (2 \*\* attempt) + rand
 sleep(delay)
 
 Rails.logger.warn "External service call failed (attempt #{attempt + 1}): #{e.message}"
 end
 end
 end
 
 private
 
 def self.external\_service\_call(data)
 \# Make the actual external service call
 HTTParty.post('https://external-api.com/webhook', 
 body: data.to\_json, 
 headers: { 'Content-Type' \=> 'application/json' })
 end
end

### Performance Considerations

#### Optimize Webhook Processing

1. **Keep Handlers Lightweight**: Minimize processing time in webhook handlers
2. **Use Background Jobs**: Queue heavy processing for background workers
3. **Database Optimization**: Use efficient queries and indexing
4. **Caching**: Cache frequently accessed data

#### Example Optimized Handler

class WebhooksController < ApplicationController
 def order\_webhook
 payload \= JSON.parse(request.body.read)
 
 \# Quick validation and queuing
 unless quick\_validate(payload)
 head :bad\_request and return
 end
 
 \# Queue for background processing using Sidekiq
 OrderWebhookJob.perform\_async(payload)
 
 head :ok
 end
 
 private
 
 def quick\_validate(payload)
 payload.present? && 
 payload\['payload'\].present? && 
 payload.dig('payload', 'order').present?
 end
end

\# Background worker
class OrderWebhookJob
 include Sidekiq::Job
 
 def perform(payload)
 process\_order\_webhook(payload)
 end
 
 private
 
 def process\_order\_webhook(payload)
 order\_data \= payload.dig('payload', 'order')
 event\_name \= payload.dig('payload', 'event\_name')
 
 OrderWebhookProcessor.new(order\_data, event\_name).process
 end
end

#### Monitoring and Alerting

Set up monitoring for your webhook endpoints:

class WebhooksController < ApplicationController
 around\_action :monitor\_webhook\_performance
 
 def webhook\_handler
 payload \= JSON.parse(request.body.read)
 event\_type \= payload.dig('payload', 'event\_name') || 'unknown'
 
 process\_webhook(payload)
 
 \# Log successful processing
 Rails.logger.info "Webhook processed successfully", 
 event\_type: event\_type, 
 webhook\_id: payload\['id'\]
 
 \# Increment success counter
 webhook\_counter.increment(labels: { event\_type: event\_type, status: 'success' })
 
 head :ok
 
 rescue StandardError \=> e
 \# Log error
 Rails.logger.error "Webhook processing failed", 
 event\_type: event\_type, 
 error: e.message,
 webhook\_id: payload&.dig('id')
 
 \# Increment error counter
 webhook\_counter.increment(labels: { event\_type: event\_type, status: 'error' })
 
 head :internal\_server\_error
 end
 
 private
 
 def monitor\_webhook\_performance
 start\_time \= Time.current
 yield
 ensure
 duration \= Time.current \- start\_time
 webhook\_duration\_histogram.observe(duration)
 end
 
 def webhook\_counter
 @webhook\_counter ||= Prometheus::Client.registry.counter(
 :webhook\_requests\_total, 
 docstring: 'Total webhook requests',
 labels: \[:event\_type, :status\]
 )
 end
 
 def webhook\_duration\_histogram
 @webhook\_duration\_histogram ||= Prometheus::Client.registry.histogram(
 :webhook\_processing\_seconds,
 docstring: 'Webhook processing time'
 )
 end
end

### Testing Webhooks

#### Local Development

Use tools like ngrok to expose local endpoints for testing:

\# Install ngrok
npm install \-g ngrok

\# Expose local port 3000
ngrok http 3000

\# Use the generated URL for webhook configuration
\# https://abc123.ngrok.io/webhooks/orders

#### Webhook Testing Tools

Create test endpoints to verify webhook delivery:

class WebhooksController < ApplicationController
 def test\_webhook
 payload \= JSON.parse(request.body.read)
 
 \# Log the received payload
 Rails.logger.info "Received webhook: #{JSON.pretty\_generate(payload)}"
 
 \# Store for inspection in development
 if Rails.env.development?
 filename \= "webhook\_#{Time.current.to\_i}.json"
 File.write(Rails.root.join('tmp', filename), JSON.pretty\_generate(payload))
 end
 
 head :ok
 rescue JSON::ParserError \=> e
 Rails.logger.error "Invalid JSON in webhook: #{e.message}"
 head :bad\_request
 end
end

#### Integration Testing

Test your webhook handlers with sample payloads:

require 'rails\_helper'

RSpec.describe WebhookProcessor do
 describe '.process\_webhook' do
 let(:sample\_payload) do
 {
 'payload' \=> {
 'event\_name' \=> 'order\_created',
 'order' \=> {
 'id' \=> 12345,
 'order\_number' \=> 'TEST-001',
 'amount' \=> 99.99
 }
 }
 }
 end
 
 it 'processes order created webhook successfully' do
 allow(OrderProcessor).to receive(:new).and\_return(double(process: true))
 
 result \= described\_class.process\_webhook(sample\_payload)
 
 expect(result\[:status\]).to eq('success')
 expect(OrderProcessor).to have\_received(:new).with(sample\_payload.dig('payload', 'order'))
 end
 
 it 'handles validation errors appropriately' do
 invalid\_payload \= { 'payload' \=> {} }
 
 expect { described\_class.process\_webhook(invalid\_payload) }
 .to raise\_error(ValidationError)
 end
 end
end

\# Controller test
RSpec.describe WebhooksController, type: :controller do
 describe 'POST #orders' do
 let(:valid\_payload) { { payload: { event\_name: 'order\_created', order: { id: 1 } } } }
 
 before do
 request.headers\['AUTH\_TOKEN'\] \= 'valid\_token'
 request.headers\['X-Fluid-Shop'\] \= 'test-shop'
 allow(ENV).to receive(:\[\]).with('WEBHOOK\_AUTH\_TOKEN').and\_return('valid\_token')
 allow(ENV).to receive(:\[\]).with('COMPANY\_SUBDOMAIN').and\_return('test-shop')
 end
 
 it 'processes valid webhook and returns 200' do
 post :orders, body: valid\_payload.to\_json
 expect(response).to have\_http\_status(:ok)
 end
 
 it 'returns 401 for invalid auth token' do
 request.headers\['AUTH\_TOKEN'\] \= 'invalid\_token'
 post :orders, body: valid\_payload.to\_json
 expect(response).to have\_http\_status(:unauthorized)
 end
 end
end

---

## Need Help?

- **API Documentation**: Check our [complete API reference](https://docs.fluid.app/docs/openapi/webhooks-v0)
- **Support**: Contact our support team for webhook-specific questions
- **Community**: Join our developer community for tips and best practices

Ready to implement webhooks? Start by [setting up your first webhook](https://docs.fluid.app/#creating-and-configuring-webhooks) and testing it with our sample payloads!