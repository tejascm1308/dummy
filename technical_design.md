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

### Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    DATABASE ARCHITECTURE                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  PRIMARY DATABASE: PostgreSQL                                   │
│  ────────────────────────────────                               │
│  • Core entities (users, roles, companies)                     │
│  • Relationships                                               │
│  • Tasks & submissions                                         │
│  • Attendance                                                  │
│  • Strong consistency                                          │
│                                                                 │
│  SECONDARY (Optional): MongoDB                                  │
│  ──────────────────────────────                                 │
│  • Audit logs (high volume)                                    │
│  • Chat messages (flexible schema)                             │
│  • Activity feeds                                              │
│                                                                 │
│  CACHE: Redis                                                   │
│  ────────────                                                   │
│  • Session management                                          │
│  • Real-time presence                                          │
│  • Rate limiting                                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### PostgreSQL Schema

#### Core Tables

```sql
-- ============================================================
-- COMPANIES (Tenants)
-- ============================================================
CREATE TABLE companies (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            VARCHAR(255) NOT NULL,
    slug            VARCHAR(100) UNIQUE NOT NULL,  -- subdomain
    logo_url        TEXT,
    timezone        VARCHAR(50) DEFAULT 'UTC',
    settings        JSONB DEFAULT '{}',   -- flexible settings
    status          VARCHAR(20) DEFAULT 'active',  -- active, suspended
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- ROLE DEFINITIONS (Dynamic roles per company)
-- ============================================================
CREATE TABLE role_definitions (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id) ON DELETE CASCADE,
    name            VARCHAR(100) NOT NULL,
    display_name    VARCHAR(100) NOT NULL,
    description     TEXT,
    category        VARCHAR(20) NOT NULL,  -- 'admin', 'staff', 'intern'
    icon            VARCHAR(50),
    color           VARCHAR(20),
    is_system       BOOLEAN DEFAULT FALSE,  -- built-in roles
    is_deletable    BOOLEAN DEFAULT TRUE,
    created_at      TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(company_id, name)
);

-- ============================================================
-- ROLE PERMISSIONS (What each role can do)
-- ============================================================
CREATE TABLE role_permissions (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    role_id         UUID REFERENCES role_definitions(id) ON DELETE CASCADE,
    permission      VARCHAR(100) NOT NULL,  -- 'task.create'
    scope           VARCHAR(20) NOT NULL,   -- 'own', 'direct', 'subtree', 'company'
    
    UNIQUE(role_id, permission)
);

-- ============================================================
-- ROLE RELATIONSHIPS (How roles connect)
-- ============================================================
CREATE TABLE role_relationships (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id          UUID REFERENCES companies(id) ON DELETE CASCADE,
    from_role_id        UUID REFERENCES role_definitions(id) ON DELETE CASCADE,
    to_role_id          UUID REFERENCES role_definitions(id) ON DELETE CASCADE,
    relationship_type   VARCHAR(30) NOT NULL,  -- 'manages', 'mentors', 'reviews', etc.
    
    UNIQUE(from_role_id, to_role_id, relationship_type)
);

-- ============================================================
-- ROLE PROFILE FIELDS (Custom fields per role)
-- ============================================================
CREATE TABLE role_profile_fields (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    role_id         UUID REFERENCES role_definitions(id) ON DELETE CASCADE,
    field_name      VARCHAR(100) NOT NULL,
    field_type      VARCHAR(30) NOT NULL,  -- 'text', 'date', 'dropdown', 'email', etc.
    display_name    VARCHAR(100) NOT NULL,
    is_required     BOOLEAN DEFAULT FALSE,
    options         JSONB,  -- for dropdown: ["Option1", "Option2"]
    sort_order      INTEGER DEFAULT 0,
    
    UNIQUE(role_id, field_name)
);

-- ============================================================
-- USERS
-- ============================================================
CREATE TABLE users (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id) ON DELETE CASCADE,
    role_id         UUID REFERENCES role_definitions(id),
    email           VARCHAR(255) NOT NULL,
    password_hash   VARCHAR(255) NOT NULL,
    name            VARCHAR(255) NOT NULL,
    profile_photo   TEXT,
    status          VARCHAR(20) DEFAULT 'active',  -- active, suspended, exited
    last_login      TIMESTAMP,
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(company_id, email)
);

-- ============================================================
-- USER PROFILE DATA (Dynamic fields based on role)
-- ============================================================
CREATE TABLE user_profile_data (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    field_id        UUID REFERENCES role_profile_fields(id) ON DELETE CASCADE,
    value           TEXT,
    
    UNIQUE(user_id, field_id)
);

-- ============================================================
-- USER CONNECTIONS (Who reports to whom)
-- ============================================================
CREATE TABLE user_connections (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    from_user_id        UUID REFERENCES users(id) ON DELETE CASCADE,
    to_user_id          UUID REFERENCES users(id) ON DELETE CASCADE,
    relationship_id     UUID REFERENCES role_relationships(id),
    is_active           BOOLEAN DEFAULT TRUE,
    created_at          TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(from_user_id, to_user_id, relationship_id)
);

-- ============================================================
-- WORKSPACES
-- ============================================================
CREATE TABLE workspaces (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id) ON DELETE CASCADE,
    name            VARCHAR(255) NOT NULL,
    description     TEXT,
    created_by      UUID REFERENCES users(id),
    status          VARCHAR(20) DEFAULT 'active',  -- active, archived
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- WORKSPACE MEMBERS
-- ============================================================
CREATE TABLE workspace_members (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id    UUID REFERENCES workspaces(id) ON DELETE CASCADE,
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    role            VARCHAR(30) DEFAULT 'member',  -- owner, member
    joined_at       TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(workspace_id, user_id)
);

-- ============================================================
-- TASK CATEGORIES
-- ============================================================
CREATE TABLE task_categories (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id) ON DELETE CASCADE,
    name            VARCHAR(100) NOT NULL,
    icon            VARCHAR(50),
    color           VARCHAR(20),
    is_system       BOOLEAN DEFAULT FALSE,  -- built-in categories
    
    UNIQUE(company_id, name)
);

-- ============================================================
-- TASKS
-- ============================================================
CREATE TABLE tasks (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id    UUID REFERENCES workspaces(id) ON DELETE CASCADE,
    category_id     UUID REFERENCES task_categories(id),
    title           VARCHAR(255) NOT NULL,
    description     TEXT,
    deadline        TIMESTAMP NOT NULL,
    points          INTEGER DEFAULT 10,
    submission_type VARCHAR(30) NOT NULL,  -- 'file', 'github', 'url', 'text', 'mixed'
    status          VARCHAR(30) DEFAULT 'active',  -- active, closed
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- TASK ASSIGNEES
-- ============================================================
CREATE TABLE task_assignees (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    task_id         UUID REFERENCES tasks(id) ON DELETE CASCADE,
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    status          VARCHAR(30) DEFAULT 'pending',  
                    -- pending, in_progress, submitted, late_submitted, 
                    -- under_review, approved, revision_needed
    assigned_at     TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(task_id, user_id)
);

-- ============================================================
-- SUBMISSIONS
-- ============================================================
CREATE TABLE submissions (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    task_id         UUID REFERENCES tasks(id) ON DELETE CASCADE,
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    submission_data JSONB NOT NULL,  -- {files: [], links: [], text: ""}
    is_late         BOOLEAN DEFAULT FALSE,
    submitted_at    TIMESTAMP DEFAULT NOW(),
    version         INTEGER DEFAULT 1  -- for resubmissions
);

-- ============================================================
-- REVIEWS (AI + Human)
-- ============================================================
CREATE TABLE reviews (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    submission_id   UUID REFERENCES submissions(id) ON DELETE CASCADE,
    reviewer_type   VARCHAR(20) NOT NULL,  -- 'ai', 'human'
    reviewer_id     UUID REFERENCES users(id),  -- null for AI
    score           DECIMAL(5,2),
    max_score       DECIMAL(5,2),
    remarks         TEXT,
    detailed_review JSONB,  -- structured feedback
    created_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- ATTENDANCE SETTINGS (Per company)
-- ============================================================
CREATE TABLE attendance_settings (
    id                      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id              UUID UNIQUE REFERENCES companies(id) ON DELETE CASCADE,
    work_start_time         TIME DEFAULT '09:00',
    work_end_time           TIME DEFAULT '18:00',
    grace_period_minutes    INTEGER DEFAULT 15,
    min_hours_full_day      DECIMAL(4,2) DEFAULT 8,
    min_hours_half_day      DECIMAL(4,2) DEFAULT 4,
    ip_restriction_enabled  BOOLEAN DEFAULT FALSE,
    allowed_ips             JSONB DEFAULT '[]',
    geo_restriction_enabled BOOLEAN DEFAULT FALSE,
    geo_coordinates         JSONB,  -- {lat, lng, radius_meters}
    created_at              TIMESTAMP DEFAULT NOW(),
    updated_at              TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- LEAVE TYPES
-- ============================================================
CREATE TABLE leave_types (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id) ON DELETE CASCADE,
    name            VARCHAR(100) NOT NULL,
    quota           INTEGER,  -- null for unlimited
    is_paid         BOOLEAN DEFAULT TRUE,
    
    UNIQUE(company_id, name)
);

-- ============================================================
-- ATTENDANCE RECORDS
-- ============================================================
CREATE TABLE attendance_records (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    date            DATE NOT NULL,
    clock_in        TIMESTAMP,
    clock_out       TIMESTAMP,
    total_hours     DECIMAL(5,2),
    status          VARCHAR(30),  -- present, half_day, absent, leave, wfh
    late_by_minutes INTEGER DEFAULT 0,
    early_by_minutes INTEGER DEFAULT 0,
    clock_in_ip     VARCHAR(50),
    clock_in_geo    JSONB,  -- {lat, lng}
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(user_id, date)
);

-- ============================================================
-- LEAVE REQUESTS
-- ============================================================
CREATE TABLE leave_requests (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    leave_type_id   UUID REFERENCES leave_types(id),
    from_date       DATE NOT NULL,
    to_date         DATE NOT NULL,
    reason          TEXT,
    status          VARCHAR(20) DEFAULT 'pending',  -- pending, approved, rejected
    approver_id     UUID REFERENCES users(id),
    approved_at     TIMESTAMP,
    rejection_reason TEXT,
    created_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- LEAVE BALANCE
-- ============================================================
CREATE TABLE leave_balance (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    leave_type_id   UUID REFERENCES leave_types(id) ON DELETE CASCADE,
    year            INTEGER NOT NULL,
    total           INTEGER NOT NULL,
    used            INTEGER DEFAULT 0,
    
    UNIQUE(user_id, leave_type_id, year)
);

-- ============================================================
-- MEETINGS
-- ============================================================
CREATE TABLE meetings (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id) ON DELETE CASCADE,
    title           VARCHAR(255) NOT NULL,
    description     TEXT,
    meeting_code    VARCHAR(20) UNIQUE NOT NULL,  -- MET-YYYYMMDD-XXXX
    passkey         VARCHAR(20) NOT NULL,
    scheduled_at    TIMESTAMP NOT NULL,
    duration_minutes INTEGER DEFAULT 60,
    host_id         UUID REFERENCES users(id),
    status          VARCHAR(20) DEFAULT 'scheduled',  -- scheduled, ongoing, ended
    recording_url   TEXT,
    created_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- MEETING PARTICIPANTS
-- ============================================================
CREATE TABLE meeting_participants (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    meeting_id      UUID REFERENCES meetings(id) ON DELETE CASCADE,
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    status          VARCHAR(20) DEFAULT 'invited',  -- invited, accepted, declined, joined
    invited_at      TIMESTAMP DEFAULT NOW(),
    joined_at       TIMESTAMP,
    left_at         TIMESTAMP,
    
    UNIQUE(meeting_id, user_id)
);

-- ============================================================
-- CERTIFICATES
-- ============================================================
CREATE TABLE certificates (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
    template_id     UUID,  -- FK to certificate_templates if needed
    certificate_no  VARCHAR(50) UNIQUE NOT NULL,
    issued_date     DATE NOT NULL,
    data            JSONB NOT NULL,  -- all certificate data
    file_url        TEXT,
    created_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- EMAIL LOGS
-- ============================================================
CREATE TABLE email_logs (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id      UUID REFERENCES companies(id),
    to_email        VARCHAR(255) NOT NULL,
    template        VARCHAR(100) NOT NULL,
    subject         VARCHAR(255) NOT NULL,
    status          VARCHAR(20) DEFAULT 'pending',  -- pending, sent, failed
    sent_at         TIMESTAMP,
    error           TEXT,
    created_at      TIMESTAMP DEFAULT NOW()
);

-- ============================================================
-- INDEXES
-- ============================================================
CREATE INDEX idx_users_company ON users(company_id);
CREATE INDEX idx_users_role ON users(role_id);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_tasks_workspace ON tasks(workspace_id);
CREATE INDEX idx_tasks_deadline ON tasks(deadline);
CREATE INDEX idx_attendance_user_date ON attendance_records(user_id, date);
CREATE INDEX idx_submissions_task ON submissions(task_id);
CREATE INDEX idx_meetings_scheduled ON meetings(scheduled_at);
```

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
│  TailwindCSS                   Prisma ORM                      │
│  Socket.io Client              Socket.io                       │
│                                                                 │
│  ─────────────────────────────────────────────────────────────│
│                                                                 │
│  PYTHON SERVICES               DATABASE                        │
│  ───────────────               ────────                        │
│  FastAPI                       PostgreSQL (Primary)            │
│  AI Review Engine              Redis (Cache/Sessions)          │
│  Email Service                 S3/CloudStorage (Files)         │
│  Report Generation                                             │
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

---

*Document Version: 1.0*
*Last Updated: January 12, 2026*
