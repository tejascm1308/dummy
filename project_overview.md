# Intern Tracking System - Complete Module Documentation

> Version 3.0 | Clear, detailed explanation of every module

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Company Module](#2-company-module)
3. [Role System](#3-role-system)
4. [User Management](#4-user-management)
5. [Workspace Module](#5-workspace-module)
6. [Task System](#6-task-system)
7. [Submission & Review System](#7-submission--review-system)
8. [Attendance System](#8-attendance-system)
9. [Leave Management](#9-leave-management)
10. [Meeting System](#10-meeting-system)
11. [Communication Module](#11-communication-module)
12. [Report System](#12-report-system)
13. [Certificate System](#13-certificate-system)
14. [Dashboard & Widgets](#14-dashboard--widgets)
15. [Tech Stack](#15-tech-stack)

---

## 1. System Overview

### What Is This Application?

This is a **multi-tenant SaaS platform** that helps companies manage their interns. Each company that signs up gets their own isolated space where they can:

- Create custom organizational structures (roles)
- Track intern tasks and progress
- Manage attendance and leaves
- Conduct reviews (AI + human)
- Generate reports and certificates

### Multi-Tenant Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    OUR PLATFORM                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│   │   COMPANY A  │  │   COMPANY B  │  │   COMPANY C  │         │
│   │  (Acme Corp) │  │  (Tech Inc)  │  │  (StartupXY) │         │
│   │              │  │              │  │              │         │
│   │  • 3 Roles   │  │  • 5 Roles   │  │  • 4 Roles   │         │
│   │  • 20 Users  │  │  • 50 Users  │  │  • 15 Users  │         │
│   │  • 100 Tasks │  │  • 200 Tasks │  │  • 80 Tasks  │         │
│   └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                 │
│   Each company is COMPLETELY ISOLATED                          │
│   Company A cannot see Company B's data                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### How Companies Get Started

1. Company admin signs up on our platform
2. Admin sets up company name, logo, timezone
3. Admin creates roles in the Role Playground
4. Admin creates users and assigns them to roles
5. Users receive login credentials via email
6. Everyone starts working

---

## 2. Company Module

### What Is a Company?

A **company** is a tenant in our system. When a company signs up, they get:

- A unique login URL (e.g., `app.ourplatform.com/acme-corp`)
- Their own set of roles, users, and data
- Customizable settings and branding

### Company Data

| Field | What It Stores | Example |
|-------|----------------|---------|
| `name` | Company display name | "Acme Corporation" |
| `slug` | URL-friendly identifier | "acme-corp" |
| `logo_url` | Company logo image | "https://..." |
| `timezone` | Default timezone | "Asia/Kolkata" |
| `settings` | Branding, features | Colors, enabled features |
| `attendance_config` | Attendance rules | Working hours, grace period |
| `leave_types` | Available leave categories | Casual, Sick, WFH |
| `task_categories` | Types of tasks | Development, Documentation |

### Company Settings Explained

**Branding Settings:**
- Primary color (for UI theme)
- Logo for dark/light mode
- Company name display style

**Feature Toggles:**
- Enable/disable GitHub integration
- Enable/disable video meetings
- Enable/disable AI reviews

**Attendance Configuration:**
```
attendance_config: {
  enabled: true,                    // Is attendance tracking on?
  methods: ["manual"],              // How do users clock in?
  work_start: "09:00",              // What time does work start?
  work_end: "18:00",                // What time does work end?
  grace_minutes: 15,                // Minutes late before marked late
  min_hours_full: 8,                // Hours needed for full day
  min_hours_half: 4,                // Hours needed for half day
  allowed_ips: [],                  // If IP restriction is on
  geo_fence: { enabled: false }     // If location restriction is on
}
```

**Leave Types:**
```
leave_types: [
  { name: "Casual", quota: 12, is_paid: true },     // 12 casual leaves per year
  { name: "Sick", quota: 6, is_paid: true },        // 6 sick leaves per year
  { name: "WFH", quota: null, is_paid: true }       // Unlimited WFH
]
```

---

## 3. Role System

### What Is a Role?

A **role** is a job template that defines:
- What permissions someone has
- What profile information they need to provide
- Who they can interact with

### Understanding Role Categories

Every role belongs to one of three categories:

| Category | Purpose | Examples |
|----------|---------|----------|
| `admin` | Full control of the company | Super Admin, Company Admin |
| `staff` | Manages and reviews interns | Mentor, Supervisor, Reviewer |
| `intern` | The people being tracked | Software Intern, Design Intern |

### Role Example: Technical Mentor

```
Role: "Technical Mentor"
Category: staff

Permissions:
┌────────────────────────────────────────────────────────────────┐
│ This role CAN:                                                 │
│                                                                │
│ • View users in their subtree (interns under them)            │
│ • Create tasks for their interns                              │
│ • Review and score submissions                                │
│ • View attendance of their interns                            │
│ • Approve/reject leave requests from direct reports           │
│ • Schedule meetings                                           │
│ • Generate reports for their interns                          │
│                                                                │
│ This role CANNOT:                                              │
│                                                                │
│ • Create or delete users                                      │
│ • Modify company settings                                     │
│ • Access other mentors' interns                               │
│ • View company-wide reports                                   │
└────────────────────────────────────────────────────────────────┘

Profile Fields (what mentor must fill):
• Department (dropdown: Engineering, Design, QA)
• Years of Experience (number)
• Specialization (text)
```

### Role Example: Intern

```
Role: "Software Intern"
Category: intern

Permissions:
┌────────────────────────────────────────────────────────────────┐
│ This role CAN:                                                 │
│                                                                │
│ • View their own tasks                                        │
│ • Submit work for tasks                                       │
│ • Clock in/out for attendance                                 │
│ • Request leaves                                              │
│ • View their own scores and reviews                          │
│ • Send messages to mentor                                     │
│ • Join meetings they're invited to                           │
│                                                                │
│ This role CANNOT:                                              │
│                                                                │
│ • Create tasks                                                │
│ • Review anyone's work                                        │
│ • See other interns' data                                     │
│ • Approve leaves                                              │
└────────────────────────────────────────────────────────────────┘

Profile Fields (what intern must fill):
• College Name (text)
• Degree (dropdown: B.Tech, B.E., M.Tech, etc.)
• Internship Start Date (date)
• Internship End Date (date)
• Skills (multi-select)
```

### Role Relationships

Roles connect to each other through **relationships**. These define the org structure.

```
RELATIONSHIP TYPES:

"manages"   → One role manages another (HR/Admin style)
"mentors"   → One role guides another (Technical guidance)
"reviews"   → One role reviews work of another
"supports"  → One role assists another (Buddy system)
"oversees"  → One role supervises another (Higher management)
```

**Example Organization:**

```
                    ┌───────────────┐
                    │  Company      │
                    │  Admin        │
                    └───────┬───────┘
                            │ manages
                    ┌───────▼───────┐
                    │  Engineering  │
                    │  Manager      │
                    └───────┬───────┘
                            │ manages
            ┌───────────────┼───────────────┐
            │               │               │
    ┌───────▼───────┐ ┌─────▼─────┐ ┌───────▼───────┐
    │  Technical    │ │   QA      │ │   Design      │
    │  Mentor       │ │ Reviewer  │ │   Lead        │
    └───────┬───────┘ └─────┬─────┘ └───────┬───────┘
            │               │               │
            │ mentors       │ reviews       │ mentors
            │               │               │
    ┌───────▼───────────────▼───────────────▼───────┐
    │                   INTERNS                      │
    └────────────────────────────────────────────────┘
```

### Permission Scopes

Every permission has a **scope** that defines whose data it applies to:

| Scope | Meaning | Example |
|-------|---------|---------|
| `own` | Only my own data | Intern can view own attendance |
| `direct` | My immediate children | Mentor can approve leave of interns directly under them |
| `subtree` | All descendants | Manager can view all users under all their mentors |
| `company` | Everyone in company | Admin can view all company data |

**Example:**
```
Permission: attendance.read
Scope: subtree

If John (Mentor) has this permission, he can see attendance of:
✓ His own attendance
✓ Alice (his intern)
✓ Bob (his intern)
✗ Charlie (another mentor's intern)
```

### How Admin Creates Roles

1. Open Role Playground (visual builder)
2. Click "Create New Role"
3. Enter role name, category, icon, color
4. Define permissions by checking boxes with scopes
5. Define profile fields (what info to collect)
6. Save role
7. Connect role to other roles (relationships)

---

## 4. User Management

### What Is a User?

A **user** is an actual person with an account. Users are created by admins and assigned to roles.

### User Creation Flow

```
ADMIN                                    SYSTEM                              NEW USER
  │                                         │                                    │
  │ 1. Fill user form:                      │                                    │
  │    • Name: Alice Johnson                │                                    │
  │    • Email: alice@gmail.com             │                                    │
  │    • Role: Software Intern              │                                    │
  │    • Connect to: John (Mentor)          │                                    │
  │                                         │                                    │
  ├─────────── Create User ─────────────────►                                    │
  │                                         │                                    │
  │                                         │ 2. Generate temp password          │
  │                                         │    "TempPass@123"                  │
  │                                         │                                    │
  │                                         │ 3. Save user to database           │
  │                                         │                                    │
  │                                         │ 4. Create user connection:         │
  │                                         │    John → mentors → Alice          │
  │                                         │                                    │
  │                                         ├───── Welcome Email ────────────────►
  │                                         │                                    │
  │                                         │      "Your account is ready!"      │
  │                                         │      "Login: alice@gmail.com"      │
  │                                         │      "Temp Password: TempPass@123" │
  │                                         │      "Login URL: app.../acme-corp" │
  │                                         │                                    │
  │                                         │                               5. User clicks link
  │                                         │                               6. Enters temp password
  │                                         │                               7. Sets new password
  │                                         │                               8. Fills profile fields
  │                                         │                               9. Ready to use!
```

### User Data

| Field | What It Stores |
|-------|----------------|
| `name` | Full name |
| `email` | Login email |
| `password_hash` | Encrypted password |
| `role_id` | Which role they belong to |
| `profile_photo` | Avatar image URL |
| `profile_data` | Dynamic fields based on role |
| `github` | GitHub integration info |
| `status` | active, suspended, exited |
| `temp_password` | True if needs to change password |

### User Connections

**User connections** track who reports to whom.

```
user_connections:
┌──────────────┬─────────────┬──────────────────┐
│ from_user    │ to_user     │ relationship     │
├──────────────┼─────────────┼──────────────────┤
│ John (Mentor)│ Alice       │ "mentors"        │
│ John (Mentor)│ Bob         │ "mentors"        │
│ Sarah (QA)   │ Alice       │ "reviews"        │
│ Sarah (QA)   │ Bob         │ "reviews"        │
└──────────────┴─────────────┴──────────────────┘

This means:
• John mentors Alice and Bob
• Sarah reviews Alice's and Bob's work
```

### User Statuses

| Status | Meaning |
|--------|---------|
| `active` | Normal working user |
| `suspended` | Temporarily disabled (can be reactivated) |
| `exited` | Internship completed or terminated |

---

## 5. Workspace Module

### What Is a Workspace?

A **workspace** is a container that groups related work together. Think of it like a project folder.

### Why Workspaces?

```
WITHOUT WORKSPACES:
All tasks dumped together, no organization

WITH WORKSPACES:
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  ┌────────────────────┐  ┌────────────────────┐                │
│  │ Q1 Backend Project │  │ Mobile App Project │                │
│  │                    │  │                    │                │
│  │ • 15 tasks         │  │ • 10 tasks         │                │
│  │ • 3 interns        │  │ • 2 interns        │                │
│  │ • Due: March 31    │  │ • Due: April 15    │                │
│  └────────────────────┘  └────────────────────┘                │
│                                                                 │
│  Each workspace has its own:                                    │
│  • Tasks                                                        │
│  • Members (who can see this workspace)                        │
│  • Announcements                                                │
│  • Resources                                                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Workspace Members

Workspaces have members with roles:

| Member Role | What They Can Do |
|-------------|------------------|
| `owner` | Full control (edit, delete workspace, manage members) |
| `member` | View workspace, see tasks, see announcements |

---

## 6. Task System

### What Is a Task?

A **task** is a piece of work assigned to interns. It has a deadline, points, and requires a submission.

### Task Categories

Every company can customize their task categories. Default categories:

| Category | Icon | Used For |
|----------|------|----------|
| Development | 💻 | Coding work |
| Documentation | 📝 | Writing docs, guides |
| Research | 🔍 | Learning, exploration, POCs |
| Design | 🎨 | UI/UX wireframes, mockups |
| Testing | 🧪 | QA work, test cases |
| Data | 📊 | Spreadsheets, analysis |
| DevOps | ⚙️ | Setup, deployment |
| Presentation | 📣 | Demos, status updates |
| Other | 📁 | Anything else |

### Task Data

| Field | What It Stores | Example |
|-------|----------------|---------|
| `title` | Short task name | "Build Login API" |
| `description` | Detailed instructions | Rich text with requirements |
| `category` | Type of work | "Development" |
| `workspace_id` | Which workspace | Ref to workspace |
| `deadline` | When it's due | Jan 20, 2026 5:00 PM |
| `points` | Score weight | 10 points |
| `submission_type` | What to submit | "github", "file", "mixed" |
| `assignees` | Who should do it | List of intern IDs |

### Submission Types

| Type | What Intern Submits | Example |
|------|---------------------|---------|
| `file` | Upload files | .zip, .pdf, .docx |
| `github` | GitHub link | PR URL, repo link |
| `url` | Any external URL | Figma, Google Docs |
| `text` | Written response | Summary, notes |
| `mixed` | Combination | Files + GitHub + text |

### Task Status Flow (Per Assignee)

```
    ┌─────────────────────────────────────────────────────────────┐
    │                      TASK LIFECYCLE                         │
    └─────────────────────────────────────────────────────────────┘
                              │
                              ▼
    ┌─────────────────────────────────────────────────────────────┐
    │ PENDING                                                     │
    │ Task assigned, intern hasn't started                        │
    └─────────────────────────────────┬───────────────────────────┘
                                      │ Intern clicks "Start"
                                      ▼
    ┌─────────────────────────────────────────────────────────────┐
    │ IN_PROGRESS                                                 │
    │ Intern is working on it                                     │
    └─────────────────────────────────┬───────────────────────────┘
                                      │ Intern submits work
                                      ▼
                      ┌───────────────┴───────────────┐
                      │                               │
            Before deadline                   After deadline
                      │                               │
                      ▼                               ▼
              ┌──────────────┐              ┌──────────────────┐
              │ SUBMITTED    │              │ LATE_SUBMITTED   │
              └──────────────┘              └──────────────────┘
                      │                               │
                      └───────────────┬───────────────┘
                                      │ Goes for review
                                      ▼
    ┌─────────────────────────────────────────────────────────────┐
    │ UNDER_REVIEW                                                │
    │ AI and/or human is reviewing                                │
    └─────────────────────────────────┬───────────────────────────┘
                                      │
                      ┌───────────────┴───────────────┐
                      │                               │
               Approved                        Needs revision
                      │                               │
                      ▼                               ▼
              ┌──────────────┐              ┌──────────────────┐
              │ APPROVED ✓   │              │ REVISION_NEEDED  │
              │ Task done!   │              │ Fix and resubmit │
              └──────────────┘              └────────┬─────────┘
                                                     │
                                                     │ Intern resubmits
                                                     ▼
                                            (Back to UNDER_REVIEW)
```

### Creating a Task (Example)

```
Mentor John creates a task:

Title: "Implement User Authentication"
Category: Development
Workspace: Q1 Backend Project
Description: 
  "Build a complete user authentication system with:
   - Email/password login
   - JWT token generation
   - Password reset via email
   - Rate limiting on login attempts
   
   Refer to these docs: [links]
   
   Expected deliverables:
   - Working code pushed to repo
   - API documentation
   - Test cases"

Deadline: January 20, 2026 5:00 PM
Points: 20
Submission Type: Mixed (GitHub + File)
Assign to: Alice, Bob
```

---

## 7. Submission & Review System

### Submission Flow

```
INTERN                           SYSTEM                          REVIEWERS
  │                                 │                                 │
  │ 1. Complete work                │                                 │
  │                                 │                                 │
  │ 2. Click "Submit"               │                                 │
  │    - Upload files               │                                 │
  │    - Paste GitHub PR link       │                                 │
  │    - Add notes                  │                                 │
  │                                 │                                 │
  ├──────── Submit ─────────────────►                                 │
  │                                 │                                 │
  │                                 │ 3. Save submission              │
  │                                 │                                 │
  │                                 │ 4. Check if late                │
  │                                 │    (compare with deadline)      │
  │                                 │                                 │
  │                                 │ 5. Trigger AI Review ───────────►
  │                                 │    (send to Python service)     │
  │                                 │                                 │
  │                         ◄─────── AI Review Result ────────────────┤
  │                                 │                                 │
  │                                 │ 6. Save AI review               │
  │                                 │                                 │
  │                                 │ 7. Notify human reviewers ──────►
  │                                 │                                 │
  │                                 │                    8. Human opens submission
  │                                 │                    9. Sees AI score + remarks
  │                                 │                    10. Adds own review
  │                                 │                    11. Approves or requests revision
  │                                 │                                 │
  │                         ◄────── Notification ─────────────────────┤
  │                                 │                                 │
  │ 12. See final result            │                                 │
```

### AI Review System (Python Service)

The AI review service is a **separate Python application** that receives submissions and returns structured reviews.

**How It Works:**

```
Node.js Backend                    Python AI Service
      │                                  │
      │  POST /ai/review                 │
      │  {                               │
      │    task: {                       │
      │      title: "Build Auth API",    │
      │      category: "Development",    │
      │      description: "...",         │
      │      rubric: [criteria]          │
      │    },                            │
      │    submission: {                 │
      │      github_link: "...",         │
      │      files: [...],               │
      │      text: "..."                 │
      │    }                             │
      │  }                               │
      ├──────────────────────────────────►
      │                                  │
      │                                  │ 1. Parse submission
      │                                  │ 2. Analyze code (if GitHub)
      │                                  │ 3. Check against rubric
      │                                  │ 4. Generate feedback
      │                                  │
      │  Response:                       │
      │  {                               │
      │    score: 8,                     │
      │    max_score: 10,                │
      │    remarks: "Good work...",      │
      │    detailed_review: {            │
      │      strengths: ["...", "..."],  │
      │      improvements: ["...", "..."],│
      │      code_quality: 8,            │
      │      functionality: 9,           │
      │      documentation: 7            │
      │    }                             │
      │  }                               │
      ◄──────────────────────────────────┤
```

### Review Storage

Reviews are embedded inside the submission document:

```javascript
submission: {
  _id: "...",
  task_id: "...",
  user_id: "...",
  submission_data: { files: [...], github_links: [...] },
  
  reviews: [
    {
      type: "ai",
      score: 8,
      max_score: 10,
      remarks: "Good implementation with clean code...",
      detailed_review: {
        strengths: ["Clean code structure", "Good error handling"],
        improvements: ["Add rate limiting", "Missing input validation"],
        code_quality: 8,
        functionality: 9,
        documentation: 7
      },
      reviewed_at: "2026-01-15T10:30:00Z"
    },
    {
      type: "human",
      reviewer_id: "john_mentor_id",
      score: 8.5,
      max_score: 10,
      remarks: "Excellent work! Minor improvements needed in security.",
      reviewed_at: "2026-01-15T14:00:00Z"
    }
  ],
  
  final_status: "approved",
  final_score: 8.5
}
```

---

## 8. Attendance System

### What It Tracks

The attendance system tracks when users clock in and out each day.

### Clock In/Out Flow

```
USER                                 SYSTEM
  │                                    │
  │ 1. Open app, click "Clock In"      │
  │                                    │
  ├──────── Clock In Request ──────────►
  │         (includes IP, location)    │
  │                                    │
  │                                    │ 2. Check current time
  │                                    │    Work starts: 9:00 AM
  │                                    │    Current time: 9:10 AM
  │                                    │    Grace period: 15 min
  │                                    │    → Within grace, NOT late
  │                                    │
  │                                    │ 3. Check IP (if enabled)
  │                                    │    Allowed IPs: 192.168.1.x
  │                                    │    User IP: 192.168.1.50
  │                                    │    → IP verified ✓
  │                                    │
  │                                    │ 4. Check location (if enabled)
  │                                    │    Office: 12.97, 77.59
  │                                    │    User: 12.97, 77.60
  │                                    │    Distance: 100m (within 500m)
  │                                    │    → Location verified ✓
  │                                    │
  │                                    │ 5. Create attendance record
  │         ◄────── Success ───────────┤
  │                                    │
  │ ... 8 hours later ...              │
  │                                    │
  │ 6. Click "Clock Out"               │
  │                                    │
  ├──────── Clock Out Request ─────────►
  │                                    │
  │                                    │ 7. Calculate total hours
  │                                    │    Clock in: 9:10 AM
  │                                    │    Clock out: 6:15 PM
  │                                    │    Total: 9h 5m
  │                                    │    → Full day ✓
  │                                    │
  │                                    │ 8. Update attendance record
  │         ◄────── Success ───────────┤
```

### Attendance Record

```javascript
attendance: {
  _id: "...",
  user_id: "alice_id",
  date: "2026-01-15",
  
  clock_in: "2026-01-15T09:10:00Z",
  clock_out: "2026-01-15T18:15:00Z",
  
  total_hours: 9.08,
  
  status: "present",           // present, half_day, absent, leave, wfh
  
  late_by_minutes: 0,          // How many minutes late
  early_by_minutes: 0,         // How many minutes left early
  
  clock_in_info: {
    ip: "192.168.1.50",
    location: { lat: 12.97, lng: 77.59 },
    method: "manual"           // manual, ip_verified, geo_verified
  }
}
```

### Attendance Statuses

| Status | Meaning | Calculated When |
|--------|---------|-----------------|
| `present` | Full working day | total_hours >= min_hours_full |
| `half_day` | Half day | total_hours >= min_hours_half |
| `absent` | Didn't clock in | No record for the day |
| `leave` | On approved leave | Leave request approved |
| `wfh` | Working from home | WFH approved |

---

## 9. Leave Management

### Leave Request Flow

```
INTERN                      SYSTEM                      APPROVER (Mentor)
  │                            │                              │
  │ 1. Fill leave form:        │                              │
  │    Type: Casual            │                              │
  │    From: Jan 20            │                              │
  │    To: Jan 21              │                              │
  │    Reason: Family function │                              │
  │                            │                              │
  ├──── Submit Request ────────►                              │
  │                            │                              │
  │                            │ 2. Check balance             │
  │                            │    Casual: 10 remaining      │
  │                            │    Requesting: 2 days        │
  │                            │    → Has enough ✓            │
  │                            │                              │
  │                            │ 3. Find approver             │
  │                            │    Who has leave.approve     │
  │                            │    for this user?            │
  │                            │    → John (Mentor)           │
  │                            │                              │
  │                            │ 4. Create leave_request      │
  │                            │    status: "pending"         │
  │                            │                              │
  │                            ├──── Notification ────────────►
  │                            │     "Alice requested leave"  │
  │                            │                              │
  │                            │                    5. Review request
  │                            │                    6. Click Approve/Reject
  │                            │                              │
  │                            ◄──── Approve ─────────────────┤
  │                            │                              │
  │                            │ 7. Update leave_request      │
  │                            │    status: "approved"        │
  │                            │                              │
  │                            │ 8. Update leave_balance      │
  │                            │    used: +2 days             │
  │                            │                              │
  │                            │ 9. Mark attendance as "leave"│
  │                            │    for Jan 20, 21            │
  │                            │                              │
  │   ◄──── Notification ──────┤                              │
  │   "Leave approved!"        │                              │
```

### Leave Balance

Each user has a balance per leave type per year:

```javascript
leave_balance: {
  user_id: "alice_id",
  year: 2026,
  balances: [
    { type: "Casual", total: 12, used: 2, remaining: 10 },
    { type: "Sick", total: 6, used: 0, remaining: 6 },
    { type: "WFH", total: null, used: 5, remaining: null }  // unlimited
  ]
}
```

---

## 10. Meeting System

### What Is a Meeting?

Meetings are scheduled video calls within the platform.

### Meeting Flow

```
HOST (Mentor)                  SYSTEM                     PARTICIPANTS
  │                               │                             │
  │ 1. Schedule meeting:          │                             │
  │    Title: "1:1 with Alice"    │                             │
  │    Date: Jan 15, 3:00 PM      │                             │
  │    Duration: 30 min           │                             │
  │    Invite: Alice              │                             │
  │                               │                             │
  ├──── Create Meeting ───────────►                             │
  │                               │                             │
  │                               │ 2. Generate meeting code    │
  │                               │    "MET-20260115-A1B2"      │
  │                               │                             │
  │                               │ 3. Generate passkey         │
  │                               │    "1234"                   │
  │                               │                             │
  │                               │ 4. Save meeting             │
  │                               │                             │
  │                               ├──── Email Invite ───────────►
  │                               │     "You're invited to..."  │
  │                               │                             │
  │                               │     ... 15 min before ...   │
  │                               │                             │
  │                               ├──── Reminder ───────────────►
  │                               │     "Meeting in 15 min"     │
  │                               │                             │
  │ 5. Click "Start Meeting"      │                             │
  │                               │                             │
  ├──── Start ────────────────────►                             │
  │                               │ 6. Update status: "ongoing" │
  │                               │                             │
  │                               │                    7. Click "Join"
  │                               ◄──────────────────── Join ───┤
  │                               │                             │
  │     ◄──── WebRTC Connection ──┴─────────────────────────────►
  │           Video call started!                               │
```

### Meeting Data

```javascript
meeting: {
  _id: "...",
  title: "1:1 with Alice",
  description: "Weekly catch-up",
  
  meeting_code: "MET-20260115-A1B2",
  passkey: "1234",
  
  scheduled_at: "2026-01-15T15:00:00Z",
  duration_minutes: 30,
  
  host_id: "john_mentor_id",
  
  participants: [
    {
      user_id: "alice_id",
      status: "accepted",         // invited, accepted, declined, joined
      invited_at: "2026-01-14T10:00:00Z",
      joined_at: "2026-01-15T15:02:00Z"
    }
  ],
  
  status: "ended",                 // scheduled, ongoing, ended, cancelled
  recording_url: "https://..."     // If recorded
}
```

---

## 11. Communication Module

### Types of Communication

| Type | Purpose | Example |
|------|---------|---------|
| **Direct Messages (DM)** | 1:1 chat between users | Intern messaging mentor |
| **Task Comments** | Discussion on a specific task | Questions about requirements |
| **Workspace Announcements** | Broadcast to all members | "New project starting Monday" |

### Message Storage

All messages use a unified structure:

```javascript
message: {
  _id: "...",
  company_id: "...",
  
  context_type: "dm",              // "dm", "task_comment", "workspace_discussion"
  context_id: "other_user_id",     // Who/what is the context
  
  sender_id: "alice_id",
  content: "Hey, can you clarify requirement #3?",
  
  attachments: [
    { type: "image", url: "https://...", name: "screenshot.png" }
  ],
  
  reply_to: null,                  // If replying to another message
  
  created_at: "2026-01-15T10:30:00Z"
}
```

### Real-Time with WebSocket

Messages are delivered in real-time using WebSocket:

```
USER A                          SERVER                          USER B
  │                               │                               │
  │ Send message to B             │                               │
  ├─── socket.emit('message:send')──►                              │
  │                               │                               │
  │                               │ 1. Save to database           │
  │                               │ 2. Find B's socket connection │
  │                               │                               │
  │                               ├──── socket.emit('message:new')──►
  │                               │                               │
  │                               │                     B sees message
  │                               │                     instantly!
```

---

## 12. Report System

### Universal Report Framework

The report system generates PDFs for any role type using a **flexible template system**.

### Report Types

| Report | Who Gets It | Contains |
|--------|-------------|----------|
| **Intern Report** | Individual intern | Tasks, scores, attendance, feedback |
| **Team Report** | Mentor/Manager | All interns' summary |
| **Attendance Report** | HR/Admin | Company-wide attendance |
| **Performance Report** | Management | Trends, comparisons |
| **Exit Report** | Intern (at completion) | Full internship summary |

### How Reports Work (Python Service)

```
Node.js Backend                    Python Report Service
      │                                  │
      │  POST /reports/generate          │
      │  {                               │
      │    type: "intern_report",        │
      │    user_id: "alice_id",          │
      │    date_range: {                 │
      │      start: "2026-01-01",        │
      │      end: "2026-01-31"           │
      │    },                            │
      │    format: "pdf"                 │
      │  }                               │
      ├──────────────────────────────────►
      │                                  │
      │                                  │ 1. Fetch data from MongoDB
      │                                  │    - User details
      │                                  │    - Tasks & scores
      │                                  │    - Attendance records
      │                                  │    - Reviews & feedback
      │                                  │
      │                                  │ 2. Apply report template
      │                                  │
      │                                  │ 3. Generate PDF
      │                                  │
      │                                  │ 4. Upload to S3
      │                                  │
      │  Response:                       │
      │  {                               │
      │    success: true,                │
      │    report_url: "https://s3..."   │
      │  }                               │
      ◄──────────────────────────────────┤
```

### Report Template Structure

Every report follows a standard structure that works for any role:

```
┌─────────────────────────────────────────────────────────────────┐
│                      REPORT TEMPLATE                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  SECTION 1: HEADER                                              │
│  ───────────────────                                            │
│  • Company logo                                                 │
│  • Report title                                                 │
│  • Date range                                                   │
│  • Generated date                                               │
│                                                                 │
│  SECTION 2: SUBJECT INFO                                        │
│  ─────────────────────                                          │
│  • User name, photo                                             │
│  • Role, department                                             │
│  • Profile data (dynamic based on role)                        │
│                                                                 │
│  SECTION 3: SUMMARY METRICS                                     │
│  ──────────────────────────                                     │
│  • Key numbers (tasks completed, avg score, etc.)              │
│  • Visual: pie chart, bar chart                                │
│                                                                 │
│  SECTION 4: DETAILED DATA                                       │
│  ─────────────────────────                                      │
│  • Table of tasks with scores                                  │
│  • Attendance calendar                                         │
│  • Reviews received                                            │
│                                                                 │
│  SECTION 5: ANALYSIS                                            │
│  ────────────────────                                           │
│  • Strengths identified                                        │
│  • Areas for improvement                                       │
│  • Trend analysis                                              │
│                                                                 │
│  SECTION 6: FOOTER                                              │
│  ─────────────────                                              │
│  • Generated by system                                         │
│  • Verification code                                           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 13. Certificate System

### What Is a Certificate?

At the end of an internship, interns receive a **completion certificate** that can be verified.

### Certificate Generation Flow

```
ADMIN/MENTOR                    SYSTEM
  │                               │
  │ 1. Go to user profile         │
  │ 2. Click "Generate Certificate│
  │                               │
  ├─── Generate Request ──────────►
  │                               │
  │                               │ 3. Gather data:
  │                               │    - Intern name
  │                               │    - Company name
  │                               │    - Duration
  │                               │    - Tasks completed
  │                               │    - Overall score
  │                               │    - Mentor name
  │                               │
  │                               │ 4. Generate certificate number
  │                               │    "CERT-2026-001234"
  │                               │
  │                               │ 5. Generate verification code
  │                               │    "abc123xyz"
  │                               │
  │                               │ 6. Create PDF using template
  │                               │
  │                               │ 7. Upload to S3
  │                               │
  │                               │ 8. Save certificate record
  │                               │
  │   ◄─── Success ───────────────┤
  │       (download link)         │
```

### Certificate Verification

Anyone can verify a certificate:

```
VERIFIER                        SYSTEM
  │                                │
  │ 1. Go to verify page           │
  │    Enter code: "abc123xyz"     │
  │                                │
  ├─── Verify Request ─────────────►
  │                                │
  │                                │ 2. Look up certificate
  │                                │
  │   ◄─── Certificate Details ────┤
  │       "Valid certificate"      │
  │       Intern: Alice Johnson    │
  │       Company: Acme Corp       │
  │       Duration: 3 months       │
  │       Issued: March 2026       │
```

---

## 14. Dashboard & Widgets

### How Dashboards Work

When a user logs in, the system:
1. Reads their role's permissions
2. Shows only the widgets they're allowed to see

### Widget Categories

**Stats Widgets (Number Cards):**
| Widget | Shows | Needs Permission |
|--------|-------|------------------|
| My Score | Personal score % | intern category |
| Pending Tasks | Incomplete count | task.read |
| Team Count | Users under you | user.read (subtree) |
| Pending Reviews | Submissions to review | submission.review |

**List Widgets:**
| Widget | Shows | Needs Permission |
|--------|-------|------------------|
| My Tasks | Personal tasks | task.read (own) |
| Team Tasks | All team's tasks | task.read (subtree) |
| Pending Reviews | List for review | submission.review |
| Leave Requests | Pending approvals | leave.approve |

**Attendance Widgets:**
| Widget | Shows | Needs Permission |
|--------|-------|------------------|
| Clock In/Out | Button to clock | attendance.clock |
| Today's Attendance | Team status | attendance.read (subtree) |
| Leave Balance | My remaining | always for interns |

**Chart Widgets:**
| Widget | Shows | Needs Permission |
|--------|-------|------------------|
| Score Trend | Progress over time | report.view |
| Task Completion | Done vs assigned | task.read |
| Team Performance | Compare team | report.view (subtree) |

**Action Widgets:**
| Widget | Action | Needs Permission |
|--------|--------|------------------|
| Create Task | Quick button | task.create |
| Schedule Meeting | Quick button | meeting.schedule |
| Generate Report | Quick button | report.generate |

### Example: Intern Dashboard

```
Permissions: task.read (own), attendance.clock, attendance.read (own)

Dashboard shows:
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                      │
│  │ My Score │  │ Tasks    │  │Attendance│                      │
│  │ 85%      │  │ 3 pending│  │ 95%      │                      │
│  └──────────┘  └──────────┘  └──────────┘                      │
│                                                                 │
│  ┌─────────────────────────┐  ┌───────────────────────┐        │
│  │ My Tasks                │  │ Clock In/Out          │        │
│  │ • Build Auth API        │  │                       │        │
│  │ • Write Docs            │  │ [ Clock In ]          │        │
│  └─────────────────────────┘  └───────────────────────┘        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Example: Mentor Dashboard

```
Permissions: task.* (subtree), submission.review, attendance.read (subtree)

Dashboard shows:
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐                │
│  │Interns │  │ Tasks  │  │Reviews │  │Avg Score│               │
│  │ 5      │  │ 15     │  │ 3      │  │ 82%    │                │
│  └────────┘  └────────┘  └────────┘  └────────┘                │
│                                                                 │
│  ┌─────────────────────────┐  ┌───────────────────────┐        │
│  │ Pending Reviews         │  │ Quick Actions         │        │
│  │ • Alice - Auth API      │  │ [+ Create Task]       │        │
│  │ • Bob - Database        │  │ [📊 Reports]          │        │
│  └─────────────────────────┘  └───────────────────────┘        │
│                                                                 │
│  ┌───────────────────────────────────────────────────┐         │
│  │ Team Attendance Today                             │         │
│  │ ✓ Present: 4  ⏰ Late: 1  ✗ Absent: 0             │         │
│  └───────────────────────────────────────────────────┘         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 15. Tech Stack

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    TECH STACK (Backend Only)                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  MAIN BACKEND                      PYTHON SERVICES              │
│  ─────────────                     ───────────────              │
│  Node.js (Express)                 FastAPI                      │
│  JavaScript (NO TypeScript)        AI Review Engine             │
│  Mongoose ODM                      Report Generation (PDF)      │
│  Socket.io (WebSocket)             Email Service                │
│                                                                 │
│  ─────────────────────────────────────────────────────────────│
│                                                                 │
│  DATABASE                          FILE STORAGE                 │
│  ────────                          ────────────                 │
│  MongoDB                           AWS S3 / Cloudinary          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Service Breakdown

| Service | Language | Responsibilities |
|---------|----------|------------------|
| **API Server** | Node.js | REST APIs, WebSocket, business logic |
| **AI Service** | Python | Review submissions with AI/ML |
| **Report Service** | Python | Generate PDF reports |
| **Email Service** | Python | Render and send emails |

### Why This Stack?

| Choice | Reason |
|--------|--------|
| **Node.js** | Fast, good for real-time, team familiarity |
| **No TypeScript** | Faster development, less ceremony |
| **MongoDB** | Flexible schema for dynamic roles |
| **Python** | Better AI/ML libraries, PDF generation |

---

*Document Version: 3.0*
*Last Updated: January 13, 2026*
