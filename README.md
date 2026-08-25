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

