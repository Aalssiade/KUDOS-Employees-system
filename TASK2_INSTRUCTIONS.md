
# Task 2: Building from Scratch with Spec-Driven Development

## Scenario

You are continuing in your role as a Graduate Developer at Datacom. Your manager has entrusted you with building a "Kudos" system for the internal employee portal. The initial request is simple: "We need a way for people to give kudos to each other." Your job is to take on the role of an AI Architect, using a spec-driven development process to transform this ambiguous idea into a robust feature.

## Learning Objectives

- The fundamental difference between interactive "vibe coding" and structured, planning-first methodology
- The practical workflow for translating high-level feature requests into formal specifications
- The critical role of the developer in reviewing, refining, and augmenting AI-generated requirements
- How to manage and execute development plans based on pre-defined task lists

## Deliverables

1. **A link to a new, public Git repository** containing the full project source code
2. **`SPECIFICATION.md`** - The final, refined requirements and technical design used to generate the code

## Step-by-Step Instructions

### Step 1: Initiate the Specification

1. In your chosen AI development environment, initiate the process for generating a new feature from a high-level description
2. Enter the initial prompt:
   ```
   Create a feature for our internal web app that allows users to give 'kudos' to their colleagues. A user should be able to select another user from a list, write a short message of appreciation, and submit it. There should also be a public feed on the main dashboard where all recently submitted kudos are visible.
   ```

### Step 2: Review and Refine the Specification of AI response 
Using a spec-driven development process, the goal is to turn the ambiguous request ("people should be able to give kudos") into a complete, implementable feature specification.

##Feature Specification: Employee Kudos System
#1. Feature Overview

Purpose

Allow employees to publicly recognize and appreciate colleagues for their contributions.

Business Goal:

Improve employee engagement and morale.
Encourage recognition and positive workplace culture.
Provide visibility into employee achievements.

#2. User Stories
Core User Stories

US-1: Give Kudos

As an employee,
I want to send kudos to a colleague,
So that I can recognize their contribution.

US-2: View Kudos Feed

As an employee,
I want to see recently submitted kudos,
So that I can stay informed about team accomplishments.

US-3: Read Kudos Details

As an employee,
I want to view the sender, recipient, message, and date,
So that I understand the recognition being given.

#3. Functional Requirements

3.1 Submit Kudos

from fields 
| Field         | Type                 | Required | Validation                   |
| ------------- | -------------------- | -------- | ---------------------------- |
| Recipient     | User Select Dropdown | Yes      | Cannot select yourself       |
| Message       | Text Area            | Yes      | 5–250 characters             |
| Submit Button | Button               | Yes      | Enabled only when form valid |


## Behavior

# When a user submits kudos:

System validates input.
System creates a new Kudos record.
Kudos appears in the public feed.
Success message is displayed.

# 3.2 Recipient Selection

Requirements

Dropdown contains all active employees.
Search capability for large organizations.
User names displayed as:
First Name Last Name
Department (optional)

Restrictions

User cannot select themselves.

Error message:



# 3.3 Kudos Message
Requirements

Minimum length:

................
## 5 characters

Maximum length:

250 characters
Examples

Valid:

Thank you for helping resolve the production issue yesterday.

Invalid:

Hi
# 3.4 Public Kudos Feed
-Location

Main Dashboard

-Sort Order

Newest first.

-Display Card

Each kudos card contains:

Recipient Name
Sender Name
Message
Submission Date/Time

Example:

🎉 Kudos to Sarah Johnson

From: Michael Lee

"Thanks for staying late to help complete the client deployment."

2 hours ago


4. Non-Functional Requirements
Performance
Kudos submission response < 2 seconds.
Feed loads within 3 seconds.
Dashboard supports at least 500 recent kudos records.
Availability
Feature available wherever employee portal is available.
Security
Only authenticated employees may submit kudos.
Only authenticated employees may view kudos feed.
Prevent XSS by sanitizing user-entered messages.
5. Data Model
kudos table
| Field        | Type         | Notes                |
| ------------ | ------------ | -------------------- |
| id           | UUID         | Primary Key          |
| sender_id    | UUID         | User giving kudos    |
| recipient_id | UUID         | User receiving kudos |
| message      | VARCHAR(250) | Appreciation message |
| created_at   | DATETIME     | Timestamp            |

