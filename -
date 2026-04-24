# Subscriptions Organiser Platform | Stage 3: Technical Documentation

---

## Task 4: Document External and Internal APIs

This section documents the external APIs and libraries integrated into the Subscriptions Organiser Platform, and defines the platform's own internal REST API endpoints with full input and output specifications.

---

### 4.1 External APIs

The following external APIs and libraries were selected based on reliability, ease of integration, cost, and direct alignment with the project's SMART objectives and risk mitigation strategies.

| **API / Library** | **Provider** | **Method** | **URL / Usage** | **Purpose** | **Reason for Selection** |
|---|---|---|---|---|---|
| **Resend Email API** | Resend | `POST` | `api.resend.com/emails` | Send automated renewal reminder emails 3 days before each subscription renews. | Reliable transactional delivery, free tier (100 emails/day), simple REST API. Directly enables the 3-day reminder SMART objective. |
| **JWT (jsonwebtoken)** | npm / Auth0 | Library | `jwt.sign()` / `jwt.verify()` | Stateless user session auth and secure API route protection. | Eliminates server-side sessions. Widely adopted. Mitigates Auth Security risk (Project Charter Risk #1). |
| **bcrypt (bcryptjs)** | npm library | Library | `bcrypt.hash()` / `bcrypt.compare()` | Secure password hashing before storing credentials in PostgreSQL. | Gold-standard adaptive hashing. Protects against database breaches. Recommended in Project Charter risk mitigation. |
| **node-cron** | npm library | Library | `cron.schedule('0 8 * * *', ...)` | Schedule daily jobs to check upcoming renewals and trigger email sending. | Runs inside Node.js process — no extra infrastructure. Timezone-aware. Mitigates Reminder Reliability risk (Risk #2). |
| **jsPDF** | npm library | Library | `new jsPDF()` — client-side | Generate monthly spending reports as downloadable PDF files from the dashboard. | Lightweight client-side PDF generation. No external API calls required. |

---

### 4.2 Internal API Endpoints

The platform exposes a RESTful API built with Node.js and Express. All endpoints use JSON for data exchange. Protected endpoints require a valid JWT in the `Authorization` header as `Bearer <token>`.

| **Method** | **URL Path** | **Description** | **Input** | **Output (JSON)** |
|---|---|---|---|---|
| **— Authentication Endpoints —** |||||
| `POST` | `/api/auth/register` | Register new user | `{ name, email, password }` | `{ message, token, userId }` |
| `POST` | `/api/auth/login` | Authenticate user | `{ email, password }` | `{ token, userId, name }` |
| `POST` | `/api/auth/logout` | End session | Header: `Bearer <token>` | `{ message }` |
| `GET` | `/api/auth/profile` | Get user profile | Header: `Bearer <token>` | `{ userId, name, email, createdAt }` |
| **— Subscription Endpoints —** |||||
| `GET` | `/api/subscriptions` | List all subscriptions | Header: `Bearer <token>` | `{ subscriptions: [ {id, name, category, amount, renewalDate, status} ] }` |
| `POST` | `/api/subscriptions` | Add subscription | `{ name, categoryId, amount, currency, billingCycle, renewalDate }` | `{ message, subscription: { id, ... } }` |
| `GET` | `/api/subscriptions/:id` | Get one subscription | URL param: `id` + Bearer token | `{ id, name, category, amount, renewalDate, notes, status }` |
| `PUT` | `/api/subscriptions/:id` | Update subscription | URL param: `id` + JSON body | `{ message, subscription: { updated fields } }` |
| `DELETE` | `/api/subscriptions/:id` | Delete subscription | URL param: `id` + Bearer token | `{ message: 'Subscription deleted' }` |
| **— Dashboard & Analytics Endpoints —** |||||
| `GET` | `/api/dashboard/summary` | Monthly spend summary | Header: `Bearer <token>` | `{ totalMonthlySpend, activeCount, upcomingRenewals: [ ... ] }` |
| `GET` | `/api/dashboard/categories` | Spend by category | Header: `Bearer <token>` | `{ categories: [ { name, totalSpend, count } ] }` |
| **— Reminders Endpoint —** |||||
| `GET` | `/api/reminders/upcoming` | Upcoming renewals | Query: `?days=3` + Bearer token | `{ reminders: [ { subscriptionId, name, renewalDate, amount } ] }` |

---

### 4.3 Sample Request and Response

#### `POST /api/subscriptions` — Add a New Subscription

**Request Body:**

```json
{
  "name": "Netflix",
  "categoryId": 1,
  "amount": 49.99,
  "currency": "SAR",
  "billingCycle": "monthly",
  "renewalDate": "2025-05-15"
}
```

**Success Response `201 Created`:**

```json
{
  "message": "Subscription added successfully",
  "subscription": {
    "id": 42,
    "name": "Netflix",
    "category": "Entertainment",
    "amount": 49.99,
    "renewalDate": "2025-05-15",
    "status": "active"
  }
}
```

**Error Response `400 Bad Request`:**

```json
{
  "error": "Validation failed",
  "details": "renewalDate is required and must be a valid date."
}
```

---

## Task 5: Plan SCM and QA Strategies

This section defines the Source Code Management (SCM) approach and Quality Assurance (QA) strategy for the Subscriptions Organiser Platform, ensuring collaborative, reviewed, and tested development throughout the project lifecycle.

---

### 5.1 Source Code Management (SCM) Strategy

The team uses Git for version control with the repository hosted on GitHub. GitHub Actions provides continuous integration, and the repository is shared across all four team members.

#### Branching Strategy

| **Branch** | **Purpose** | **Rules** |
|---|---|---|
| `main` | Production-ready code only | Protected. No direct commits. Merged from `development` only after full QA sign-off. |
| `development` | Integration branch | All feature branches merge here via PR. Requires peer review and passing automated tests. |
| `staging` | Pre-production testing environment | Auto-deployed when code is merged into `development`. Used for UAT and integration testing before any release to production. |
| `feature/<n>` | Individual feature development | Created from `development`. Named descriptively: `feature/user-auth`, `feature/reminder-system`. |
| `bugfix/<n>` | Bug isolation and fixing | Created from `development`. Merged back after fix is verified. |

#### Commit and Code Review Standards

- Commit messages follow the format: `[type]: short description` — e.g., `feat: add reminder cron job` / `fix: correct JWT expiry` / `docs: update API table`.
- Commits are small and focused — each represents a single logical change.
- Every pull request requires review by at least one other team member before merging.
- PR descriptions must include: what changed, why it changed, and how it was tested.
- No developer may approve and merge their own pull request.
- All review feedback must be addressed before the PR is approved.

---

### 5.2 QA Strategy — Testing Types and Tools

QA combines automated and manual testing across multiple layers to validate individual components and complete user flows, directly supporting the project SMART objectives and mitigating Project Charter risks.

| **Test Type** | **Tool** | **What is Tested** | **Details** |
|---|---|---|---|
| **Unit Testing** | Jest | Individual functions: password hashing, token generation, date calculations. | Min 70% code coverage. Runs automatically on every push via GitHub Actions. |
| **Integration Testing** | Jest + Supertest | Full API request-response cycle including database interactions. | Runs against a dedicated test database. Validates all CRUD and auth flows. |
| **API Testing** | Postman | All REST endpoints: status codes, validation, error responses, JWT protection. | Shared Postman Collection in the repository. Each developer tests before opening a PR. |
| **Manual / UAT** | Browser + Checklist | Critical flows: registration, login, add/edit/delete subscription, dashboard, reminders. | On staging before any merge to `main`. Validates SMART objective (80% register under 2 min). |
| **Regression Testing** | Jest (full suite) | Previously working features — ensures new changes don't break existing functionality. | Full suite re-run on every PR to `development`. Any failure blocks the merge. |

---

### 5.3 Deployment Pipeline

The deployment pipeline automates the journey from code commit to production release, ensuring that only tested and reviewed code reaches end users.

| **Stage** | **Trigger** | **Steps** | **Purpose** |
|---|---|---|---|
| **1. Code Push** | Push to any feature/bugfix branch | Run unit tests via GitHub Actions. Report pass/fail on commit. | Fast feedback. Catch issues before code review. |
| **2. Pull Request** | PR opened targeting `development` | Full test suite runs. Peer review required. All checks must pass before merge. | Ensures quality. Prevents regressions in the integration branch. |
| **3. Staging Deploy** | Merge into `development` | Auto-deploy to staging. Manual UAT and integration testing by the team. | Production-like environment for thorough testing before release. |
| **4. Production Deploy** | PR from `development` to `main` approved | Auto-deploy to production. Final smoke test confirms critical flows work live. | Releases stable, tested features to users with minimal risk. |

---

### 5.4 Environments

- **Development (local):** Each developer runs the application locally for active development and initial testing.
- **Staging:** A shared environment that mirrors production. Used for UAT and integration testing before any release.
- **Production:** The live environment accessible to end users. Only receives code merged into `main` via an approved pull request with all tests passing.
