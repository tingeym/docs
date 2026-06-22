# Source: https://docs.fluid.app/docs/guides/custom-catch-ups-guide

# Custom Catch Ups API Guide

This guide covers the Custom Catch Ups API endpoints, which allow you to create, manage, and execute custom catch-up actions for affiliates within your company.

## Overview

Custom Catch Ups are user-defined follow-up actions that can be assigned to affiliates within a company. Unlike system-generated catch ups that are automatically created based on user behavior, custom catch ups are manually created via API to track specific tasks or communications.

## Authentication

All Custom Catch Ups API endpoints require:

- **Bearer Token Authentication**: Include your company token in the Authorization header

Authorization: Bearer {your\_company\_token}

## Available Endpoints

### 1\. List Custom Catch Ups

**GET** `/api/company/custom_catch_ups`

Retrieves all custom catch ups for the authenticated company.

#### Response

{
 "catch\_ups": \[
 {
 "id": 123,
 "company\_id": 456,
 "user\_company\_id": 789,
 "suggestion\_title": "Follow up with John",
 "suggestion\_description": "Call John about the product inquiry",
 "catch\_up\_class": "CUSTOM\_CATCH\_UP",
 "catch\_up\_actions": \[\],
 "created\_at": "2023-12-01T10:00:00Z",
 "updated\_at": "2023-12-01T10:00:00Z"
 }
 \],
 "meta": {
 "request\_id": "abc123",
 "timestamp": "2023-12-01T10:00:00Z"
 }
}

### 2\. Create Custom Catch Up

**POST** `/api/company/custom_catch_ups`

Creates a new custom catch up with optional actions.

#### Request Body

{
 "affiliate\_id": 789,
 "title": "Follow up with customer",
 "description": "Contact customer about their recent inquiry",
 "type": "follow\_up",
 "contact\_id": 456,
 "actions": {
 "type": "SendSmsCatchUpAction",
 "action\_prompt": "Send a personalized follow-up message",
 "data": {
 "custom\_field": "value"
 }
 }
}

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `affiliate_id` | integer | Yes | ID of the UserCompany record for the affiliate creating the catch-up |
| `title` | string | Yes | Title of the catch-up |
| `description` | string | No | Detailed description of the catch-up |
| `type` | string | Yes | Type identifier for the catch-up |
| `contact_id` | integer | No | ID of the contact associated with this catch-up |
| `actions` | object | No\* | Action configuration (see Action Types section) |

_\* The actions object is optional, but if provided, must follow the structure described in the Action Types section._

#### Response

{
 "catch\_up": {
 "id": 123,
 "company\_id": 456,
 "user\_company\_id": 789,
 "suggestion\_title": "Follow up with customer",
 "suggestion\_description": "Contact customer about their recent inquiry",
 "catch\_up\_class": "CUSTOM\_CATCH\_UP",
 "catch\_up\_actions": \[
 {
 "id": 321,
 "type": "SendSmsCatchUpAction",
 "status": "pending",
 "action\_data": {
 "meta": {
 "klass": "CUSTOM\_CATCH\_UP",
 "language": "English",
 "action\_prompt": "Send a personalized follow-up message"
 }
 }
 }
 \],
 "created\_at": "2023-12-01T10:00:00Z",
 "updated\_at": "2023-12-01T10:00:00Z"
 },
 "meta": {
 "request\_id": "abc123",
 "timestamp": "2023-12-01T10:00:00Z"
 }
}

### 3\. Update Custom Catch Up

**PATCH** `/api/company/custom_catch_ups/{id}`

Updates an existing custom catch up.

#### Request Body

{
 "suggestion\_title": "Updated follow up title",
 "suggestion\_description": "Updated description with more details"
}

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `suggestion_title` | string | No | Updated title for the catch-up |
| `suggestion_description` | string | No | Updated description for the catch-up |

#### Response

Same format as create response with updated values.

### 4\. Delete Custom Catch Up

**DELETE** `/api/company/custom_catch_ups/{id}`

Permanently deletes a custom catch up and all associated actions.

#### Response

{
 "catch\_up": {
 "id": 123
 },
 "meta": {
 "request\_id": "abc123",
 "timestamp": "2023-12-01T10:00:00Z"
 }
}

## Action Types

Custom Catch Ups support three types of actions that can be attached when creating a catch up:

> **Note:** The `actions` parameter is optional. Custom catch ups without actions can be created and used for tracking purposes, but they have limited functionality since they cannot be executed (i.e., no actions will be performed). It's recommended to include at least one action unless you only need to track the catch up.

### 1\. SendSmsCatchUpAction

Sends an SMS or email message to a contact. Automatically determines the message type based on contact information (phone for SMS, email for email, or direct message as fallback).

**Requirements:**

- `contact_id` must be provided in the catch up creation
- Contact must have either phone number or email address

**Action Configuration:**

{
 "type": "SendSmsCatchUpAction",
 "action\_prompt": "Custom message prompt for AI generation"
}

**Execution:** When executed, this action:

- Creates a conversation with the contact
- Sends the specified message via SMS, email, or direct message
- Marks the action as executed

### 2\. ToDoCatchUpAction

Creates a task that can be assigned to a user for follow-up.

**Requirements:**

- `contact_id` must be provided in the catch up creation

