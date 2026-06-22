# Source: https://docs.fluid.app/docs/guides/targeted-marketing

# **Targeted Marketing: Customer Segmentation with the Fluid API**

The Fluid API equips you with valuable customer data, enabling you to segment your customer base and craft targeted marketing strategies. Here's one way you can leverage the API to create effective customer segments and personalize marketing efforts.

## 1\. Accessing Customer Data

Fluid API offers various endpoints for retrieving customer information. Here's an example using the `/customers` endpoint to get a list of customers:

// Assuming you have a valid company token stored in 'companyToken' variable
const url \= \`https://api.fluid.app/v1/companies/${companyId}/customers\`;

fetch(url, {
 headers: {
 'Authorization': \`Bearer ${companyToken}\`,
 }
})
.then(response \=> response.json())
.then(data \=> {
 // Process the retrieved customer data
 const customers \= data.data;
 // ... (your logic to segment customers)
})
.catch(error \=> console.error(error));

## 2\. Building Customer Segments

Based on your marketing goals, segment your customer base using various criteria. Here are some examples:

- **Purchase History:** Identify customers who haven't purchased in a certain timeframe for targeted reactivation campaigns.
- **Browsing Behavior:** Segment users based on their viewed products or categories to recommend similar items.
- **Demographics:** Tailor promotions based on customer location, age group, etc.

## 3\. Leveraging Segmentation Data

Once you have defined your segments, utilize their unique identifiers (e.g., customer IDs) for targeted marketing initiatives. Here's a simplified example of sending abandoned cart reminders:

// Identify customers with abandoned carts (replace with your logic)
const abandonedCarts \= \[\];

// Loop through customers and send emails for abandoned carts
abandonedCarts.forEach(customer \=> {
 // ... (your email sending logic using customer details)
});

## 4\. Integration with Marketing Tools

The Fluid API integrates seamlessly with various marketing tools. Explore our APIs to send personalized emails, push notifications, or targeted ads based on your customer segments.

**Remember:**

- Refer to the Fluid API documentation for detailed information on specific endpoints and data structures.
- Implement robust logic for defining and filtering customer segments based on your specific needs.
- Explore the capabilities of your marketing tools to leverage the segmentation data for personalized campaigns.