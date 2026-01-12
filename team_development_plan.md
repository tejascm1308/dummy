# Backend Team Development Plan

> 4 developers, pure backend development, sequential phases

---

## Team Structure

| Dev | Module | Focus Area |
|-----|--------|------------|
| **Dev 1** | Core & Auth | Companies, Roles, Users, Permissions, Authentication |
| **Dev 2** | Tasks & Reviews | Tasks, Submissions, AI Review Integration |
| **Dev 3** | Attendance & HR | Attendance, Leave, Certificates, Reports |
| **Dev 4** | Communication | Messages, Meetings, WebSocket, Notifications |

---

## Phase 1: Foundation (Week 1-2)

### All Developers Together

```
┌─────────────────────────────────────────────────────────────────┐
│                    SHARED SETUP (Day 1-2)                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  • Initialize Node.js project (Express/Fastify)                │
│  • MongoDB connection setup                                     │
│  • Project structure conventions                                │
│  • Environment config (.env)                                    │
│  • Error handling middleware                                    │
│  • Request validation (Zod/Joi)                                 │
│  • Logger setup (Winston/Pino)                                  │
│  • Base response format                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Shared Utilities (All contribute)

```
src/
├── config/
│   ├── database.ts         # MongoDB connection
│   ├── env.ts              # Environment variables
│   └── constants.ts        # App constants
├── middleware/
│   ├── errorHandler.ts     # Global error handling
│   ├── auth.ts             # JWT middleware (Dev 1 owns)
│   ├── validate.ts         # Request validation
│   └── permission.ts       # Permission check (Dev 1 owns)
├── utils/
│   ├── response.ts         # Standard response format
│   ├── logger.ts           # Logging utility
│   ├── pagination.ts       # Pagination helper
│   └── helpers.ts          # Common helpers
└── types/
    └── index.ts            # Shared TypeScript types
```

---

## Phase 2: Core Development (Week 2-6)

### Dev 1: Core & Auth Module

**Owns:** Authentication, Authorization, Company, Roles, Users

#### Week 2-3: Auth Foundation

```
src/modules/auth/
├── controllers/
│   ├── auth.controller.ts
│   └── password.controller.ts
├── services/
│   ├── auth.service.ts
│   ├── jwt.service.ts
│   └── password.service.ts
├── models/
│   └── session.model.ts
├── routes/
│   └── auth.routes.ts
└── validators/
    └── auth.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/login` | Login with email/password |
| POST | `/api/auth/logout` | Logout, invalidate token |
| POST | `/api/auth/refresh` | Refresh access token |
| POST | `/api/auth/forgot-password` | Request password reset |
| POST | `/api/auth/reset-password` | Reset with token |
| PUT | `/api/auth/change-password` | Change own password |
| GET | `/api/auth/me` | Get current user |

#### Week 3-4: Company & Roles

```
src/modules/company/
├── controllers/
│   └── company.controller.ts
├── services/
│   └── company.service.ts
├── models/
│   └── company.model.ts     # With embedded settings
├── routes/
│   └── company.routes.ts
└── validators/
    └── company.validator.ts

