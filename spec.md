# Feature Specification: LostNfoundNija MVP

**Feature Branch**: `001-lost-and-found-mvp`  
**Created**: 2026-11-17  
**Status**: Draft  
**Input**: User description: "Create a project specification for LostNfoundNija. The GitHub repository name is `lostitems`."

## Project Overview

**Project title**: LostNfoundNija  
**Repository**: `lostitems`  
**Description**: A community-based lost-and-found application that helps people in
Nigeria report, search for, and recover missing belongings.

### Problem Statement

People who lose or find belongings in schools, churches, workplaces, organizations,
and local communities often lack a shared, trusted place to publish reports and
verify ownership. Reports are fragmented across informal channels, making relevant
items difficult to find and increasing the risk of false ownership claims or
disclosure of identifying details.

### Purpose and Target Audience

The MVP provides public discovery of lost-and-found reports and authenticated
workflows for reporting items and resolving claims. It serves students, church
members, employees, organization members, and residents of local communities in
Nigeria.

### User Roles

- **Visitor**: Browse and search public reports and view public report details.
- **Authenticated member**: Manage their own profile and reports, submit and manage
  their claims, and view their dashboard.
- **Report owner**: An authenticated member acting on a report they created; may
  edit or delete that report and approve or reject claims submitted for it.
- **Claimant**: An authenticated member acting on a claim they submitted; may view,
  edit, or withdraw their own pending claim.

Report owner and claimant are contextual roles, not separate account types. The MVP
does not include a moderator or administrator workflow.

### Scope and Priorities

- **P0 — Core recovery workflow**: Public report discovery and detail; account
  access; report creation and owner-authorized management; claim submission,
  review, and resolution; protection of private verification details.
- **P1 — Complete MVP experience**: Profile management, dashboard, report editing
  and deletion, claim editing and withdrawal, responsive/accessibility polish, and
  complete loading, empty, success, and error states.
- **P2 — Deferred, not part of MVP**: Real-time messaging, maps, notifications,
  AI matching, payments, and public contact information. These require a later
  phase specification and approval.

P0 and P1 requirements below are both in MVP scope. P2 features are explicitly
excluded.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Discover a Report (Priority: P0)

As a visitor, I want to browse, search, and inspect public lost-and-found reports
so that I can find information about an item without creating an account.

**Why this priority**: Public discovery is the primary entry point and provides
community value before a visitor signs up.

**Independent Test**: Publish sample lost and found reports, search by supported
terms and filters, and verify that results and details contain only public
information.

**Acceptance Scenarios**:

1. **Given** public open reports exist, **When** a visitor opens the browse view,
   **Then** the visitor sees a paginated list of reports with type, title, category,
   date, general location, status, and an optional public image.
2. **Given** reports exist, **When** a visitor searches by keyword and selects
   available report type, category, or status filters, **Then** only matching
   public reports are shown and the active search criteria are clear.
3. **Given** a public report is visible, **When** a visitor opens its detail view,
   **Then** the visitor sees the report's public description and details but no
   claim records, verification answers, or private contact information.
4. **Given** a search has no matches, **When** results load, **Then** the visitor
   sees an informative empty state and can change or clear the search.

### User Story 2 - Create an Account and Manage a Profile (Priority: P0)

As a community member, I want to sign up, sign in, sign out, and maintain a basic
profile so that I can securely manage reports and claims.

**Why this priority**: Account identity is required for ownership checks and claim
review.

**Independent Test**: Register a new account, sign in, update the profile display
name, and sign out; verify that authenticated pages reject unauthenticated access.

**Acceptance Scenarios**:

1. **Given** an unused valid email and a valid password, **When** a visitor signs
   up, **Then** an account is created and the user receives an authenticated
   session.
2. **Given** a registered account, **When** the user submits valid credentials,
   **Then** the user signs in; invalid credentials produce a safe error without
   revealing whether an email is registered.
3. **Given** an authenticated user, **When** they update their display name,
   **Then** the profile reflects the saved name and validation errors are presented
   accessibly.
4. **Given** an authenticated user, **When** they sign out, **Then** their session
   is ended and protected views require authentication again.

### User Story 3 - Report a Lost or Found Item (Priority: P0)

As an authenticated member, I want to publish a lost or found item report so that
my community can help identify and recover the item.

**Why this priority**: Reports are the core information unit of the service.

