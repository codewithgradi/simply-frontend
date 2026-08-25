SIMPLI FRONTEND — COMPLETE ARCHITECTURE & ENGINEERING CONTEXT

Project:
Simpli Frontend Client

Domain:
Enterprise & Residential Digital Visitor & Contactless Guest Management System.

Purpose:
Simpli replaces legacy paper visitor logbooks with a contactless digital check-in platform supporting digital visitor registration, dynamic QR access passes, bulk visitor operations, host notifications, visitor approvals, security verification, and real-time dashboards.

Architecture:
Decoupled Single Page Application (SPA) built with Next.js 14 App Router, communicating with an ASP.NET Core Web API through REST APIs and SignalR WebSockets.

TECHNOLOGY STACK

Framework:
- Next.js 14
- App Router
- React 18
- Server Components and Client Components

Language:
- TypeScript 5
- Strict mode enabled
- Strongly typed DTOs and domain models

Styling:
- Tailwind CSS
- Shadcn UI
- Custom utility classes
- Lucide React icons

State Management:
- React Context API for authentication/session state
- TanStack Query / React Query for server state, caching, mutations, synchronization, and API data

HTTP:
- Axios
- Centralized Axios instance
- Request/response interceptors
- Bearer authentication handling
- Standardized API error handling

Real-Time:
- @microsoft/signalr
- ASP.NET Core SignalR notification hubs
- Real-time arrival notifications
- Approval state changes
- Dashboard updates

Deployment:
- Render production web service

Backend:
- ASP.NET Core Web API
- RESTful APIs
- SignalR
- PostgreSQL
- Neon DB

Authentication:
- JWT Bearer authentication
- Secure HTTP-only cookie strategy where applicable
- Axios handles authentication attachment
- Authentication/session state managed through React Context
- SignalR connection established after authentication

Environment Variables:
- NEXT_PUBLIC_API_BASE_URL
- NEXT_PUBLIC_SIGNALR_HUB_URL

CORE FEATURES

1. VISITOR SELF-REGISTRATION & CHECK-IN

Requirements:
- Kiosk-compatible check-in flow
- Mobile-responsive visitor flow
- Step-by-step wizard
- Dynamic validation
- Emergency contact collection
- Host selection
- Visitor details
- Check-in completion
- Appropriate loading, validation, error, and recovery states

Architecture principle:
Treat check-in as a stateful business workflow rather than a collection of unrelated forms.

2. REAL-TIME HOST & ADMIN DASHBOARD

Dashboard requirements:
- Pending visitor approvals
- Active checked-in visitors
- Historical visitor logs
- Visitor status
- Host notifications
- Immediate arrival alerts
- Approval state changes
- No manual page refresh required

SignalR principle:
SignalR should act as an event/update mechanism rather than becoming a second global state-management system.

Incoming SignalR events should generally:
- Invalidate relevant TanStack Query queries
- Update relevant TanStack Query caches when appropriate
- Trigger targeted UI updates
- Avoid duplicating server state in React Context
- Avoid unnecessary full-page refreshes

SignalR lifecycle must properly handle:
- Connection initialization
- Authentication
- Reconnection
- Connection failures
- Event registration
- Event cleanup
- Duplicate subscription prevention
- Logout/disconnect behavior

3. BULK VISITOR MANAGEMENT

Requirements:
- Schedule multi-guest visits
- Upload bulk guest lists
- Batch visitor operations
- Large event check-ins
- Optimized rendering
- Batch API mutations
- Per-item and batch error handling
- Progress/loading states
- Large dataset scalability

Performance considerations:
- Pagination where appropriate
- Virtualization for very large lists
- Avoid unnecessary React re-renders
- Avoid creating expensive objects/functions repeatedly
- Prefer server-side filtering/sorting when datasets become large
- Use TanStack Query strategically for cache management

4. QR ACCESS PASS ENGINE

Requirements:
- Secure visitor QR pass rendering
- Time-bound visitor passes
- Scanner-ready camera interface
- Security personnel validation
- Access-gate verification

