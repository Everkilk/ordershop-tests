# Demo Shop API — Test Cases (Postman)
**System Under Test (SUT):** Skyramp Demo Shop API  
**Base URL:** `https://demoshop.skyramp.dev`  
**API Version:** v1  
**Spec:** `https://demoshop.skyramp.dev/openapi.json`  
**Auth:** HTTP Bearer (`Authorization: Bearer <session_id>`)  
**Generate session:** `GET /api/v1/generate` (returns plain text session id)

**Execution date:** 2026-03-15  
**Tools:** Postman (Collection + Environment + Tests scripts)

---

## Variables (Postman Environment)
| Variable | Example | Notes |
|---|---|---|
| `baseUrl` | `https://demoshop.skyramp.dev` | Base server |
| `sessionId` | `myself-drop-aboard` | Set by `GET /api/v1/generate` (plaintext) |
| `productId` | `123` | Set after create product |
| `orderId` | `456` | Set after create order |

---

## Endpoints in scope
### Status
- `GET /`
- `GET /health`

### Auth/Session
- `GET /api/v1/generate`

### Products
- `GET /api/v1/products`
- `POST /api/v1/products`
- `GET /api/v1/products/{product_id}`
- `PUT /api/v1/products/{product_id}`
- `DELETE /api/v1/products/{product_id}`

### Reviews
- `GET /api/v1/products/{product_id}/reviews`
- `POST /api/v1/products/{product_id}/reviews`

### Orders
- `GET /api/v1/orders`
- `POST /api/v1/orders`
- `GET /api/v1/orders/{order_id}`
- `DELETE /api/v1/orders/{order_id}` (Cancel order)

### Reset
- `POST /api/v1/reset`

---

## Sample valid payloads (use as baseline)
### ProductCreate (valid)
```json
{
  "name": "Bigbear",
  "description": "Bear Soft Toy",
  "price": 9.99,
  "image_url": "https://images.app.goo.gl/cgcHpeehRdu5osot8",
  "category": "Toys",
  "in_stock": true
}
```

### ProductUpdate (valid; fields optional)
```json
{
  "name": "Bigbear",
  "description": "Bear Soft Toy Updated",
  "price": 29.99,
  "image_url": "https://images.app.goo.gl/cgcHpeehRdu5osot8",
  "category": "Toys",
  "in_stock": false
}
```

### ReviewCreate (valid)
```json
{
  "rating": 5,
  "comment": "Great product!"
}
```

### OrderCreate (valid)
```json
{
  "customer_email": "abc@mail.com",
  "items": [
    { "product_id": 1, "quantity": 2 }
  ]
}
```

---

# Test Cases

> **Legend:**  
> **Type:** Positive / Negative / Boundary / Security / Smoke  
> **Expected status codes** are based on the OpenAPI spec:
> - Success: `200`, `201`, `204`  
> - Validation errors: `422`  
> - Auth errors are not explicitly documented in the spec; expect `401/403` if enforced.

---

## Module A — Status & Availability

### TC-API-001 — Root endpoint returns API info
- **Type:** Smoke
- **Endpoint:** `GET /`
- **Auth:** Not required (per spec)
- **Steps:**
  1. Send request
- **Expected:**
  - Status `200`
  - Response is JSON (any non-empty object is acceptable)

### TC-API-002 — Health check returns healthy response
- **Type:** Smoke
- **Endpoint:** `GET /health`
- **Auth:** Not required (per spec)
- **Steps:**
  1. Send request
- **Expected:**
  - Status `200`
  - Response is JSON (any non-empty object is acceptable)

---

## Module B — Authentication / Session

### TC-API-003 — Generate session_id (plaintext)
- **Type:** Smoke
- **Endpoint:** `GET /api/v1/generate`
- **Auth:** Not required
- **Steps:**
  1. Send request
- **Expected:**
  - Status `200`
  - Response body is non-empty plain text (e.g., `word-word-word`)
  - Save value as `sessionId` in Postman environment

### TC-API-004 — Access protected endpoint without token (if enforced)
- **Type:** Security (Negative)
- **Endpoint:** `GET /api/v1/products`
- **Auth:** Omit `Authorization` header
- **Steps:**
  1. Ensure Authorization is disabled/removed
  2. Send request
- **Expected:**
  - Status `401` or `403` (if API enforces auth)
  - Response contains an error message
- **Notes:** If API still returns `200`, record it as an observation (auth not enforced).

