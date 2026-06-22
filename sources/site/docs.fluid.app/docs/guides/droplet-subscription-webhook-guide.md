# Source: https://docs.fluid.app/docs/guides/droplet-subscription-webhook-guide

# Droplet Subscription Webhook Guide

This guide explains how to build a droplet that listens for subscription webhooks. The example droplet registers for the `subscription_started` event and updates other subscriptions for the same customer to bill on the 15th of the month, so all of that customer's subscriptions process on the same day.

## Overview

This guide covers:

- Creating a droplet and registering its install/uninstall lifecycle webhooks
- Receiving the `droplet_installed` lifecycle webhook and exchanging the one-time `exchange_token` for a permanent installation token
- Subscribing to the `subscription_started` business webhook
- Verifying inbound webhook requests with HMAC-SHA256
- Calling Fluid's `/api/subscriptions` endpoints from your droplet to read and update subscription data

## Prerequisites

- A Fluid company account and a company token (prefix `C-`) capable of creating droplets
- A publicly reachable HTTPS endpoint to receive webhooks (HTTPS is enforced in production)
- Basic knowledge of webhook handling, HMAC signature verification, and subscription management

## Architecture Overview

Your StorageYour DropletFluid PlatformCustomerYour StorageYour DropletFluid PlatformCustomer1\. Install (one-time per company)2\. Business event (per subscription)POST install\_webhook\_urlX-Fluid-Signature (HMAC-SHA256 of dws\_ secret)body.payload.company.credentials.exchange\_tokenPOST /api/droplet\_installations/exchange (exchange\_token)permanent dit\_… installation tokenPersist token per companyCreates new subscriptionPOST your subscribed urlX-Fluid-Signature (HMAC-SHA256 of wvt\_ or auth\_token)body.payload.subscription { … }GET /api/subscriptions?customer\_id=…Bearer dit\_…{ subscriptions: \[...\] }PATCH /api/subscriptions/:tokenBearer dit\_…

## Step 1: Create the Droplet

Create a droplet record. The `embed_url` is the URL Fluid uses to embed your droplet inside the admin UI. `install_webhook_url` and `uninstall_webhook_url` receive lifecycle events; they must be HTTPS in production.

curl \-X POST "https://api.fluid.app/api/droplets" \\
 \-H "Authorization: Bearer C-YOUR\_COMPANY\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "droplet": {
 "name": "Subscription Billing Synchronizer",
 "embed\_url": "https://your-app.com/droplet/subscription-sync",
 "install\_webhook\_url": "https://your-app.com/webhooks/droplet/installed",
 "uninstall\_webhook\_url": "https://your-app.com/webhooks/droplet/uninstalled",
 "settings": {
 "marketplace\_page": {
 "title": "Subscription Billing Synchronizer",
 "summary": "Synchronizes customer subscription billing dates to the 15th of each month",
 "logo\_url": "https://your-app.com/logo.svg"
 },
 "details\_page": {
 "title": "Subscription Billing Synchronizer",
 "summary": "Keeps all customer subscriptions on the same billing cycle",
 "logo\_url": "https://your-app.com/big-logo.svg",
 "features": \[
 {
 "name": "Automatic Synchronization",
 "summary": "Updates subscription billing dates",
 "details": "When a new subscription is created, other subscriptions for that customer are updated to bill on the 15th of the month"
 }
 \]
 }
 },
 "requested\_scopes": \["main", "orders", "settings"\]
 }
 }'

`requested_scopes` must include `settings` if you intend to register webhooks from your droplet using its installation token (Step 5) — `POST /api/company/webhooks` requires the `developer:update` permission, which sits inside the `settings` scope group. Drop `settings` only if you plan to register webhooks using a company token (`C-…`) instead.

