# Kudos System Specification

## 1. Functional Requirements

### 1.1 User Stories

#### Core Features
1. As a user, I can log in to the system using my corporate account.
2. As a user, I can select another employee from a searchable list.
3. As a user, I can write a short message of appreciation (5–250 characters).
4. As a user, I can submit kudos which is stored in the database.
5. As a user, I can view a public feed of recent kudos on the dashboard.

#### Moderation Features
6. As an administrator, I can hide inappropriate kudos from the public feed.
7. As an administrator, I can permanently delete kudos messages.
8. As an administrator, I can view moderation history and actions taken.

---

### 1.2 Acceptance Criteria

#### Kudos Submission
- User must be authenticated.
- User cannot send kudos to themselves.
- Message must be between 5 and 250 characters.
- Submission is saved in database and appears in feed.

#### Feed Display
- Only visible and approved kudos are shown.
- Feed is sorted by newest first.
- Pagination or infinite scroll is supported.

#### Moderation
- Only admin users can moderate kudos.
- Hidden kudos are removed from public feed but retained in DB.
- Deleted kudos are permanently removed.
- All moderation actions are logged.

#### Validation & Edge Cases
- Duplicate submissions (same sender, recipient, message within 5 minutes) are blocked.
- Daily limit: max 10 kudos per user per day.
- Max 3 kudos per recipient per 24 hours.
- Empty or spam-like messages are rejected.
- Input is sanitized to prevent XSS.

---

## 2. Technical Design

## 2.1 Database Schema

### Users (Django default)
- id (PK)
- username
- email
- is_staff (admin flag)

---

### Kudos Table

| Field             | Type         | Description        |
|------             |------        |-------------|
| id                | UUID         | Primary Key |
| sender_id         | FK(User)     | Who sent kudos |
| recipient_id      | FK(User)     | Who received kudos |
| message           | VARCHAR(250) | Appreciation text |
| created_at        | DATETIME     | Submission time |
| updated_at        | DATETIME     | Last update |
| is_visible        | BOOLEAN      | Controls feed visibility (default TRUE) |
| moderation_status | ENUM         | ACTIVE / HIDDEN / FLAGGED |
| moderated_by      | FK(User)     | Admin who moderated |
| moderated_at      | DATETIME     | Time of moderation |
| reason_for_moderation|VARCHAR(255) | Moderation reason |

---

### Kudos_Moderation_Log

| Field        | Type    | Description |
|------        |------   |-------------|
| id          | UUID     | Primary key |
| kudos_id    | FK       | Related kudos |
| admin_id    | FK(User) | Admin performing action |
| action      | ENUM     | HIDE / UNHIDE / DELETE / FLAG |
| reason      | TEXT     | Reason for action |
| created_at  | DATETIME | Timestamp |

---

## 2.2 API Endpoints

### Authentication
- Uses Django session auth or JWT (recommended)

---

### User APIs

#### Create Kudos

## 2.3 Frontend Components
Component Structure
DashboardPage
 ├── KudosFeed
 │     ├── KudosCard
 │     └── PaginationControls
 ├── KudosForm
 │     ├── UserSearchDropdown
 │     ├── MessageInput
 │     └── SubmitButton
 └── AdminPanel (role-based)
       ├── ModerationTable
       ├── HideButton
       └── DeleteButton

  Component Behavior
KudosForm
Validates input before submission
Prevents self-selection
Shows success/error messages
KudosFeed
Fetches /api/kudos/feed
Updates dynamically after submission
AdminPanel
Visible only for is_staff
Supports moderation actions
## 2.4 Security Considerations
Authentication required for all endpoints
Authorization checks for admin actions
Input sanitization (prevent XSS)
Rate limiting (per user)
Duplicate detection
CSRF protection (Django default)
Message validation rules enforced server-side
## 2.5 Performance Considerations
Pagination on feed (limit/offset or cursor-based)
Indexes on:
created_at
sender_id
recipient_id
Cache recent feed results (Redis optional)
Avoid N+1 queries (use select_related)
## 2.6 Error Handling & Logging
Error Responses   
| Code | Meaning             |
| ---- | ------------------- |
| 400  | Validation error    |
| 401  | Unauthorized        |
| 403  | Forbidden           |
| 429  | Rate limit exceeded |
| 500  | Server error        |
Logging Strategy
Log all:
kudos creation
moderation actions
blocked submissions
Store logs in:
Django logging system
Moderation audit table
# 3. Implementation Plan
## Phase 1: Backend Setup
Create Django project
Configure authentication
Create Kudos model
Run migrations
## Phase 2: Core API
Implement create kudos API
Implement feed API
Add validation rules
Add duplicate detection logic
## Phase 3: Moderation System
Add admin endpoints
Implement hide/delete functionality
Create moderation log table
Add role-based permissions
## Phase 4: Frontend
Build React/Vue frontend (or Django templates)
Implement:
Kudos form
Feed view
Admin panel
## Phase 5: Validation & Security
Add rate limiting
Add spam detection rules
Add XSS protection
Add permission enforcement
## Phase 6: Testing
Unit tests for models
API tests for endpoints
Edge case testing:
duplicates
self-kudos
spam messages
Admin moderation tests
## Phase 7: Deployment
Configure environment variables
Set up database (PostgreSQL recommended)
Configure static files
Deploy via:
Docker OR
AWS / Azure / internal server
# 4. Dependencies Between Tasks
Database models → required before APIs
APIs → required before frontend
Moderation system → depends on APIs
Frontend → depends on API stability
Testing → runs parallel with each phase
Deployment → final step after testing
# 5. Final Notes

This system is designed to be:

Scalable (pagination + indexing)
Secure (auth + moderation)
Maintainable (separated concerns)
Extendable (future reactions, badges, analytics)