**Independent Test**: Sign in, create one lost report and one found report with
valid required fields, and verify each appears in public browse results with the
correct status and public fields.

**Acceptance Scenarios**:

1. **Given** an authenticated member with valid report details, **When** they
   submit a lost or found report, **Then** the report is saved as `OPEN` and shown
   in public discovery.
2. **Given** a member is creating a report, **When** required fields are missing
   or invalid, **Then** the report is not saved and actionable field-level errors
   are shown.
3. **Given** a report owner edits their report, **When** they save valid changes,
   **Then** the public report reflects the changes and retains its existing status
   unless a permitted status transition is made.
4. **Given** a member attempts to edit or delete another member's report,
   **When** the operation is submitted, **Then** it is denied and no report data is
   changed.
5. **Given** an owner deletes a report, **When** deletion is confirmed, **Then**
   it is removed from public discovery and its claim history is retained privately
   as needed for consistency.

### User Story 4 - Submit and Manage an Ownership Claim (Priority: P0)

As an authenticated member who believes they own a found item, I want to submit
private evidence and explanation so that the report owner can verify my claim
without exposing those details publicly.

**Why this priority**: Safe, verifiable claims are the mechanism for recovering
found items and are central to trust in the service.

**Independent Test**: Submit a claim for another member's found report, inspect
public views and responses for leaks, then view, edit, and withdraw the pending
claim as the claimant.

**Acceptance Scenarios**:

1. **Given** an authenticated member and another member's open found report,
   **When** the member submits a valid ownership explanation and private
   verification information, **Then** a `PENDING` claim is created and the report
   becomes `CLAIM_PENDING`.
2. **Given** a member owns a report, **When** they try to claim it, **Then** the
   claim is rejected and no claim is created.
3. **Given** a member already has a pending claim for a report, **When** they try
   to submit another active claim for the same report, **Then** the duplicate is
   rejected and the existing claim remains unchanged.
4. **Given** a pending claim, **When** its claimant views it, **Then** the
   claimant sees their explanation, private verification information, and current
   status.
5. **Given** a pending claim, **When** its claimant edits valid claim details or
   withdraws it, **Then** the changes are saved or the status becomes `WITHDRAWN`,
   respectively, and the report status is recalculated.
6. **Given** a claim is pending, **When** a visitor or unrelated member views any
   public page or report endpoint, **Then** claim existence, explanation, and
   verification information are not disclosed.

### User Story 5 - Review Claims and Resolve a Report (Priority: P0)

As a report owner, I want to review claims on my found-item reports and approve or
reject them so that an item can be returned to its verified owner.

**Why this priority**: A claim that cannot be resolved does not complete the
recovery workflow.

**Independent Test**: Submit claims from separate accounts, sign in as the report
owner, review each pending claim, approve one, and verify permissions, statuses,
and private-data visibility.

**Acceptance Scenarios**:

1. **Given** an owner has pending claims for a report, **When** they open their
   dashboard or report claim view, **Then** they can inspect each claim's
   explanation and private verification information.
2. **Given** a pending claim on the owner's report, **When** the owner approves
   it, **Then** that claim becomes `APPROVED` and the report becomes `CLAIMED`.
3. **Given** a pending claim on the owner's report, **When** the owner rejects
   it, **Then** the claim becomes `REJECTED` and the report returns to `OPEN`
   when no other pending claim exists.
4. **Given** an unrelated member or the claimant tries to decide a claim,
   **When** the decision is submitted, **Then** it is denied and the claim remains
   unchanged.
5. **Given** a report has an approved claim, **When** the owner confirms the item
   has been returned, **Then** the report becomes `RETURNED` and no new claims can
   be submitted.
6. **Given** one claim is approved while other claims remain pending for that
   report, **When** approval succeeds, **Then** the remaining pending claims
   become `REJECTED` and the report remains `CLAIMED`.

### User Story 6 - Manage Reports and Claims from a Dashboard (Priority: P1)

As an authenticated member, I want a dashboard of my reports and claims so that I
can manage activity and see what needs attention.

**Why this priority**: Centralized management makes the MVP usable beyond the
individual create and claim flows.

**Independent Test**: Create reports and claims as multiple users, then verify
each user's dashboard contains only reports they own and claims they submitted
or are authorized to review.

**Acceptance Scenarios**:

