# NIRNAYAFlORALS
Symbolizes love, union, and a chosen path together --Flower shop web app built with React, Node/Express, and MongoDB. Includes inventory CRUD, search/filter, and API-driven product listing.

1) Project Overview
Product name: NirnayaFlorals
Domain: Online flower store (eCommerce)
Goal: Let customers browse flowers, customize bouquets, place orders, pay, and track delivery. Admin can manage products, inventory, orders, coupons, and customers.

Key User Roles
Guest: browse, search, view product details

Customer: login, add to cart, checkout, order history, tracking

Admin: product/catalog management, inventory, order management, pricing, discounts

Delivery/Operations (optional): assign deliveries, update status

2) Scope and Features
Customer Features (MVP)
Home + category listing

Search + filters (price, color, occasion, availability)

Product details (images, description, price, variants)

Add to cart / update quantity / remove

Checkout (address, delivery slot, message card, payment)

Order confirmation + email/SMS (optional)

Order tracking (Placed → Packed → Out for delivery → Delivered)

Admin Features (MVP)
Admin login

CRUD products, categories, variants

Inventory update

Order list + status update

Coupon create/apply rules

Dashboard summary (orders today, revenue, top products)

Nice-to-have (Phase 2)
Wishlist

Subscriptions (weekly/monthly flowers)

Recommendations (“frequently bought together”)

Reviews & ratings

Real-time delivery ETA (integrate maps)

3) UX: User Journeys
Journey A: Guest → Purchase
Landing page → browse collections

Search/filter product

Product detail → choose size/variant → add message card

Cart → review → proceed to checkout

Address + delivery slot → payment → order placed

Journey B: Customer → Track order
Login

Orders page

Open order details

Track status timeline + delivery notes

Journey C: Admin → Fulfill order
Admin login

Orders → open new order

Verify payment + stock

Update status (Packed → Out for delivery)

Mark delivered + capture proof (optional)

4) UI Screens (Suggested)
Customer UI
Home

Category/Product Listing

Product Detail

Cart

Checkout

Payment Result

Login/Signup

Profile (addresses)

Orders + Order Details/Tracking

Admin UI
Admin Dashboard

Products (list + add/edit)

Categories

Inventory

Orders (list + detail)

Coupons

Users (optional)

UI/UX Rules
Mobile-first layout

Fast product discovery (search, filters always visible)

Clear price breakdown in checkout (subtotal, discount, delivery fee, taxes)

Status timeline for orders

5) System Architecture (High Level)
Frontend (UX/UI):

React (or Angular/Next.js)

Calls REST APIs

Backend (API):

Java 17 + Spring Boot

JWT auth + Role based access (CUSTOMER/ADMIN)

PostgreSQL for transactional data

MongoDB for flexible documents (catalog content, logs, event history, search docs, etc.)

Storage (Optional):

S3/Cloud storage for images

External Integrations (Optional):

Payment gateway (Stripe/Razorpay/etc.)

Email/SMS (SendGrid/Twilio)

6) Backend Modules (Java / Spring Boot)
Suggested Spring Boot Services
auth-service: signup/login, JWT, roles

catalog-service: categories, products, variants, pricing

cart-service: cart operations

order-service: checkout, order creation, order status workflow

admin-service: admin CRUD + management endpoints

notification-service (optional): email/SMS events

If you want one codebase: keep them as modules/packages in one Spring Boot app.

7) API Design (REST)
Auth
POST /api/auth/signup

POST /api/auth/login

GET /api/auth/me

Catalog
GET /api/products?search=&category=&minPrice=&maxPrice=&sort=

GET /api/products/{productId}

GET /api/categories

Cart
GET /api/cart

POST /api/cart/items (add item)

PUT /api/cart/items/{itemId} (update qty)

DELETE /api/cart/items/{itemId}

Checkout & Orders
POST /api/checkout (address, slot, payment method)

GET /api/orders

GET /api/orders/{orderId}