You can also create a droplet via the [Droplet Marketplace](https://admin.fluid.app/droplets) → **Create Droplet**.

When a droplet is created, Fluid generates a `webhook_secret` (prefix `dws_`) — this is the HMAC key used to sign **lifecycle** webhooks delivered to your `install_webhook_url` and `uninstall_webhook_url`. Store this secret securely; you will use it to verify lifecycle webhooks in Step 3.

## Step 2: Install the Droplet

Install the droplet against a company:

1. Open the [Droplet Marketplace](https://admin.fluid.app/droplets)
2. Select your droplet
3. Click **Install Droplet**

Installation creates a `DropletInstallation` record. Fluid then POSTs a `droplet_installed` event to your `install_webhook_url`.

## Step 3: Handle the `droplet_installed` Lifecycle Webhook

When a company installs your droplet, Fluid sends a signed POST to `install_webhook_url`.

### Request headers

| Header | Description |
| --- | --- |
| `Content-Type` | Always `application/json` |
| `X-Fluid-Shop` | The installing company's `fluid_shop` slug |
| `X-Fluid-Timestamp` | Unix timestamp (seconds, as string) |
| `X-Fluid-Signature` | `HMAC-SHA256(droplet.webhook_secret, "{timestamp}.{raw_body}")` as a lowercase hex string |

### Request body

{
 "id": 12345,
 "identifier": "dropletinstallation\_AbCdEf...",
 "name": "droplet\_installed",
 "payload": {
 "event\_name": "droplet\_installed",
 "schema\_version": 1,
 "schema\_hash": "...",
 "contract\_version": "v2",
 "company\_id": 42,
 "resource\_name": "DropletInstallation",
 "resource": "droplet",
 "event": "installed",
 "company": {
 "droplet\_installation\_uuid": "dri\_AbCdEf...",
 "droplet\_uuid": "drp\_GhIjKl...",
 "fluid\_company\_id": 42,
 "fluid\_shop": "acme",
 "name": "Acme Inc",
 "credentials": {
 "exchange\_token": "dex\_oneTimeOpaqueToken...",
 "exchange\_token\_expires\_at": "2026-05-18T15:00:00Z",
 "exchange\_endpoint": "/api/droplet\_installations/exchange"
 }
 }
 },
 "timestamp": "2026-05-18T14:30:45Z"
}

The exact fields under `payload.company` come from `DropletInstallationBlueprint` in the `:lifecycle_installed_v2` view. The `credentials.exchange_token` is a one-time token your droplet exchanges (Step 4) for a permanent installation token.

### Verify the signature

class Webhooks::Droplet::InstalledController < ApplicationController
 skip\_before\_action :verify\_authenticity\_token

 def create
 raw\_body \= request.raw\_post

 return head :request\_timeout unless fresh\_timestamp?
 return head :unauthorized unless valid\_signature?(raw\_body)

 \# Acknowledge fast; do the exchange + persistence in a Sidekiq job so a
 \# transient DB error does not consume the single-use exchange\_token and
 \# permanently break the install. See Step 4.
 Droplet::InstallJob.perform\_later(JSON.parse(raw\_body))
 head :ok
 end

 private

 def valid\_signature?(raw\_body)
 timestamp \= request.headers\["X-Fluid-Timestamp"\]
 provided \= request.headers\["X-Fluid-Signature"\].to\_s
 secret \= ENV.fetch("FLUID\_DROPLET\_WEBHOOK\_SECRET") \# the dws\_ value from droplet creation

 expected \= OpenSSL::HMAC.hexdigest("SHA256", secret, "#{timestamp}.#{raw\_body}")
 ActiveSupport::SecurityUtils.secure\_compare(expected, provided)
 end

 def fresh\_timestamp?
 timestamp \= request.headers\["X-Fluid-Timestamp"\].to\_i
 (Time.now.to\_i \- timestamp).abs <= 300 \# 5-minute tolerance
 end
end

Use `request.raw_post` — recomputing over a parsed-and-re-serialized body will not match the signature.

### Uninstall (symmetric)

When a company removes your droplet, Fluid POSTs a `droplet_uninstalled` event to the `uninstall_webhook_url` you supplied in Step 1. The headers, HMAC scheme, signing key (`droplet.webhook_secret`), and 300-second freshness window are all identical to the install case — only the `name`, `event`, and `payload` differ. Returning `2xx` (any 200–299) tells Fluid to finalize the uninstall; returning `4xx` is treated as terminal (Fluid finalizes anyway, since the droplet owner has been notified); returning `5xx`, `429`, or timing out causes Fluid to retry the delivery via Sidekiq. After the uninstall is finalized, the installation's `dit_…` token stops working — clean up any stored credentials for that company.

## Step 4: Exchange the One-Time Token for a Permanent Installation Token

The `exchange_token` is single-use. Exchange it for a permanent installation token (prefix `dit_`) before it expires. The exchange endpoint responds with:

{
 "droplet\_installation": {
 "droplet\_installation\_uuid": "dri\_...",
 "droplet\_uuid": "drp\_...",
 "fluid\_company\_id": 42,
 "fluid\_shop": "acme"
 },
 "credentials": {
 "authentication\_token": "dit\_...",
 "webhook\_verification\_token": "wvt\_...",
 "issued\_at": "2026-05-18T14:30:45Z",
 "token\_type": "bearer"
 }
}

class Droplet::InstallJob < ApplicationJob
 queue\_as :webhooks

 def perform(install\_payload)
 company \= install\_payload.fetch("payload").fetch("company")
 uuid \= company.fetch("droplet\_installation\_uuid")

 \# Pre-create the tenant row with NULL tokens BEFORE the exchange. On a
 \# Sidekiq retry after a partial failure, this find\_or\_create\_by! resolves
 \# to the existing row instead of duplicating, and the credentials check
 \# below short-circuits if the previous attempt's exchange already wrote
 \# them. Requires a unique index on droplet\_tenants.droplet\_installation\_uuid.
 tenant \= DropletTenant.find\_or\_create\_by!(droplet\_installation\_uuid: uuid) do |t|
 t.company\_id \= company.fetch("fluid\_company\_id")
 t.fluid\_shop \= company.fetch("fluid\_shop")
 end

 \# Idempotent: if a prior attempt completed the exchange and wrote the
 \# tokens, do nothing. The exchange\_token is single-use, so we cannot
 \# exchange again — but we also do not need to.
 return if tenant.installation\_token.present?

 response \= HTTParty.post(
 "https://api.fluid.app#{company.dig('credentials', 'exchange\_endpoint')}",
 headers: { "Content-Type" \=> "application/json" },
 body: { exchange\_token: company.dig("credentials", "exchange\_token") }.to\_json,
 timeout: 20, \# stay inside Fluid's 30-second lifecycle webhook window
 )
 raise "Exchange failed: #{response.code} #{response.body}" unless response.success?

 credentials \= JSON.parse(response.body).fetch("credentials")
 tenant.update!(
 installation\_token: credentials.fetch("authentication\_token"), \# dit\_… — call Fluid back as this company
 webhook\_verification\_token: credentials.fetch("webhook\_verification\_token"), \# wvt\_… — HMAC key for business webhooks
 )
 end
end

The failure mode you cannot recover from automatically: the exchange succeeds, Sidekiq exhausts retries on the subsequent `tenant.update!` (e.g. extended DB outage), and the exchange\_token is now consumed on Fluid's side. The tenant row still exists with NULL tokens, and the credentials are gone. The job's payload is in Sidekiq's dead set — an operator can extract the credentials from Fluid's audit log and write them by hand, or trigger a reinstall. The pre-created tenant row is the durable handle that lets the operator find the right install.

Store the `dit_…` token alongside the `company_id`. You will use it as `Authorization: Bearer dit_…` in every subsequent call to Fluid for this company.

The example assumes a `DropletTenant` model on your side with these columns:

\# db/migrate/<timestamp>\_create\_droplet\_tenants.rb
create\_table :droplet\_tenants do |t|
 t.bigint :company\_id, null: false
 t.string :fluid\_shop, null: false
 t.string :droplet\_installation\_uuid, null: false
 t.string :installation\_token \# dit\_… — populated by InstallJob after exchange
 t.string :webhook\_verification\_token \# wvt\_… — populated by InstallJob after exchange
 t.timestamps
end
add\_index :droplet\_tenants, :company\_id, unique: true \# one tenant per company
add\_index :droplet\_tenants, :fluid\_shop, unique: true \# hot path for verifier lookup in Step 6
add\_index :droplet\_tenants, :droplet\_installation\_uuid, unique: true \# idempotency key for InstallJob

\# app/models/droplet\_tenant.rb
class DropletTenant < ApplicationRecord
end

Index `:fluid_shop` because every inbound business webhook performs a `find_by(fluid_shop: …)` during signature verification (Step 6).

## Step 5: Register the Subscription Webhook

With the installation token in hand, register a webhook on Fluid for `subscription.started`:

curl \-X POST "https://api.fluid.app/api/company/webhooks" \\
 \-H "Authorization: Bearer dit\_YOUR\_INSTALLATION\_TOKEN" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "webhook": {
 "resource": "subscription",
 "event": "started",
 "url": "https://your-app.com/webhooks/subscription-sync",
 "http\_method": "post"
 }
 }'

Notes:

- `resource: "subscription"` and `event: "started"` are validated against `Webhook::WEBHOOK_EVENTS`. Other valid `subscription` events: `paused`, `cancelled`, `billed`, `declined`, `resumed`, `resumed_after_payment_fix`, `reactivated`, `skipped`.
- **Signing key:** because this webhook is owned by your droplet installation, Fluid signs every delivery with the installation's `webhook_verification_token` (the `wvt_…` value returned by the exchange in Step 4). Even if you pass `auth_token` in this request, the installation token wins (`Webhook#auth_token` returns the installation's `wvt_…` first, the stored `auth_token` only as a fallback). Use the `wvt_…` value in your verifier (Step 6) and skip the `auth_token` field here.
- If you'd rather pick your own signing secret, register with a company token (`Bearer C-…`) instead, supply `auth_token`, and verify against that value. The droplet-installation path is the realistic flow for an installed droplet.
- `http_method` defaults to `post`; `active` defaults to `true`.
- URLs must be HTTPS in production; internal/private hosts are rejected.