Relationships
User (1) ---- (*) Kudos (sender)
User (1) ---- (*) Kudos (recipient)
6. API Specification
Create Kudos
Endpoint
POST /api/kudos
Request
{
  "recipientId": "u123",
  "message": "Thanks for helping with the release."
}
Response
{
  "id": "k789",
  "status": "created"
}
Get Recent Kudos
Endpoint
GET /api/kudos?limit=20
Response
[
  {
    "id": "k789",
    "senderName": "Michael Lee",
    "recipientName": "Sarah Johnson",
    "message": "Thanks for helping with the release.",
    "createdAt": "2026-06-17T09:00:00Z"
  }
]
7. Validation Rules
| Rule                   | Expected Result |
| ---------------------- | --------------- |
| Recipient missing      | Error           |
| Message missing        | Error           |
| Message < 5 chars      | Error           |
| Message > 250 chars    | Error           |
| Sender = Recipient     | Error           |
| User not authenticated | Reject request  |

8. Dashboard UI
Recognition Widget
-------------------------------------
| Give Kudos                       |
-------------------------------------
| Recipient: [ Search Employee ]   |
|                                  |
| Message:                         |
| [____________________________]   |
| [____________________________]   |
|                                  |
|             [Send Kudos]         |
-------------------------------------
Feed Widget
-------------------------------------
| Recent Kudos                     |
-------------------------------------
| 🎉 Sarah Johnson                 |
| From Michael Lee                 |
| Thanks for helping...            |
| 2 hours ago                      |
-------------------------------------
| 🎉 David Brown                   |
| From Emma Wilson                 |
| Great presentation today!        |
| 4 hours ago                      |
-------------------------------------
9. Acceptance Criteria
AC-1 Submit Kudos

Given I am logged in
When I select a colleague and submit a valid message
Then a new kudos record is created and appears in the feed.

AC-2 Prevent Self-Kudos

Given I am logged in
When I select myself as recipient
Then the system displays an error and blocks submission.

AC-3 Feed Visibility

Given kudos have been submitted
When I open the dashboard
Then I see recent kudos ordered by newest first.

AC-4 Message Validation

Given I enter a message shorter than 5 characters
When I submit
Then the system displays a validation error.


#### My Task (Requirements): Adding Content Moderation


## Additional User Story

### US-4: Moderate Kudos (Administrator)

* As an administrator,
* I want to hide or delete inappropriate kudos messages,
* So that the public feed remains professional and respectful.

### Acceptance Criteria

**AC-5 Hide Kudos**

Given I am an administrator
When I choose to hide a kudos message
Then the message is removed from the public feed but retained in the database for auditing purposes.

**AC-6 Delete Kudos**

Given I am an administrator
When I choose to delete a kudos message
Then the message is permanently removed from the system.

**AC-7 Admin Permissions**

Given I am a regular employee
When I access moderation functions
Then access is denied.

---

## New Functional Requirements

### 3.5 Kudos Moderation

#### Admin Actions

Administrators can:

* View all kudos submissions
* Hide a kudos message
* Unhide a hidden kudos message
* Delete a kudos message
* View moderation history

#### Hidden Kudos Behavior

* Hidden kudos are not displayed in the public feed.
* Hidden kudos remain stored for auditing.
* The sender and recipient records remain intact.

#### Deleted Kudos Behavior

* Deleted kudos are permanently removed.
* Deleted entries no longer appear in any feed or reports.

---

## Edge Case Requirements

### Spam Prevention

#### Rate Limiting

To prevent abuse:

* A user may submit a maximum of 10 kudos per day.
* A user may submit a maximum of 3 kudos to the same recipient within 24 hours.

Error message:

"You have reached the kudos submission limit. Please try again later."

---

### Duplicate Submission Detection

The system should prevent accidental duplicate submissions.

A duplicate is defined as:

* Same sender
* Same recipient
* Same message content
* Submitted within 5 minutes

When detected:

* Submission is blocked.
* User receives a warning.

Message:

"This kudos appears to be a duplicate of a recently submitted message."

---

### Inappropriate Content Handling

The system should validate messages against prohibited content.

Examples:

