# Source: https://docs.fluid.app/docs/guides/payment-processing

# Payment Processing

#### Table of Contents

---

> 1. [Overview](https://docs.fluid.app/#overview)
> 2. [Key Features](https://docs.fluid.app/#key-features)
> 3. [How It Works](https://docs.fluid.app/#how-it-works)
> 4. [Configuration Types](https://docs.fluid.app/#configuration-types)
> 5. [Supported Conditions](https://docs.fluid.app/#supported-conditions)
> 6. [API Reference](https://docs.fluid.app/#api-reference)
> 7. [Use Cases](https://docs.fluid.app/#use-cases)
> 8. [Best Practices](https://docs.fluid.app/#best-practices)

---

## Overview

Payment Processing provides intelligent payment routing capabilities that allow you to distribute card transactions across multiple Payment Service Providers (PSPs). This enables you to optimize payment processing by routing transactions to the most appropriate gateway based on configurable rules or volume distribution. Under the hood, its handled by our **Fluid Orchestration Routing Engine**.

Whether you want to split traffic between processors for redundancy, route specific payment types to preferred gateways, or implement geographic routing rules, Payment Processing gives you full control over your payment flow.

## Key Features

- **Volume-Based Routing**: Distribute traffic by percentage across multiple payment accounts (e.g., 60% to one processor, 40% to another)
- **Rule-Based Routing**: Define conditional rules to route payments to specific gateways based on payment type, country, amount, or currency
- **Default Fallback**: Priority-ordered list of payment accounts used when no configuration matches
- **Auto-Sync**: Payment accounts are automatically added to or removed from the fallback list when created, activated, deactivated, or deleted
- **Apple Pay & Google Pay Support**: Routes digital wallet transactions alongside traditional card payments

## How It Works

Volume Based

Rule Based

None

Match Found

No Match

Customer Initiates Payment

Routing Engine

Active Configuration?

Weighted Random Selection

Evaluate Rules

Default Fallback

Selected Payment Account

Process Payment

When a payment is initiated:

1. The routing engine checks for an active configuration
2. If **volume-based**: Randomly selects a payment account based on configured percentages
3. If **rule-based**: Evaluates rules in priority order until a match is found
4. If no configuration or no rule matches: Uses the default fallback priority list
5. The selected payment account processes the transaction

## Configuration Types

### Volume-Based Configuration

Distributes traffic by percentage across multiple payment accounts using weighted random selection. Over time, the distribution converges to your configured percentages.

**Example: 60/40 Split**

curl \-X POST "https://api.fluid.app/api/fluid\_orchestration/configurations" \\
 \-H "Authorization: Bearer YOUR\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "configuration": {
 "name": "60/40 Traffic Split",
 "configuration\_type": "volume\_based",
 "description": "Route 60% to primary processor, 40% to secondary",
 "settings": {
 "distributions": \[
 { "payment\_account\_id": 1, "percentage": 60 },
 { "payment\_account\_id": 2, "percentage": 40 }
 \]
 }
 }
 }'

**Important**: Percentages must sum to exactly 100.

### Rule-Based Configuration

Routes traffic based on conditional rules evaluated in priority order. The first matching rule determines the payment account.

**Example: Route Apple Pay to a Specific Processor**

curl \-X POST "https://api.fluid.app/api/fluid\_orchestration/configurations" \\
 \-H "Authorization: Bearer YOUR\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "configuration": {
 "name": "Payment Source Routing",
 "configuration\_type": "rule\_based",
 "description": "Route Apple Pay to NMI, high-value US orders to Stripe",
 "settings": {
 "rules": \[
 {
 "name": "Apple Pay to NMI",
 "priority": 1,
 "payment\_account\_id": 123,
 "conditions": {
 "payment\_source": "apple\_pay"
 }
 },
 {
 "name": "High Value US Orders",
 "priority": 2,
 "payment\_account\_id": 456,
 "conditions": {
 "country\_iso": \["US"\],
 "amount\_greater\_than": 500
 }
 }
 \]
 }
 }
 }'

Rules are evaluated in order of priority (lowest number first). If no rule matches, the default fallback is used.

### Default Fallback

When no active configuration exists or no rules match, the system uses a priority-ordered list of payment accounts. This list is automatically maintained:

- New active payment accounts are added to the end
- Deactivated or deleted accounts are removed
- You can manually reorder the priority

## Supported Conditions

Use these conditions in rule-based configurations to control routing:

| Condition | Type | Description | Example Values |
| --- | --- | --- | --- |
| `payment_source` | enum | Payment method type | `"card"`, `"apple_pay"`, `"google_pay"` |
| `country_iso` | array | List of country codes | `["US", "CA", "GB"]` |
| `amount_greater_than` | number | Minimum amount (exclusive) | `100.00`, `500` |
| `amount_less_than` | number | Maximum amount (exclusive) | `1000.00`, `50` |
| `currency_code` | string | Currency code | `"USD"`, `"EUR"`, `"GBP"` |

**Combining Conditions**: Multiple conditions in a single rule are evaluated with AND logic. All conditions must match for the rule to apply.

## API Reference

### Base URL

/api/fluid\_orchestration

### Get Current Configuration

Returns your complete routing configuration including the active configuration, default fallback, and available payment accounts.

GET /status

**Response:**

{
 "active\_configuration": {
 "id": 1,
 "name": "Volume Based Routing",
 "configuration\_type": "volume\_based",
 "active": true,
 "settings": {
 "distributions": \[
 { "payment\_account\_id": 1, "percentage": 60 },
 { "payment\_account\_id": 2, "percentage": 40 }
 \]
 }
 },
 "default\_fallback": {
 "priority\_order": \[
 { "payment\_account\_id": 1, "name": "Braintree", "priority": 1 },
 { "payment\_account\_id": 2, "name": "NMI", "priority": 2 }
 \]
 },
 "available\_payment\_accounts": \[...\],
 "supported\_conditions": \[...\],
 "configuration\_types": \["volume\_based", "rule\_based"\]
}

### List All Configurations

GET /status

### Create Configuration

POST /configurations

See [Configuration Types](https://docs.fluid.app/#configuration-types) for request body examples.

### Update Configuration

PATCH /configurations/:id

### Delete Configuration

DELETE /configurations/:id

**Note**: You cannot delete an active configuration. Deactivate it first.

### Activate Configuration

Activates the specified configuration. Any previously active configuration is automatically deactivated.

POST /configurations/:id/activate

### Deactivate Configuration

Deactivates the configuration. The default fallback will be used for routing.

POST /configurations/:id/deactivate

### Get Default Fallback

GET /default\_fallback

### Reorder Default Fallback

PATCH /default\_fallback

**Request:**

{
 "priority\_order": \[
 { "payment\_account\_id": 2, "priority": 1 },
 { "payment\_account\_id": 1, "priority": 2 }
 \]
}

## Use Cases

### Processor Redundancy

Split traffic between two processors to reduce dependency on a single provider:

{
 "configuration\_type": "volume\_based",
 "settings": {
 "distributions": \[
 { "payment\_account\_id": 1, "percentage": 50 },
 { "payment\_account\_id": 2, "percentage": 50 }
 \]
 }
}

### Payment Type Optimization

Route different payment methods to processors that offer better rates or features:

{
 "configuration\_type": "rule\_based",
 "settings": {
 "rules": \[
 {
 "name": "Apple Pay",
 "priority": 1,
 "payment\_account\_id": 123,
 "conditions": { "payment\_source": "apple\_pay" }
 },
 {
 "name": "Google Pay",
 "priority": 2,
 "payment\_account\_id": 123,
 "conditions": { "payment\_source": "google\_pay" }
 }
 \]
 }
}

### Geographic Routing

Route transactions to region-specific processors for better approval rates:

{
 "configuration\_type": "rule\_based",
 "settings": {
 "rules": \[
 {
 "name": "European Orders",
 "priority": 1,
 "payment\_account\_id": 456,
 "conditions": { "country\_iso": \["GB", "DE", "FR", "ES", "IT"\] }
 },
 {
 "name": "North American Orders",
 "priority": 2,
 "payment\_account\_id": 789,
 "conditions": { "country\_iso": \["US", "CA", "MX"\] }
 }
 \]
 }
}

### High-Value Transaction Routing

Route high-value transactions to processors with better fraud protection:

{
 "configuration\_type": "rule\_based",
 "settings": {
 "rules": \[
 {
 "name": "High Value Orders",
 "priority": 1,
 "payment\_account\_id": 456,
 "conditions": { "amount\_greater\_than": 1000 }
 }
 \]
 }
}

## Best Practices

### 3D Secure (3DS) Compatibility

When configuring routing across multiple payment accounts, ensure all accounts in your configuration use the same 3DS provider. Mixing accounts with different 3DS providers (or accounts with and without 3DS) can cause checkout failures.

**Recommendation**: Group payment accounts by 3DS provider when creating configurations.

| Scenario | Result |
| --- | --- |
| All accounts use VGS 3DS | Works correctly |
| All accounts have no 3DS | Works correctly |
| Mixed 3DS providers | May cause failures |

### Testing Your Configuration

1. **Start with a dry run**: Create your configuration but don't activate it immediately
2. **Verify payment accounts**: Ensure all referenced payment account IDs are valid and active
3. **Check percentage totals**: For volume-based configs, percentages must sum to 100
4. **Test rule ordering**: For rule-based configs, verify rules are in the correct priority order
5. **Monitor after activation**: Watch for any payment failures after activating a new configuration

### Configuration Management

- **One active configuration**: Only one configuration can be active at a time
- **Fallback safety**: The default fallback ensures payments continue even if rules don't match
- **Gradual rollout**: Consider starting with a conservative split (e.g., 90/10) when adding a new processor
- **Documentation**: Use descriptive names and descriptions for your configurations and rules