## Step 6: Receive and Verify the `subscription_started` Webhook

### Headers

Every business webhook delivery includes:

| Header | Description |
| --- | --- |
| `Content-Type` | Always `application/json` |
| `X-Fluid-Shop` | The company's `fluid_shop` slug |
| `AUTH_TOKEN` | The signing key value itself — sent for backward compatibility with the old static-token scheme. Treat as informational only; verify the signature instead. |
| `X-Fluid-Token` | Same value as `AUTH_TOKEN` |
| `X-Fluid-Timestamp` | Unix timestamp (seconds, as string) |
| `X-Fluid-Signature` | `HMAC-SHA256(signing_key, "{timestamp}.{raw_body}")` as lowercase hex |

The `signing_key` is the installation's `webhook_verification_token` (`wvt_…`) when the webhook is owned by a droplet installation, otherwise the `auth_token` you provided when creating the webhook. Verify the signature rather than comparing `AUTH_TOKEN` directly — the signature covers timestamp and body and is replay-resistant when paired with a freshness check.

### Body

{
 "id": 9876,
 "identifier": "commerce::subscription\_aBcDeF...",
 "name": "subscription\_started",
 "payload": {
 "event\_name": "subscription\_started",
 "schema\_version": 3,
 "schema\_hash": "...",
 "company\_id": 42,
 "resource\_name": "Commerce::Subscription",
 "resource": "subscription",
 "event": "started",
 "subscription": {
 "id": 12345,
 "subscription\_token": "sb\_AbCdEf...",
 "status": "active",
 "next\_bill\_date": "2026-06-01T00:00:00Z",
 "customer": { "id": 789, "email": "customer@example.com" },
 "subscription\_plan": { "id": 10, "name": "Monthly Premium", "billing\_interval\_unit": "month" }
 }
 },
 "timestamp": "2026-05-18T14:30:45Z"
}