Security principles:
- The frontend must never be the ultimate authority for QR validity.
- QR validation must be verified by the backend.
- Expiration must be enforced server-side.
- Replay/use-after-expiration must be considered.
- Sensitive data should not unnecessarily be embedded directly into QR codes.
- UI verification state must clearly distinguish valid, expired, invalid, revoked, and already-used passes where applicable.

5. RBAC

Supported roles:
- Visitor
- Host
- Security
- Admin

RBAC must influence:
- Navigation
- Routing
- UI capabilities
- Dashboard access
- Feature visibility
- API operations

Important:
Frontend RBAC is a UX/security boundary, not the authoritative authorization layer.

The backend API must remain authoritative for permissions and authorization.

The frontend should not assume that hiding a button or route makes an operation secure.

REPOSITORY STRUCTURE

simpli-frontend/
├── src/
│   ├── app/
│   │   ├── routes
│   │   ├── pages
│   │   ├── layouts
│   │   └── middleware
│   │
│   ├── components/
│   │   ├── dashboard/
│   │   │   └── Host & Security dashboard components
│   │   ├── guest/
│   │   │   └── Check-in, kiosk, visitor, QR pass components
│   │   └── ui/
│   │       └── Shadcn/reusable UI primitives
│   │
│   ├── hooks/
│   │   ├── useAuth
│   │   ├── useSignalR
│   │   ├── useVisitors
│   │   └── other reusable client hooks
│   │
│   ├── lib/
│   │   ├── Axios configuration
│   │   ├── SignalR configuration
│   │   ├── helpers
│   │   └── client-side infrastructure
│   │
│   ├── services/
│   │   └── REST/API service layer
│   │
│   ├── types/
│   │   ├── DTOs
│   │   ├── domain models
│   │   ├── API response types
│   │   └── enums/types
│   │
│   └── styles/
│       └── global CSS and Tailwind directives

DIRECTORY RESPONSIBILITIES

app/
Purpose:
- Next.js routes
- Layouts
- Server/client component boundaries
- Loading states
- Error boundaries
- Route composition
- Middleware integration

components/
Purpose:
- Feature-oriented UI
- Presentation
- User interaction
- Component composition

components/ui/
Purpose:
- Generic reusable UI primitives
- Minimal business/domain knowledge
- Shadcn-based components

components/dashboard/
Purpose:
- Host dashboards
- Security dashboards
- Admin dashboard views
- Visitor status displays
- Approval interfaces
- Real-time dashboard presentation

components/guest/
Purpose:
- Visitor check-in
- Kiosk experience
- Guest registration
- QR pass presentation
- Scanner interfaces

hooks/
Purpose:
- Reusable client-side behavior
- Feature-specific orchestration
- Authentication hooks
- SignalR lifecycle hooks
- Visitor query/mutation hooks

lib/
Purpose:
- Infrastructure
- Axios client
- SignalR connection setup
- Configuration
- Generic helpers
- Shared client utilities

services/
Purpose:
- REST API abstraction
- Endpoint-specific operations
- API request/response handling
- Keep API access out of presentation components

types/
Purpose:
- Shared DTOs
- Domain types
- API contracts
- Request/response types
- Enums
- Strong typing across application layers

styles/
Purpose:
- Global styling
- Tailwind directives
- Application-wide CSS

HIGH-LEVEL ARCHITECTURE

                    ┌────────────────────────────┐
                    │       Next.js App          │
                    │       App Router           │
                    └──────────────┬─────────────┘
                                   │
                 ┌─────────────────┼─────────────────┐
                 │                 │                 │
                 ▼                 ▼                 ▼
          UI / Components    Auth / Session     Route / RBAC
                 │                 │                 │
                 └─────────────────┼─────────────────┘
                                   │
                           TanStack Query
                                   │
                             Service Layer
                                   │
                                Axios
                                   │
                   ┌───────────────┴───────────────┐
                   │                               │
                   ▼                               ▼
          ASP.NET Core REST API              SignalR Hub
                   │                               │
                   └───────────────┬───────────────┘
                                   │
                                   ▼
                              PostgreSQL
                                Neon DB

ARCHITECTURAL PRINCIPLES

1. SERVER STATE VS CLIENT STATE