1. **Given** an authenticated member with reports or claims, **When** they open
   the dashboard, **Then** their reports and submitted claims are grouped by
   current status.
2. **Given** a member owns a found report with pending claims, **When** they open
   the dashboard, **Then** they can navigate to those claims and review them.
3. **Given** an authenticated member has no reports or claims, **When** they open
   the dashboard, **Then** useful empty states explain how to create a report or
   submit a claim.

### Edge Cases

- Concurrent submissions by the same claimant for the same report MUST NOT create
  multiple pending claims.
- A claim decision MUST fail safely if the claim or report is no longer pending,
  has been deleted, or has already changed state.
- An owner cannot approve a claim after closing or deleting the report.
- A lost-item report cannot receive an ownership claim; claims are only for found
  reports.
- If an image upload fails, the report MUST not silently claim the image was saved;
  the user receives an error and may retry or continue without an image.
- Empty, malformed, oversized, or unsupported image input MUST be rejected with a
  clear validation message.
- A search with special characters or no results MUST return a safe empty result,
  not an unhandled error.
- Expired sessions MUST not permit any write or claim-review action.
- Deleted, closed, claimed, or returned reports MUST not accept new claims.
- Public views MUST remain free of private verification details even when a report
  is edited, closed, deleted, or returned.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001 (P0)**: The system MUST allow visitors to browse, search, and view
  publicly visible lost-and-found reports without signing in.
- **FR-002 (P0)**: The system MUST support account sign-up, sign-in, and sign-out.
- **FR-003 (P1)**: Authenticated users MUST be able to view and update their basic
  profile, including their display name.
- **FR-004 (P0)**: Authenticated users MUST be able to create lost and found
  reports containing a title, description, category, item date, general location,
  optional image, report type, and status.
- **FR-005 (P0)**: The system MUST allow report owners to read, update, and delete
  their reports; it MUST deny edit and delete operations by all other users.
- **FR-006 (P0)**: The system MUST expose public report fields only and MUST keep
  claim records and private claim-verification details out of public pages,
  responses, and search results.
- **FR-007 (P0)**: Authenticated users MUST be able to submit an ownership claim
  for a found report they do not own, including an ownership explanation and
  private verification information.
- **FR-008 (P0)**: Claimants MUST be able to read, edit, and withdraw their own
  pending claims. Withdrawal MUST be represented by `WITHDRAWN` or an equivalent
  soft-deletion state, not public disclosure.
- **FR-009 (P0)**: Report owners MUST be able to read and approve or reject pending
  claims for their reports.
- **FR-010 (P0)**: The system MUST prevent self-claims, claims on lost reports,
  claims on reports that cannot accept claims, and more than one active claim by
  the same user for the same report.
- **FR-011 (P0)**: The system MUST provide the required report and claim statuses
  and enforce the business rules and transitions in this specification.
- **FR-012 (P0)**: The report owner MUST be able to mark an approved item as
  returned; the report status MUST then become `RETURNED`.
- **FR-013 (P1)**: The system MUST provide a user dashboard for the user's own
  reports, submitted claims, and claims on reports they own.
- **FR-014 (P0)**: The system MUST provide report browsing/search, report detail,
  report creation/editing, dashboard, and authentication interfaces.
- **FR-015 (P1)**: Forms MUST provide client-side feedback and authoritative
  server-side validation, with field-level errors where applicable.
- **FR-016 (P1)**: Every relevant view and mutation MUST provide loading,
  success, empty, and error states appropriate to that operation.
- **FR-017 (P1)**: The interface MUST work on desktop and mobile layouts and
  provide accessible labels, keyboard interaction, focus handling, and status
  announcements for interactive feedback.
- **FR-018 (P1)**: The application MUST provide at least five reusable components
  for shared interface patterns, including report presentation, form controls,
  status display, and feedback or loading states.
- **FR-019 (P0)**: The application MUST use the mandated Next.js App Router,
  strict TypeScript without `any`, Tailwind CSS, PostgreSQL with Prisma, Auth.js
  v5, Next.js Route Handlers, and Vercel deployment.
- **FR-020 (P1)**: Server Components MUST be the default; Client Components MUST
  be limited to browser interactivity such as forms, filters, and dialogs.
- **FR-021 (P1)**: ESLint and Prettier MUST be configured and run as code-quality
  checks.
- **FR-022 (P0)**: Automated tests MUST cover authentication, reports, claims,
  validation, authorization, ownership enforcement, and public/private data
  boundaries.
