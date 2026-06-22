# Source: https://docs.fluid.app/docs/themes/supported-paths

# Supported Paths

This guide documents all available URL paths in Fluid themes. These paths define the routes that can be themed and customized within your storefront.

> **Note**: The `:credit` parameter represents the affiliate/referral identifier (Rep username). When no affiliate attribution is present, `home` is used (e.g., `/home/products/my-product`).

---

## Table of Contents

1. [Home & Navigation](https://docs.fluid.app/#home--navigation)
2. [Shop & Products](https://docs.fluid.app/#shop--products)
3. [Collections & Categories](https://docs.fluid.app/#collections--categories)
4. [Enrollment & Joining](https://docs.fluid.app/#enrollment--joining)
5. [Content Pages](https://docs.fluid.app/#content-pages)
6. [User Pages](https://docs.fluid.app/#user-pages)
7. [Cart & Checkout](https://docs.fluid.app/#cart--checkout)

---

## Home & Navigation

### Home Page

| Path | Description |
| --- | --- |
| `/` | Company's root URL |
| `/home` | Main home page |
| `/:credit` | Home page with affiliate attribution |

The home page serves as the main landing page for your storefront. It typically displays featured products, promotions, and brand messaging.

---

## Shop & Products

### Shop Page

| Path | Description |
| --- | --- |
| `/:credit/shop` | Product catalog page displaying all available products in the company |

The shop page provides a browsable catalog of all products available for purchase.

### Product Detail Page

| Path | Description |
| --- | --- |
| `/:credit/products/:slug` | Individual product detail page (PDP) |

Displays comprehensive product information including images, descriptions, pricing, variants, and add-to-cart functionality.

---

## Collections & Categories

### Collection Pages

| Path | Description |
| --- | --- |
| `/:credit/collections` | List of all available collections |
| `/:credit/collections/:slug` | Collection detail page with products in the collection |

Collections allow you to group related products together for easier browsing and merchandising.

### Category Pages

| Path | Description |
| --- | --- |
| `/:credit/categories` | List of all available categories |
| `/:credit/categories/:slug` | Category detail page with products in the category |

Categories provide hierarchical organization for your product catalog.

---

## Enrollment & Joining

### Join Page

| Path | Description |
| --- | --- |
| `/:credit/join` | List of enrollment options and enrollment packs |

The join page displays available ways to enroll with the company, including different membership tiers and enrollment packs.

### Enrollment Pack Detail Page

| Path | Description |
| --- | --- |
| `/:credit/enrollments/:slug` | Enrollment pack detail page |

Displays detailed information about a specific enrollment pack, including included products, pricing, and enrollment benefits.

---

## Content Pages

### Media Detail Page

| Path | Description |
| --- | --- |
| `/:credit/media/:slug` | Media detail page for videos, images, and documents |

Displays media content such as training videos, promotional materials, or downloadable resources.

### Blog & Post Pages

| Path | Description |
| --- | --- |
| `/:credit/blog` | Blog index page listing all posts |
| `/:credit/posts/:slug` | Blog post or article detail page |

The blog index page displays a list of all published posts and articles. The post detail page shows the full content of an individual post.

### Page

| Path | Description |
| --- | --- |
| `/:credit/pages/:slug` | Custom page built with the page editor |

Custom pages allow you to create unique landing pages, about pages, or any other content using the page editor.

### Playlist Detail Page

| Path | Description |
| --- | --- |
| `/:credit/libraries/:slug` | Playlist detail page with curated media and content |

Displays a curated playlist of media items, products, or other content grouped together for a specific purpose or campaign.

---

## Rep Pages

### MySite Page

| Path | Description |
| --- | --- |
| `/my/:credit` | Affiliate's personal MySite page |

The MySite page is a personalized landing page for affiliates/representatives to share with their customers and prospects. MySite themes are available to add in the admin.

---

## Cart & Checkout

### Cart Page

| Path | Description |
| --- | --- |
| `/:credit/cart` | Shopping cart detail page |

Displays the current shopping cart contents, allowing customers to review items, update quantities, and proceed to checkout. By default, Fluid uses a side drawer cart, but you can override the settings in the admin.

---

## Path Parameters

| Parameter | Description |
| --- | --- |
| `:credit` | Affiliate/referral identifier (e.g., `home`, `john-smith`, or a rep's username) |
| `:slug` | URL-friendly identifier for a resource (e.g., `summer-collection`, `premium-product`) |

---

## Examples

Here are some example URLs using these paths:

\# Home pages
https://example.fluid.app/
https://example.fluid.app/home
https://example.fluid.app/john\-smith
https://example.com/
https://example.com/home
https://example.com/john\-smith
https://john\-smith.example.com

# Product pages
https://example.fluid.app/home/shop
https://example.fluid.app/john\-smith/shop
https://example.fluid.app/home/products/premium\-widget
https://example.com/john\-smith/products/blender\-bottle

# Collection pages
https://example.fluid.app/home/collections
https://example.fluid.app/home/collections/summer\-sale

# Category pages
https://example.fluid.app/home/categories
https://example.fluid.app/home/categories/electronics

# Enrollment pages
https://example.fluid.app/home/join
https://john\-smith.example.com/home/join
https://example.fluid.app/home/enrollments/starter\-pack

# Content pages
https://example.fluid.app/home/media/welcome\-video
https://example.fluid.app/home/blog
https://example.fluid.app/home/posts/company\-news
https://example.fluid.app/home/pages/about\-us
https://example.fluid.app/home/libraries/getting\-started\-playlist

# User pages
https://example.fluid.app/my/john\-smith
https://example.com/my/john\-smith

# Cart
https://example.fluid.app/home/cart