Use TanStack Query for:
- Visitors
- Hosts
- Approvals
- Visitor history
- Dashboard data
- QR pass data
- API responses
- Server-side resources
- Mutations and cache synchronization

Use React Context primarily for:
- Authentication/session state
- Current authenticated user
- Current role/session information where appropriate
- Global client-side authentication concerns

Do not use Context as a replacement for TanStack Query.

Avoid storing API datasets globally in Context when they can be represented as TanStack Query state.

2. SERVER COMPONENTS VS CLIENT COMPONENTS

Use Server Components by default when possible.

Use Client Components when requiring:
- useState
- useEffect
- useContext
- Browser APIs
- Camera APIs
- QR scanning
- SignalR
- Interactive forms
- Event handlers
- TanStack Query hooks
- Client-side authentication/session behavior

Avoid unnecessarily marking large component trees with "use client".

Keep client boundaries as small and deliberate as practical.

3. API LAYER

Components should generally not make raw Axios calls directly.

Preferred flow:

Component
    ↓
Hook
    ↓
Service
    ↓
Axios
    ↓
ASP.NET Core API

Example conceptual structure:

useVisitors()
    ↓
visitorService.getVisitors()
    ↓
apiClient.get(...)
    ↓
ASP.NET Core API

This improves:
- Testability
- Reusability
- Separation of concerns
- Error consistency
- Maintainability
- Type safety

4. AXIOS

Use a centralized Axios instance.

Responsibilities:
- Base URL configuration
- Authentication headers
- Request interceptors
- Response interceptors
- Error normalization
- Unauthorized handling
- Consistent API behavior

Avoid creating independent Axios clients throughout components.

Authentication behavior must account for:
- Login
- Logout
- Token/session expiration
- Unauthorized responses
- Refresh behavior if supported
- SSR/client differences
- HTTP-only cookie limitations

Never expose secrets through NEXT_PUBLIC_* environment variables.

5. TANSTACK QUERY

Use query keys consistently.

Queries should represent server resources.

Mutations should handle:
- Loading
- Success
- Error
- Cache invalidation
- Optimistic updates only when safe
- Rollback where required

After mutations:
- Invalidate affected queries
- Update cache directly when appropriate
- Avoid unnecessary broad invalidation

Query caching should be designed around domain resources rather than arbitrary UI components.

6. SIGNALR

SignalR should be treated as real-time event infrastructure.

Preferred conceptual lifecycle:

Authenticated user
    ↓
Create SignalR connection
    ↓
Authenticate/establish connection
    ↓
Register event listeners
    ↓
Receive server events
    ↓
Update/invalidate TanStack Query cache
    ↓
UI automatically reacts

On logout/unmount:
- Remove event handlers
- Stop connection
- Clear references
- Prevent duplicate connections

Connection management should account for:
- Reconnects
- Temporary network failures
- Token/session changes
- Browser lifecycle
- Duplicate subscriptions
- Cleanup

7. RBAC

Role checks should exist at multiple levels:

Middleware / routing
    ↓
Page/layout access
    ↓
Feature-level UI
    ↓
Action-level UI
    ↓
Backend authorization

The frontend should improve UX by preventing unauthorized actions from being presented.

The backend must still validate authorization for every protected operation.

8. DOMAIN TYPES

Prefer explicit types over any.

Avoid:
- any
- unnecessary type assertions
- duplicated interfaces
- weakly typed API responses
- loosely typed service methods

Prefer:
- DTO types
- Domain types
- Request types
- Response types
- Discriminated unions when appropriate
- Explicit nullable/optional semantics

Example conceptual distinction:

VisitorDto
Visitor
CreateVisitorRequest
UpdateVisitorRequest
VisitorResponse
PaginatedResponse<T>

Do not automatically assume API DTOs and frontend domain models should always be identical.

9. ERROR HANDLING

Errors should be normalized at the API boundary where possible.

UI should distinguish between:
- Validation errors
- Authentication errors
- Authorization errors
- Not found
- Conflict
- Network failure
- Server failure
- Timeout
- Rate limiting

Do not expose raw backend error structures directly throughout the component tree.

Use consistent user-facing error states.

10. LOADING STATES