The full set of fields under `payload.subscription` is defined by `Commerce::SubscriptionBlueprinter` in the `:with_associations` view (status, prices, dates, customer, plan, address, payment\_method, etc.).

### Verifier

class Webhooks::SubscriptionSyncController < ApplicationController
 skip\_before\_action :verify\_authenticity\_token

 def create
 raw\_body \= request.raw\_post

 return head :request\_timeout unless fresh\_timestamp?
 return head :unauthorized unless valid\_signature?(raw\_body, signing\_key)

 payload \= JSON.parse(raw\_body)
 subscription \= payload.dig("payload", "subscription")
 return head :ok unless subscription

 SubscriptionSyncJob.perform\_later(payload)
 head :ok
 end

 private

 def signing\_key
 \# Look up the wvt\_ value stored when we exchanged the install token in Step 4.
 \# Use find\_by (not find\_by!) so unknown shops fail closed with 401 below,
 \# rather than raising RecordNotFound and returning 404/500. Also guard
 \# against a missing X-Fluid-Shop header so we never look up \`IS NULL\`.
 shop \= request.headers\["X-Fluid-Shop"\]
 return nil if shop.blank?

 DropletTenant.find\_by(fluid\_shop: shop)&.webhook\_verification\_token
 end

 def valid\_signature?(raw\_body, secret)
 return false if secret.nil?

 timestamp \= request.headers\["X-Fluid-Timestamp"\]
 provided \= request.headers\["X-Fluid-Signature"\].to\_s

 expected \= OpenSSL::HMAC.hexdigest("SHA256", secret, "#{timestamp}.#{raw\_body}")
 ActiveSupport::SecurityUtils.secure\_compare(expected, provided)
 end

 def fresh\_timestamp?
 timestamp \= request.headers\["X-Fluid-Timestamp"\].to\_i
 (Time.now.to\_i \- timestamp).abs <= 300
 end