- **FR-023 (P0)**: Real-time messaging, maps, notifications, AI matching,
  payments, and public contact information MUST remain outside the MVP.
- **FR-024 (P1)**: Any deletion that would orphan claim history MUST preserve
  relational consistency; a deleted report MUST disappear from public discovery.

### Business Rules

1. **Report type** is exactly one of `LOST` or `FOUND`.
2. **Report status** is exactly one of `OPEN`, `CLAIM_PENDING`, `CLAIMED`,
   `RETURNED`, or `CLOSED`.
3. **Claim status** is exactly one of `PENDING`, `APPROVED`, `REJECTED`, or
   `WITHDRAWN`.
4. A newly published report starts as `OPEN`.
5. Only `FOUND` reports in `OPEN` or `CLAIM_PENDING` may accept claims.
6. A report owner cannot submit a claim for their own report.
7. A user may have at most one `PENDING` claim for a given report. Rejected or
   withdrawn claims do not count as active; a new claim may be submitted if the
   report is still claimable.
8. Submitting the first pending claim changes the report to `CLAIM_PENDING`.
   Rejecting or withdrawing a claim returns the report to `OPEN` only when no
   other pending claim remains.
9. An owner may approve or reject only a pending claim for their own report.
   Approval sets the claim to `APPROVED` and the report to `CLAIMED`. Other
   pending claims for that report become `REJECTED`.
10. Only the report owner may mark a `CLAIMED` report `RETURNED`. A returned
    report is not claimable.
11. The owner may close a report that is not `CLAIMED` or `RETURNED`. A `CLOSED`
    report is not claimable. Reopening a closed report is outside the MVP.
12. Report and claim mutations MUST verify the current user, ownership, and
    current record status on the server at the time of the operation.
13. Claim explanations and verification information are visible only to the
    claimant and the owner of the associated report. They MUST never be included
    in visitor-facing report data.
14. Public report location MUST be a general community location and MUST NOT
    require or expose a precise home address or private contact information.
15. Report deletion is a soft deletion when claim history exists; the report is
    removed from public results, pending claims are rejected, and authorized
    parties may retain access to relevant history. Deletion without claim history
    may be permanent.

### Status Transitions

| Entity | Current state | Action | Result |
|---|---|---|---|
| Report | New | Owner publishes report | `OPEN` |
| Report | `OPEN` | First claim submitted | `CLAIM_PENDING` |
| Report | `CLAIM_PENDING` | Last pending claim rejected or withdrawn | `OPEN` |
| Report | `CLAIM_PENDING` | Owner approves a pending claim | `CLAIMED` |
| Report | `CLAIMED` | Owner confirms return | `RETURNED` |
| Report | `OPEN` or `CLAIM_PENDING` | Owner closes report | `CLOSED` |
| Claim | New | Eligible claimant submits | `PENDING` |
| Claim | `PENDING` | Claimant edits details | `PENDING` |
| Claim | `PENDING` | Claimant withdraws | `WITHDRAWN` |
| Claim | `PENDING` | Report owner rejects or another claim is approved | `REJECTED` |
| Claim | `PENDING` | Report owner approves | `APPROVED` |

### Data Entities and Relationships

- **User**: A registered community member. Fields include unique identifier,
  display name, unique normalized email, authentication credential/session
  references as required, and created/updated timestamps. Passwords, if used,
  MUST only be stored as secure hashes.
- **ItemReport**: A lost or found item published by a user. Fields include
  identifier, owner user identifier, title, description, category, item date,
  general location, optional image reference, report type, report status,
  creation/update timestamps, and optional deletion timestamp.
- **Claim**: An ownership assertion for a found report. Fields include identifier,
  report identifier, claimant user identifier, ownership explanation, private
  verification information, claim status, creation/update timestamps, and
  optional decision/withdrawal timestamps.

Relationships and constraints:

- One `User` may own many `ItemReport` records.
- One `User` may submit many `Claim` records.
- One `ItemReport` may have many `Claim` records over time.
- Every `ItemReport` has exactly one owner; every `Claim` references exactly one
  report and one claimant.
- A pending claim is unique for each `(report, claimant)` pair.
- Claim history MUST remain associated with its report and users when soft
  deletion is used.

### Validation Requirements

