# Intern Tracking System - Technical Design

> Database schema, attendance system, permissions, and email templates.

---

## Quick Navigation

| Section | Description |
|---------|-------------|
| [Attendance System](#1-attendance-system) | Tracking methods and implementation |
| [Permissions Library](#2-permissions-library) | Complete granular operations |
| [Database Schema](#3-database-schema) | PostgreSQL design for dynamic roles |
| [User & Login System](#4-user--login-system) | How users are created and authenticated |
| [Email System](#5-email-system) | Templates and sending strategy |
| [Tech Stack](#6-tech-stack) | Architecture overview |

---

## 1. Attendance System

### Industry Practices

| Method | Description | Used By |
|--------|-------------|---------|
| **Manual Clock In/Out** | User clicks button | Most common |
| **IP-Based** | Only from office IP | Enterprises |
| **Geo-Fencing** | Location-based | Field work |
| **Biometric** | Fingerprint/face | Large companies |
| **Manager Approval** | Manager marks attendance | Traditional |
| **Time Tracking** | Track active time | Remote teams |
| **Calendar Integration** | Auto from meetings | Tech companies |

### Our Implementation

We'll support **configurable methods** per company:

```
┌─────────────────────────────────────────────────────────────────┐
│ ATTENDANCE CONFIGURATION (Company Admin)                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Tracking Method:                                                │
│ ☑ Manual clock in/out                                          │
│ ☐ IP restriction (whitelist IPs)                               │
│ ☐ Geo-fencing (office location)                                │
│                                                                 │
│ Working Hours:                                                  │
│ Start: [09:00]  End: [18:00]  Timezone: [Asia/Kolkata ▼]       │
│                                                                 │
│ Grace Period:                                                   │
│ Late after: [15] minutes                                       │
│ Early leave before: [15] minutes                               │
│                                                                 │
│ Half Day Rules:                                                 │
│ Minimum hours for half day: [4] hours                          │
│ Minimum hours for full day: [8] hours                          │
│                                                                 │
│ Leave Types:                                                    │
│ ☑ Casual Leave (quota: [12] per year)                          │
│ ☑ Sick Leave (quota: [6] per year)                             │
│ ☑ Work From Home (quota: [unlimited / 4 per month])            │
│ ☐ Custom: [___________]                                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Attendance Flow

```
CLOCK IN
    │
    ├── Check restrictions (IP/Geo if enabled)
    │       │
    │       ├── Pass → Record clock in time
    │       │
    │       └── Fail → Show error, suggest WFH request
    │
    ▼
DURING DAY
    │
    ├── Break tracking (optional)
    │
    ▼
CLOCK OUT
    │
    ├── Calculate total hours
    │
    ├── Determine status:
    │       │
    │       ├── Present (≥ min hours)
    │       ├── Half Day (≥ half day hours)
    │       ├── Late (clock in after grace)
    │       └── Early Leave (clock out before time)
    │
    ▼
RECORD SAVED
```

### Leave Request Flow

```
INTERN REQUESTS LEAVE
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│ LEAVE REQUEST                                                   │
│ Type: [Casual Leave ▼]                                          │
│ From: [Jan 15, 2026]  To: [Jan 16, 2026]                       │
│ Reason: [Family function]                                       │
│ [Submit Request]                                                │
└─────────────────────────────────────────────────────────────────┘
        │
        ▼
NOTIFICATION to Approver (based on relationship)
        │
        ▼
APPROVER ACTION
        │
        ├── Approve → Leave recorded, quota updated
        │
        └── Reject → Intern notified with reason
```

### Attendance Data Points

| Field | Type | Description |
|-------|------|-------------|
| `date` | DATE | Attendance date |
| `clock_in` | TIMESTAMP | When clocked in |
| `clock_out` | TIMESTAMP | When clocked out |
| `total_hours` | DECIMAL | Calculated hours |
| `status` | ENUM | present, half_day, absent, leave, wfh |
| `late_by` | INTEGER | Minutes late |
| `early_by` | INTEGER | Minutes early |
| `clock_in_ip` | VARCHAR | IP address |
| `clock_in_location` | POINT | Lat/long if geo |
| `notes` | TEXT | Any remarks |

---

## 2. Permissions Library

### Complete Granular Operations

Every operation has a **scope**: `own` | `direct` | `subtree` | `company`

```
┌─────────────────────────────────────────────────────────────────┐
│                    PERMISSIONS LIBRARY                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ USERS                                                           │
│ ══════════════════════════════════════════════════════════════ │
│ user.create              Create new users                       │
│ user.read                View user profiles                     │
│ user.update              Edit user details                      │
│ user.delete              Remove users                           │
│ user.suspend             Suspend/activate users                 │
│ user.bulk_import         Import users via CSV                   │
│ user.reset_password      Reset user passwords                   │
│ user.assign_role         Assign/change user roles               │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ TASKS                                                           │
│ ══════════════════════════════════════════════════════════════ │
│ task.create              Create new tasks                       │
│ task.read                View tasks                             │
│ task.update              Edit task details                      │
│ task.delete              Delete tasks                           │
│ task.assign              Assign tasks to users                  │
│ task.extend_deadline     Extend task deadlines                  │
│ task.set_priority        Set task priority                      │
│ task.bulk_create         Bulk create tasks                      │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ SUBMISSIONS                                                     │
│ ══════════════════════════════════════════════════════════════ │
│ submission.create        Submit work (for interns)              │
│ submission.read          View submissions                       │
│ submission.resubmit      Resubmit work                          │
│ submission.trigger_ai    Trigger AI review manually             │
│ submission.review        Review submissions                     │
│ submission.score         Set/edit scores                        │
│ submission.override_ai   Override AI score                      │
│ submission.add_remarks   Add feedback remarks                   │
│ submission.request_resubmit  Request resubmission               │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ ATTENDANCE                                                      │
│ ══════════════════════════════════════════════════════════════ │
│ attendance.clock         Clock in/out (for self)                │
│ attendance.read          View attendance records                │
│ attendance.edit          Edit attendance records                │
│ attendance.export        Export attendance data                 │
│ attendance.configure     Configure attendance settings          │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ LEAVE                                                           │
│ ══════════════════════════════════════════════════════════════ │
│ leave.request            Request leave (for self)               │
│ leave.read               View leave requests                    │
│ leave.approve            Approve leave requests                 │
│ leave.reject             Reject leave requests                  │
│ leave.configure          Configure leave policies               │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ WORKSPACES                                                      │
│ ══════════════════════════════════════════════════════════════ │
│ workspace.create         Create workspaces                      │
│ workspace.read           View workspaces                        │
│ workspace.update         Edit workspace details                 │
│ workspace.delete         Delete workspaces                      │
│ workspace.archive        Archive workspaces                     │
│ workspace.add_member     Add members to workspace               │
│ workspace.remove_member  Remove members                         │
│ workspace.post           Post announcements/resources           │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ COMMUNICATION                                                   │
│ ══════════════════════════════════════════════════════════════ │
│ message.send_dm          Send direct messages                   │
│ message.read_dm          Read direct messages                   │
│ discussion.create        Start discussions                      │
│ discussion.read          View discussions                       │
│ discussion.moderate      Delete/edit any message                │
│ comment.create           Comment on tasks                       │
│ comment.read             View comments                          │
│ announcement.create      Create announcements                   │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ MEETINGS                                                        │
│ ══════════════════════════════════════════════════════════════ │
│ meeting.schedule         Schedule meetings                      │
│ meeting.join             Join meetings                          │
│ meeting.invite           Invite participants                    │
│ meeting.record           Record meetings                        │
│ meeting.manage_lobby     Admit/reject from lobby                │
│ meeting.view_history     View meeting history                   │
│ meeting.delete           Cancel/delete meetings                 │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ REPORTS                                                         │
│ ══════════════════════════════════════════════════════════════ │
│ report.view_own          View own reports                       │
│ report.view              View reports                           │
│ report.generate          Generate reports                       │
│ report.export_pdf        Export as PDF                          │
│ report.export_csv        Export as CSV                          │
│ report.schedule          Schedule automated reports             │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ CERTIFICATES                                                    │
│ ══════════════════════════════════════════════════════════════ │
│ certificate.generate     Generate certificates                  │
│ certificate.view         View certificates                      │
│ certificate.design       Design certificate templates           │
│ certificate.verify       Verify certificate authenticity        │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ CONFIGURATION                                                   │
│ ══════════════════════════════════════════════════════════════ │
│ config.profile           Edit own profile                       │
│ config.company           Edit company settings                  │
│ config.roles             Manage role definitions                │
│ config.permissions       Manage permissions                     │
│ config.playground        Access role playground                 │
│ config.branding          Edit company branding                  │
│ config.integrations      Manage integrations                    │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ AUDIT                                                           │
│ ══════════════════════════════════════════════════════════════ │
│ audit.view               View audit logs                        │
│ audit.export             Export audit logs                      │
│                                                                 │
│ ══════════════════════════════════════════════════════════════ │
│ GITHUB                                                          │
│ ══════════════════════════════════════════════════════════════ │
│ github.connect           Connect own GitHub                     │
│ github.view              View GitHub stats                      │
│ github.disconnect        Disconnect GitHub                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Permission Assignment Example

```
Role: "Technical Mentor"

Permissions:
┌─────────────────────────────┬───────────────────────────────────┐
│ Permission                  │ Scope                              │
├─────────────────────────────┼───────────────────────────────────┤
│ user.read                   │ subtree                           │
│ task.create                 │ subtree                           │
│ task.read                   │ subtree                           │
│ task.update                 │ subtree                           │
│ task.assign                 │ subtree                           │
│ submission.read             │ subtree                           │
│ submission.review           │ subtree                           │
│ submission.score            │ subtree                           │
│ attendance.read             │ subtree                           │
│ leave.read                  │ subtree                           │
│ leave.approve               │ direct                            │
│ meeting.schedule            │ subtree                           │
│ meeting.invite              │ subtree                           │
│ report.view                 │ subtree                           │
│ report.generate             │ subtree                           │
│ config.profile              │ own                               │
└─────────────────────────────┴───────────────────────────────────┘
```

---

## 3. Database Schema

### Why MongoDB for This Project?

```
┌─────────────────────────────────────────────────────────────────┐
│            WHY MONGODB IS BETTER FOR OUR USE CASE               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ✅ DYNAMIC ROLES                                               │
│     • Admin creates custom roles with varying fields            │
│     • Each role has different profile fields                    │
│     • MongoDB's flexible schema = perfect fit                   │
│                                                                 │
│  ✅ FLEXIBLE PERMISSIONS                                        │
│     • Permissions are arrays of objects                         │
│     • Easy to add/remove without schema changes                │
│     • Natural fit for [{permission, scope}] structure          │
│                                                                 │
│  ✅ EMBEDDED DOCUMENTS                                          │
│     • Role + permissions + profile fields in one document      │
│     • Faster reads, fewer lookups                               │
│     • Natural hierarchical structure                            │
│                                                                 │
│  ✅ HIGH VOLUME WRITES                                          │
│     • Audit logs, activity feeds                                │
│     • Real-time updates                                         │
│     • Excellent write performance                               │
│                                                                 │
│  ✅ FUTURE FLEXIBILITY                                          │
│     • Add new fields without migrations                         │
│     • Easy to evolve schema as product grows                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Database Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    DATABASE ARCHITECTURE                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  PRIMARY DATABASE: MongoDB                                      │
│  ─────────────────────────                                      │
│  • All core entities                                           │
│  • Dynamic roles and permissions                               │
│  • Users, tasks, submissions                                   │
│  • Attendance, meetings                                        │
│  • Audit logs, chat messages                                   │
│  • Activity feeds                                              │
│                                                                 │
│  FILE STORAGE: S3 / Cloud Storage                              │
│  ────────────────────────────                                   │
│  • Uploaded files                                              │
│  • Profile photos                                              │
│  • Certificates                                                │
│  • Meeting recordings                                          │
│                                                                 │
│  NOTE: Redis caching deferred for later if needed              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

### MongoDB Collections

#### 1. companies

```javascript
// Collection: companies
{
  _id: ObjectId,
  name: "Acme Corp",
  slug: "acme-corp",                    // subdomain identifier
  logo_url: "https://...",
  timezone: "Asia/Kolkata",
  
  // Flexible settings - can add any setting without schema change
  settings: {
    branding: {
      primary_color: "#4F46E5",
      logo_dark: "https://..."
    },
    features: {
      github_integration: true,
      video_calls: true
    }
  },
  
  // Attendance configuration embedded
  attendance_config: {
    enabled: true,
    methods: ["manual"],                // ["manual", "ip", "geo"]
    work_start: "09:00",
    work_end: "18:00",
    grace_minutes: 15,
    min_hours_full: 8,
    min_hours_half: 4,
    allowed_ips: ["192.168.1.0/24"],
    geo_fence: {
      enabled: false,
      lat: 12.9716,
      lng: 77.5946,
      radius_meters: 500
    }
  },
  
  // Leave types embedded (company-specific)
  leave_types: [
    { name: "Casual", quota: 12, is_paid: true },
    { name: "Sick", quota: 6, is_paid: true },
    { name: "WFH", quota: null, is_paid: true }  // null = unlimited
  ],
  
  // Task categories (can be customized)
  task_categories: [
    { name: "Development", icon: "💻", color: "#3B82F6" },
    { name: "Documentation", icon: "📝", color: "#10B981" },
    { name: "Research", icon: "🔍", color: "#8B5CF6" },
    { name: "Design", icon: "🎨", color: "#F59E0B" },
    { name: "Testing", icon: "🧪", color: "#EF4444" },
    { name: "Data", icon: "📊", color: "#06B6D4" },
    { name: "DevOps", icon: "⚙️", color: "#6366F1" },
    { name: "Presentation", icon: "📣", color: "#EC4899" },
    { name: "Other", icon: "📁", color: "#6B7280" }
  ],
  
  status: "active",                     // active, suspended
  created_at: ISODate,
  updated_at: ISODate
}
```

---

#### 2. roles

```javascript
// Collection: roles
{
  _id: ObjectId,
  company_id: ObjectId,                 // ref: companies
  
  name: "technical_mentor",             // system name (lowercase, no spaces)
  display_name: "Technical Mentor",
  description: "Guides interns on technical tasks",
  category: "staff",                    // "admin", "staff", "intern"
  
  icon: "👨‍💻",
  color: "#4F46E5",
  
  is_system: false,                     // built-in roles = true
  is_deletable: true,
  
  // Permissions embedded - no separate table needed!
  permissions: [
    { permission: "user.read", scope: "subtree" },
    { permission: "task.create", scope: "subtree" },
    { permission: "task.read", scope: "subtree" },
    { permission: "task.update", scope: "subtree" },
    { permission: "task.assign", scope: "subtree" },
    { permission: "submission.read", scope: "subtree" },
    { permission: "submission.review", scope: "subtree" },
    { permission: "submission.score", scope: "subtree" },
    { permission: "attendance.read", scope: "subtree" },
    { permission: "leave.approve", scope: "direct" },
    { permission: "meeting.schedule", scope: "subtree" },
    { permission: "report.view", scope: "subtree" },
    { permission: "report.generate", scope: "subtree" },
    { permission: "config.profile", scope: "own" }
  ],
  
  // Custom profile fields for this role
  profile_fields: [
    { 
      name: "department", 
      display_name: "Department",
      type: "dropdown",
      required: true,
      options: ["Engineering", "Design", "QA", "Data"]
    },
    { 
      name: "experience_years", 
      display_name: "Years of Experience",
      type: "number",
      required: false
    },
    {
      name: "specialization",
      display_name: "Specialization",
      type: "text",
      required: false
    }
  ],
  
  // Which roles can this role connect to?
  allowed_relationships: [
    { to_role: ObjectId, type: "mentors" },
    { to_role: ObjectId, type: "reviews" }
  ],
  
  created_at: ISODate,
  updated_at: ISODate
}

// Index
{ company_id: 1, name: 1 } // unique
```

---

#### 3. role_relationships

```javascript
// Collection: role_relationships
// Defines the ORG STRUCTURE (which roles can connect to which)
{
  _id: ObjectId,
  company_id: ObjectId,
  
  from_role_id: ObjectId,               // e.g., "Mentor" role
  to_role_id: ObjectId,                 // e.g., "Intern" role
  relationship_type: "mentors",         // "manages", "mentors", "reviews", "supports", "oversees"
  
  created_at: ISODate
}

// Index
{ company_id: 1 }
{ from_role_id: 1, to_role_id: 1, relationship_type: 1 } // unique
```

---

#### 4. users

```javascript
// Collection: users
{
  _id: ObjectId,
  company_id: ObjectId,
  role_id: ObjectId,                    // ref: roles
  
  email: "john@acme.com",
  password_hash: "$2b$12$...",
  name: "John Smith",
  profile_photo: "https://...",
  
  // Dynamic profile data (based on role's profile_fields)
  profile_data: {
    department: "Engineering",
    experience_years: 5,
    specialization: "Backend Development"
  },
  
  // GitHub integration (optional)
  github: {
    connected: true,
    username: "johnsmith",
    access_token: "encrypted_token",
    connected_at: ISODate
  },
  
  status: "active",                     // active, suspended, exited
  
  // For first login flow
  temp_password: true,                  // true if needs to change password
  
  last_login: ISODate,
  created_by: ObjectId,                 // who created this user
  created_at: ISODate,
  updated_at: ISODate
}

// Indexes
{ company_id: 1, email: 1 } // unique
{ company_id: 1, role_id: 1 }
{ company_id: 1, status: 1 }
```

---

#### 5. user_connections

```javascript
// Collection: user_connections
// Actual relationships between USERS (not roles)
{
  _id: ObjectId,
  company_id: ObjectId,
  
  from_user_id: ObjectId,               // e.g., John (Mentor)
  to_user_id: ObjectId,                 // e.g., Alice (Intern)
  relationship_type: "mentors",
  
  is_active: true,
  created_at: ISODate,
  deactivated_at: ISODate               // when relationship ended
}

// Indexes
{ company_id: 1, from_user_id: 1 }
{ company_id: 1, to_user_id: 1 }
{ from_user_id: 1, to_user_id: 1, relationship_type: 1 } // unique
```

---

#### 6. workspaces

```javascript
// Collection: workspaces
{
  _id: ObjectId,
  company_id: ObjectId,
  
  name: "Backend Development Q1 2026",
  description: "All backend tasks for Q1",
  
  created_by: ObjectId,                 // user who created
  
  // Members embedded for quick access
  members: [
    { user_id: ObjectId, role: "owner", joined_at: ISODate },
    { user_id: ObjectId, role: "member", joined_at: ISODate }
  ],
  
  status: "active",                     // active, archived
  created_at: ISODate,
  updated_at: ISODate
}

// Indexes
{ company_id: 1 }
{ "members.user_id": 1 }
```

---

#### 7. tasks

```javascript
// Collection: tasks
{
  _id: ObjectId,
  company_id: ObjectId,
  workspace_id: ObjectId,
  
  title: "Build User Authentication API",
  description: "Implement JWT-based authentication...",  // rich text
  
  category: "Development",              // from company's task_categories
  
  deadline: ISODate,
  points: 10,
  
  submission_type: "mixed",             // "file", "github", "url", "text", "mixed"
  
  // What to submit (for mixed type)
  submission_requirements: {
    allow_files: true,
    allow_github: true,
    allow_url: false,
    allow_text: true,
    max_files: 5
  },
  
  // Assignees embedded
  assignees: [
    { 
      user_id: ObjectId, 
      status: "in_progress",            // pending, in_progress, submitted, late_submitted, under_review, approved, revision_needed
      assigned_at: ISODate 
    }
  ],
  
  status: "active",                     // active, closed
  
  created_by: ObjectId,
  created_at: ISODate,
  updated_at: ISODate
}

// Indexes
{ company_id: 1, workspace_id: 1 }
{ "assignees.user_id": 1 }
{ deadline: 1 }
```

---

#### 8. submissions

```javascript
// Collection: submissions
{
  _id: ObjectId,
  company_id: ObjectId,
  task_id: ObjectId,
  user_id: ObjectId,
  
  // Flexible submission data
  submission_data: {
    files: [
      { name: "auth.zip", url: "https://s3...", size: 1024000 }
    ],
    github_links: [
      { type: "pr", url: "https://github.com/...", title: "Add auth API" }
    ],
    text: "I completed the task by implementing..."
  },
  
  is_late: false,
  version: 1,                           // for resubmissions
  
  submitted_at: ISODate,
  
  // Reviews embedded
  reviews: [
    {
      type: "ai",
      score: 8,
      max_score: 10,
      remarks: "Good implementation...",
      detailed_review: {
        strengths: ["Clean code", "Good error handling"],
        improvements: ["Add rate limiting", "Missing refresh tokens"],
        metrics: { commits: 12, lines_added: 450 }
      },
      reviewed_at: ISODate
    },
    {
      type: "human",
      reviewer_id: ObjectId,
      score: 8.5,
      max_score: 10,
      remarks: "Excellent work, just minor improvements needed",
      reviewed_at: ISODate
    }
  ],
  
  // Final status after review
  final_status: "approved",             // approved, revision_needed
  final_score: 8.5,
  
  created_at: ISODate
}

// Indexes
{ task_id: 1, user_id: 1 }
{ company_id: 1, user_id: 1 }
```

---

#### 9. attendance

```javascript
// Collection: attendance
{
  _id: ObjectId,
  company_id: ObjectId,
  user_id: ObjectId,
  
  date: ISODate,                        // just the date part
  
  clock_in: ISODate,
  clock_out: ISODate,
  
  total_hours: 8.5,                     // calculated
  
  status: "present",                    // present, half_day, absent, leave, wfh
  
  late_by_minutes: 0,
  early_by_minutes: 0,
  
  // Tracking metadata
  clock_in_info: {
    ip: "192.168.1.100",
    location: { lat: 12.9716, lng: 77.5946 },
    method: "manual"                    // manual, ip_verified, geo_verified
  },
  
  notes: "",
  
  created_at: ISODate,
  updated_at: ISODate
}

// Indexes
{ company_id: 1, user_id: 1, date: 1 } // unique
{ company_id: 1, date: 1 }
```

---

#### 10. leave_requests

```javascript
// Collection: leave_requests
{
  _id: ObjectId,
  company_id: ObjectId,
  user_id: ObjectId,
  
  leave_type: "Casual",                 // from company's leave_types
  from_date: ISODate,
  to_date: ISODate,
  days: 2,                              // calculated
  
  reason: "Family function",
  
  status: "pending",                    // pending, approved, rejected
  
  approver_id: ObjectId,
  approved_at: ISODate,
  rejection_reason: null,
  
  created_at: ISODate
}

// Indexes
{ company_id: 1, user_id: 1 }
{ company_id: 1, status: 1 }
```

---

#### 11. leave_balance

```javascript
// Collection: leave_balance
{
  _id: ObjectId,
  company_id: ObjectId,
  user_id: ObjectId,
  year: 2026,
  
  balances: [
    { type: "Casual", total: 12, used: 2, remaining: 10 },
    { type: "Sick", total: 6, used: 0, remaining: 6 }
  ],
  
  updated_at: ISODate
}

// Indexes
{ company_id: 1, user_id: 1, year: 1 } // unique
```

---

#### 12. meetings

```javascript
// Collection: meetings
{
  _id: ObjectId,
  company_id: ObjectId,
  
  title: "Weekly 1:1 with Alice",
  description: "Weekly catch-up meeting",
  
  meeting_code: "MET-20260115-A1B2",    // unique identifier
  passkey: "1234",
  
  scheduled_at: ISODate,
  duration_minutes: 60,
  
  host_id: ObjectId,
  
  // Participants embedded
  participants: [
    { 
      user_id: ObjectId, 
      status: "accepted",               // invited, accepted, declined, joined
      invited_at: ISODate,
      joined_at: ISODate,
      left_at: ISODate
    }
  ],
  
  status: "scheduled",                  // scheduled, ongoing, ended, cancelled
  
  recording_url: null,
  
  created_at: ISODate
}

// Indexes
{ company_id: 1, scheduled_at: 1 }
{ meeting_code: 1 } // unique
{ "participants.user_id": 1 }
```

---

#### 13. messages (DMs & Discussions)

```javascript
// Collection: messages
{
  _id: ObjectId,
  company_id: ObjectId,
  
  // Context: where is this message?
  context_type: "dm",                   // "dm", "task_comment", "workspace_discussion"
  context_id: ObjectId,                 // user_id for DM, task_id, workspace_id
  
  sender_id: ObjectId,
  
  content: "Hey, can you review my code?",
  
  // For rich messages
  attachments: [
    { type: "image", url: "https://...", name: "screenshot.png" }
  ],
  
  // For replies
  reply_to: ObjectId,                   // message_id if reply
  
  is_edited: false,
  edited_at: null,
  
  created_at: ISODate
}

// Indexes
{ context_type: 1, context_id: 1, created_at: 1 }
{ company_id: 1, sender_id: 1 }
```

---

#### 14. announcements

```javascript
// Collection: announcements
{
  _id: ObjectId,
  company_id: ObjectId,
  workspace_id: ObjectId,
  
  type: "announcement",                 // "announcement", "resource"
  
  title: "New Project Starting Monday",
  content: "We will be starting...",    // rich text
  
  attachments: [
    { name: "Guidelines.pdf", url: "https://...", size: 2048000 }
  ],
  
  posted_by: ObjectId,
  
  // Comments embedded (for simple cases)
  comments: [
    {
      _id: ObjectId,
      user_id: ObjectId,
      content: "Looking forward to it!",
      created_at: ISODate
    }
  ],
  
  created_at: ISODate,
  updated_at: ISODate
}

// Indexes
{ company_id: 1, workspace_id: 1, created_at: -1 }
```

---

#### 15. certificates

```javascript
// Collection: certificates
{
  _id: ObjectId,
  company_id: ObjectId,
  user_id: ObjectId,
  
  certificate_no: "CERT-2026-001234",   // unique
  issued_date: ISODate,
  
  // All certificate data for PDF generation
  data: {
    intern_name: "Alice Johnson",
    company_name: "Acme Corp",
    duration: "Jan 2026 - Mar 2026",
    tasks_completed: 25,
    overall_score: 87,
    mentor_name: "John Smith",
    department: "Engineering"
  },
  
  file_url: "https://s3.../cert.pdf",
  
  // For verification
  verification_code: "abc123xyz",
  
  created_at: ISODate
}

// Indexes
{ certificate_no: 1 } // unique
{ company_id: 1, user_id: 1 }
{ verification_code: 1 }
```

---

#### 16. audit_logs

```javascript
// Collection: audit_logs
{
  _id: ObjectId,
  company_id: ObjectId,
  
  actor_id: ObjectId,                   // who did it
  actor_email: "john@acme.com",         // denormalized for quick display
  
  action: "task.create",
  
  target_type: "task",
  target_id: ObjectId,
  target_name: "Build Auth API",        // denormalized
  
  details: {
    before: null,                       // for updates
    after: { title: "Build Auth API", points: 10 }
  },
  
  ip_address: "192.168.1.100",
  user_agent: "Mozilla/5.0...",
  
  created_at: ISODate
}

// Indexes
{ company_id: 1, created_at: -1 }
{ company_id: 1, actor_id: 1 }
{ company_id: 1, action: 1 }

// TTL index (auto-delete after 1 year)
{ created_at: 1 }, { expireAfterSeconds: 31536000 }
```

---

#### 17. email_logs

```javascript
// Collection: email_logs
{
  _id: ObjectId,
  company_id: ObjectId,
  
  to_email: "alice@acme.com",
  template: "task_assigned",
  subject: "New Task Assigned: Build Auth API",
  
  // Template variables used
  variables: {
    intern_name: "Alice",
    task_title: "Build Auth API",
    deadline: "Jan 15, 2026"
  },
  
  status: "sent",                       // pending, sent, failed
  sent_at: ISODate,
  error: null,
  
  created_at: ISODate
}

// Indexes
{ company_id: 1, created_at: -1 }
{ status: 1, created_at: 1 }
```

---

### Collection Summary

| Collection | Purpose | Key Features |
|------------|---------|--------------|
| `companies` | Tenants | Settings, attendance config embedded |
| `roles` | Role definitions | Permissions & profile fields embedded |
| `role_relationships` | Org structure | Which roles connect to which |
| `users` | All users | Dynamic profile_data, GitHub info |
| `user_connections` | User relationships | Who reports to whom |
| `workspaces` | Project containers | Members embedded |
| `tasks` | Work items | Assignees embedded |
| `submissions` | Task submissions | Reviews embedded |
| `attendance` | Clock in/out | Daily records |
| `leave_requests` | Leave applications | Approval workflow |
| `leave_balance` | Leave quotas | Per user per year |
| `meetings` | Video calls | Participants embedded |
| `messages` | DMs & comments | Flexible context |
| `announcements` | Workspace posts | Comments embedded |
| `certificates` | Completion certs | Verification code |
| `audit_logs` | Activity tracking | TTL for auto-cleanup |
| `email_logs` | Email history | Template tracking |

### Dynamic Roles Explanation

```
┌─────────────────────────────────────────────────────────────────┐
│                HOW DYNAMIC ROLES WORK                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ 1. ROLE DEFINITIONS                                            │
│    ────────────────                                             │
│    Admin creates roles in playground:                          │
│    • "Technical Mentor" (category: staff)                      │
│    • "QA Reviewer" (category: staff)                           │
│    • "Summer Intern" (category: intern)                        │
│                                                                 │
│    Stored in: role_definitions                                 │
│                                                                 │
│ 2. ROLE RELATIONSHIPS                                          │
│    ──────────────────                                           │
│    Admin connects roles:                                        │
│    • Technical Mentor → mentors → Summer Intern                │
│    • QA Reviewer → reviews → Summer Intern                     │
│                                                                 │
│    Stored in: role_relationships                               │
│                                                                 │
│ 3. PERMISSIONS                                                  │
│    ───────────                                                  │
│    Each role gets specific permissions:                        │
│    • Technical Mentor: task.create (subtree)                   │
│    • QA Reviewer: submission.review (subtree)                  │
│                                                                 │
│    Stored in: role_permissions                                 │
│                                                                 │
│ 4. USER ASSIGNMENT                                              │
│    ────────────────                                             │
│    Users are assigned to roles:                                │
│    • John → Technical Mentor                                   │
│    • Alice → Summer Intern                                     │
│                                                                 │
│    Stored in: users (role_id)                                  │
│                                                                 │
│ 5. USER CONNECTIONS                                             │
│    ────────────────                                             │
│    Actual relationships between users:                         │
│    • John → mentors → Alice                                    │
│                                                                 │
│    Stored in: user_connections                                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 4. User & Login System

### Answering: "When admin creates role, is new user created?"

**NO!** Creating a role ≠ Creating a user.

```
┌─────────────────────────────────────────────────────────────────┐
│              ROLE vs USER - CLARIFICATION                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  STEP 1: Admin creates ROLE DEFINITION                         │
│  ─────────────────────────────────────                          │
│  "Technical Mentor" with:                                      │
│  • Permissions: task.create, submission.review, etc.           │
│  • Profile fields: department, experience                      │
│  • Relationships: can mentor interns                           │
│                                                                 │
│  → This is just a TEMPLATE. No user created.                   │
│                                                                 │
│  STEP 2: Admin creates ACTUAL USER                             │
│  ────────────────────────────────                               │
│  "John Smith" with:                                            │
│  • Email: john@company.com                                     │
│  • Role: Technical Mentor (selected from dropdown)             │
│  • Reports to: Lead X (connection)                             │
│                                                                 │
│  → NOW a user is created with credentials.                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### User Creation Flow

```
ADMIN CREATES USER
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│ CREATE NEW USER                                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Email: [john@company.com]                                       │
│ Name: [John Smith]                                              │
│ Role: [Technical Mentor ▼]  ← dropdown from role_definitions   │
│ Reports To: [Lead X ▼]      ← creates user_connection          │
│                                                                 │
│ ─── Role-specific fields appear based on selection ───         │
│                                                                 │
│ Department: [Engineering]   (defined in role_profile_fields)   │
│ Experience: [5 years]                                          │
│                                                                 │
│ [Create User]                                                   │
└─────────────────────────────────────────────────────────────────┘
        │
        ▼
SYSTEM DOES:
1. Creates user record
2. Generates temporary password
3. Creates user_connection (John → mentors → under Lead X)
4. Stores profile data
5. Sends welcome email with credentials

        │
        ▼
USER RECEIVES EMAIL
"Welcome to [Company]! Login at portal.com with:
Email: john@company.com
Temporary Password: xxxxxx
(Change on first login)"
```

### Login Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                       LOGIN FLOW                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  STEP 1: User visits portal                                    │
│  ──────────────────────────                                     │
│  portal.company.com  OR  app.ourplatform.com/company-slug      │
│                                                                 │
│  STEP 2: Enter credentials                                     │
│  ────────────────────────                                       │
│  Email: [john@company.com]                                      │
│  Password: [••••••••]                                           │
│  [Login]                                                        │
│                                                                 │
│  STEP 3: System validates                                      │
│  ────────────────────────                                       │
│  • Check email exists in company                               │
│  • Verify password hash                                        │
│  • Check user status (active/suspended)                        │
│                                                                 │
│  STEP 4: Generate session                                      │
│  ────────────────────────                                       │
│  • Create JWT token                                            │
│  • Store session in Redis                                      │
│  • Return token to client                                      │
│                                                                 │
│  STEP 5: Load dashboard                                        │
│  ─────────────────────                                          │
│  • Fetch user's role                                           │
│  • Fetch permissions                                           │
│  • Render appropriate dashboard                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### First Login (Temporary Password)

```
┌─────────────────────────────────────────────────────────────────┐
│ WELCOME! Please set your password.                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ New Password: [••••••••]                                        │
│ Confirm Password: [••••••••]                                    │
│                                                                 │
│ Password must have:                                            │
│ ☑ At least 8 characters                                        │
│ ☑ One uppercase letter                                         │
│ ☐ One number                                                   │
│ ☐ One special character                                        │
│                                                                 │
│ [Set Password & Continue]                                       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 5. Email System

### Email Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    EMAIL SYSTEM                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  SENDING:                                                       │
│  ────────                                                       │
│  • All emails sent from: noreply@ourplatform.com               │
│  • OR company's custom domain (if configured)                  │
│                                                                 │
│  PROVIDER:                                                      │
│  ─────────                                                      │
│  • Primary: AWS SES / SendGrid / Resend                        │
│  • Fallback: Secondary provider                                │
│                                                                 │
│  PROCESSING:                                                    │
│  ───────────                                                    │
│  • Python service handles email rendering                      │
│  • Templates stored as Jinja2 / HTML                           │
│  • Queue-based sending (for reliability)                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Email Templates

#### 1. Welcome Email (New User)

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Welcome to {company_name}! Your account is ready      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {user_name},                                                │
│                                                                 │
│ Welcome to {company_name}'s intern tracking portal!            │
│                                                                 │
│ Your account has been created. Here are your login details:   │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Login URL: {portal_url}                 │                    │
│ │ Email: {user_email}                     │                    │
│ │ Temporary Password: {temp_password}     │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ Please change your password upon first login.                  │
│                                                                 │
│ [Login Now] ← Button                                           │
│                                                                 │
│ If you have any questions, contact {admin_email}.              │
│                                                                 │
│ Best,                                                          │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 2. Password Reset

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Reset Your Password - {company_name}                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {user_name},                                                │
│                                                                 │
│ We received a request to reset your password.                  │
│                                                                 │
│ Click the button below to set a new password:                  │
│                                                                 │
│ [Reset Password] ← Button (link expires in 1 hour)             │
│                                                                 │
│ If you didn't request this, ignore this email.                 │
│                                                                 │
│ Best,                                                          │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 3. Task Assigned

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: New Task Assigned: {task_title}                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {intern_name},                                              │
│                                                                 │
│ You've been assigned a new task:                               │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Task: {task_title}                      │                    │
│ │ Category: {task_category}               │                    │
│ │ Deadline: {deadline}                    │                    │
│ │ Points: {points}                        │                    │
│ │ Assigned by: {assigner_name}            │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ [View Task] ← Button                                           │
│                                                                 │
│ Good luck!                                                     │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 4. Task Deadline Reminder

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: ⏰ Reminder: {task_title} due in {time_left}           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {intern_name},                                              │
│                                                                 │
│ Just a reminder that your task is due soon:                    │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Task: {task_title}                      │                    │
│ │ Due: {deadline} ({time_left})           │                    │
│ │ Status: {current_status}                │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ [Submit Work] ← Button                                         │
│                                                                 │
│ Don't forget to submit on time!                                │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 5. Task Reviewed

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Your submission for {task_title} has been reviewed     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {intern_name},                                              │
│                                                                 │
│ Your submission has been reviewed:                             │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Task: {task_title}                      │                    │
│ │ Score: {score}/{max_score}              │                    │
│ │ Status: {approved/revision_needed}      │                    │
│ │ Reviewer: {reviewer_name}               │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ Feedback:                                                      │
│ "{feedback_summary}"                                           │
│                                                                 │
│ [View Full Feedback] ← Button                                  │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 6. Leave Request (For Approver)

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Leave Request from {intern_name}                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {approver_name},                                            │
│                                                                 │
│ {intern_name} has requested leave:                             │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Type: {leave_type}                      │                    │
│ │ From: {from_date}                       │                    │
│ │ To: {to_date}                           │                    │
│ │ Reason: {reason}                        │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ [Approve] [Reject] ← Buttons                                   │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 7. Leave Status Update

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Leave Request {approved/rejected}                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {intern_name},                                              │
│                                                                 │
│ Your leave request has been {approved/rejected}:               │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Type: {leave_type}                      │                    │
│ │ From: {from_date} To: {to_date}         │                    │
│ │ Status: {approved/rejected}             │                    │
│ │ {if rejected: Reason: {rejection_reason}} │                  │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 8. Meeting Invitation

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Meeting Invite: {meeting_title}                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {user_name},                                                │
│                                                                 │
│ You've been invited to a meeting:                              │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Title: {meeting_title}                  │                    │
│ │ Date: {date}                            │                    │
│ │ Time: {time} ({timezone})               │                    │
│ │ Duration: {duration} minutes            │                    │
│ │ Host: {host_name}                       │                    │
│ │ Meeting ID: {meeting_code}              │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ [Accept] [Decline] ← Buttons                                   │
│                                                                 │
│ Or join via: {join_link}                                       │
│ Passkey: {passkey}                                             │
│                                                                 │
│ [Add to Calendar] ← .ics download                              │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 9. Meeting Reminder

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: ⏰ Meeting in {time_left}: {meeting_title}             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {user_name},                                                │
│                                                                 │
│ Reminder: Your meeting starts soon!                            │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Title: {meeting_title}                  │                    │
│ │ Starts in: {time_left}                  │                    │
│ │ Time: {time}                            │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ [Join Meeting] ← Button                                        │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 10. Internship Completion

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Congratulations! You've completed your internship 🎉   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {intern_name},                                              │
│                                                                 │
│ Congratulations on completing your internship at               │
│ {company_name}!                                                │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │ Duration: {start_date} - {end_date}     │                    │
│ │ Tasks Completed: {task_count}           │                    │
│ │ Overall Score: {overall_score}%         │                    │
│ │ Attendance: {attendance_percentage}%    │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ Your certificate is ready for download:                        │
│                                                                 │
│ [Download Certificate] ← Button                                │
│                                                                 │
│ We wish you the best in your future endeavors!                 │
│                                                                 │
│ Best regards,                                                  │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 11. Weekly Progress Summary (Auto-generated)

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Weekly Progress Summary - {week_start} to {week_end}   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {user_name},                                                │
│                                                                 │
│ Here's your weekly summary:                                    │
│                                                                 │
│ TASKS                                                          │
│ • Completed: {completed_count}                                 │
│ • In Progress: {in_progress_count}                             │
│ • Pending Review: {pending_review_count}                       │
│                                                                 │
│ ATTENDANCE                                                      │
│ • Days Present: {present_days}/5                               │
│ • Total Hours: {total_hours}                                   │
│                                                                 │
│ SCORE                                                           │
│ • This Week: {week_score}                                      │
│ • Overall: {overall_score}                                     │
│                                                                 │
│ [View Full Dashboard] ← Button                                 │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 12. Account Suspended

```
┌─────────────────────────────────────────────────────────────────┐
│ Subject: Your account has been suspended                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Hi {user_name},                                                │
│                                                                 │
│ Your account at {company_name} has been suspended.             │
│                                                                 │
│ If you believe this is an error, please contact your admin.   │
│                                                                 │
│ Contact: {admin_email}                                         │
│                                                                 │
│ {company_name} Team                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Email Template Summary

| # | Template | Trigger | Recipient |
|---|----------|---------|-----------|
| 1 | Welcome | User created | New user |
| 2 | Password Reset | Forgot password | User |
| 3 | Task Assigned | Task created | Assignees |
| 4 | Deadline Reminder | 24h before deadline | Assignees |
| 5 | Task Reviewed | Review complete | Intern |
| 6 | Leave Request | Leave submitted | Approver |
| 7 | Leave Status | Approved/rejected | Requester |
| 8 | Meeting Invite | Meeting created | Participants |
| 9 | Meeting Reminder | 15min before | Participants |
| 10 | Internship Complete | Exit processed | Intern |
| 11 | Weekly Summary | Every Monday | All users |
| 12 | Account Suspended | Account suspended | User |

---

## 6. Tech Stack

### Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    TECH STACK                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  FRONTEND                      BACKEND                         │
│  ────────                      ───────                         │
│  Next.js (React)               Node.js (Express/Fastify)       │
│  TypeScript                    TypeScript                      │
│  TailwindCSS                   Mongoose ODM                    │
│  Socket.io Client              Socket.io                       │
│                                                                 │
│  ─────────────────────────────────────────────────────────────│
│                                                                 │
│  PYTHON SERVICES               DATABASE                        │
│  ───────────────               ────────                        │
│  FastAPI                       MongoDB (Primary)               │
│  AI Review Engine              S3/CloudStorage (Files)         │
│  Email Service                                                 │
│  Report Generation             (Redis deferred for later)      │
│                                                                 │
│  ─────────────────────────────────────────────────────────────│
│                                                                 │
│  INFRASTRUCTURE                                                 │
│  ──────────────                                                 │
│  Docker                        Nginx                           │
│  AWS/GCP                       GitHub Actions (CI/CD)          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Service Breakdown

| Service | Language | Purpose |
|---------|----------|---------|
| **API Server** | Node.js | Main backend, REST APIs, WebSocket |
| **AI Service** | Python | AI reviews, report generation |
| **Email Service** | Python | Email rendering and sending |
| **Video Service** | Node.js | WebRTC signaling for meetings |

### Database Choice Rationale

| Requirement | Why MongoDB Fits |
|-------------|------------------|
| **Dynamic Roles** | Flexible schema for custom fields |
| **Embedded Permissions** | Natural array storage |
| **Varying Profile Fields** | No schema migrations needed |
| **Audit Logs** | High write throughput |
| **Real-time Features** | Change streams for notifications |
| **Future Flexibility** | Easy to add new fields |

---

*Document Version: 1.1*
*Last Updated: January 12, 2026*