end

The verifier looks up the signing key per-company via `X-Fluid-Shop` because the `wvt_…` value is unique to each `DropletInstallation`. If you registered the webhook with a company token and supplied an `auth_token`, replace `signing_key` with that stored secret instead.

## Step 7: Call Fluid Back to Synchronize Billing Dates

To act on the event, list the customer's other subscriptions and update their `next_bill_date`.

### List subscriptions for a customer

GET https://api.fluid.app/api/subscriptions?customer\_id\=789&status\=active
Authorization: Bearer dit\_YOUR\_INSTALLATION\_TOKEN

Response (truncated):

{
 "subscriptions": \[
 {
 "id": 12345,
 "subscription\_token": "sb\_AbCdEf...",
 "status": "active",
 "next\_bill\_date": "2026-06-01T00:00:00Z",
 "subscription\_plan": { "id": 10, ... },
 "customer": { "id": 789, ... },
 "currency": { "id": 1, ... },
 "variant": { "id": 333, "product": { ... } }
 }
 \],
 "meta": { "current\_page": 1, "total\_pages": 1, "total\_count": 1, "stats": { ... } }
}

The list endpoint uses the `:api_index` blueprinter view, which intentionally omits `address` and `payment_method` to keep the response small. Before patching a subscription you must fetch it individually to get `address_id` and `payment_method_id`:

### Fetch a single subscription

GET https://api.fluid.app/api/subscriptions/sb\_AbCdEf...
Authorization: Bearer dit\_YOUR\_INSTALLATION\_TOKEN

The show response uses the `:with_associations_and_customer_extended` blueprint view, which composes `:with_associations` plus extra customer detail. That includes `address`, `payment_method`, and the full set of associations needed for an update.

### Update `next_bill_date` on a single subscription

The update endpoint takes the **subscription token** (not numeric id) as the path param, and it requires several fields in the body in addition to `next_bill_date`:

PATCH https://api.fluid.app/api/subscriptions/sb\_AbCdEf...
Authorization: Bearer dit\_YOUR\_INSTALLATION\_TOKEN
Content\-Type: application/json

{
 "subscription": {
 "subscription\_plan\_id": 10,
 "customer\_id": 789,
 "variant\_id": 333,
 "address\_id": 555,
 "payment\_method\_id": 444,
 "next\_bill\_date": "2026-06-15"
 }
}