- Validate all fields on both client and server; server-side validation is
  authoritative and MUST reject invalid requests even if client checks are
  bypassed.
- Required report fields: title, description, category, item date, general
  location, and report type. Status is controlled by valid workflow actions, not
  arbitrary user input.
- Trim text values; reject empty-after-trimming values, invalid enum values,
  dates outside the allowed domain range, and inputs exceeding documented length
  limits.
- Search terms and pagination/filter values MUST be bounded and validated.
- Claim explanation and verification information are required, length-bounded,
  and stored separately from public report fields.
- Image upload is optional; validate supported image content type and size and
  return an explicit error on failure.
- Email addresses MUST be normalized for uniqueness. Passwords MUST meet the
  chosen authentication provider's minimum policy and MUST never be returned by
  profile or authentication endpoints.
- Validation errors MUST identify the affected field without echoing secrets or
  private verification content.

### Required Interfaces

- **Public browse/search**: List and search public reports with keyword, report
  type, category, and status filtering; include pagination and an empty state.
- **Public report detail**: Show the public fields for one report and offer
  sign-in/sign-up entry points where an action requires authentication. Never show
  claims or verification details.
- **Authentication**: Sign-up, sign-in, and sign-out screens with accessible
  feedback.
- **Report form**: Create/edit report screen with fields for report details,
  optional image, validation feedback, and submit/cancel actions.
- **Member dashboard**: Show reports owned by the user, claims submitted by the
  user, and claims to review on reports they own. Enforce per-role visibility.
- **Claim form/detail**: Submit or edit an ownership explanation and private
  verification details; show the claimant's status and provide withdrawal while
  pending. The owner view exposes verification details only to that owner.
- **Responsive and accessible behavior**: All required interfaces MUST adapt to
  desktop and mobile widths and support keyboard navigation, visible focus,
  meaningful labels, semantic structure, and accessible status/error feedback.

### Reusable Components

At least five reusable components MUST be implemented. The initial component set:

1. **ReportCard**: Public summary of a report with type and status.
2. **ReportSearchFilters**: Search input and report/category/status filters.
3. **ReportForm**: Shared create/edit report fields and validation presentation.
4. **ClaimForm**: Claim explanation and private verification inputs.
5. **StatusBadge**: Consistent accessible report and claim status indicator.
6. **FeedbackState**: Reusable loading, empty, success, and error presentations.
7. **ConfirmDialog**: Accessible confirmation for deletion, withdrawal, and
   irreversible claim decisions.

### API Endpoints

All endpoints below are served as Next.js Route Handlers. Authentication and
authorization are enforced server-side. Responses MUST return only fields the
requesting user is authorized to see.

| Method and path | Access | Purpose and constraints |
|---|---|---|
| `GET /api/reports` | Public | Search/list public reports; supports validated query, filters, and pagination; excludes private fields. |
| `POST /api/reports` | Authenticated | Create a report owned by the current user; server validates fields and sets initial status. |
| `GET /api/reports/[reportId]` | Public | Read one public, non-deleted report; never includes claim data. |
| `PATCH /api/reports/[reportId]` | Owner | Update allowed report fields; deny status changes not allowed by workflow. |
| `DELETE /api/reports/[reportId]` | Owner | Delete/hide the report; preserve claim history when applicable. |
| `POST /api/reports/[reportId]/claims` | Authenticated | Submit a claim only for another user's eligible found report. |
| `GET /api/claims` | Authenticated | List the current user's submitted claims; report owners may request claims on their own reports through an authorized filter. |
| `GET /api/claims/[claimId]` | Claimant or report owner | Read a claim only if the caller is its claimant or associated report owner. |
| `PATCH /api/claims/[claimId]` | Claimant or report owner | Claimant may edit details while pending; report owner may decide a pending claim. The operation must enforce role-appropriate fields and transitions. |
| `DELETE /api/claims/[claimId]` | Claimant | Withdraw a pending claim using the `WITHDRAWN` status; do not expose it publicly. |
| `PATCH /api/reports/[reportId]/return` | Owner | Mark an approved report returned; update status to `RETURNED`. |
| `GET /api/users/me` | Authenticated | Read the current user's basic profile only. |
| `PATCH /api/users/me` | Authenticated | Update permitted profile fields, initially display name. |

Auth.js v5 owns its authentication endpoints. Endpoint response shapes and
pagination limits are to be defined in the implementation plan; the private/public
field boundary in this specification is mandatory.

