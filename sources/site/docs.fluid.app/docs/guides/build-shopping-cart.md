# Source: https://docs.fluid.app/docs/guides/build-shopping-cart

# Building a Shopping Cart with Real-Time Updates

Let's take a look at a scenario that uses the Fluid API to manage product data.

## 1\. Fetching Product Information

- On the product page or while browsing products, the application needs to fetch product information from the Fluid API.
- Use the `/company/v1/products` endpoint (assuming your company subdomain is `myco`).
- Use the endpoint (assuming your company subdomain is `myco`).
- You can filter products based on various parameters like category, availability, etc. (refer to the documentation for details on parameters).
- The API response will include details like title, price, description, variants (with pricing for different countries).

**Fetching product details:**

// Assuming you have a function to make API requests
const fetchProductDetails \= async (productId) \=> {
 const url \= \`https://myco.fluid.app/api/company/v1/products/${productId}\`
 const response \= await fetch(url, {
 headers: {
 'Content-Type': 'application/json',
 // Add authorization header if required by your API
 },
 })
 const data \= await response.json()
 return data
}

// Example usage
const productId \= 2260 // Replace with the actual product ID
const productData \= await fetchProductDetails(productId)
console.log(productData) // This will contain the product details

## 2\. Adding Items to Cart

- When a user adds a product to their cart, you need to store that information. This could be done on the client-side using local storage or cookies, or on the server-side using a database.
- The stored information should include the product ID, quantity, and potentially any variant information (if applicable).

**Adding item to cart - client-side storage:**

const addToCart \= (productId, quantity) \=> {
 const cartItems \= JSON.parse(localStorage.getItem('cartItems')) || \[\]
 const existingItem \= cartItems.find((item) \=> item.productId \=== productId)
 if (existingItem) {
 existingItem.quantity += quantity
 } else {
 cartItems.push({ productId, quantity })
 }
 localStorage.setItem('cartItems', JSON.stringify(cartItems))
}

// Example usage
addToCart(2260, 2) // Adding product with ID 2260, quantity 2

## 3\. Updating Cart Display

- Whenever the cart changes (item added, removed, quantity updated), the application needs to update the cart display to reflect these changes.
- You can retrieve the cart information from storage (local storage or database) and iterate over the items to display product details, quantity, and total price.

**Updating cart display:**

const updateCartDisplay \= () \=> {
 const cartItems \= JSON.parse(localStorage.getItem('cartItems')) || \[\];
 // ... Logic to calculate total price based on cart items
 const cartElement \= document.getElementById('cart');
 cartElement.innerHTML \= ''; // Clear previous cart content
 cartItems.forEach(item \=> {
 const productDetails \= await fetchProductDetails(item.productId);
 // ... Create HTML elements to display product details, quantity, and price
 });
};

// Call updateCartDisplay whenever the cart changes

## 4\. Real-time Updates

To achieve real-time updates for your shopping cart, you can leverage the Fluid API's webhook functionality. This allows you to subscribe to specific events and receive notifications in real-time.

**1\. Set up Webhook Subscriptions:**

- Create a webhook endpoint on your server to handle incoming notifications.
- Register this endpoint with the Fluid API, specifying the events you want to subscribe to, such as `cart_updated` and `cart_abandoned`.

**2\. Handle Webhook Events:**

- When a `cart_updated` event is received:
 - Parse the event payload to extract the updated cart information.
 - Update the client-side cart display to reflect the changes. This might involve adding, removing, or updating items, as well as recalculating the total price.
- When a `cart_abandoned` event is received:
 - You might want to implement specific actions, such as sending reminder emails or analyzing abandoned cart data.

**Example Webhook Handler:**

app.post('/webhook', async (req, res) \=> {
 const event \= req.body

 if (event.name \=== 'cart\_updated') {
 const updatedCart \= event.payload.cart
 // Update client-side cart display or perform other actions
 updateCartDisplay(updatedCart)
 } else if (event.name \=== 'cart\_abandoned') {
 // Handle abandoned cart logic
 handleAbandonedCart(event.payload.cart)
 }

 res.sendStatus(200)
})

**3\. Client-side Synchronization**

While webhooks provide real-time updates from the server, you might also want to implement additional synchronization mechanisms on the client-side, especially for user actions like adding or removing items from the cart.

Consider using techniques like:

- **WebSockets:** Establish a persistent connection between the client and server, allowing for real-time bidirectional communication.
- **Server-Sent Events (SSE):** A server-push technology where the server can send updates to the client without explicit requests.