The `subscription_plan_id`, `customer_id`, `variant_id`, `address_id`, and `payment_method_id` fields are required by `Commerce::Api::Subscriptions::UpdateAction`. Take them from the per-subscription **show** response (the list response omits `address` and `payment_method`). Sending only `next_bill_date` will be rejected with a 400.

`next_bill_date` accepts a date string; Fluid preserves the existing time-of-day in the subscription's timezone. A value in the past is rejected.

### Synchronizer service

class SubscriptionSynchronizer
 BILLING\_DAY \= 15

 def initialize(payload)
 @subscription \= payload.fetch("subscription")
 @customer\_id \= @subscription.dig("customer", "id")
 @company\_id \= payload.fetch("company\_id")
 end

 def synchronize
 other\_subs \= list\_other\_subscriptions
 target \= next\_billing\_date

 other\_subs.each { |sub| update\_bill\_date(sub, target) }
 end

 private

 \# Paginate through every active subscription for the customer — the list
 \# endpoint defaults to 25 per page, and silently skipping later pages would
 \# leave heavy customers' billing dates unsynchronized.
 def list\_other\_subscriptions
 page \= 1
 all \= \[\]

 loop do
 response \= fluid.get("/api/subscriptions", query: { customer\_id: @customer\_id, status: "active", page: page })
 raise "List failed: #{response.code}" unless response.success?

 body \= JSON.parse(response.body)
 all.concat(body.fetch("subscriptions"))

 break if page \>= body.dig("meta", "total\_pages").to\_i

 page += 1
 end

 all.reject { |s| s\["subscription\_token"\] \== @subscription\["subscription\_token"\] }
 end

 \# The list endpoint's :api\_index view omits address and payment\_method,
 \# so we re-fetch each subscription via show to get the full association set
 \# required by the update endpoint.
 def fetch\_full(token)
 response \= fluid.get("/api/subscriptions/#{token}")
 raise "Show failed: #{response.code}" unless response.success?

 JSON.parse(response.body).fetch("subscription")
 end

 def update\_bill\_date(stub, target)
 sub \= fetch\_full(stub.fetch("subscription\_token"))

 \# UpdateAction requires payment\_method\_id and address\_id as integers.
 \# Some subscriptions (e.g. zero-price ones created without a stored
 \# payment method) legitimately have nil here — patching them would 400.
 unless sub\["payment\_method"\] && sub\["address"\]
 Rails.logger.info("Skipping #{sub\['subscription\_token'\]}: missing payment\_method or address")
 return
 end

 body \= {
 subscription: {
 subscription\_plan\_id: sub.dig("subscription\_plan", "id"),
 customer\_id: sub.dig("customer", "id"),
 variant\_id: sub.dig("variant", "id"),
 address\_id: sub.dig("address", "id"),
 payment\_method\_id: sub.dig("payment\_method", "id"),
 next\_bill\_date: target.strftime("%Y-%m-%d"),
 },
 }

 response \= fluid.patch("/api/subscriptions/#{sub\['subscription\_token'\]}", body: body.to\_json)

 if response.success?
 Rails.logger.info("Synced #{sub\['subscription\_token'\]} → #{target}")
 else
 Rails.logger.error("Sync failed for #{sub\['subscription\_token'\]}: #{response.code} #{response.body}")
 end
 end

 def next\_billing\_date
 today \= Date.current
 this\_15 \= Date.new(today.year, today.month, BILLING\_DAY)
 today.day <= BILLING\_DAY ? this\_15 : this\_15.next\_month
 end

 def fluid
 @fluid ||= FluidClient.new(token: DropletTenant.find\_by!(company\_id: @company\_id).installation\_token)
 end
end