src/modules/role/
├── controllers/
│   └── role.controller.ts
├── services/
│   ├── role.service.ts
│   └── permission.service.ts   # Permission checking logic
├── models/
│   ├── role.model.ts
│   └── roleRelationship.model.ts
├── routes/
│   └── role.routes.ts
└── validators/
    └── role.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/company` | Get company details |
| PUT | `/api/company` | Update company settings |
| PUT | `/api/company/attendance-config` | Update attendance config |
| PUT | `/api/company/leave-types` | Update leave types |
| GET | `/api/roles` | List all roles |
| POST | `/api/roles` | Create role |
| GET | `/api/roles/:id` | Get role details |
| PUT | `/api/roles/:id` | Update role |
| DELETE | `/api/roles/:id` | Delete role |
| PUT | `/api/roles/:id/permissions` | Update permissions |
| GET | `/api/roles/relationships` | Get role relationships |
| POST | `/api/roles/relationships` | Create relationship |
| DELETE | `/api/roles/relationships/:id` | Delete relationship |
| GET | `/api/permissions` | List all available permissions |

#### Week 4-5: Users

```
src/modules/user/
├── controllers/
│   └── user.controller.ts
├── services/
│   ├── user.service.ts
│   └── userConnection.service.ts
├── models/
│   ├── user.model.ts
│   └── userConnection.model.ts
├── routes/
│   └── user.routes.ts
└── validators/
    └── user.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/users` | List users (with filters) |
| POST | `/api/users` | Create user |
| GET | `/api/users/:id` | Get user details |
| PUT | `/api/users/:id` | Update user |
| DELETE | `/api/users/:id` | Delete user |
| PUT | `/api/users/:id/suspend` | Suspend user |
| PUT | `/api/users/:id/activate` | Activate user |
| POST | `/api/users/bulk-import` | Import from CSV |
| GET | `/api/users/:id/connections` | Get user connections |
| POST | `/api/users/connections` | Create connection |
| DELETE | `/api/users/connections/:id` | Remove connection |
| GET | `/api/users/:id/subtree` | Get all users in subtree |

#### Week 5-6: Permission System

```
src/services/permission/
├── permissionChecker.ts     # Core permission logic
├── scopeResolver.ts         # Resolve own/direct/subtree/company
└── permissionList.ts        # All permission definitions
```

**Key Functions:**
```typescript
// Check if user has permission on target
hasPermission(userId, permission, targetId): boolean

// Get all users in user's scope for a permission
getUsersInScope(userId, permission): User[]

// Check if user can perform action on target user
canActOnUser(actorId, action, targetUserId): boolean
```

---

### Dev 2: Tasks & Reviews Module

**Owns:** Workspaces, Tasks, Submissions, Reviews, AI Integration

**Dependencies:** Waits for Dev 1's user model (Week 3)

#### Week 3-4: Workspaces & Tasks

```
src/modules/workspace/
├── controllers/
│   └── workspace.controller.ts
├── services/
│   └── workspace.service.ts
├── models/
│   └── workspace.model.ts
├── routes/
│   └── workspace.routes.ts
└── validators/
    └── workspace.validator.ts

