# Source: https://docs.fluid.app/docs/guides/inventory-management

# Leveraging the Fluid API for Real-Time Inventory Management

The Fluid API empowers businesses to automate inventory management tasks and respond promptly to stock level changes. By leveraging webhooks, you can receive real-time notifications about product stock updates.

## 1\. Authentication

Fluid uses token-based authentication for company-related endpoints. You'll need to obtain a company token from the admin settings page within the Fluid application. This token will be used in the authorization header of your API requests.

## 2\. Webhooks

To receive real-time notifications about product stock level changes, you can subscribe to the `inventory.updated` webhook event. This event will be triggered whenever a product's stock quantity is modified in the Fluid system.

## 3\. Webhook Endpoint

Set up a webhook endpoint on your server to handle incoming notifications from Fluid. This endpoint should be able to receive and parse JSON data.

## 4\. Subscribe to the Webhook

Once you have a webhook endpoint, you can use the Fluid API to subscribe to the `inventory.updated` event. The specific API call will depend on your programming language, but it will generally involve sending a POST request to the `/webhooks/subscriptions` endpoint with the following details in the request body:

{
 "url": "YOUR\_WEBHOOK\_ENDPOINT\_URL",
 "events": \["inventory.updated"\]
}

## 5\. Webhook Handler

Implement a logic on your server to handle the incoming webhook notifications from Fluid. The notification payload will contain details about the product whose stock level has changed, including the product ID, new stock quantity, and other relevant information.

## 6\. Inventory Management Actions

Based on the information received in the webhook notification, you can perform various inventory management actions. Here are a couple of examples:

- If the stock level of a product falls below a predefined threshold, you can trigger an automated inventory replenishment process. This might involve sending purchase orders to your suppliers or initiating internal stock transfers from other warehouses.
- You can maintain an up-to-date inventory display on your web application or marketplace by reflecting the changes received through webhooks.

**Additional Notes:**

- The Fluid API documentation provides more details on the structure of webhook notifications and the available events you can subscribe to.
- You can implement additional logic in your webhook handler to handle different scenarios, such as sending low-stock alerts or managing backorders.