class FluidClient
 BASE\_URL \= "https://api.fluid.app"

 def initialize(token:)
 @headers \= {
 "Authorization" \=> "Bearer #{token}",
 "Content-Type" \=> "application/json",
 }
 end

 def get(path, query: {})
 HTTParty.get("#{BASE\_URL}#{path}", headers: @headers, query: query, timeout: 30)
 end

 def patch(path, body:)
 HTTParty.patch("#{BASE\_URL}#{path}", headers: @headers, body: body, timeout: 30)
 end
end

## Step 8: Process Asynchronously

Webhook delivery timeouts differ between the two paths:

- **Business webhooks** (subscription, order, etc.): connect timeout 5s, total timeout 15s.
- **Lifecycle webhooks** (install/uninstall): connect timeout 5s, total timeout 30s.

In both cases, acknowledge fast and do work in a background job:

class SubscriptionSyncJob < ApplicationJob
 queue\_as :webhooks

 def perform(webhook\_body)
 SubscriptionSynchronizer.new(webhook\_body.fetch("payload")).synchronize
 end
end

\# config/routes.rb
Rails.application.routes.draw do
 post "/webhooks/droplet/installed", to: "webhooks/droplet/installed#create"
 post "/webhooks/droplet/uninstalled", to: "webhooks/droplet/uninstalled#create"
 post "/webhooks/subscription-sync", to: "webhooks/subscription\_sync#create"
end

## Step 9: Testing

Test the controller with a precomputed signature:

require "rails\_helper"

RSpec.describe Webhooks::SubscriptionSyncController, type: :request do
 let(:wvt) { "wvt\_test\_value" }
 let(:tenant) { DropletTenant.create!(fluid\_shop: "acme", company\_id: 1, webhook\_verification\_token: wvt, installation\_token: "dit\_x") }
 let(:body) { { id: 1, name: "subscription\_started", payload: { subscription: { id: 99 } } }.to\_json }
 let(:ts) { Time.now.to\_i.to\_s }
 let(:sig) { OpenSSL::HMAC.hexdigest("SHA256", wvt, "#{ts}.#{body}") }

 before { tenant }

 it "accepts a valid signature" do
 post "/webhooks/subscription-sync",
 params: body,
 headers: {
 "Content-Type" \=> "application/json",
 "X-Fluid-Shop" \=> "acme",
 "X-Fluid-Timestamp" \=> ts,
 "X-Fluid-Signature" \=> sig,
 }
 expect(response).to have\_http\_status(:ok)
 end

 it "rejects a stale timestamp before computing the signature" do
 post "/webhooks/subscription-sync",
 params: body,
 headers: {
 "Content-Type" \=> "application/json",
 "X-Fluid-Shop" \=> "acme",
 "X-Fluid-Timestamp" \=> (Time.now.to\_i \- 3600).to\_s,
 "X-Fluid-Signature" \=> "deadbeef",
 }
 expect(response).to have\_http\_status(:request\_timeout)
 end

 it "rejects an invalid signature" do
 post "/webhooks/subscription-sync",
 params: body,
 headers: {
 "Content-Type" \=> "application/json",
 "X-Fluid-Shop" \=> "acme",
 "X-Fluid-Timestamp" \=> ts,
 "X-Fluid-Signature" \=> "deadbeef",
 }
 expect(response).to have\_http\_status(:unauthorized)
 end
end

## Configuration

\# Lifecycle webhook signing key — the dws\_ secret generated when the droplet was created.
\# Same for every install of your droplet; safe to keep in env.
FLUID\_DROPLET\_WEBHOOK\_SECRET\=dws\_...

The business webhook signing key is the per-company `webhook_verification_token` (`wvt_…`) returned by the install token exchange (Step 4). Persist it in your database alongside `company_id` and `installation_token`, and look it up at verification time using `X-Fluid-Shop` — different installations have different signing keys.

## Best Practices

### Idempotency

The `id` field in the webhook body is the `WebhookEvent` primary key; the `identifier` field is a unique opaque identifier (e.g. `commerce::subscription_…`, `dropletinstallation_…`). Persist `identifier` and short-circuit if you have seen it before:

return if ProcessedWebhook.exists?(identifier: payload.fetch("identifier"))
ProcessedWebhook.create!(identifier: payload.fetch("identifier"))