Account for:
- Initial loading
- Background fetching
- Mutation loading
- SignalR connection state
- Scanner initialization
- Camera permissions
- Bulk upload processing
- QR validation
- Authentication restoration

Avoid using a single global spinner for unrelated operations.

11. FORMS

Forms should:
- Have explicit validation rules
- Provide useful field-level errors
- Handle server-side validation
- Prevent duplicate submissions
- Preserve user input where appropriate
- Support kiosk/touch interaction
- Work well on mobile
- Have clear completion/recovery states

Complex workflows should have explicit state machines or clearly defined states where necessary rather than deeply nested boolean flags.

12. PERFORMANCE

Pay attention to:
- React re-renders
- Query invalidation scope
- Large visitor lists
- Bulk operations
- Image/QR rendering
- Camera/scanner resources
- SignalR event frequency
- Client bundle size
- Server/client component boundaries
- Expensive derived calculations
- Memoization only where it provides actual value

For large datasets consider:
- Pagination
- Virtualized lists
- Server-side filtering
- Server-side sorting
- Debounced search
- Cursor pagination if supported
- Batched operations

13. ACCESSIBILITY

The application should follow accessible UI practices:
- Keyboard navigation
- Proper labels
- Focus management
- Screen-reader semantics
- Appropriate color contrast
- Accessible dialogs
- Accessible form errors
- Clear status messages
- Touch-friendly kiosk controls

Do not rely solely on color to communicate visitor/security states.

14. KIOSK UX

Kiosk flows should prioritize:
- Large touch targets
- Minimal typing
- Clear progression
- Strong validation feedback
- Recovery from interruptions
- Timeout/session reset where appropriate
- Mobile/tablet responsiveness
- Camera permission handling
- Network failure recovery
- Clear completion confirmation

15. QR SECURITY

QR codes should not be treated as trusted client data.

The backend should determine:
- Whether a pass exists
- Whether it is valid
- Whether it has expired
- Whether it has been revoked
- Whether it is already used
- Whether the current operation is authorized

Frontend responsibilities:
- Scan
- Submit verification
- Display verification result
- Handle camera state
- Handle invalid/expired/error states

16. REAL-TIME CONSISTENCY

Important scenario:

Host approves visitor
    ↓
REST mutation succeeds
    ↓
Backend emits SignalR event
    ↓
Other connected clients receive event
    ↓
Relevant TanStack Query caches update/invalidate

The architecture must prevent:
- Duplicate notifications
- Stale dashboard state
- Race conditions
- Excessive refetching
- Conflicting optimistic updates

17. SECURITY

Never trust client-side state for security decisions.

Never treat:
- localStorage
- React state
- Context
- URL parameters
- hidden UI
- disabled buttons
- client-side role checks

as authoritative authorization.

Backend remains authoritative.

JWT handling should favor secure cookie-based patterns when compatible with the backend architecture.

Be cautious about:
- XSS
- token exposure
- CSRF depending on cookie strategy
- CORS
- WebSocket authentication
- sensitive data in QR codes
- sensitive data in URLs
- logging tokens
- exposing secrets through NEXT_PUBLIC_* variables

18. ENVIRONMENT CONFIGURATION

Known configuration:

NEXT_PUBLIC_API_BASE_URL
Purpose:
- Public API base URL used by the frontend

NEXT_PUBLIC_SIGNALR_HUB_URL
Purpose:
- Public SignalR hub endpoint

Important:
Anything prefixed NEXT_PUBLIC_ may be exposed to browser/client code.

Never place:
- JWT signing secrets
- API private keys
- database credentials
- backend secrets
- encryption secrets

inside NEXT_PUBLIC_* variables.

19. CODE QUALITY

Preferred code characteristics:
- Strict TypeScript
- Small focused components
- Explicit interfaces/types
- Clear domain boundaries
- Reusable hooks
- Centralized API communication
- Predictable state management
- Minimal side effects
- Good error handling
- Good accessibility
- Testable business logic
- Clear naming
- No unnecessary abstractions

Avoid:
- God components
- Massive hooks
- API calls embedded everywhere
- Business logic in JSX
- Global state for server data
- Excessive useEffect
- Excessive prop drilling when a domain hook is better
- Premature abstraction
- `any`
- Duplicate API logic
- Duplicate SignalR connections
- Client-side authorization treated as backend security