* Harassment
* Hate speech
* Profanity
* Offensive language
* Personal attacks

Behavior:

* Flag potentially inappropriate content.
* Prevent submission when confidence exceeds the configured threshold.
* Log blocked attempts for administrative review.

Message:

"Your message contains content that does not meet company guidelines."

---

### Empty or Low-Value Messages

The following should be rejected:

* Messages containing only spaces
* Messages containing only emojis
* Messages with repeated characters (e.g., "aaaaaaa")
* Messages with generic filler text (e.g., "good", "nice")

Minimum meaningful content requirement:

* At least 5 non-whitespace characters.
* Must contain at least one alphabetic character.

---

## Data Model Updates

### Kudos Table

Additional fields:

| Field             | Type         | Notes                         |
| ----------------- | ------------ | ----------------------------- |
| status            | ENUM         | ACTIVE, HIDDEN                |
| hidden_by         | UUID         | Administrator who hid message |
| hidden_at         | DATETIME     | Timestamp of moderation       |
| moderation_reason | VARCHAR(255) | Optional reason               |
| created_at        | DATETIME     | Timestamp                     |

### Audit Log Table

| Field      | Type         | Notes                |
| ---------- | ------------ | -------------------- |
| id         | UUID         | Primary Key          |
| kudos_id   | UUID         | Related kudos        |
| admin_id   | UUID         | Administrator        |
| action     | ENUM         | HIDE, UNHIDE, DELETE |
| reason     | VARCHAR(255) | Optional             |
| created_at | DATETIME     | Timestamp            |

---

## New API Endpoints

### Hide Kudos

POST /api/admin/kudos/{id}/hide

### Unhide Kudos

POST /api/admin/kudos/{id}/unhide

### Delete Kudos

DELETE /api/admin/kudos/{id}

### Get Moderation Log

GET /api/admin/kudos/moderation-history

---

## Updated Feed Requirements

The public dashboard feed:

* Displays only ACTIVE kudos.
* Excludes hidden messages.
* Excludes deleted messages.
* Continues to display newest entries first.

---

## Additional Non-Functional Requirements

### Security

* Moderation actions require Administrator role.
* All moderation actions must be logged.
* Audit logs must be retained for at least one year.

### Monitoring

The system should track:

* Number of kudos submitted
* Number of blocked submissions
* Number of hidden messages
* Number of deleted messages
* Duplicate submission attempts
............................................................................

#### Your Task (Design): Update Database Schema

# Updated Data Model

## Kudos Table

| Field                 | Type         | Constraints           | Description                                             |
| --------------------- | ------------ | --------------------- | ------------------------------------------------------- |
| id                    | UUID         | Primary Key           | Unique identifier for the kudos                         |
| sender_id             | UUID         | Not Null, FK -> Users | Employee giving the kudos                               |
| recipient_id          | UUID         | Not Null, FK -> Users | Employee receiving the kudos                            |
| message               | VARCHAR(250) | Not Null              | Appreciation message                                    |
| created_at            | DATETIME     | Not Null              | Submission timestamp                                    |
| is_visible            | BOOLEAN      | Default TRUE          | Determines whether the kudos appears in the public feed |
| moderated_by          | UUID         | Nullable, FK -> Users | Administrator who performed the moderation action       |
| moderated_at          | DATETIME     | Nullable              | Timestamp of the most recent moderation action          |
| reason_for_moderation | VARCHAR(255) | Nullable              | Explanation for hiding or moderating the kudos          |
| moderation_status     | ENUM         | Default 'ACTIVE'      | ACTIVE, HIDDEN, FLAGGED                                 |
| updated_at            | DATETIME     | Nullable              | Last modification timestamp                             |

### Field Descriptions

#### is_visible

* TRUE = kudos is displayed in the public dashboard feed.
* FALSE = kudos is hidden from employees.
* Hidden kudos remain stored for auditing and possible restoration.

#### moderated_by

Stores the administrator who last moderated the kudos.

Example:

| moderated_by |
| ------------ |
| admin_123    |

#### moderated_at

Stores when the moderation action occurred.

Example:

| moderated_at        |
| ------------------- |
| 2026-06-17 14:30:00 |

#### reason_for_moderation

Provides context for why the kudos was hidden or flagged.

Examples:

* Inappropriate language
* Spam submission
* Duplicate message
* Reported by employee

#### moderation_status

Possible values:

| Value   | Description                   |
| ------- | ----------------------------- |
| ACTIVE  | Visible and approved          |
| HIDDEN  | Removed from public feed      |
| FLAGGED | Requires administrator review |

---

## Feed Query Logic

The public dashboard should display only kudos records that satisfy:

```sql
WHERE is_visible = TRUE
AND moderation_status = 'ACTIVE'
ORDER BY created_at DESC
```

---

## Moderation Workflow

### Normal Submission

1. User submits kudos.
2. Record created with:

   * is_visible = TRUE
   * moderation_status = 'ACTIVE'

### Hidden by Administrator

1. Administrator hides message.
2. System updates:

   * is_visible = FALSE
   * moderation_status = 'HIDDEN'
   * moderated_by = Admin ID
   * moderated_at = Current Timestamp
   * reason_for_moderation = Admin-provided reason

### Flagged for Review

1. Automated content filter detects potential issue.
2. System updates:

   * moderation_status = 'FLAGGED'
3. Administrator reviews and either:

   * Approves (ACTIVE)
   * Hides (HIDDEN)

---

## Optional Moderation Audit Table

To maintain a complete history of moderation actions:

### Kudos_Moderation_Log

| Field      | Type         | Description                     |
| ---------- | ------------ | ------------------------------- |
| id         | UUID         | Primary Key                     |
| kudos_id   | UUID         | Related kudos record            |
| admin_id   | UUID         | Administrator performing action |
| action     | ENUM         | FLAG, HIDE, UNHIDE, DELETE      |
| reason     | VARCHAR(255) | Moderation reason               |
| created_at | DATETIME     | Action timestamp                |

### Benefits

* Preserves audit history.
* Supports compliance requirements.
* Tracks who performed moderation actions.
* Allows reporting on moderation activity.
For a production system, we keep both:

-Moderation fields on the Kudos table (is_visible, moderated_by, etc.) for fast feed filtering.
-A separate Kudos_Moderation_Log table for a complete audit trail of all moderation actions.

This approach avoids losing historical moderation data while keeping dashboard queries efficient.
...............................................................................
### Step 3: Approve the Specification and Execute Implementation

# Kudos System Specification Approval

## Approval Status

APPROVED FOR IMPLEMENTATION

The specification has been reviewed and is considered sufficiently complete for MVP development.

### Included Scope

#### Core Features

* Submit kudos to another employee
* Employee selection from user directory
* Appreciation message entry
* Public dashboard feed
* Recent kudos display

#### Validation

* Recipient required
* Message required
* Message length validation
* Self-kudos prevention
* Authentication required

#### Moderation

* Administrator hide/unhide capability
* Administrator delete capability
* Inappropriate content handling
* Spam prevention controls
* Duplicate submission detection

#### Database Design

* Kudos table with moderation fields
* Visibility controls (`is_visible`)
* Moderation metadata (`moderated_by`, `moderated_at`, `reason_for_moderation`)
* Moderation status tracking
* Audit logging support

#### Security

* Role-based access control
* Authenticated access only
* Moderation permissions restricted to administrators
* Audit trail requirements

---

## Implementation Tasks

### Phase 1: Database

Create or update the following tables:

#### Kudos

* id
* sender_id
* recipient_id
* message
* created_at
* updated_at
* is_visible
* moderation_status
* moderated_by
* moderated_at
* reason_for_moderation

#### Kudos_Moderation_Log

* id
* kudos_id
* admin_id
* action
* reason
* created_at

---

### Phase 2: Backend APIs

#### Employee Features

* POST /api/kudos
* GET /api/kudos

#### Administrative Features

* POST /api/admin/kudos/{id}/hide
* POST /api/admin/kudos/{id}/unhide
* DELETE /api/admin/kudos/{id}
* GET /api/admin/kudos/moderation-history

---

### Phase 3: Frontend

#### Dashboard

* Recent Kudos Feed
* Pagination or infinite scroll

#### Kudos Submission Form

* Employee search/dropdown
* Message textbox
* Validation messages
* Success notification

#### Admin Moderation Screen

* View all kudos
* Filter by status
* Hide/unhide actions
* Delete actions
* Moderation reason entry