src/modules/task/
├── controllers/
│   └── task.controller.ts
├── services/
│   └── task.service.ts
├── models/
│   └── task.model.ts
├── routes/
│   └── task.routes.ts
└── validators/
    └── task.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/workspaces` | List workspaces |
| POST | `/api/workspaces` | Create workspace |
| GET | `/api/workspaces/:id` | Get workspace details |
| PUT | `/api/workspaces/:id` | Update workspace |
| DELETE | `/api/workspaces/:id` | Delete workspace |
| POST | `/api/workspaces/:id/members` | Add member |
| DELETE | `/api/workspaces/:id/members/:userId` | Remove member |
| GET | `/api/tasks` | List tasks (filtered) |
| POST | `/api/tasks` | Create task |
| GET | `/api/tasks/:id` | Get task details |
| PUT | `/api/tasks/:id` | Update task |
| DELETE | `/api/tasks/:id` | Delete task |
| POST | `/api/tasks/:id/assignees` | Add assignee |
| DELETE | `/api/tasks/:id/assignees/:userId` | Remove assignee |
| PUT | `/api/tasks/:id/status` | Update task status |
| GET | `/api/tasks/my` | Get my assigned tasks |
| GET | `/api/tasks/team` | Get team's tasks |

#### Week 4-5: Submissions

```
src/modules/submission/
├── controllers/
│   └── submission.controller.ts
├── services/
│   ├── submission.service.ts
│   └── fileUpload.service.ts
├── models/
│   └── submission.model.ts
├── routes/
│   └── submission.routes.ts
└── validators/
    └── submission.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/tasks/:id/submit` | Submit work |
| GET | `/api/submissions` | List submissions |
| GET | `/api/submissions/:id` | Get submission details |
| PUT | `/api/submissions/:id/resubmit` | Resubmit work |
| GET | `/api/submissions/pending-review` | Get pending reviews |

#### Week 5-6: Reviews & AI Integration

```
src/modules/review/
├── controllers/
│   └── review.controller.ts
├── services/
│   ├── review.service.ts
│   └── aiReview.service.ts    # Calls Python AI service
├── routes/
│   └── review.routes.ts
└── validators/
    └── review.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/submissions/:id/review` | Submit human review |
| POST | `/api/submissions/:id/trigger-ai` | Trigger AI review |
| PUT | `/api/submissions/:id/approve` | Approve submission |
| PUT | `/api/submissions/:id/request-revision` | Request revision |
| GET | `/api/submissions/:id/reviews` | Get all reviews |

**Python AI Service (separate microservice):**
```
POST /ai/review
Body: { submission_data, task_info, rubric }
Response: { score, remarks, detailed_review }
```

---

### Dev 3: Attendance & HR Module

**Owns:** Attendance, Leave, Certificates, Reports

**Dependencies:** Waits for Dev 1's user model (Week 3)

#### Week 3-4: Attendance

```
src/modules/attendance/
├── controllers/
│   └── attendance.controller.ts
├── services/
│   ├── attendance.service.ts
│   ├── clockIn.service.ts
│   └── ipGeoCheck.service.ts
├── models/
│   └── attendance.model.ts
├── routes/
│   └── attendance.routes.ts
└── validators/
    └── attendance.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/attendance/clock-in` | Clock in |
| POST | `/api/attendance/clock-out` | Clock out |
| GET | `/api/attendance/today` | Get today's record |
| GET | `/api/attendance/my` | Get my attendance |
| GET | `/api/attendance/team` | Get team attendance |
| GET | `/api/attendance/user/:id` | Get user's attendance |
| PUT | `/api/attendance/:id` | Edit attendance (admin) |
| GET | `/api/attendance/summary` | Monthly summary |
| GET | `/api/attendance/export` | Export as CSV |

#### Week 4-5: Leave Management

```
src/modules/leave/
├── controllers/
│   └── leave.controller.ts
├── services/
│   ├── leave.service.ts
│   └── leaveBalance.service.ts
├── models/
│   ├── leaveRequest.model.ts
│   └── leaveBalance.model.ts
├── routes/
│   └── leave.routes.ts
└── validators/
    └── leave.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/leave/request` | Request leave |
| GET | `/api/leave/my` | My leave requests |
| GET | `/api/leave/pending` | Pending approvals |
| PUT | `/api/leave/:id/approve` | Approve leave |
| PUT | `/api/leave/:id/reject` | Reject leave |
| GET | `/api/leave/balance` | Get my balance |
| GET | `/api/leave/balance/:userId` | Get user's balance |
| PUT | `/api/leave/balance/:userId` | Adjust balance |
| DELETE | `/api/leave/:id` | Cancel request |

#### Week 5-6: Certificates & Reports

```
src/modules/certificate/
├── controllers/
│   └── certificate.controller.ts
├── services/
│   ├── certificate.service.ts
│   └── pdfGenerator.service.ts
├── models/
│   └── certificate.model.ts
├── routes/
│   └── certificate.routes.ts
└── validators/
    └── certificate.validator.ts

src/modules/report/
├── controllers/
│   └── report.controller.ts
├── services/
│   ├── report.service.ts
│   └── reportData.service.ts
├── routes/
│   └── report.routes.ts
└── validators/
    └── report.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/certificates/generate/:userId` | Generate certificate |
| GET | `/api/certificates` | List certificates |
| GET | `/api/certificates/:id` | Get certificate |
| GET | `/api/certificates/verify/:code` | Verify certificate |
| GET | `/api/reports/dashboard` | Dashboard stats |
| GET | `/api/reports/intern/:id` | Intern report |
| GET | `/api/reports/team` | Team report |
| POST | `/api/reports/generate` | Generate PDF report |
| GET | `/api/reports/export` | Export data |

---

### Dev 4: Communication Module

**Owns:** Messages, Announcements, Meetings, WebSocket, Notifications

**Dependencies:** Waits for Dev 1's user model (Week 3)

#### Week 3-4: Messages & Announcements

```
src/modules/message/
├── controllers/
│   └── message.controller.ts
├── services/
│   └── message.service.ts
├── models/
│   └── message.model.ts
├── routes/
│   └── message.routes.ts
└── validators/
    └── message.validator.ts