### TC-API-005 — Access protected endpoint with invalid token (if enforced)
- **Type:** Security (Negative)
- **Endpoint:** `GET /api/v1/products`
- **Auth:** `Authorization: Bearer invalid-token`
- **Steps:**
  1. Set Bearer token to an invalid value
  2. Send request
- **Expected:**
  - Status `401` or `403` (if API enforces auth)

---

## Module C — Products API

### TC-API-010 — Get list of products (default params)
- **Type:** Smoke
- **Endpoint:** `GET /api/v1/products`
- **Auth:** Bearer `sessionId`
- **Steps:**
  1. Send request with no query params
- **Expected:**
  - Status `200`
  - Response is JSON array
  - Each item includes at least: `product_id`, `name`, `price`, `created_at`, `updated_at`

### TC-API-011 — Get list of products with pagination params
- **Type:** Positive
- **Endpoint:** `GET /api/v1/products?limit=5&offset=0`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `200`
  - Response array length `<= 5`

### TC-API-012 — Get list of products invalid limit (limit > 100)
- **Type:** Negative (Validation)
- **Endpoint:** `GET /api/v1/products?limit=101`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `422`
  - Response body matches validation error structure (`detail[]` with `loc/msg/type`)

### TC-API-013 — Get list of products invalid offset (negative)
- **Type:** Negative (Validation)
- **Endpoint:** `GET /api/v1/products?offset=-1`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `422`

### TC-API-014 — Create product (valid data)
- **Type:** Positive
- **Endpoint:** `POST /api/v1/products`
- **Auth:** Bearer `sessionId`
- **Body:** valid `ProductCreate`
- **Steps:**
  1. Send request with valid JSON body
- **Expected:**
  - Status `201`
  - Response includes `product_id`
  - Save `product_id` into `productId`

### TC-API-015 — Create product missing required field `name`
- **Type:** Negative (Validation)
- **Endpoint:** `POST /api/v1/products`
- **Auth:** Bearer `sessionId`
- **Body:** omit `name`
- **Expected:**
  - Status `422`

### TC-API-016 — Create product invalid price (negative value)
- **Type:** Negative (Validation/Boundary)
- **Endpoint:** `POST /api/v1/products`
- **Auth:** Bearer `sessionId`
- **Body:** set `price = -1`
- **Expected:**
  - Status `422` (price minimum is 0)

### TC-API-017 — Create product invalid field patterns (name not matching pattern)
- **Type:** Negative (Validation)
- **Endpoint:** `POST /api/v1/products`
- **Auth:** Bearer `sessionId`
- **Body:** set `name = "123###"`
- **Expected:**
  - Status `422` (pattern violation)

### TC-API-018 — Get product by ID (valid)
- **Type:** Positive
- **Endpoint:** `GET /api/v1/products/{product_id}`
- **Auth:** Bearer `sessionId`
- **Preconditions:** `productId` exists (from TC-API-014)
- **Steps:**
  1. Send request using `product_id={{productId}}`
- **Expected:**
  - Status `200`
  - Response `product_id` equals `{{productId}}`

### TC-API-019 — Get product by ID invalid type (non-integer path)
- **Type:** Negative (Validation)
- **Endpoint:** `GET /api/v1/products/abc`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `422` (path param must be integer)

### TC-API-020 — Update product by ID (valid)
- **Type:** Positive
- **Endpoint:** `PUT /api/v1/products/{product_id}`
- **Auth:** Bearer `sessionId`
- **Preconditions:** `productId` exists
- **Body:** valid `ProductUpdate`
- **Expected:**
  - Status `200`
  - Response includes `product_id={{productId}}`
  - `updated_at` changes (or at least is present)

### TC-API-021 — Update product by ID invalid body (price negative)
- **Type:** Negative (Validation)
- **Endpoint:** `PUT /api/v1/products/{product_id}`
- **Auth:** Bearer `sessionId`
- **Body:** `{"price": -10}`
- **Expected:**
  - Status `422`

### TC-API-022 — Delete product by ID (valid)
- **Type:** Positive
- **Endpoint:** `DELETE /api/v1/products/{product_id}`
- **Auth:** Bearer `sessionId`
- **Preconditions:** `productId` exists
- **Expected:**
  - Status `204`
  - Empty response body is acceptable

### TC-API-023 — Delete product invalid path (non-integer)
- **Type:** Negative (Validation)
- **Endpoint:** `DELETE /api/v1/products/xyz`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `422`

