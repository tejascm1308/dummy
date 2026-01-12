# Intern Tracking System - Team Development Plan

> 4-person team, clean development stretch

---

## Team Roles

| Role | Focus Area | Skills Needed |
|------|------------|---------------|
| **Dev 1** | Backend Core | Node.js, MongoDB, APIs |
| **Dev 2** | Frontend Core | Next.js, React, TailwindCSS |
| **Dev 3** | Backend Features + Python | Node.js, Python, FastAPI |
| **Dev 4** | Frontend Features + Integration | React, WebSocket, Testing |

---

## Development Phases (4-Week Sprint)

### Week 1: Foundation

```
┌─────────────────────────────────────────────────────────────────┐
│ WEEK 1: FOUNDATION                                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ DEV 1 (Backend Core)           DEV 2 (Frontend Core)           │
│ ───────────────────            ─────────────────────           │
│ • Project setup                • Next.js project setup         │
│ • MongoDB connection           • TailwindCSS config            │
│ • Auth system (JWT)            • Component library setup       │
│ • Company/Role/User models     • Auth pages (login, reset)     │
│ • Basic CRUD APIs              • Layout components             │
│                                • Routing structure              │
│                                                                 │
│ DEV 3 (Backend Features)       DEV 4 (Frontend Features)       │
│ ────────────────────           ──────────────────────          │
│ • Python service setup         • State management (Zustand)    │
│ • Email service scaffold       • API client setup              │
│ • Environment configs          • Socket.io client setup        │
│ • Docker compose               • Toast notifications           │
│ • CI/CD pipeline               • Loading states                │
│                                                                 │
│ DELIVERABLE: Auth working end-to-end                           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Week 2: Core Features

```
┌─────────────────────────────────────────────────────────────────┐
│ WEEK 2: CORE FEATURES                                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ DEV 1 (Backend Core)           DEV 2 (Frontend Core)           │
│ ───────────────────            ─────────────────────           │
│ • Role Playground APIs         • Role Playground UI            │
│ • Permission system            • Drag-drop role builder        │
│ • User management APIs         • User management pages         │
│ • Workspace APIs               • Workspace views               │
│                                • Dashboard widgets              │
│                                                                 │
│ DEV 3 (Backend Features)       DEV 4 (Frontend Features)       │
│ ────────────────────           ──────────────────────          │
│ • Task APIs                    • Task list/detail pages        │
│ • Submission APIs              • Task creation form            │
│ • File upload (S3)             • Submission UI                 │
│ • Email templates              • File upload component         │
│                                • Rich text editor               │
│                                                                 │
│ DELIVERABLE: Roles, Users, Tasks working                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Week 3: Advanced Features

```
┌─────────────────────────────────────────────────────────────────┐
│ WEEK 3: ADVANCED FEATURES                                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ DEV 1 (Backend Core)           DEV 2 (Frontend Core)           │
│ ───────────────────            ─────────────────────           │
│ • Attendance APIs              • Attendance UI                 │
│ • Leave request APIs           • Clock in/out widget           │
│ • Meeting APIs                 • Leave request forms           │
│ • WebSocket events             • Meeting scheduler             │
│ • Real-time notifications      • Notification center           │
│                                                                 │
│ DEV 3 (Backend Features)       DEV 4 (Frontend Features)       │
│ ────────────────────           ──────────────────────          │
│ • AI Review service            • Review UI                     │
│ • Review APIs                  • Score display                 │
│ • Report generation            • Charts & analytics            │
│ • Certificate generation       • Report views                  │
│                                • Dashboard graphs               │
│                                                                 │
│ DELIVERABLE: Attendance, Reviews, Reports working              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Week 4: Polish & Integration

```
┌─────────────────────────────────────────────────────────────────┐
│ WEEK 4: POLISH & INTEGRATION                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ DEV 1 (Backend Core)           DEV 2 (Frontend Core)           │
│ ───────────────────            ─────────────────────           │
│ • Audit logging                • UI polish                     │
│ • Error handling               • Responsive design             │
│ • Performance optimization     • Dark mode                     │
│ • Security review              • Accessibility                 │
│                                • Animation polish               │
│                                                                 │
│ DEV 3 (Backend Features)       DEV 4 (Frontend Features)       │
│ ────────────────────           ──────────────────────          │
│ • Email sending                • End-to-end testing            │
│ • Scheduled jobs               • Bug fixes                     │
│ • API documentation            • Integration testing           │
│ • Deployment scripts           • Performance testing           │
│                                                                 │
│ DELIVERABLE: Production-ready MVP                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Detailed Task Breakdown

### DEV 1: Backend Core (Node.js + MongoDB)

| Week | Tasks | Priority |
|------|-------|----------|
| **1** | Project setup, MongoDB, Auth (JWT), Company/Role/User models | High |
| **2** | Role Playground API, Permission engine, User CRUD, Workspace API | High |
| **3** | Attendance API, Leave API, Meeting API, WebSocket setup | Medium |
| **4** | Audit logs, Security hardening, Performance tuning | Medium |

