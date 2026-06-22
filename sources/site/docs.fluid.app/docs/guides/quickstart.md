# Source: https://docs.fluid.app/docs/guides/quickstart

# Warp API Quickstart Guide

Welcome to Warp! This quickstart guide will help you get started with Warp's time travel API, allowing you to make your first API call and begin exploring the possibilities of temporal manipulation.

## Step 1: Sign Up and Get Your API Key

1. Visit the [Warp Sign-Up Page](https://docs.fluid.app/#).
2. Create your account and log in.
3. Navigate to your dashboard to retrieve your API key.

Your API key is required to authenticate all API requests.

## Step 2: Make Your First API Request

Let's start by making a simple request to the Warp API to establish a temporal anchor—a fixed point in time that you can return to later.

### Setting a Temporal Anchor

Use the following `curl` command to set a temporal anchor:

curl \-X POST https://api.warp.com/v1/set-anchor \\
\-H "Content-Type: application/json" \\
\-H "Authorization: Bearer YOUR\_API\_KEY" \\
\-d '{
 "timestamp": "2024-09-01T00:00:00Z",
 "description": "Starting Point"
}'

- **`timestamp`**: The date and time you wish to anchor, in ISO 8601 format.
- **`description`**: A brief description of the anchor’s purpose.

### Response Example

{
 "anchor\_id": "1234567890abcdef",
 "status": "Anchor set successfully"
}

This response confirms that the anchor has been successfully set and provides an `anchor_id` for future reference.

## Step 3: Traveling to a Different Time

Next, let's travel to a different point in time using the Warp API.

### Making a Time Travel Request

To travel to a specific date and time, use this `curl` command:

curl \-X POST https://api.warp.com/v1/travel \\
\-H "Content-Type: application/json" \\
\-H "Authorization: Bearer YOUR\_API\_KEY" \\
\-d '{
 "destination\_time": "1889-03-10T23:45:00Z",
 "location": "Tesla's Lab"
}'

- **`destination_time`**: The date and time you wish to travel to.
- **`location`**: The specific location you want to arrive at.

### Response Example

{
 "status": "Travel successful",
 "current\_time": "1889-03-10T23:45:00Z",
 "location": "Tesla's Lab"
}

This response confirms that you have successfully traveled to the specified time and location.

## Step 4: Retrieving Historical Data

While in the past, you may want to retrieve specific data or objects. Here's how to do that:

### Retrieving an Item

Use the following `curl` command to retrieve an item:

curl \-X GET https://api.warp.com/v1/retrieve-item \\
\-H "Content-Type: application/json" \\
\-H "Authorization: Bearer YOUR\_API\_KEY" \\
\-d '{
 "item\_description": "Tesla's Blueprint",
 "location": "Tesla's Lab",
 "time": "1889-03-10T23:50:00Z"
}'

### Response Example

{
 "item\_id": "0987654321fedcba",
 "status": "Item retrieved successfully",
 "item\_details": {
 "description": "Tesla's Blueprint",
 "retrieved\_at": "1889-03-10T23:50:00Z"
 }
}

This response indicates that the item has been successfully retrieved and provides details about the item.

## Step 5: Returning to Your Temporal Anchor

Once you've completed your mission, return to your previously set temporal anchor.

### Returning to an Anchor

To return to your temporal anchor, use this `curl` command:

curl \-X POST https://api.warp.com/v1/return \\
\-H "Content-Type: application/json" \\
\-H "Authorization: Bearer YOUR\_API\_KEY" \\
\-d '{
 "anchor\_id": "1234567890abcdef"
}'

### Response Example

{
 "status": "Return successful",
 "current\_time": "2024-09-01T00:00:00Z"
}

This response confirms that you've successfully returned to your starting point.

## Step 6: Monitoring for Paradoxes

Finally, ensure that your actions haven’t created any temporal paradoxes:

### Checking for Paradoxes

Use the following `curl` command:

curl \-X GET https://api.warp.com/v1/check-paradox \\
\-H "Content-Type: application/json" \\
\-H "Authorization: Bearer YOUR\_API\_KEY" \\
\-d '{
 "timeline\_id": "primary"
}'

### Response Example

{
 "status": "No paradoxes detected",
 "timeline\_stability": "Stable"
}

This response indicates that your timeline is stable and free of paradoxes.

## Congratulations!

You’ve completed your first time travel mission using the Warp API! Explore more advanced features in our [documentation](https://docs.fluid.app/#) or join our [community](https://docs.fluid.app/#) to connect with other time travelers.

[Start Your Next Mission](https://docs.fluid.app/#)