src/modules/announcement/
├── controllers/
│   └── announcement.controller.ts
├── services/
│   └── announcement.service.ts
├── models/
│   └── announcement.model.ts
├── routes/
│   └── announcement.routes.ts
└── validators/
    └── announcement.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/messages/conversations` | List conversations |
| GET | `/api/messages/:userId` | Get DM with user |
| POST | `/api/messages/:userId` | Send DM |
| GET | `/api/tasks/:id/comments` | Get task comments |
| POST | `/api/tasks/:id/comments` | Add comment |
| DELETE | `/api/messages/:id` | Delete message |
| GET | `/api/announcements` | List announcements |
| POST | `/api/announcements` | Create announcement |
| GET | `/api/announcements/:id` | Get announcement |
| PUT | `/api/announcements/:id` | Update announcement |
| DELETE | `/api/announcements/:id` | Delete announcement |
| POST | `/api/announcements/:id/comments` | Add comment |

#### Week 4-5: Meetings

```
src/modules/meeting/
├── controllers/
│   └── meeting.controller.ts
├── services/
│   ├── meeting.service.ts
│   └── meetingCode.service.ts
├── models/
│   └── meeting.model.ts
├── routes/
│   └── meeting.routes.ts
└── validators/
    └── meeting.validator.ts
```

**Endpoints:**
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/meetings` | Schedule meeting |
| GET | `/api/meetings` | List meetings |
| GET | `/api/meetings/:id` | Get meeting details |
| PUT | `/api/meetings/:id` | Update meeting |
| DELETE | `/api/meetings/:id` | Cancel meeting |
| POST | `/api/meetings/:id/invite` | Invite users |
| PUT | `/api/meetings/:id/respond` | Accept/decline |
| GET | `/api/meetings/upcoming` | Upcoming meetings |
| POST | `/api/meetings/:id/join` | Get join info |

#### Week 5-6: WebSocket & Notifications

```
src/modules/socket/
├── socketServer.ts           # Socket.io setup
├── handlers/
│   ├── connection.handler.ts
│   ├── message.handler.ts
│   ├── notification.handler.ts
│   └── presence.handler.ts
└── middleware/
    └── socketAuth.ts

src/modules/notification/
├── services/
│   ├── notification.service.ts
│   └── emailQueue.service.ts
├── templates/
│   └── ... (email templates)
└── jobs/
    └── emailWorker.ts
```

**Socket Events:**
| Event | Direction | Description |
|-------|-----------|-------------|
| `message:new` | Server→Client | New message received |
| `message:send` | Client→Server | Send message |
| `notification:new` | Server→Client | New notification |
| `meeting:started` | Server→Client | Meeting has started |
| `user:online` | Server→Client | User came online |
| `user:offline` | Server→Client | User went offline |
| `task:updated` | Server→Client | Task status changed |

---

## Phase 3: Integration (Week 6-7)

### All Developers Together

```
┌─────────────────────────────────────────────────────────────────┐
│                    INTEGRATION TASKS                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. CONNECT ALL MODULES                                         │
│     • Ensure all cross-module calls work                       │
│     • Test permission checks across modules                    │
│     • Verify WebSocket integration                             │
│                                                                 │
│  2. EMAIL SYSTEM (Dev 4 leads)                                  │
│     • Connect Python email service                             │
│     • Test all 12 email templates                              │
│     • Queue system for async emails                            │
│                                                                 │
│  3. AUDIT LOGGING (Dev 1 leads)                                 │
│     • Add audit log calls to all mutations                     │
│     • Test TTL cleanup                                         │
│                                                                 │
│  4. DASHBOARD DATA (Dev 3 leads)                                │
│     • Widget data endpoints                                    │
│     • Aggregation queries                                      │
│                                                                 │
│  5. ERROR HANDLING & VALIDATION                                 │
│     • Consistent error responses                               │
│     • Input validation on all endpoints                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 4: Testing & Polish (Week 7-8)

### Testing Responsibilities

| Dev | Test Focus |
|-----|------------|
| **Dev 1** | Auth flows, permission edge cases |
| **Dev 2** | Task lifecycle, review flow, AI integration |
| **Dev 3** | Attendance calculations, leave balance, reports |
| **Dev 4** | Real-time messaging, WebSocket reliability |

### Shared Testing Tasks

- [ ] API documentation (Swagger/OpenAPI)
- [ ] Unit tests for services
- [ ] Integration tests for APIs
- [ ] Load testing for critical endpoints
- [ ] Security testing (auth bypass, injection)

---

## Dependency Graph

```
Week 2          Week 3          Week 4          Week 5          Week 6
  │               │               │               │               │
  ▼               ▼               ▼               ▼               ▼