**Key Files:**
- `/backend/src/models/*` - All MongoDB schemas
- `/backend/src/routes/*` - API routes
- `/backend/src/middleware/*` - Auth, permissions
- `/backend/src/services/permission.service.ts` - Permission engine

---

### DEV 2: Frontend Core (Next.js + React)

| Week | Tasks | Priority |
|------|-------|----------|
| **1** | Next.js setup, TailwindCSS, Component library, Auth pages | High |
| **2** | Role Playground UI, User pages, Workspace views, Dashboard | High |
| **3** | Attendance UI, Leave forms, Meeting UI, Notifications | Medium |
| **4** | Responsive, Dark mode, Animations, Polish | Medium |

**Key Files:**
- `/frontend/src/components/*` - Reusable components
- `/frontend/src/app/*` - Pages (App Router)
- `/frontend/src/styles/*` - CSS/Tailwind config
- `/frontend/src/hooks/*` - Custom React hooks

---

### DEV 3: Backend Features + Python

| Week | Tasks | Priority |
|------|-------|----------|
| **1** | Python FastAPI setup, Email service, Docker, CI/CD | High |
| **2** | Task API, Submission API, File upload (S3), Email templates | High |
| **3** | AI Review service, Report generation, Certificate PDF | Medium |
| **4** | Email sending, Scheduled jobs, API docs | Medium |

**Key Files:**
- `/backend/src/routes/task.routes.ts` - Task APIs
- `/python-services/ai_review/*` - AI review logic
- `/python-services/email_service/*` - Email templates
- `/python-services/report_service/*` - PDF generation

---

### DEV 4: Frontend Features + Integration

| Week | Tasks | Priority |
|------|-------|----------|
| **1** | State management, API client, Socket.io, Toast system | High |
| **2** | Task pages, Submission UI, File upload, Rich editor | High |
| **3** | Review UI, Charts, Analytics, Dashboard graphs | Medium |
| **4** | E2E testing, Bug fixes, Integration tests | Medium |

**Key Files:**
- `/frontend/src/app/tasks/*` - Task pages
- `/frontend/src/components/charts/*` - Analytics
- `/frontend/src/lib/api.ts` - API client
- `/frontend/src/lib/socket.ts` - WebSocket client

---

## Folder Structure

```
intern-tracker/
├── backend/                    # Node.js API (Dev 1, Dev 3)
│   ├── src/
│   │   ├── models/            # MongoDB schemas
│   │   ├── routes/            # API endpoints
│   │   ├── controllers/       # Business logic
│   │   ├── services/          # Shared services
│   │   ├── middleware/        # Auth, permissions
│   │   └── utils/             # Helpers
│   └── package.json
│
├── frontend/                   # Next.js (Dev 2, Dev 4)
│   ├── src/
│   │   ├── app/               # App Router pages
│   │   ├── components/        # React components
│   │   ├── hooks/             # Custom hooks
│   │   ├── lib/               # API, socket, utils
│   │   └── styles/            # CSS
│   └── package.json
│
├── python-services/            # Python microservices (Dev 3)
│   ├── ai_review/             # AI scoring
│   ├── email_service/         # Email rendering
│   └── report_service/        # PDF generation
│
├── docker-compose.yml          # Local development
└── README.md
```

---

## Daily Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│ DAILY STANDUP (15 min max)                                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ 1. What did I complete yesterday?                              │
│ 2. What am I working on today?                                 │
│ 3. Any blockers?                                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│ GIT WORKFLOW                                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ main ─────────────────────────────────────────────► (stable)   │
│    │                                                            │
│    └── develop ───────────────────────────────────► (staging)  │
│           │                                                     │
│           ├── feat/auth (Dev 1)                                │
│           ├── feat/role-playground (Dev 2)                     │
│           ├── feat/tasks (Dev 3)                               │
│           └── feat/dashboard (Dev 4)                           │
│                                                                 │
│ MERGE: Feature → Develop → Main (after review)                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Communication Points

| Event | When | Who |
|-------|------|-----|
| **Standup** | Daily 10:00 AM | All |
| **API Contract** | Before integration | Backend ↔ Frontend |
| **Code Review** | Before merge | At least 1 reviewer |
| **Demo** | End of each week | All + stakeholders |

---

## Integration Checkpoints

| Checkpoint | When | What to Verify |
|------------|------|----------------|
| **Auth Complete** | End of Week 1 | Login → JWT → Protected routes |
| **Core CRUD** | Mid Week 2 | Roles, Users, Tasks E2E |
| **Real-time** | Mid Week 3 | WebSocket notifications |
| **Full MVP** | End of Week 4 | All features integrated |

---

## Risk Areas & Mitigation

| Risk | Owner | Mitigation |
|------|-------|------------|
| Permission engine complexity | Dev 1 | Define test cases early |
| Role Playground drag-drop | Dev 2 | Use proven library (react-dnd) |
| AI Review accuracy | Dev 3 | Start with rule-based, add AI later |
| Real-time sync issues | Dev 4 | Test WebSocket edge cases |

---

*Created: January 12, 2026*