### Security and Privacy Requirements

- Authentication MUST be checked on the server for every protected page and
  mutation. Authorization MUST be rechecked for each individual record.
- Only the report owner may update or delete a report, review its claims, or mark
  it returned. Only the claimant may edit or withdraw their pending claim.
- Public routes and serialized responses MUST use explicit public-field
  allowlists; private claim fields MUST not be passed to public Server or Client
  Components.
- Private claim information MUST be accessible only to the claimant and the
  associated report owner. It MUST not appear in URLs, public search indexes,
  logs, analytics, or unhandled error messages.
- All mutations MUST validate ownership, current status, and input on the server.
  Concurrent operations MUST preserve the one-active-claim and status rules.
- Authentication secrets and password material MUST not be exposed in logs,
  client payloads, or profile responses.
- Public reports MUST not require or publish a user's phone number, email address,
  exact home address, or other public contact information.
- Image uploads MUST be constrained to allowed image formats and size limits and
  MUST not allow an untrusted file to be served as executable content.

### Error-Handling Requirements

- Invalid input MUST return field-level validation feedback and MUST not persist
  partial invalid changes.
- Unauthenticated protected requests MUST receive an authentication-required
  response; unauthorized record operations MUST be denied without changing data.
- Missing or inaccessible records MUST return a safe not-found response where
  revealing record existence would leak private information.
- Stale status changes, duplicate active claims, self-claims, and invalid
  transitions MUST return a clear conflict or validation response and preserve
  existing state.
- Authentication failures MUST use non-enumerating messages that do not reveal
  whether an account exists.
- Storage, image, and unexpected service failures MUST present a recoverable
  error state when possible; errors MUST not falsely indicate success.
- Error output MUST not include credentials, private verification details,
  internal stack traces, or sensitive database content.

### Technical Requirements

- The application MUST use Next.js App Router and Next.js Route Handlers.
- TypeScript MUST run in strict mode; use of the `any` type is prohibited.
- Tailwind CSS MUST be used with a utility-first styling approach.
- PostgreSQL MUST be accessed through Prisma ORM.
- Authentication MUST use Auth.js v5 and protected operations MUST be
  authenticated and authorized server-side.
- Server Components MUST be used by default. Client Components are limited to
  necessary interaction, including forms, filters, and dialogs.
- The application MUST be responsive for desktop and mobile and follow
  accessible interface practices.
- ESLint and Prettier MUST be configured and used.
- Production deployment MUST target Vercel.
- Changes MUST follow feature-branch, pull-request, code-review, and descriptive
  commit practices in the GitHub repository.

### Assumptions

- Sign-up and sign-in use email and password unless the implementation plan
  identifies a repository-supported Auth.js provider as the established method.
- Public location is a general community/location description supplied for the
  report, not a precise residential address.
- Claimants and report owners coordinate any physical handoff outside the
  application; the MVP does not provide public contact exchange or messaging.
- When one claim is approved, other pending claims on the same report are
  rejected to avoid multiple simultaneous ownership resolutions.
- Report and claim identifiers, maximum text lengths, image-size limits, and
  search page sizes will be specified during planning before implementation.
- Reports with claim history are soft-deleted to preserve audit and relationship
  integrity; they are not publicly discoverable after deletion.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: At least 90% of first-time test participants can find a matching
  report or correctly determine that no matching report is listed within two
  minutes.
- **SC-002**: At least 90% of test participants can create a valid report without
  assistance in three minutes or less.
- **SC-003**: At least 90% of test participants can submit a claim and find its
  status from their dashboard within three minutes.
- **SC-004**: In authorization tests, 100% of attempts by non-owners to edit or
  delete another user's report and attempts by non-owners to decide a claim are
  denied without changing data.
- **SC-005**: In privacy tests, 100% of public browse, detail, and search outputs
  omit private claim-verification information and personal contact information.
- **SC-006**: All report and claim lifecycle states and prohibited transitions
  are covered by passing automated tests before MVP release.
- **SC-007**: All required primary flows have usable loading, empty, success, and
  error feedback on both desktop and mobile layouts.

## MVP Exclusions

The MVP MUST NOT include real-time messaging, maps, notifications, AI matching,
payments, or public contact information. No undocumented feature may introduce
these capabilities indirectly. Any Phase 2 addition requires a separate
specification and explicit scope approval.