POST /api/orders/{orderId}/cancel

GET /api/orders/{orderId}/track

Admin
POST /api/admin/products

PUT /api/admin/products/{id}

DELETE /api/admin/products/{id}

PUT /api/admin/inventory/{sku} (update stock)

GET /api/admin/orders?status=

PUT /api/admin/orders/{orderId}/status

8) PostgreSQL Data Model (Transactional)
Core Tables
users

id (uuid, pk)

name, email (unique), phone

password_hash

role (CUSTOMER/ADMIN)

created_at

addresses

id, user_id (fk)

line1, line2, city, state, zip, country

is_default

categories

id, name, slug

products

id

category_id (fk)

name, description

base_price

is_active

created_at

product_variants

id

product_id (fk)

sku (unique)

variant_name (Small/Medium/Large)

price

attributes_json (optional)

is_active

inventory

sku (pk, fk to product_variants.sku)

available_qty

reserved_qty

updated_at

carts

id

user_id (fk)

status (ACTIVE/CHECKED_OUT)

updated_at

cart_items

id

cart_id (fk)

sku

quantity

unit_price

custom_message (nullable)

orders

id

user_id

address_id

status (PLACED, PACKED, OUT_FOR_DELIVERY, DELIVERED, CANCELLED)

subtotal, discount, delivery_fee, tax, total

payment_status (PENDING, PAID, FAILED, REFUNDED)

placed_at

order_items

id

order_id (fk)

sku

product_name_snapshot

variant_snapshot

unit_price

quantity

custom_message

coupons

id

code (unique)

type (PERCENT/FIXED)

value

min_order_value

valid_from, valid_to

is_active

9) MongoDB Design (Flexible / Non-Relational)
Use MongoDB where structure changes often or you want fast document reads.

Recommended Collections
product_content

_id

productId

seo: { title, metaDesc, keywords }

media: [{url, type, alt}]

longDescription (rich content)

tags ["anniversary", "roses", "gift"]

order_events

_id

orderId

events: [
{ status, timestamp, updatedBy, note }
]

audit_logs

_id

actorId, actorRole

action, entityType, entityId

before, after

timestamp

Simple rule: Postgres = truth for orders/payments/inventory. Mongo = content + logs + event history.

10) Business Rules
Inventory
Reserve stock at checkout start (or after payment success)

On payment failure → release reservation

On cancel/refund → restock

Pricing
Variant overrides base price

Coupon rules apply to subtotal

Delivery fee based on zip / distance (phase 2) or flat rate (MVP)

Order Status Flow
PLACED → PACKED → OUT_FOR_DELIVERY → DELIVERED
CANCELLED can happen only if not OUT_FOR_DELIVERY (MVP rule)

11) Security
JWT authentication

Password hashing: BCrypt

RBAC:

CUSTOMER: cart, orders, profile

ADMIN: products, inventory, admin orders

Input validation + rate limiting (basic)

Secure headers + CORS allowed list

12) Non-Functional Requirements
Response time: < 300ms for product listing (cached if needed)

Pagination on listing and admin tables

Observability: logs + metrics

Backups: Postgres daily snapshots + Mongo backups

Disaster recovery plan (basic)

13) Testing Strategy
Frontend
Unit tests (components)

E2E (checkout flow)

Backend
Unit tests (services)

Integration tests (API + DB)

Contract tests (frontend ↔ backend)

Load test (product listing / checkout)

14) Deployment (Simple)
Dockerize frontend + backend

Environments: dev / stage / prod

CI/CD:

Build → test → docker image → deploy

DB migrations: Flyway or Liquibase for Postgres

Mongo migrations optional (versioned scripts)

15) Deliverables Checklist
UX wireframes (Figma)

UI design system (colors, typography, components)

API spec (OpenAPI/Swagger)

DB schema scripts (Postgres)

Mongo collection definitions + indexes

Java Spring Boot repo with modules

Test reports

Deployment docs

