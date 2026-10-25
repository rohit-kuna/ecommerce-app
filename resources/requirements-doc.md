# Software Requirements Specification (SRS)
## Simple E-Commerce Application

---

## 1. Executive Summary
This document outlines the functional and non-functional requirements for building a **Simple E-Commerce Application** using **Java Spring Boot** (Backend API) and **React** (Frontend SPA) with **Simple Authentication (JWT)**.

The goal of this application is to serve as a practical full-stack project covering user authentication, catalog management, cart handling, and order processing.

---

## 2. System Architecture & Tech Stack
* **Backend:** Java Spring Boot (REST API, Spring Data JPA, Spring Security)
* **Frontend:** React.js (Component-based UI, Axios/Fetch API, React Router)
* **Database:** Relational Database (H2 for development, PostgreSQL/MySQL for production)
* **Authentication:** Simple JWT (JSON Web Token) with BCrypt password hashing

---

## 3. Core Entities & Data Model

### 3.1 Entities Overview
1. **User:** System users (Customers and Administrators).
2. **Product:** Items available for purchase in the store.
3. **CartItem:** Temporary items added by a user before checkout.
4. **Order:** Completed purchases made by users.
5. **OrderItem:** Snapshot of specific products within a completed order (captures historical price and quantity).

---

## 4. Functional Requirements

### Module 1: User & Authentication
* **FR-1.1 Register Account:** New users can sign up with name, email, and password. Default role assigned is `ROLE_CUSTOMER`.
* **FR-1.2 Login:** Users authenticate with email and password to receive a JWT token.
* **FR-1.3 Profile Management:** Users can view their profile and order history.
* **FR-1.4 Roles:** 
  * `CUSTOMER`: Can browse, add to cart, and place orders.
  * `ADMIN`: Can manage products, categories, and view all system orders.

---

### Module 2: Product Catalog
* **FR-2.1 View Products:** Publicly accessible list of available products displaying title, description, price, image URL, and stock quantity.
* **FR-2.2 Product Details:** View individual product details page.
* **FR-2.3 Product Management (Admin Only):**
  * Create new product (`POST /api/products`)
  * Update existing product (`PUT /api/products/{id}`)
  * Delete product (`DELETE /api/products/{id}`)
* **FR-2.4 Search & Filter (Basic):** Filter products by category or simple keyword search.

---

### Module 3: Shopping Cart
* **FR-3.1 Add to Cart:** Authenticated users can add products to their cart specifying quantity.
* **FR-3.2 View Cart:** Displays items in the cart with line-item totals and overall cart total.
* **FR-3.3 Update Cart Quantity:** Users can increase, decrease, or remove items from the cart.
* **FR-3.4 Clear Cart:** Empty all items from the active cart.

---

### Module 4: Order Management
* **FR-4.1 Checkout & Place Order:**
  * User initiates checkout from the cart.
  * Enter shipping address and payment method (simulated/dummy payment).
  * Backend verifies stock availability, calculates total price, converts `CartItem`s into `OrderItem`s, creates an `Order`, decreases product stock, and clears the user's cart in a single transaction.
* **FR-4.2 Order History (Customer):** Customers can view past orders with order date, status, total price, and item breakdown.
* **FR-4.3 All Orders View (Admin):** Admins can view all orders across all users and update order status (`PENDING`, `SHIPPED`, `DELIVERED`, `CANCELLED`).

---

## 5. REST API Endpoints Specification

### 5.1 Auth Endpoints
* `POST /api/auth/register` — Register a new account.
* `POST /api/auth/login` — Authenticate and get JWT token.

### 5.2 Product Endpoints
* `GET /api/products` — Get list of products.
* `GET /api/products/{id}` — Get single product details.
* `POST /api/products` — Add product *(Admin)*.
* `PUT /api/products/{id}` — Update product *(Admin)*.
* `DELETE /api/products/{id}` — Delete product *(Admin)*.

### 5.3 Cart Endpoints
* `GET /api/cart` — Get logged-in user's cart.
* `POST /api/cart/items` — Add item to cart.
* `PUT /api/cart/items/{itemId}` — Update item quantity in cart.
* `DELETE /api/cart/items/{itemId}` — Remove item from cart.

### 5.4 Order Endpoints
* `POST /api/orders` — Create order from current cart (Checkout).
* `GET /api/orders/my-orders` — Get logged-in user's order history.
* `GET /api/orders` — Get all orders in system *(Admin)*.
* `PUT /api/orders/{id}/status` — Update order status *(Admin)*.

---

## 6. Key Non-Functional & Security Requirements
1. **Password Security:** Passwords must be hashed using BCrypt before saving to the database.
2. **Stateless Sessions:** API routes must be stateless using JWT tokens in the `Authorization: Bearer <token>` header.
3. **Transactional Integrity:** Order placement (`POST /api/orders`) must be wrapped in a `@Transactional` block to prevent partial orders or negative stock.
4. **Error Handling:** Centralized exception handling on the backend returning clear JSON error messages (e.g., `404 Not Found`, `400 Bad Request` for insufficient stock).