┌─────┐       ┌─────┐       ┌─────┐       ┌─────┐       ┌─────┐
│ Dev1│───────│Auth │───────│Roles│───────│Users│───────│Perms│
│Setup│       │     │       │     │       │     │       │     │
└─────┘       └──┬──┘       └──┬──┘       └──┬──┘       └─────┘
                 │             │             │
                 │    Uses     │    Uses     │
                 ▼             ▼             ▼
              ┌─────┐       ┌─────┐       ┌─────┐
        Dev2  │Work │───────│Tasks│───────│Revws│
              │space│       │     │       │     │
              └─────┘       └─────┘       └─────┘
                 │
                 │    Uses
                 ▼
              ┌─────┐       ┌─────┐       ┌─────┐
        Dev3  │Atten│───────│Leave│───────│Certs│
              │dance│       │     │       │Repts│
              └─────┘       └─────┘       └─────┘
                 │
                 │    Uses
                 ▼
              ┌─────┐       ┌─────┐       ┌─────┐
        Dev4  │Msgs │───────│Meets│───────│Socks│
              │Anncs│       │     │       │Notif│
              └─────┘       └─────┘       └─────┘
```

---

## Daily Sync Checklist

```
┌─────────────────────────────────────────────────────────────────┐
│                    DAILY STANDUP (15 min)                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Each dev answers:                                              │
│  1. What did I complete yesterday?                             │
│  2. What am I working on today?                                │
│  3. Am I blocked on anyone?                                    │
│                                                                 │
│  Key questions to surface:                                      │
│  • Does anyone need the User model changes?                    │
│  • Does anyone need a new shared utility?                      │
│  • Any API contract changes needed?                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## API Count Summary

| Module | Dev | Endpoints |
|--------|-----|-----------|
| Auth | Dev 1 | 7 |
| Company | Dev 1 | 5 |
| Roles | Dev 1 | 12 |
| Users | Dev 1 | 12 |
| Workspaces | Dev 2 | 7 |
| Tasks | Dev 2 | 12 |
| Submissions | Dev 2 | 5 |
| Reviews | Dev 2 | 5 |
| Attendance | Dev 3 | 9 |
| Leave | Dev 3 | 9 |
| Certificates | Dev 3 | 4 |
| Reports | Dev 3 | 5 |
| Messages | Dev 4 | 6 |
| Announcements | Dev 4 | 6 |
| Meetings | Dev 4 | 9 |
| **Total** | | **~113 endpoints** |

---

## Git Workflow

```
main
  │
  └── develop
        │
        ├── feature/auth (Dev 1)
        ├── feature/roles (Dev 1)
        ├── feature/users (Dev 1)
        │
        ├── feature/workspaces (Dev 2)
        ├── feature/tasks (Dev 2)
        ├── feature/submissions (Dev 2)
        │
        ├── feature/attendance (Dev 3)
        ├── feature/leave (Dev 3)
        ├── feature/reports (Dev 3)
        │
        ├── feature/messages (Dev 4)
        ├── feature/meetings (Dev 4)
        └── feature/websocket (Dev 4)
```

**Rules:**
1. Always branch from `develop`
2. PR must be reviewed by 1 other dev
3. Merge to `develop` daily (at least)
4. No direct pushes to `main` or `develop`

---

*Document Version: 1.0*
*Team Size: 4 developers*
*Estimated Duration: 8 weeks*
