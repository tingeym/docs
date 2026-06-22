# Source: https://docs.fluid.app/docs/guides/headless-commerce

# Headless Commerce Integration

## Introduction

Fluid's Headless Commerce API enables direct sales and multi-level marketing organizations to implement custom checkout experiences. Our API is built specifically for direct sales operations, supporting key functionality like downline placement, distributor enrollments, and attribute management.

This guide demonstrates how to implement a complete checkout flow using Fluid's APIs. We'll cover the core operations: listing products, creating and managing carts, handling enrollments, and processing orders. The APIs follow RESTful principles, with examples provided in multiple programming languages.

The following sections detail each step of the integration process, starting with authentication and moving through the complete checkout lifecycle. Each section includes API specifications, example requests, and common implementation considerations.

## Authentication

Before interacting with Fluid's Commerce APIs, you'll need to obtain API credentials. Fluid supports both company tokens and partner tokens for API authentication. Partner tokens are recommended for third-party integrations as they can be managed programmatically and support expiration dates.

The product list endpoint and cart creation endpoint require authentication using these credentials. However, once you've created a cart, you'll receive a secure cart token that can be safely used on the client side to complete the checkout experience.

This two-tier authentication approach allows you to keep your API credentials secure on your server while still enabling rich client-side commerce experiences. The cart token is specifically scoped to a single cart instance, providing a secure way to handle checkout operations from public-facing applications.