**Action Configuration:**

{
 "type": "ToDoCatchUpAction",
 "action\_prompt": "Task description for the to-do item"
}

**Execution:** When executed with task parameters, this action:

- Creates a new Task record
- Associates it with the specified contact and user
- Marks the action as executed

**Execution Parameters:**

{
 "task": {
 "body": "Task description",
 "due\_at": "2023-12-15T10:00:00Z"
 }
}

### 3\. LinkRedirectionCatchUpAction

A simple action that can be used for tracking or redirecting users to specific content.

**Requirements:**

- No contact required

**Action Configuration:**

{
 "type": "LinkRedirectionCatchUpAction",
 "data": {
 "url": "https://example.com/redirect",
 "tracking\_info": "campaign\_123"
 }
}

**Execution:** When executed, this action simply marks itself as read/executed.

## Error Responses

### Authentication Errors

{
 "message": "Invalid credentials"
}

**Status:** 401 Unauthorized

### Authorization Errors

{
 "message": "Not authorized"
}

**Status:** 401 Unauthorized

### Validation Errors

{
 "error\_message": "Failed to create Catch Up",
 "errors": {
 "title": \["can't be blank"\],
 "affiliate\_id": \["is required"\]
 },
 "meta": {
 "request\_id": "abc123",
 "timestamp": "2023-12-01T10:00:00Z"
 }
}

**Status:** 422 Unprocessable Entity

### Not Found Errors

{
 "error\_message": "Catch Up not found",
 "errors": {
 "id": \["not found"\]
 },
 "meta": {
 "request\_id": "abc123",
 "timestamp": "2023-12-01T10:00:00Z"
 }
}

**Status:** 404 Not Found

## Complete Examples

### Creating a Custom Catch Up with SMS Action

curl \-X POST https://api.example.com/api/company/custom\_catch\_ups \\
 \-H "Authorization: Bearer your\_company\_token" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "affiliate\_id": 789,
 "title": "Follow up on product inquiry",
 "description": "Customer showed interest in our premium package",
 "type": "follow\_up",
 "contact\_id": 456,
 "actions": {
 "type": "SendSmsCatchUpAction",
 "action\_prompt": "Send a friendly follow-up about the premium package inquiry"
 }
 }'

### Creating a Custom Catch Up with Todo Action

curl \-X POST https://api.fluid.com/api/company/custom\_catch\_ups \\
 \-H "Authorization: Bearer your\_company\_token" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "affiliate\_id": 789,
 "title": "Schedule product demo",
 "description": "Customer requested a demo of our platform",
 "type": "demo\_request",
 "contact\_id": 456,
 "actions": {
 "type": "ToDoCatchUpAction",
 "action\_prompt": "Schedule and prepare demo for customer"
 }
 }'

### Creating a Simple Tracking Catch Up

curl \-X POST https://api.fluid.com/api/company/custom\_catch\_ups \\
 \-H "Authorization: Bearer your\_company\_token" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "affiliate\_id": 789,
 "title": "Campaign conversion tracking",
 "description": "Track user engagement with holiday campaign",
 "type": "tracking",
 "actions": {
 "type": "LinkRedirectionCatchUpAction",
 "data": {
 "campaign": "holiday\_2023",
 "source": "email\_newsletter"
 }
 }
 }'

### Creating a Catch Up Without Actions (Basic Tracking)

curl \-X POST https://api.fluid.com/api/company/custom\_catch\_ups \\
 \-H "Authorization: Bearer your\_company\_token" \\
 \-H "Content-Type: application/json" \\
 \-d '{
 "affiliate\_id": 789,
 "title": "Manual follow-up reminder",
 "description": "Remember to call customer next week",
 "type": "reminder"
 }'

> **Note:** Catch ups without actions are created successfully but have no executable actions. They serve as simple reminders or tracking entries.

## Best Practices

### 1\. Action Selection

- Use **SendSmsCatchUpAction** for direct customer communication
- Use **ToDoCatchUpAction** for internal task management
- Use **LinkRedirectionCatchUpAction** for simple tracking or redirects

### 2\. Contact Requirements

- Always provide `contact_id` for SMS and Todo actions
- Ensure contacts have valid phone numbers or email addresses for SMS actions
- Verify contact ownership within your company before creating catch ups

### 3\. Error Handling

- Always check for validation errors in the response
- Handle authentication failures gracefully
- Implement retry logic for temporary failures

### 4\. Action Prompts

- Provide clear, specific action prompts for AI-generated content
- Include context about the customer or situation
- Keep prompts concise but informative

## Integration Notes

### Relationship to Other APIs

- Custom Catch Ups integrate with the Contact Management system
- Actions can create tasks that appear in the Task Management interface
- SMS actions integrate with the Messaging system for conversation tracking

### Permissions

- Affiliates can view and execute catch ups assigned to them through the mobile app
- All catch ups are scoped to the authenticated company

### Lifecycle Management

- Custom catch ups remain active until manually deleted or executed
- Actions can be snoozed, marked as read, or executed
- Completed catch ups can be filtered and archived through the main catch ups API

---

For additional help or questions about implementing Custom Catch Ups, refer to our [Authentication Guide](https://docs.fluid.app/docs/guides/authentication-guide) for token setup or contact our support team.