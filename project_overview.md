# Intern Tracking System

> A comprehensive B2B SaaS platform for end-to-end intern progress tracking.

---

## Quick Navigation

| Section | Description |
|---------|-------------|
| [Problem Statement](#1-problem-statement) | Current gaps in intern tracking |
| [Existing Solutions](#2-existing-solutions) | How companies currently manage |
| [Our Strategy](#3-our-strategy) | What makes us different |
| [Admin Playground](#4-admin-playground) | Custom role builder |
| [Task Tracking System](#5-task-tracking-system) | Universal work tracking |
| [Dashboard Framework](#6-dashboard-framework) | Flexible views for any role |
| [Intern Lifecycle](#7-intern-lifecycle) | End-to-end journey |

---

## 1. Problem Statement

### What's Broken Today?

```
┌─────────────────────────────────────────────────────────────────┐
│                 CURRENT STATE OF INTERN TRACKING                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  📊 Spreadsheets         → Hard to track, no real-time        │
│  📧 Emails               → Gets lost, unorganized             │
│  📝 Google Docs          → Scattered, no workflow             │
│  📅 Manual Reports       → Time-consuming, inconsistent        │
│  💬 Slack/Teams          → No formal tracking                  │
│                                                                 │
│  RESULT:                                                        │
│  ❌ No unified view of intern progress                         │
│  ❌ Manual effort for mentors                                  │
│  ❌ No standardized evaluation                                 │
│  ❌ Difficult to generate reports                              │
│  ❌ No visibility for management                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Pain Points by Role

| Role | Pain Point |
|------|------------|
| **HR/Admin** | No visibility into program effectiveness |
| **Mentor** | Manual tracking in spreadsheets |
| **Manager** | Hard to compare intern performance |
| **Intern** | Unclear expectations, scattered feedback |

---

## 2. Existing Solutions

### How Companies Currently Manage

```
┌─────────────────────────────────────────────────────────────────┐
│              TYPICAL INTERN MANAGEMENT STACK                    │
│                                                                 │
│   ┌──────────────────┐   ┌──────────────────┐                  │
│   │  Google Sheets   │   │   Jira/Asana     │                  │
│   │  (Tracking)      │   │   (Tasks)        │                  │
│   └────────┬─────────┘   └────────┬─────────┘                  │
│            │                      │                             │
│            └──────────┬───────────┘                             │
│                       │                                         │
│               ❌ NOT INTEGRATED                                 │
│                       │                                         │
│            ┌──────────┴───────────┐                             │
│            │                      │                             │
│   ┌────────▼─────────┐   ┌────────▼─────────┐                  │
│   │   Slack/Teams    │   │    Email         │                  │
│   │   (Comm)         │   │   (Reports)      │                  │
│   └──────────────────┘   └──────────────────┘                  │
│                                                                 │
│   PROBLEMS:                                                     │
│   • Data in multiple places                                    │
│   • Manual export/import                                       │
│   • No intern-specific features                                │
│   • No built-in evaluation                                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Competitors Analysis

| Solution | Type | Limitation |
|----------|------|------------|
| **Softr/Airtable Templates** | Generic | Not purpose-built for interns |
| **HRIS Systems** | Full HR | Overkill, expensive |
| **Project Management Tools** | Task-focused | No intern lifecycle tracking |
| **Custom Spreadsheets** | DIY | No automation, manual effort |

---

## 3. Our Strategy

### Core Positioning

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│      WE ARE NOT:                   WE ARE:                     │
│      ────────────                  ───────                     │
│                                                                 │
│      ❌ Replacing Slack           ✅ Tracking intern work      │
│      ❌ Replacing GitHub          ✅ Evaluating submissions    │
│      ❌ Full HR system            ✅ Purpose-built for interns │
│      ❌ Project management        ✅ Progress visibility       │
│                                                                 │
│      ─────────────────────────────────────────────────────     │
│                                                                 │
│                   INTERN TRACKING LAYER                        │
│            (Sits on top of existing workflows)                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Key Differentiators

| Feature | Why It Matters |
|---------|----------------|
| **Admin Playground** | Build ANY org structure visually |
| **Universal Task Types** | Track ANY kind of work |
| **AI Evaluation** | Automated, consistent scoring |
| **Flexible Dashboards** | Views adapt to custom roles |
| **End-to-End Lifecycle** | Onboarding → Active → Exit |

---

## 4. Admin Playground

### Concept: Visual Role Builder

Admin can build **any** organization structure using a graph-like interface.

```
┌─────────────────────────────────────────────────────────────────┐
│ 🎮 ORG STRUCTURE BUILDER                        [Save] [Reset] │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ROLE PALETTE                 │        CANVAS                  │
│  ┌──────────────┐             │                                │
│  │ + New Role   │             │     ┌──────────┐               │
│  ├──────────────┤             │     │   PM     │               │
│  │ 👤 Staff     │             │     └────┬─────┘               │
│  │ 🎓 Intern    │             │          │                     │
│  │ 👁 Reviewer  │             │     ┌────▼─────┐               │
│  │ 🤝 Buddy    │             │     │Tech Lead │               │
│  └──────────────┘             │     └────┬─────┘               │
│                               │     ┌────┴────┐                │
│                               │     ▼         ▼                │
│                               │ ┌──────┐ ┌──────┐              │
│                               │ │Mentor│ │Buddy │              │
│                               │ └──┬───┘ └──┬───┘              │
│                               │    ▼         ▼                 │
│                               │ ┌──────────────┐               │
│                               │ │    Intern    │               │
│                               │ └──────────────┘               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Creating Roles (Nodes)

Each role has:

| Property | Description |
|----------|-------------|
| **Name** | Custom name (Host, Mentor, Lead, etc.) |
| **Category** | Staff / Intern |
| **Icon + Color** | Visual identifier |
| **Profile Fields** | What info to collect |
| **Permissions** | What this role can do |

### Connecting Roles (Edges)

**Predefined Relationship Types:**

| Relationship | Direction | Meaning |
|--------------|:---------:|---------|
| **manages** | A → B | Full authority |
| **mentors** | A → B | Guides, assigns tasks |
| **reviews** | A → B | Reviews submissions only |
| **supports** | A → B | Peer support, no authority |
| **oversees** | A → B | Read-only visibility |

### Permissions Library

```
┌─────────────────────────────────────────────────────────────────┐
│ PERMISSION CATEGORIES                                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ TASKS                       │ USERS                            │
│ • Create                    │ • Create                         │
│ • Assign                    │ • Edit                           │
│ • Review                    │ • View (subtree/all)             │
│ • Score                     │                                  │
│                             │                                  │
│ ATTENDANCE                  │ REPORTS                          │
│ • View                      │ • View own                       │
│ • Approve leave             │ • View subtree                   │
│                             │ • Generate                       │
│                             │ • Export                         │
│ MEETINGS                    │                                  │
│ • Schedule                  │ SETTINGS                         │
│ • Invite                    │ • Edit profile                   │
│ • Record                    │ • Configure roles                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Data Scope Options

For each permission, admin sets **scope**:

| Scope | Meaning |
|-------|---------|
| **Own** | Only their own data |
| **Direct** | Immediate children only |
| **Subtree** | All descendants |
| **Company** | Everyone |

### Templates

Pre-built structures for quick start:

| Template | Structure |
|----------|-----------|
| **Simple** | Admin → Mentor → Intern |
| **With Supervisor** | Admin → Supervisor → Mentor → Intern |
| **Big Tech** | Admin → Manager → Host + Buddy → Intern |
| **With Reviewer** | Admin → Mentor → Intern + Reviewer |

---

## 5. Task Tracking System

### Universal Task Types

Our system tracks **any** kind of work an intern does:

```
┌─────────────────────────────────────────────────────────────────┐
│                  TASK CATEGORIES                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  💻 DEVELOPMENT       │  📝 DOCUMENTATION     │  🔍 RESEARCH   │
│  ─────────────────    │  ─────────────────    │  ───────────   │
│  • Bug fixes          │  • Technical docs     │  • POC         │
│  • Feature dev        │  • API docs           │  • Learning    │
│  • API work           │  • User guides        │  • Tech research│
│  • Frontend UI        │  • Meeting notes      │  • Exploration │
│  • Testing            │  • Handover docs      │               │
│                       │                       │               │
│  🎨 DESIGN            │  📊 DATA              │  ⚙️ DEVOPS     │
│  ─────────────────    │  ─────────────────    │  ───────────   │
│  • Wireframes         │  • Spreadsheet work   │  • Setup       │
│  • UI mockups         │  • Report generation  │  • Deployment  │
│  • UX research        │  • Data entry         │  • CI/CD       │
│  • Design review      │  • Analysis           │  • Monitoring  │
│                       │                       │               │
│  🧪 TESTING           │  📣 PRESENTATION      │  📁 OTHER      │
│  ─────────────────    │  ─────────────────    │  ───────────   │
│  • Manual testing     │  • Demo               │  • Custom      │
│  • Test cases         │  • Status update      │               │
│  • Bug reporting      │  • Showcase           │               │
│  • Automation         │                       │               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Task Structure

Every task has:

| Field | Description |
|-------|-------------|
| **Title** | Short name |
| **Category** | Type from above |
| **Description** | Rich text details |
| **Deadline** | Due date/time |
| **Points** | Score weight |
| **Assignees** | Which interns |
| **Submission Type** | What to submit |

### Submission Types

| Type | Description | Example |
|------|-------------|---------|
| **File Upload** | Any file type | .zip, .pdf, doc |
| **GitHub Link** | PR or repo link | github.com/... |
| **External URL** | Figma, Docs, Drive | figma.com/... |
| **Text** | Written response | Summary, notes |
| **Mixed** | Combination | Files + link |

### Task Flow

```
TASK CREATED (by authorized role)
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│ TASK: "Build User Auth API"                                     │
│ Category: Development  │  Deadline: Jan 15  │  Points: 10      │
│ Submit: GitHub Link + Documentation File                       │
└─────────────────────────────────────────────────────────────────┘
        │
        ├── Notification sent to assignees
        │
        ▼
INTERN WORKS
        │
        ├── Updates status: Pending → In Progress
        │
        ▼
INTERN SUBMITS
        │
        ├── Upload files / paste links
        ├── Can resubmit before deadline
        ├── Late? Marked "Submitted Late"
        │
        ▼
AI REVIEW (Automatic after deadline)
        │
        ├── Analyzes submission
        ├── Generates score + remarks
        │
        ▼
HUMAN REVIEW (Mentor or Reviewer)
        │
        ├── Views AI analysis
        ├── Approves / Modifies score
        ├── Adds remarks
        │
        ▼
COMPLETED
        │
        └── Intern sees final score + feedback
```

### Status Mapping

| Staff View | Intern View |
|------------|-------------|
| Assigned | Pending |
| In Progress | In Progress |
| Submitted (3/5) | Submitted |
| Under AI Review | Under Review |
| Pending Review | Under Review |
| Completed | Approved / Needs Revision |

---

## 6. Dashboard Framework

### How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│              DASHBOARD = ROLE PERMISSIONS → WIDGETS             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Admin creates ROLE with PERMISSIONS                        │
│                     │                                           │
│                     ▼                                           │
│  2. System AUTO-GENERATES dashboard for that role              │
│                     │                                           │
│                     ▼                                           │
│  3. Only RELEVANT WIDGETS appear based on permissions          │
│                                                                 │
│  ─────────────────────────────────────────────────────────────│
│                                                                 │
│  EXAMPLE:                                                       │
│  Role has permission: attendance.read (scope: subtree)         │
│       ↓                                                        │
│  Dashboard shows: "Team Attendance" widget                     │
│                                                                 │
│  Role has permission: submission.review                        │
│       ↓                                                        │
│  Dashboard shows: "Pending Reviews" widget                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

### Complete Widget Catalog

#### 📊 STATS WIDGETS (Number Cards)

| Widget | What It Shows | Required Permission |
|--------|--------------|---------------------|
| **My Score** | Personal score % | `category: intern` |
| **Total Tasks** | Count of tasks in scope | `task.read` |
| **Pending Tasks** | Incomplete tasks | `task.read` |
| **Team Count** | Users in subtree | `user.read (subtree/company)` |
| **Pending Reviews** | Submissions to review | `submission.review` |
| **Average Score** | Team average | `report.view (subtree)` |
| **Attendance Rate** | % present | `attendance.read (subtree)` |
| **Leave Requests** | Pending approvals | `leave.approve` |

---

#### 📋 LIST WIDGETS

| Widget | What It Shows | Required Permission |
|--------|--------------|---------------------|
| **My Tasks** | Personal assigned tasks | `Always for interns` |
| **Upcoming Deadlines** | Tasks sorted by deadline | `task.read` |
| **Team Tasks** | All tasks in subtree | `task.read (subtree)` |
| **Submissions to Review** | List of pending reviews | `submission.review` |
| **Team Members** | Users under this role | `user.read (subtree)` |
| **Leave Requests** | Pending leave approvals | `leave.approve` |
| **Recent Activity** | Audit log entries | `audit.view` |

---

#### ⏱️ ATTENDANCE WIDGETS

| Widget | What It Shows | Required Permission |
|--------|--------------|---------------------|
| **Clock In/Out** | Button to clock | `attendance.clock` (own) |
| **My Attendance** | Personal record | `Always for interns` |
| **Today's Attendance** | Who's present/absent | `attendance.read (subtree)` |
| **Weekly Attendance** | Chart of week | `attendance.read` |
| **Leave Balance** | Remaining leaves | `Always for interns` |

---

#### 📈 CHART WIDGETS

| Widget | What It Shows | Required Permission |
|--------|--------------|---------------------|
| **Score Trend** | Score over time | `report.view (own/subtree)` |
| **Task Completion** | Tasks done vs assigned | `task.read` |
| **Attendance Chart** | Present/absent graph | `attendance.read` |
| **Team Performance** | Compare team scores | `report.view (subtree)` |
| **Category Breakdown** | Tasks by category | `task.read (subtree)` |

---

#### 📅 CALENDAR/TIME WIDGETS

| Widget | What It Shows | Required Permission |
|--------|--------------|---------------------|
| **Upcoming Meetings** | Next 3 meetings | `meeting.join` |
| **Task Calendar** | Deadlines on calendar | `task.read` |
| **My Schedule** | Today's meetings | `Always` |

---

#### 🔗 INTEGRATION WIDGETS

| Widget | What It Shows | Required Permission |
|--------|--------------|---------------------|
| **GitHub Stats** | Commits, PRs | `github.view` + connected |
| **GitHub Activity** | Recent commits | `github.view` |

---

#### ⚡ ACTION WIDGETS

| Widget | Quick Action | Required Permission |
|--------|-------------|---------------------|
| **Create Task** | Button | `task.create` |
| **Create User** | Button | `user.create` |
| **Schedule Meeting** | Button | `meeting.schedule` |
| **Generate Report** | Button | `report.generate` |
| **View All Tasks** | Link | `task.read` |
| **View All Users** | Link | `user.read` |

---

### Permission → Widget Mapping Table

| If Role Has This Permission | These Widgets Appear |
|-----------------------------|----------------------|
| `task.read (own)` | My Tasks, Upcoming Deadlines |
| `task.read (subtree)` | + Team Tasks, Task Stats |
| `task.create` | + Create Task button |
| `submission.review` | Pending Reviews, Review count |
| `attendance.clock` | Clock In/Out button |
| `attendance.read (own)` | My Attendance, Leave Balance |
| `attendance.read (subtree)` | + Today's Attendance, Attendance Chart |
| `leave.approve` | Leave Requests widget |
| `user.read (subtree)` | Team Members, Team Count |
| `user.create` | Create User button |
| `meeting.schedule` | Schedule Meeting button |
| `meeting.join` | Upcoming Meetings |
| `report.view (own)` | Score Trend (own) |
| `report.view (subtree)` | + Team Performance, Average Score |
| `report.generate` | Generate Report button |
| `audit.view` | Recent Activity |
| `github.view` | GitHub Stats (if connected) |

---

### Dashboard Examples by Role

#### Intern Dashboard (Minimal Permissions)

Permissions: `task.read (own)`, `attendance.clock`, `attendance.read (own)`, `meeting.join`

```
┌─────────────────────────────────────────────────────────────────┐
│ Welcome, Alice 👋                                    🔔  ⚙️     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│ │ My Score     │  │ Tasks        │  │ Attendance   │           │
│ │ 87%          │  │ 4 pending    │  │ 92% ✓        │           │
│ └──────────────┘  └──────────────┘  └──────────────┘           │
│                                                                 │
│ ┌─────────────────────────────────────────┐ ┌─────────────────┐│
│ │ My Tasks                               │ │ Clock In/Out    ││
│ │ ▸ Build Auth API (Due: Jan 15)         │ │                 ││
│ │ ▸ Write API Docs (Due: Jan 18)         │ │ [Clock In ▶]    ││
│ │ ▸ Fix Login Bug (Due: Jan 12)          │ │                 ││
│ └─────────────────────────────────────────┘ └─────────────────┘│
│                                                                 │
│ ┌─────────────────────────────────────────┐ ┌─────────────────┐│
│ │ Upcoming Meetings                      │ │ Leave Balance   ││
│ │ ▸ 1:1 with John - Today 3:00 PM       │ │ Casual: 10/12   ││
│ │ ▸ Sprint Demo - Tomorrow 11:00 AM     │ │ Sick: 6/6       ││
│ └─────────────────────────────────────────┘ └─────────────────┘│
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Widgets shown: My Score, Tasks count, Attendance %, My Tasks list, 
               Clock In/Out, Upcoming Meetings, Leave Balance
```

---

#### Mentor Dashboard (More Permissions)

Permissions: `task.* (subtree)`, `submission.review`, `attendance.read (subtree)`, `user.read (subtree)`, `leave.approve`, `report.view (subtree)`

```
┌─────────────────────────────────────────────────────────────────┐
│ Dashboard                                            🔔  ⚙️     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐         │
│ │ 5      │ │ 12     │ │ 3      │ │ 1      │ │ 85%    │         │
│ │Interns │ │ Tasks  │ │ Review │ │ Leave  │ │ Avg    │         │
│ └────────┘ └────────┘ └────────┘ └────────┘ └────────┘         │
│                                                                 │
│ ┌─────────────────────────────────┐ ┌───────────────────────────┐
│ │ Pending Reviews                │ │ Quick Actions             │
│ │ ▸ Alice - Auth API (AI: 8/10)  │ │ [+ Create Task]          │
│ │ ▸ Bob - DB Design (AI: 7/10)   │ │ [📊 Generate Report]      │
│ │ ▸ Charlie - UI Fix (AI: 9/10)  │ │ [📅 Schedule Meeting]     │
│ └─────────────────────────────────┘ └───────────────────────────┘
│                                                                 │
│ ┌─────────────────────────────────┐ ┌───────────────────────────┐
│ │ Team Attendance Today          │ │ Team Performance         │
│ │ ✅ Present: 4                   │ │ 📈 [Line Chart]          │
│ │ ⏰ Late: 1                      │ │                          │
│ │ ❌ Absent: 0                    │ │                          │
│ └─────────────────────────────────┘ └───────────────────────────┘
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Additional widgets: Team count, Leave requests count, Pending Reviews list,
                    Quick Actions, Team Attendance, Team Performance chart
```

---

#### Admin Dashboard (Full Permissions)

Permissions: All permissions with `company` scope

```
┌─────────────────────────────────────────────────────────────────┐
│ Admin Dashboard                                      🔔  ⚙️     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐         │
│ │ 25     │ │ 5      │ │ 50     │ │ 78%    │ │ 3      │         │
│ │ Users  │ │Mentors │ │ Tasks  │ │ Attend │ │ Leave  │         │
│ └────────┘ └────────┘ └────────┘ └────────┘ └────────┘         │
│                                                                 │
│ ┌──────────────────────────┐ ┌──────────────────────────────────┐
│ │ Quick Actions           │ │ Recent Activity                 │
│ │ [+ Create User]         │ │ ▸ John created task "API Doc"  │
│ │ [+ Create Role]         │ │ ▸ Alice submitted "Auth API"   │
│ │ [📊 Generate Report]    │ │ ▸ Bob clocked in at 9:15 AM    │
│ │ [⚙️ Role Playground]    │ │ ▸ Admin approved leave for X   │
│ └──────────────────────────┘ └──────────────────────────────────┘
│                                                                 │
│ ┌──────────────────────────┐ ┌──────────────────────────────────┐
│ │ Company Performance     │ │ All Roles Overview              │
│ │ 📈 [Chart over time]    │ │ Admin: 2 │ Mentor: 5 │ Intern: 18│
│ └──────────────────────────┘ └──────────────────────────────────┘
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Additional widgets: All stats, Role Playground access, Recent Activity,
                    All Roles Overview, Full company charts
```

---

#### Reviewer Dashboard (Review-Only Permissions)

Permissions: `submission.read`, `submission.review`, `submission.score`, `task.read`

```
┌─────────────────────────────────────────────────────────────────┐
│ Reviewer Dashboard                                   🔔  ⚙️     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│ │ To Review    │  │ Reviewed     │  │ Avg Score    │           │
│ │ 5            │  │ 23           │  │ 7.8/10       │           │
│ └──────────────┘  └──────────────┘  └──────────────┘           │
│                                                                 │
│ ┌───────────────────────────────────────────────────────────────┐
│ │ Submissions Awaiting Review                                  │
│ │                                                              │
│ │ Task             │ Intern  │ AI Score │ Submitted  │ Action  │
│ │ Auth API         │ Alice   │ 8/10     │ 2 hrs ago  │ [Review]│
│ │ Database Design  │ Bob     │ 7/10     │ 1 day ago  │ [Review]│
│ │ UI Component     │ Charlie │ 9/10     │ 3 hrs ago  │ [Review]│
│ │ API Testing      │ David   │ 6/10     │ 5 hrs ago  │ [Review]│
│ └───────────────────────────────────────────────────────────────┘
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Only review-related widgets appear (no attendance, no user management)
```

---

### Widget Display Logic (Summary)

```
┌─────────────────────────────────────────────────────────────────┐
│                    WIDGET VISIBILITY RULES                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  IF permission.scope === 'own'                                 │
│     → Show personal data only                                  │
│     → Widget title: "My ___"                                   │
│                                                                 │
│  IF permission.scope === 'direct'                              │
│     → Show data for direct children only                       │
│     → Widget title: "Direct ___"                               │
│                                                                 │
│  IF permission.scope === 'subtree'                             │
│     → Show data for all descendants                            │
│     → Widget title: "Team ___"                                 │
│                                                                 │
│  IF permission.scope === 'company'                             │
│     → Show company-wide data                                   │
│     → Widget title: "Company ___" or "All ___"                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 7. Intern Lifecycle

### End-to-End Journey

```
┌─────────────────────────────────────────────────────────────────┐
│                    COMPLETE INTERN JOURNEY                      │
│                                                                 │
│  ONBOARDING          ACTIVE               EVALUATE      EXIT   │
│  ──────────          ──────               ────────      ────   │
│                                                                 │
│  ┌────────┐         ┌────────┐          ┌────────┐   ┌────────┐│
│  │ Login  │────────►│ Tasks  │─────────►│ Review │──►│  Exit  ││
│  │ Setup  │         │ Work   │          │ Score  │   │ Cert   ││
│  └────────┘         └────────┘          └────────┘   └────────┘│
│                                                                 │
│  Week 1             Week 2-11            Ongoing      Week 12  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Phase Details

#### Phase 1: Onboarding

| Step | Description |
|------|-------------|
| **1. Receive Invite** | Email with login credentials |
| **2. First Login** | Set password, accept terms |
| **3. Complete Profile** | Fill role-specific fields |
| **4. Connect GitHub** | Optional OAuth |
| **5. Access Workspace** | See assigned workspace |

#### Phase 2: Active Work

| Activity | Frequency |
|----------|-----------|
| **View Tasks** | Daily |
| **Update Status** | As needed |
| **Submit Work** | Per deadline |
| **Clock In/Out** | Daily |
| **Attend Meetings** | As scheduled |
| **Participate in Discussions** | Ongoing |

#### Phase 3: Evaluation

| Component | Description |
|-----------|-------------|
| **Task Scores** | AI + Human review |
| **Attendance** | Tracked automatically |
| **Progress** | Cumulative score |
| **Feedback** | Per-task remarks |

#### Phase 4: Exit

| Step | Description |
|------|-------------|
| **Complete Tasks** | Finish pending work |
| **Final Review** | Overall performance |
| **Generate Report** | Comprehensive summary |
| **Certificate** | Formal completion cert |

---

## 8. Reviewer System

### When Needed

- Mentor is busy
- Need technical expert review
- Quality assurance process
- Second opinion on work

### Configuration

```
┌─────────────────────────────────────────────────────────────────┐
│ REVIEW SETTINGS (configured by Admin)                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Review Flow:                                                    │
│ ○ Mentor reviews all submissions                               │
│ ● Submissions go to designated Reviewer                        │
│                                                                 │
│ Reviewer Permissions:                                           │
│ ☑ View AI score                                                │
│ ☑ Edit AI score                                                │
│ ☑ Add remarks                                                  │
│ ☐ Reject submission                                            │
│                                                                 │
│ Visibility:                                                     │
│ ☑ Mentor can see reviewer's feedback                           │
│ ☑ Intern can see reviewer's name                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Flow

```
Intern Submits
      │
      ▼
AI Review (automatic)
      │
      ├── If reviewer configured ──► Reviewer receives
      │                                    │
      │                                    ▼
      │                             Reviewer scores
      │                                    │
      │                                    ▼
      │                             Mentor can view
      │
      └── If no reviewer ──────────► Mentor reviews
                                           │
                                           ▼
                                    Intern sees result
```

---

## 9. Multi-Tenant Architecture

### Company Isolation

```
┌─────────────────────────────────────────────────────────────────┐
│                     PLATFORM (SaaS)                             │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │   COMPANY A     │  │   COMPANY B     │  │   COMPANY C     │ │
│  │                 │  │                 │  │                 │ │
│  │  • Own roles    │  │  • Own roles    │  │  • Own roles    │ │
│  │  • Own users    │  │  • Own users    │  │  • Own users    │ │
│  │  • Own data     │  │  • Own data     │  │  • Own data     │ │
│  │  • Own settings │  │  • Own settings │  │  • Own settings │ │
│  │                 │  │                 │  │                 │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                 │
│  ──────────────────────────────────────────────────────────────│
│                                                                 │
│  SHARED:                                                        │
│  • Infrastructure                                              │
│  • AI evaluation engine                                        │
│  • Video conferencing                                          │
│  • Authentication system                                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Super Admin View

| Feature | Description |
|---------|-------------|
| **Company Management** | Onboard, suspend, delete |
| **Billing** | Plans, invoices |
| **Analytics** | Platform-wide metrics |
| **Support** | Ticket management |

---

## 10. Core Features Summary

### By Role Type

| Feature | Admin | Staff Roles | Intern |
|---------|:-----:|:-----------:|:------:|
| Playground | ✅ | ❌ | ❌ |
| User Management | ✅ | Varies | ❌ |
| Task Creation | ✅ | Varies | ❌ |
| Task Submission | ❌ | ❌ | ✅ |
| Review | ✅ | Varies | ❌ |
| Reports | ✅ | Varies | Self |
| Settings | ✅ | Limited | Limited |

### Feature List

| Module | Features |
|--------|----------|
| **Tracking** | Tasks, submissions, status, deadlines |
| **Evaluation** | AI scoring, human review, feedback |
| **Attendance** | Clock in/out, leave requests, history |
| **Communication** | Discussions, comments, DMs |
| **Meetings** | Video calls, scheduling, recording |
| **Reports** | Progress, attendance, performance |
| **Certificates** | Completion certificate generation |

---

## 11. Communication

### Text Editors

| Type | Used For | Features |
|------|----------|----------|
| **Simple** | DMs, comments | Text, emoji, images, attachments |
| **Rich** | Announcements, task descriptions | Formatting, tables, links |

### Channels

| Channel | Visibility |
|---------|------------|
| **Workspace Feed** | All workspace members |
| **Task Comments** | Assignees + creator |
| **Private Comments** | Individual + mentor |
| **Direct Messages** | 1-on-1 |

---

## 12. Next Steps (To Discuss)

| Topic | Status |
|-------|--------|
| Technology Stack | ⏳ Pending |
| Database Schema | ⏳ Pending |
| API Design | ⏳ Pending |
| UI/UX Flows | ⏳ Pending |
| Business Model | ⏳ Pending |

---

*Document Version: 2.0*
*Last Updated: January 12, 2026*