For detailed instructions on obtaining and managing API credentials, please refer to our [Authentication Guide](https://docs.fluid.app/./authentication-guide.md).

---

## Listing Products

The Product API provides access to your organization's complete product catalog. While Fluid hosts the catalog and provides a default catalog browsing experience, you can also build custom catalog interfaces using these endpoints.

The Product API supports filtering, pagination, and search functionality, allowing you to create tailored browsing experiences. Common use cases include:

1. Building custom category pages with filtered product views
2. Creating searchable product catalogs with specific attribute filtering
3. Displaying product details with real-time pricing and availability

#### Request to list products:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/catalog-v1.yaml' in docs/guides/headless-commerce.md

_any_

### Products List API Reference

For more detailed information about product filtering, search parameters, and response schemas, see the [Product API Reference](https://docs.fluid.app/docs/openapi/catalog-v1).

Now that you can list products, make note of a `variant_id` you'd like to use in the following cart creation steps.

---

## Creating a Cart

To begin the checkout process, you'll need to create a cart using a product variant ID. The cart serves as the foundation for the entire checkout experience and generates a secure cart token that you'll use for subsequent operations.

#### Request to create a basic cart:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

The cart token you receive (`CT-SECURE_TOKEN_XYZ` in this example) is crucial - you'll use it for all future cart operations, including adding items, updating quantities, and proceeding to checkout. This token can be safely used in client-side applications, unlike your API credentials.

Remember to store this cart token securely in your application's state, as you'll need it for all subsequent cart operations.

For additional cart creation options, including bulk item addition and special cart attributes, refer to the [Cart API Reference](https://docs.fluid.app/docs/openapi/carts-v0).

---

## Retrieving a Cart

While each cart update operation returns the current cart state, you can also retrieve the cart's latest status at any time using the cart token.

#### Request to retrieve cart:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

This endpoint is useful when returning to an existing cart session or verifying the current state of prices, taxes, and other calculated values.

---

## Updating Your Cart

The cart update process involves several key operations that can be performed in any order to prepare for checkout. Each operation uses the secure cart token obtained during cart creation, and you can update these components as needed until you have a valid cart for checkout. We will cover a basic checkout flow here, but you can also perform these operations individually to update different aspects of the cart.

### Modifying Cart Items

Once you have a cart, you can add items to it using the cart items endpoint. Each item requires a product variant ID and quantity, with optional fields for subscription status and tracking information.

#### Request to add items to cart:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

For bulk operations or additional cart item modifications, refer to the [Cart Items API Reference](https://docs.fluid.app/docs/openapi/carts-v0).

---

### Adding Customer Email

The cart requires a customer email address for order notifications and account management. You can add or update the email using the cart update endpoint.

#### Request to add email:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

For additional cart update options, refer to the [Cart API Reference](https://docs.fluid.app/docs/openapi/carts-v0).

---

### Adding Address Information

Before proceeding to shipping options, you'll need to provide a shipping address for the cart. The address endpoint accepts standard shipping address fields and validates the provided information.

#### Request to add address:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

For additional address management options, refer to the [Cart Address API Reference](https://docs.fluid.app/docs/openapi/carts-v0).

---

### Retrieving Shipping Options

Once you've added a shipping address to the cart, the system automatically calculates available shipping options based on various factors including:

- Destination country and region
- Cart contents and weight
- Customer type (retail vs. wholesale)
- Special handling requirements

These options are returned in the cart response under `available_shipping_options`:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

You don't need to explicitly request shipping options - they are automatically included in the cart object whenever you retrieve or update the cart after setting an address. The options will update automatically if you change the shipping address or modify cart contents that affect shipping calculations.

For detailed shipping option specifications and business rules, refer to the [Shipping API Reference](https://docs.fluid.app/docs/openapi/carts-v0).

---

### Selecting Shipping Method

Once you have retrieved the available shipping options, you can select one for your cart using the shipping method endpoint. This will update the cart's totals to reflect the chosen shipping cost.

#### Request to select shipping method:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/carts-v0.yaml' in docs/guides/headless-commerce.md

_any_

After setting the shipping method, the cart's totals will automatically update to include the selected shipping cost. You can verify the selected method and updated totals by retrieving the cart.

## Adding Payment Information

Programmatic card tokenization is not covered by this guide. Payment collection for headless integrations is handled by Fluid's hosted checkout experience, which supports vaulted card payments, Apple Pay, and Google Pay. For applications that need to vault cards programmatically, use the customer payment-method endpoints in the Checkout v2026-04 API.

### Special Considerations

Each cart update operation returns a "cart" object containing the latest state, including recalculated:

- Tax calculations based on shipping address
- Shipping costs based on selected method
- Any applicable discounts or promotions
- Updated totals and line items

While the cart state is returned with each modification, you can still fetch the current cart state at any time using:

Incorrect OpenAPI file path

This can be useful when returning to an existing session or verifying the cart state independently of updates.

### Additional Update Options

Depending on your business needs, you might also need to handle:

- Enrollment information
- Custom attributes

For detailed information about each update operation and their specific requirements, please refer to:

- [Address Management API Reference](https://docs.fluid.app/docs/openapi/carts-v0)
- [Shipping API Reference](https://docs.fluid.app/docs/openapi/carts-v0)
- [Payment API Reference](https://docs.fluid.app/docs/openapi/payments-v0)
- [Enrollment API Reference](https://docs.fluid.app/docs/openapi/admin-v0)

## Completing Checkout

The final step in the commerce flow transforms your cart into an order. This process finalizes all selections, processes the payment, and creates an immutable order record. Once completed, the cart is closed and can no longer be modified.

#### Request to complete checkout:

Incorrect OpenAPI file path

#### Sample response:

Problem in json-schema tag

Can't resolve $ref: ENOENT: no such file or directory, open '/home/child\_process/data/repos/org01jcrxscgvzbzxvhqkrxftrx2r/prj01jcrxt586gwy2wr0212q5mwnx/docs/openapi/checkout-v2026-04.yaml' in docs/guides/headless-commerce.md

_any_

A few important considerations about the checkout completion process:

1. The system performs final validation checks before processing:
 
 - Verifies all required information is present
 - Confirms product availability
 - Validates payment method
 - Checks for any business rules or restrictions
2. If any validation fails, you'll receive a specific error response indicating what needs to be corrected before trying again.
 
3. Once an order is created, you can retrieve its status and details using the Order API endpoints.
 
4. The original cart token becomes invalid after successful checkout completion.
 

For detailed information about order management, tracking, and post-purchase operations, please refer to the [Order API Reference](https://docs.fluid.app/docs/openapi/admin-v0).

This concludes the core commerce flow. Your implementation may include additional features depending on your business requirements, such as:

- Order confirmation emails
- Commission calculations for distributors
- Inventory updates
- Shipping label generation

Each of these features has its own dedicated API endpoints and documentation.

## Conclusion

The Fluid Headless Commerce API provides a structured flow from product selection to order completion, with specialized support for direct sales operations. We've covered the essential steps: authentication, product listing, cart creation, information gathering, and checkout completion.

The API architecture supports the unique requirements of direct sales, including distributor relationships and enrollment scenarios, while maintaining flexibility for your specific business needs.

For complete API specifications, including request/response schemas and detailed examples, refer to our OpenAPI documentation:

https://docs.fluid.app/docs/openapi/admin\-v0

Our developer support team is available through the support portal if you need assistance during implementation.