---

### Phase 4: Business Rules

Implement:

* Self-kudos prevention
* Duplicate detection
* Daily rate limits
* Content filtering
* Visibility filtering

---

### Phase 5: Testing

#### Unit Tests

* Validation rules
* Moderation actions
* Permission checks

#### Integration Tests

* API endpoints
* Database updates
* Feed visibility logic

#### Acceptance Tests

* Submit kudos
* View feed
* Hide kudos
* Delete kudos
* Prevent duplicates
* Prevent self-kudos

---

## Release Readiness Criteria

The feature is ready for production deployment when:

* All acceptance criteria pass
* Security review passes
* Moderation workflows pass testing
* Audit logging is verified
* Performance requirements are met
* Stakeholder sign-off is obtained


I’ll generate a working Django MVP implementation for the Kudos system including:

Kudos model (with moderation fields)
APIs (create + feed)
Admin moderation (hide/delete)
Basic spam + duplicate protection
Simple service layer logic
..............................................................................
### Step 4: Commit and Push to Repository

1. Once the AI has completed the implementation, your feature is complete
2. Save the final requirements and design you approved into a single `SPECIFICATION.md` file
3. Initialize a new Git repository, add all the generated files (including your `SPECIFICATION.md`), and create an initial commit
4. Create a new public repository on a platform like GitHub or GitLab and push your local repository to it
.........................................................................................................
## Specification Requirements Checklist

### Functional Requirements Should Include:

- [ ] User authentication and authorization
- [ ] User selection from a list of colleagues
- [ ] Kudos message creation and submission
- [ ] Public feed display of recent kudos
- [ ] Content moderation capabilities
- [ ] Input validation and error handling
- [ ] Responsive design for different screen sizes
..............................................................
### Technical Design Should Include:

- [ ] Database schema with all necessary tables and relationships
- [ ] API endpoints and their specifications
- [ ] Frontend components and their interactions
- [ ] Security considerations (authentication, authorization, input sanitization)
- [ ] Performance considerations (pagination, caching, etc.)
- [ ] Error handling and logging strategies
........................................................................
### Implementation Plan Should Include:

- [ ] Step-by-step breakdown of development tasks
- [ ] Dependencies between tasks
- [ ] Testing strategy
- [ ] Deployment considerations
.......................................................................
## Example Specification Structure

Your `SPECIFICATION.md` should follow this structure:

```markdown
# Kudos System Specification

## Functional Requirements

### User Stories

1. As a user, I can select another user from a dropdown list
2. As a user, I can write a message of appreciation (max 500 characters)
3. As a user, I can submit the kudos which gets stored in the database
4. As a user, I can view a feed of recent kudos on the dashboard
5. As an administrator, I can hide or delete inappropriate kudos messages

### Acceptance Criteria

- [Detailed criteria for each user story]

## Technical Design

### Database Schema

- [Table definitions with fields, types, and relationships]

### API Endpoints

- [List of endpoints with methods, parameters, and responses]

### Frontend Components

- [Component hierarchy and interactions]

## Implementation Plan

- [Step-by-step task breakdown]
```

## Key Principles to Remember

1. **Specification First**: Don't start coding until the specification is complete and approved
2. **Human-in-the-Loop**: You are responsible for ensuring the AI understands the business requirements correctly
3. **Iterative Refinement**: The specification may need multiple rounds of review and refinement
4. **Completeness**: Ensure all edge cases and requirements are captured before implementation

## Reflection Questions

After completing the task, consider:

1. **How did the structured approach to specification change your development process compared to traditional coding?**

2. **What was the most challenging part of reviewing and refining the AI-generated specification?**

3. **How did having a complete specification before implementation affect the quality and completeness of the final code?**

## Tips for Success

- Take time to thoroughly review the AI-generated specification
- Don't hesitate to add missing requirements or modify the design
- Consider edge cases and error scenarios
- Ensure the specification is detailed enough for the AI to implement correctly
- Remember that your role as Architect is to think strategically, not tactically

This structured process highlights a fundamental shift in the developer's role. The highest-value work is performed during the specification and review phases. By ensuring the blueprint is correct, you guarantee that the subsequent AI-driven implementation will be successful.
