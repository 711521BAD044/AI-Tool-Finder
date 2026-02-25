# AI Tool Finder

## 📌 Project Overview

**AI Tool Finder** is a full-stack platform for discovering, filtering, and reviewing AI tools.
It consists of:

* **Backend:** FastAPI + PostgreSQL providing secure user authentication, role-based access control, and dynamic ratings aggregation.
* **Frontend:** Responsive web interface (HTML, CSS, JavaScript) for browsing tools, submitting reviews, and admin management.

Key features:

* JWT-based authentication and authorization (USER / ADMIN)
* Admin moderation of reviews
* Advanced filtering by category, pricing, and rating
* Dynamic `avg_rating` calculation from approved reviews
* Role-based UI rendering

---

## 🧱 Technology Stack

**Backend:**

* Framework: FastAPI (Python 3.10+)
* Data validation: Pydantic
* Database: PostgreSQL (ACID-compliant)
* Authentication: JWT
* Password Security: bcrypt / passlib
* Async REST APIs, automatic OpenAPI (Swagger) documentation

**Frontend:**

* Markup: HTML5
* Styling: CSS3
* Logic: Vanilla JavaScript
* API Communication: Fetch API
* Authentication: JWT stored in localStorage

---

## 🔧 Setup & Run Instructions

### Backend

```bash
# 1. Clone repository
git clone <repo-url>

# 2. Create virtual environment
python -m venv .venv

# 3. Activate virtual environment
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

# 4. Install dependencies
pip install -r requirements.txt

# 5. Create .gitignore
echo .venv/ > .gitignore

# 6. Run application
uvicorn main:app --reload --port 8080
```

### Frontend

1. Open `index.html` in a browser.
2. Ensure `config.js` points to your backend `BASE_URL`.
3. Login / Register to access user-specific features.

---

## 🗄️ Data Models

### AI Tool

| Field          | Type     | Description                    |
| -------------- | -------- | ------------------------------ |
| `tool_id`      | string   | Unique ID                      |
| `tool_name`    | string   | Name (unique)                  |
| `use_case`     | string   | Purpose                        |
| `category`     | string   | Domain category                |
| `pricing_type` | string   | `FREE`, `PAID`, `SUBSCRIPTION` |
| `avg_rating`   | float    | Derived from approved reviews  |
| `created_at`   | datetime | Timestamp                      |
| `updated_at`   | datetime | Timestamp                      |

### Review

| Field             | Type     | Description                       |
| ----------------- | -------- | --------------------------------- |
| `review_id`       | string   | Unique ID                         |
| `tool_id`         | string   | FK to AI Tool                     |
| `user_id`         | string   | User submitting review            |
| `user_rating`     | int      | 1–5 stars                         |
| `comment`         | string   | Optional text                     |
| `approval_status` | string   | `PENDING`, `APPROVED`, `REJECTED` |
| `created_at`      | datetime | Timestamp                         |
| `updated_at`      | datetime | Timestamp                         |

### User

| Field        | Type     | Description      |
| ------------ | -------- | ---------------- |
| `user_id`    | string   | Unique ID        |
| `username`   | string   | Unique           |
| `email`      | string   | Unique           |
| `first_name` | string   | First name       |
| `last_name`  | string   | Last name        |
| `role`       | string   | `USER` / `ADMIN` |
| `password`   | string   | Hashed           |
| `created_at` | datetime | Account creation |

---

## 🔌 API Endpoints

### Public APIs

#### GET `/tools`

* Fetch all AI tools
* Supports filters: `category`, `pricing_type`, `min_rating`
* Example: `/tools?category=NLP&pricing_type=FREE&min_rating=4`

#### POST `/review`

* Submit a review (initially `PENDING`)
* Body: `{ "tool_id": "123", "user_rating": 5, "comment": "Great tool!" }`

### Admin APIs

#### POST `/admin/add-tool`

* Add a new tool

#### PUT `/admin/Update_tool/{tool_id}`

* Update existing tool

#### DELETE `/admin/delete_tool/{tool_id}`

* Delete tool

#### PATCH `/admin/approve_review/{review_id}`

* Approve review & update tool’s `avg_rating`

#### PATCH `/admin/reject_review/{review_id}`

* Reject review

---

## ⭐ Rating & Review Logic

1. Reviews start as `PENDING`.
2. Admin approves → rating added to `avg_rating`.
3. Formula: `avg_rating = SUM(approved_ratings) / COUNT(approved_reviews)`
4. Edge Cases:

   * No approved reviews → `avg_rating = 0`
   * Updates/deletions trigger recalculation

---

## 🔎 Filtering Logic

* Multi-condition filters supported
* Partial match for `category`
* Exact match for `pricing_type`
* `min_rating` filters by `avg_rating`

---

## 🔐 Security & Access Control

* JWT authentication required for protected routes
* Admin endpoints enforce `role=ADMIN`
* Passwords stored securely
* Role-based UI in frontend
* All inputs validated (422 errors on invalid requests)

---

## 🖥️ Frontend Pages

| Page             | Purpose               | Features                                  | Script         |
| ---------------- | --------------------- | ----------------------------------------- | -------------- |
| index.html       | Home / tool discovery | Search, filters, dynamic tool cards       | script.js      |
| auth.html        | Login & registration  | JWT-based login, role detection           | auth.js        |
| tool-detail.html | Tool info             | Metadata, approved reviews, submit review | tool-detail.js |
| profile.html     | User profile          | View user info, logout                    | profile.js     |
| admin.html       | Admin dashboard       | CRUD tools, approve/reject reviews        | admin.js       |

**Other:**

* `styles.css` – Global styling
* `loader.html` – API loading overlay
* `api.js` – Centralized Fetch API integration
* `config.js` – Backend URL

---

## 🎛️ Search & Filtering

* Category: partial, case-insensitive
* Pricing: exact match
* Minimum rating: 0–5
* Filters sent as query parameters to `/tools` endpoint
* UI updates dynamically

---

## 🚀 Extensibility

* Pagination, sorting
* Favorites / bookmarks
* Review editing
* Dark mode
* OAuth login

---

## 🧪 Browser Compatibility

* Chrome, Firefox, Edge, modern mobile browsers

---
