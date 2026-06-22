# Source: https://docs.fluid.app/docs/guides/data-dashboard

# Developing a Real-time Data Dashboard

Let's look at how you might go about building a real-time dashboard that displays information about destroyed products. We'll leverage the Fluid API to subscribe to a data stream and update the dashboard whenever a product is destroyed.

## 1\. Define Data and Visualization

- **Data source:** Fluid API endpoint for product destruction events (e.g., `/api/products/destroyed`).
- **Data elements:** Product information (title, image, etc.) and destruction details (timestamp, reason, etc.).
- **Visualization:** A table or list displaying destroyed products with relevant details. Optionally, charts could track destruction trends over time.

## 2\. Choose a Development Framework

There are various web development frameworks suitable for building dashboards. Popular choices include:

- React
- Vue.js
- Angular

We'll use a simple HTML structure for this example, but the core concepts translate to these frameworks.

## 3\. Implement Data Fetching and Subscriptions

- Use JavaScript's `fetch` API or a library like Axios to make requests to the Fluid API endpoint for product destruction data.
- Utilize the Fluid API's subscription functionality to receive real-time updates whenever a product is destroyed. Popular libraries like Pusher or Socket.IO can simplify this process.

**Here's an example:**

const endpoint \= "https://your-fluid-api-domain/api/products/destroyed";

function fetchProducts() {
 fetch(endpoint)
 .then(response \=> response.json())
 .then(data \=> {
 updateDashboard(data);
 })
 .catch(error \=> console.error(error));
}

function updateDashboard(data) {
 // Clear existing data (optional)
 const productList \= document.getElementById("product-list");
 productList.innerHTML \= "";

 // Loop through products and create elements
 data.forEach(product \=> {
 const listItem \= document.createElement("li");
 listItem.innerHTML \= \`
      <img src="${product.image\_url}" alt="${product.title}" />
      <h3>${product.title}</h3>
      <p>Destroyed at: ${product.destroyed\_at}</p>
    \`;
 productList.appendChild(listItem);
 });
}

// Subscribe to real-time updates using Pusher or Socket.IO (example omitted for brevity)

fetchProducts();

## 4\. Update Dashboard on New Data

- Parse the received JSON data from the Fluid API response.
- Update the dashboard UI elements with the new product information. This might involve dynamically creating new HTML elements or modifying existing ones.

## 5\. Considerations

- Error handling: Implement mechanisms to handle potential errors during data fetching or subscription.
- User authentication: If your data is sensitive, ensure proper user authentication before granting access to the dashboard.
- Security: Validate and sanitize data received from the API to prevent security vulnerabilities.