EXPECTED ENGINEERING FLOW

For a typical feature:

1. Define domain/request/response types.
2. Define or update the API service.
3. Create TanStack Query query/mutation hooks.
4. Define cache/query-key behavior.
5. Add SignalR event handling if the feature is real-time.
6. Implement business logic at the appropriate feature layer.
7. Build UI components around that behavior.
8. Add RBAC/route protection where necessary.
9. Handle loading/error/empty/success states.
10. Verify mobile/kiosk/accessibility behavior.
11. Consider performance and cache consistency.
12. Ensure backend authorization remains authoritative.
13. Test edge cases and race conditions.

CODE REVIEW MINDSET

When reviewing code, evaluate:

Architecture:
- Is responsibility in the correct layer?
- Is the Server/Client boundary appropriate?
- Is there unnecessary coupling?

Type safety:
- Are domain/API types explicit?
- Are nullable and optional values handled correctly?
- Is any avoidable?

State:
- Is this client state or server state?
- Should TanStack Query own it?
- Is Context actually necessary?
- Are cache updates correct?

API:
- Is the service layer being respected?
- Are errors normalized?
- Is authentication handled safely?

Real-time:
- Are SignalR subscriptions duplicated?
- Is cleanup correct?
- Are query caches synchronized correctly?
- Are reconnection scenarios handled?

Security:
- Is the frontend trusting client-controlled data?
- Could authorization be bypassed?
- Are sensitive values exposed?
- Is QR validation actually server-authoritative?

Performance:
- Could this cause unnecessary renders?
- Is a large dataset handled efficiently?
- Is query invalidation too broad?
- Are expensive operations repeated?

UX:
- Are loading/error/empty states handled?
- Is the flow usable on mobile/kiosk?
- Is accessibility considered?

Maintainability:
- Can another engineer understand the code?
- Is business logic isolated?
- Are abstractions justified?
- Is the implementation consistent with the existing architecture?

IMPORTANT ARCHITECTURAL RULES

Rule 1:
TanStack Query owns server state.

Rule 2:
React Context should primarily own authentication/session concerns.

Rule 3:
SignalR is an event/update channel, not a replacement for server-state management.

Rule 4:
API calls should normally flow through services and centralized Axios infrastructure.

Rule 5:
Backend authorization is authoritative; frontend RBAC is primarily for routing and UX enforcement.

Rule 6:
QR validity must be determined server-side.

Rule 7:
NEXT_PUBLIC_* variables are public and must never contain secrets.

Rule 8:
Prefer Server Components unless client-side interactivity or browser APIs require a Client Component.

Rule 9:
Keep Client Component boundaries as small as reasonably possible.

Rule 10:
Use strict TypeScript and avoid any unless there is a documented, justified reason.

Rule 11:
Design real-time updates and REST mutations together to avoid stale or conflicting UI state.

Rule 12:
Optimize bulk visitor functionality for large datasets rather than assuming small lists.

Rule 13:
Kiosk and mobile workflows require dedicated UX considerations rather than simply making desktop layouts responsive.

Rule 14:
Business logic should not be buried inside generic UI components.

Rule 15:
When proposing implementation changes, preserve the existing architecture unless there is a clear technical reason to change it.

DEFAULT ENGINEERING ASSUMPTION

When I provide code, architecture recommendations, debugging guidance, refactoring suggestions, or implementation plans for Simpli, I should assume:

- Next.js 14 App Router
- React 18
- TypeScript strict mode
- Tailwind CSS
- Shadcn UI
- Lucide React
- TanStack Query
- React Context for authentication/session
- Axios
- ASP.NET Core REST API
- ASP.NET Core SignalR
- JWT Bearer authentication
- PostgreSQL via Neon
- Render deployment
- Roles: Visitor, Host, Security, Admin
- Repository structure:
  src/app
  src/components
  src/hooks
  src/lib
  src/services
  src/types
  src/styles

The goal is to maintain a scalable, secure, strongly typed, real-time enterprise frontend with clean separation between presentation, client behavior, server state, API infrastructure, authentication, and domain logic.