### Signature verification order

1. Read `request.raw_post` once and reuse it.
2. Reject if the timestamp is missing or skewed by more than ~5 minutes.
3. Recompute the HMAC over `"{timestamp}.{raw_body}"` with your stored secret.
4. Compare using `ActiveSupport::SecurityUtils.secure_compare`.
5. Only then parse JSON and act.

### Per-company secrets

Store the `dit_…` installation token and the `wvt_…` webhook verification token per company in your database, keyed by `company_id` (and `fluid_shop` for fast lookup at webhook-verification time). Never share these across companies — `dit_…` grants scoped API access for one company; `wvt_…` is that company's signing key for inbound business webhooks.

### Retries

Lifecycle webhooks (install/uninstall) and business webhooks behave differently here:

- **Lifecycle webhooks** are retried by Sidekiq on connection errors, timeouts, 5xx responses, and 429s — `DropletLifecycleWebhook` re-raises retryable failures so the wrapping job can retry. Non-retryable 4xx responses are treated as terminal and the uninstall is finalized regardless.
- **Business webhooks** (subscription, order, etc.) are NOT retried by Fluid. `Webhook#call` catches `RestClient::Exception` and other `StandardError`s, records the failure on the `WebhookEvent` record, and returns normally — so Sidekiq sees a successful job and never retries. If your receiver returns non-2xx (or times out at 15 seconds), the event is logged on the `WebhookEvent` but not re-delivered. Build a reconciliation path (e.g. periodic poll of `/api/subscriptions` using your `dit_` token, or surface missed events to your support team) for failures you care about.

In both cases, return `2xx` only when you have safely accepted the event — typically after enqueuing a job, before doing any slow work.

## Troubleshooting

| Symptom | Likely cause |
| --- | --- |
| `401 Unauthorized` on calls to `/api/subscriptions` | Wrong or expired installation token; missing `Bearer` prefix |
| Signature mismatch | Body was parsed and re-serialized before signing; whitespace/encoding changes; signing wrong secret (lifecycle vs business webhook use different keys); using the `auth_token` you passed at webhook creation when the webhook is owned by a droplet installation — the actual signing key is the installation's `wvt_…` value |
| `400 Bad Request` on `PATCH /api/subscriptions/:token` | Missing one of the required ids (`subscription_plan_id`, `customer_id`, `variant_id`, `address_id`, `payment_method_id`) |
| `404 Not Found` on PATCH | Path used numeric id; must be `subscription_token` |
| Webhook never delivers | URL is HTTP in production (HTTPS required) or targets an internal/private host |
| Resource/event rejected on webhook creation | Combination not in `Webhook::WEBHOOK_EVENTS`; e.g. `subscription.created` is not a real event — use `subscription.started` |

## Reference

| Item | Value |
| --- | --- |
| Lifecycle signing key | `droplet.webhook_secret` (prefix `dws_`) |
| Business webhook signing key (droplet-installation-owned) | installation `webhook_verification_token` (prefix `wvt_`) — takes precedence over any stored `auth_token` |
| Business webhook signing key (other) | the `auth_token` supplied at webhook creation |
| Exchange token (one-time, returned in install payload) | prefix `dex_` |
| Installation token (call back to Fluid) | prefix `dit_` |
| Subscription token | prefix `sb_` |
| Company token (alternative caller credential) | prefix `C-` |
| Signature algorithm | HMAC-SHA256, lowercase hex |
| Signing string | `"{unix_timestamp}.{raw_body}"` |
| Signature header | `X-Fluid-Signature` |
| Timestamp header | `X-Fluid-Timestamp` (Unix seconds, as string) |
| Shop header | `X-Fluid-Shop` |
| Exchange endpoint (v2 lifecycle) | `POST /api/droplet_installations/exchange` |
| Subscriptions list | `GET /api/subscriptions` |
| Subscription update | `PATCH /api/subscriptions/:subscription_token` |

For additional help, see the [Fluid API documentation](https://docs.fluid.app/docs/openapi/admin-v0) or contact support.