---

## Module D — Reviews API

### TC-API-030 — Create review for product (valid)
- **Type:** Positive
- **Endpoint:** `POST /api/v1/products/{product_id}/reviews`
- **Auth:** Bearer `sessionId`
- **Preconditions:** `productId` exists
- **Body:** valid `ReviewCreate`
- **Expected:**
  - Status `201`
  - Response includes `rating` and `comment`

### TC-API-031 — Create review missing required field `comment`
- **Type:** Negative (Validation)
- **Endpoint:** `POST /api/v1/products/{product_id}/reviews`
- **Auth:** Bearer `sessionId`
- **Body:** `{ "rating": 5 }`
- **Expected:**
  - Status `422`

### TC-API-032 — Get all reviews for selected product
- **Type:** Positive
- **Endpoint:** `GET /api/v1/products/{product_id}/reviews`
- **Auth:** Bearer `sessionId`
- **Preconditions:** At least one review created for `productId`
- **Expected:**
  - Status `200`
  - Response is JSON array
  - Array contains objects with `rating` and `comment`

### TC-API-033 — Get reviews invalid product_id (non-integer)
- **Type:** Negative (Validation)
- **Endpoint:** `GET /api/v1/products/abc/reviews`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `422`

---

## Module E — Orders API

### TC-API-040 — Create order (valid)
- **Type:** Positive
- **Endpoint:** `POST /api/v1/orders`
- **Auth:** Bearer `sessionId`
- **Preconditions:** Ensure at least one product exists; use `productId` if created
- **Body:** valid `OrderCreate` using an existing `product_id`
- **Expected:**
  - Status `201`
  - Response includes `order_id`
  - Save as `orderId`

### TC-API-041 — Create order invalid email format
- **Type:** Negative (Validation)
- **Endpoint:** `POST /api/v1/orders`
- **Auth:** Bearer `sessionId`
- **Body:** set `customer_email = "not-an-email"`
- **Expected:**
  - Status `422`

### TC-API-042 — Create order with empty items array
- **Type:** Negative (Validation)
- **Endpoint:** `POST /api/v1/orders`
- **Auth:** Bearer `sessionId`
- **Body:** `{ "customer_email":"abc@mail.com", "items":[] }`
- **Expected:**
  - Status `422` (or validation error per server behavior)

### TC-API-043 — Create order with invalid quantity (negative)
- **Type:** Negative (Validation/Boundary)
- **Endpoint:** `POST /api/v1/orders`
- **Auth:** Bearer `sessionId`
- **Body:** quantity = `-1`
- **Expected:**
  - Status `422` (minimum is 0)

### TC-API-044 — Get all orders (default)
- **Type:** Positive
- **Endpoint:** `GET /api/v1/orders`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `200`
  - Response is JSON array

### TC-API-045 — Get order by ID (valid)
- **Type:** Positive
- **Endpoint:** `GET /api/v1/orders/{order_id}`
- **Auth:** Bearer `sessionId`
- **Preconditions:** `orderId` exists (from TC-API-040)
- **Expected:**
  - Status `200`
  - Response `order_id` equals `{{orderId}}`

### TC-API-046 — Get order by ID invalid type (non-integer)
- **Type:** Negative (Validation)
- **Endpoint:** `GET /api/v1/orders/abc`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `422`

### TC-API-047 — Cancel order (valid)
- **Type:** Positive
- **Endpoint:** `DELETE /api/v1/orders/{order_id}`
- **Auth:** Bearer `sessionId`
- **Preconditions:** `orderId` exists
- **Expected:**
  - Status `200`
  - Response contains message like “Order cancelled successfully” (per `OrderCancel` model)

---

## Module F — Reset

### TC-API-050 — Reset data for session
- **Type:** Positive / Maintenance
- **Endpoint:** `POST /api/v1/reset`
- **Auth:** Bearer `sessionId`
- **Expected:**
  - Status `200`
  - Response is JSON (may be empty object)

---

## Notes / Assumptions
- The OpenAPI spec documents `422` for validation errors across endpoints.
- Not-found behavior (`404`) is not explicitly documented for some endpoints; if you observe `404` for missing `product_id/order_id`, record actual behavior in the test report.
- Some schema constraints include `pattern` rules (e.g., `^[A-Za-z]+.*\s*$`)—tests validate the server enforces these consistently.
