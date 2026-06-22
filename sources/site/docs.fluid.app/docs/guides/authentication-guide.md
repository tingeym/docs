# Source: https://docs.fluid.app/docs/guides/authentication-guide

# Authentication Guide

Let's walk through the authentication methods used by the Fluid API. Understanding and implementing these methods correctly ensures secure and authorized access to your company's data.

## Authentication Methods

Fluid utilizes token-based authentication for all company-related API endpoints. We support two types of company authentication tokens:

### Company Tokens

Company tokens provide full administrative access to your company's data and settings.

### Partner Tokens

Partner tokens provide company-level API access and are ideal for third-party integrations and automated systems. Partner tokens can be managed programmatically and support expiration dates for enhanced security.

## Obtaining Your Authentication Token

### Company Token

1. **Access Admin Settings:** Navigate to the Fluid Admin Settings page: [https://www.fluid.app/settings/developer](https://www.fluid.app/settings/developer).
2. **Generate Company Token:** Locate the **Developer** section and generate a new company token.

### Partner Token

1. **Use the Partner Tokens API:** Create partner tokens programmatically using the `/api/v2025-06/partner_tokens` endpoint.
2. **Set Label and Expiration:** Provide a descriptive label and optional expiration date for better token management.

**Important Note:** Treat your authentication tokens with the utmost care. They grant access to your company's data within the Fluid API. Do not share them with unauthorized individuals and store them securely, preferably using environment variables or a dedicated configuration management tool.

## Including the Token in Requests

Once you have your authentication token (either company or partner token), include it in the `Authorization` header of every API request related to your company's data. The format for the header is:

Authorization: Bearer <your\_token\>

**Example (curl):**

curl \-X GET https://api.fluid.app/api/companies \\
 \-H "Authorization: Bearer your\_company\_token"

## Public Tokens (SDK & Embeds)

Public tokens (`pub-` prefix) are designed for client-side use in browser-based SDKs like the [DAM Picker](https://docs.fluid.app/sdk/files-sdk). They carry scoped permissions (e.g. `dam:browse`, `dam:upload`) and are safe to expose in frontend code.

### Token Expiration & Domain Allowlist

Public tokens have two security modes:

**Option A: Short-lived tokens (no domain allowlist)**

Tokens without a domain allowlist **must have an expiration date**. These are ideal when you generate a fresh token server-side on each page load. The token expires automatically, so even if leaked it has a limited window of use.

POST /api/v2025\-06/tokens/public
{
 "public\_token": {
 "name": "Session Token",
 "scopes": \["dam:browse", "dam:upload"\],
 "expires\_at": "2026-04-16T01:00:00Z"
 }
}

Use this approach when:

- You have a backend that can call the Fluid API with a private key (company or partner token)
- You want maximum security — each user session gets a unique, short-lived token
- You're building a server-rendered app that injects the token into the page

**Recommended flow:**

1. User loads your page
2. Your server creates a public token with a short expiration (e.g. 1 hour) using a private company/partner token
3. Your server passes the `pub-` token to the frontend
4. The frontend uses the token with the DAM Picker SDK
5. The token expires automatically — no cleanup needed

**Option B: Long-lived tokens (with domain allowlist)**

Tokens with a `domain_allowlist` can live **without an expiration date**. The allowlist restricts which origins can use the token, so it's safe to embed in client-side code permanently.

POST /api/v2025\-06/tokens/public
{
 "public\_token": {
 "name": "Production Picker Token",
 "scopes": \["dam:browse", "dam:upload"\],
 "domain\_allowlist": \[
 "https://mystore.com",
 "https://\*.mystore.com"
 \]
 }
}

Use this approach when:

- You want a single token that works across all page loads
- You don't have a backend to generate tokens dynamically
- You're embedding the SDK in a static site or third-party platform
- You know the exact domains where the SDK will be used

The domain allowlist supports wildcards (`*.mystore.com`) and is checked against the browser's `Origin` header. Requests from unlisted origins are rejected.

A public token **without** an expiration **and** without a domain allowlist will fail validation. You must provide at least one: `expires_at` or `domain_allowlist`.

### Scopes

Public tokens are scoped — they can only access endpoints that match their declared scopes. Available scopes:

| Scope | Access |
| --- | --- |
| `dam:browse` | Browse and search DAM assets |
| `dam:upload` | Upload files to the DAM |
| `dam:unsplash` | Search Unsplash stock photos |
| `dam:ai_generate` | Generate images with AI |
| `media:read` | Read media content |
| `products:read` | Read product data |
| `enrollments:read` | Read enrollment data |
| `playlists:read` | Read playlist data |
| `forms:read` | Read form data |

For the DAM Picker SDK, the typical scopes are `dam:browse` and `dam:upload`. Add `dam:unsplash` for stock photos and `dam:ai_generate` for AI image generation.

## Additional Considerations

- **Basic Authentication for Users:** While this guide focuses on company token-based authentication, Fluid also supports basic authentication for specific user endpoints. Refer to the official Fluid API documentation for details on these endpoints and their authentication methods.
- **Security Best Practices:** Always adhere to security best practices when interacting with the Fluid API. These include:
 - **Protecting Your Token:** Treat your company token as sensitive information.
 - **Rate Limiting:** Be mindful of Fluid's API rate limits to avoid throttling. Implement strategies to handle rate limits, like retry mechanisms with exponential backoff.
 - **Error Handling:** Properly handle errors and exceptions returned by the API for effective troubleshooting.
 - **Input and Output Validation:** Validate and sanitize all data sent to and received from the API to prevent security vulnerabilities.
- **Custom Authentication:** In specific situations, Fluid may offer custom authentication mechanisms. Contact Fluid support for further information.

## Troubleshooting

If you encounter issues with authentication, consider these steps:

- **Verify Token Validity:** Ensure your company token is active and hasn't expired.
- **Check Headers:** Double-check the `Authorization` header for proper formatting.
- **Network Configuration:** Confirm your network configuration allows outbound requests to Fluid API endpoints.
- **API Documentation:** Refer to the official Fluid API documentation for specific endpoint requirements and error codes.
- **Contact Support:** If the problem persists, contact Fluid support for further assistance.