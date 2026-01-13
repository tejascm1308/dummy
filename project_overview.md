# Intern Tracking System - Complete Documentation

> Version 4.0 | Focus: Why Each Module Helps Track Interns

---

## What Does "Tracking" Mean in This System?

```
┌─────────────────────────────────────────────────────────────────┐
│                 WHAT WE TRACK ABOUT INTERNS                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  📊 PERFORMANCE                                                 │
│     • Tasks assigned vs completed                               │
│     • Scores on each task                                       │
│     • Quality of submissions                                    │
│     • Improvement over time                                     │
│                                                                 │
│  ⏱️  TIME                                                        │
│     • When they clock in/out                                    │
│     • Total hours worked                                        │
│     • Punctuality (late arrivals)                               │
│     • Leave taken                                               │
│                                                                 │
│  📈 PROGRESS                                                    │
│     • % of internship completed                                 │
│     • Skills demonstrated                                       │
│     • Mentor feedback                                           │
│     • AI review insights                                        │
│                                                                 │
│  👥 RELATIONSHIPS                                                │
│     • Who mentors them                                          │
│     • Who reviews their work                                    │
│     • Communication activity                                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Why Each Module Exists (Tracking Justification)

| Module | What It Tracks | Why It's Needed |
|--------|----------------|-----------------|
| **Company** | Company settings | Different companies track differently |
| **Role** | Who can do what | Know who tracks whom |
| **User** | Intern profiles | Know WHO we're tracking |
| **Workspace** | Project groupings | Organize what we track |
| **Task** | Assigned work | Track what intern should do |
| **Submission** | Completed work | Track what intern actually did |
| **Review** | Quality scores | Track how well they did |
| **Attendance** | Time presence | Track when they're working |
| **Leave** | Absences | Track when they're NOT working |
| **Meeting** | 1:1s, reviews | Track mentor interactions |
| **Message** | Communication | Track engagement |
| **Report** | Summaries | Present tracked data |
| **Certificate** | Completion proof | Final tracking outcome |

---

## Module 1: Company

### Why It Exists in a Tracking System

Different companies have different tracking needs:
- **Startup A**: Tracks only task completion, no attendance
- **Corporate B**: Strict attendance + daily reports
- **Agency C**: Tracks billable hours to clients

The Company module stores **how each company wants to track their interns.**

### What Tracking Settings It Stores

| Setting | Tracking Purpose |
|---------|------------------|
| `attendance_config` | Should we track clock in/out? How strictly? |
| `leave_types` | What absences do we track (sick, casual, WFH)? |
| `task_categories` | What types of work are we assigning? |
| `settings.features` | Which tracking features are enabled? |

### Example

```javascript
// Company A wants relaxed tracking
{
  attendance_config: {
    enabled: false          // Don't track attendance at all
  }
}

// Company B wants strict tracking  
{
  attendance_config: {
    enabled: true,
    grace_minutes: 0,       // No grace period - late is late
    methods: ["geo"]        // Must clock in from office location
  }
}
```

---

## Module 2: Role System

### Why It Exists in a Tracking System

In a tracking system, you need to know:
- **WHO is being tracked?** (Interns)
- **WHO does the tracking?** (Mentors, Managers)
- **WHO sees the tracking data?** (Admin, HR)

Roles define these relationships.

### How Roles Enable Tracking

```
┌─────────────────────────────────────────────────────────────────┐
│                   TRACKING HIERARCHY                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                       ┌───────────┐                             │
│                       │   ADMIN   │                             │
│                       │ Sees ALL  │                             │
│                       └─────┬─────┘                             │
│                             │                                   │
│                    Can see everyone's data                      │
│                             │                                   │
│                       ┌─────▼─────┐                             │
│                       │  MANAGER  │                             │
│                       │ Tracks    │                             │
│                       │ Mentors   │                             │
│                       └─────┬─────┘                             │
│                             │                                   │
│                    Can see their team's data                    │
│                             │                                   │
│              ┌──────────────┼──────────────┐                    │
│        ┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼─────┐              │
│        │  MENTOR A │  │  MENTOR B │  │  MENTOR C │              │
│        │ Tracks    │  │ Tracks    │  │ Tracks    │              │
│        │ Interns   │  │ Interns   │  │ Interns   │              │
│        └─────┬─────┘  └─────┬─────┘  └─────┬─────┘              │
│              │              │              │                    │
│        Can see only their own interns' data                     │
│              │              │              │                    │
│        ┌─────▼───┐    ┌─────▼───┐    ┌─────▼───┐                │
│        │ Intern1 │    │ Intern3 │    │ Intern5 │                │
│        │ Intern2 │    │ Intern4 │    │ Intern6 │                │
│        └─────────┘    └─────────┘    └─────────┘                │
│                                                                 │
│        Can see only their OWN data                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Permission Scopes for Tracking

| Scope | Tracking Meaning |
|-------|------------------|
| `own` | Can see only my own data |
| `direct` | Can see data of people directly under me |
| `subtree` | Can see data of everyone in my tree |
| `company` | Can see everyone's data (admin only) |

---

## Module 3: User Management

### Why It Exists in a Tracking System

You can't track interns without knowing who they are!

### What We Store for Tracking

| Field | Tracking Purpose |
|-------|------------------|
| `name, email` | Who is this intern? |
| `role_id` | What are we tracking them for? |
| `profile_data` | Context (college, skills, dates) |
| `status` | Are they still active? |
| `created_at` | When did they start? |

### Tracking-Relevant Profile Fields

For each intern, we capture:
```javascript
profile_data: {
  college: "MIT",
  degree: "B.Tech CS",
  start_date: "2026-01-01",      // When tracking begins
  end_date: "2026-03-31",        // When tracking ends
  skills: ["Node.js", "React"]   // What we expect them to learn
}
```

This helps us track:
- Duration of internship
- Expected vs actual learning

---

## Module 4: Workspace

### Why It Exists in a Tracking System

Workspaces group related work so we can:
- Track progress on a project
- See which interns worked on what
- Compare performance across projects

### Tracking Example

```
Workspace: "E-commerce Backend Project"

Tracking data for this workspace:
┌────────────────────────────────────────┐
│ Total Tasks: 15                        │
│ Completed: 12 (80%)                    │
│ On Time: 10 (83%)                      │
│ Average Score: 7.5/10                  │
│                                        │
│ Intern Performance:                    │
│ • Alice: 5 tasks, avg 8.2 ⬆️ Top       │
│ • Bob: 4 tasks, avg 7.0                │
│ • Charlie: 3 tasks, avg 7.3            │
└────────────────────────────────────────┘
```

Without workspaces, we'd have no way to group and compare performance by project.

---

## Module 5: Task System

### Why It Exists in a Tracking System

**Tasks are the CORE of tracking.** Everything revolves around:
- What work was assigned?
- Was it completed?
- Was it on time?
- Was it good quality?

### What We Track About Tasks

| Data Point | Why We Track It |
|------------|-----------------|
| `title, description` | What was assigned |
| `deadline` | Was it completed on time? |
| `points` | How important is this task? |
| `assignees[].status` | Current progress |
| `category` | Type of work (development, docs, etc.) |

### Task Status = Tracking State

```
┌─────────────────────────────────────────────────────────────────┐
│                  TASK TRACKING STATES                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  PENDING         → Assigned but not started                    │
│                    ⚠️ Flag if pending too long                  │
│                                                                 │
│  IN_PROGRESS     → Working on it                                │
│                    Track time spent                             │
│                                                                 │
│  SUBMITTED       → Done on time ✓                               │
│                    Good! Count as timely                        │
│                                                                 │
│  LATE_SUBMITTED  → Done but late ⚠️                             │
│                    Flag for tracking                            │
│                                                                 │
│  UNDER_REVIEW    → Being evaluated                              │
│                    Waiting for score                            │
│                                                                 │
│  APPROVED        → Completed successfully ✓                     │
│                    Count as success                             │
│                                                                 │
│  REVISION_NEEDED → Needs improvement                            │
│                    Track revision count                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Tracking Metrics from Tasks

From tasks, we calculate:
- **Completion rate**: Tasks approved / Tasks assigned
- **On-time rate**: Submitted before deadline / Total submitted
- **Average score**: Sum of scores / Total reviewed tasks
- **Revision rate**: Tasks needing revision / Total tasks

---

## Module 6: Submission & Review System

### Why It Exists in a Tracking System

Submissions capture **what the intern actually delivered**. Reviews capture **how good it was**.

### What We Track

**From Submissions:**
| Data | Tracking Use |
|------|--------------|
| `files, github_links` | Proof of work |
| `submitted_at` | Was it on time? |
| `is_late` | Punctuality flag |
| `version` | How many attempts? |

**From Reviews:**
| Data | Tracking Use |
|------|--------------|
| `score` | Quality measurement |
| `remarks` | Qualitative feedback |
| `detailed_review.strengths` | What they do well |
| `detailed_review.improvements` | Where to improve |

### AI Review for Tracking

AI review provides **consistent, unbiased tracking**:

```
AI Review Output:
┌────────────────────────────────────────┐
│ Score: 7.5/10                          │
│                                        │
│ Code Quality: 8/10                     │
│ Functionality: 7/10                    │
│ Documentation: 6/10                    │
│ Test Coverage: 8/10                    │
│                                        │
│ Strengths:                             │
│ • Clean code structure                 │
│ • Good error handling                  │
│                                        │
│ Improvements:                          │
│ • Missing input validation             │
│ • Could add more comments              │
└────────────────────────────────────────┘
```

This gives us **trackable, comparable data** across all interns.

---

## Module 7: Attendance System

### Why It Exists in a Tracking System

Attendance answers: **"Is the intern actually working?"**

### What We Track

| Data | Tracking Purpose |
|------|------------------|
| `clock_in` | What time did they start? |
| `clock_out` | What time did they leave? |
| `total_hours` | How long did they work? |
| `status` | Present, half-day, absent? |
| `late_by_minutes` | Were they late? How late? |

### Attendance Tracking Metrics

From attendance, we calculate:
- **Attendance %**: Days present / Total working days
- **Punctuality %**: On-time days / Days present
- **Average hours**: Total hours / Days present
- **Late arrivals**: Count of late days

### Tracking Dashboard View

```
Alice's Attendance (January 2026):
┌────────────────────────────────────────┐
│ Days Worked: 20/22 (91%)               │
│ On Time: 18/20 (90%)                   │
│ Average Hours: 8.2 hrs/day             │
│ Late Arrivals: 2 days                  │
│                                        │
│ Calendar:                              │
│ M  T  W  T  F                          │
│ ✓  ✓  ✓  ✓  ✓  Week 1                  │
│ ✓  ⏰ ✓  ✓  ✓  Week 2 (1 late)         │
│ ✓  ✓  ✗  ✓  ✓  Week 3 (1 absent)       │
│ ✓  ✓  ✓  ⏰ ✓  Week 4 (1 late)         │
└────────────────────────────────────────┘
```

---

## Module 8: Leave Management

### Why It Exists in a Tracking System

Leave tracking answers: **"Why was the intern absent?"**

Without leave tracking:
- Absence = Unknown reason
- Can't differentiate sick leave from skipping

With leave tracking:
- Absence = Approved leave (valid)
- Absence = Not approved (flag for follow-up)

### What We Track

| Data | Tracking Purpose |
|------|------------------|
| `leave_type` | What kind of absence? |
| `from_date, to_date` | Duration of absence |
| `status` | Was it approved? |
| `reason` | Why were they absent? |

### Leave Balance Tracking

```
Alice's Leave Balance (2026):
┌────────────────────────────────────────┐
│ Casual Leave: 10/12 remaining          │
│ Sick Leave: 6/6 remaining              │
│ WFH Days: 3 used (unlimited)           │
│                                        │
│ History:                               │
│ • Jan 5-6: Casual (Family event) ✓     │
│ • Jan 12: WFH (Internet issue) ✓       │
│ • Jan 15: WFH (Weather) ✓              │
│ • Jan 20: WFH (Doctor visit) ✓         │
└────────────────────────────────────────┘
```

---

## Module 9: Meeting System

### Why It Exists in a Tracking System

Meetings track **mentor-intern interactions**:
- Are mentors meeting with interns regularly?
- Are interns attending scheduled meetings?
- How much 1:1 time is being invested?

### What We Track

| Data | Tracking Purpose |
|------|------------------|
| `scheduled_at` | When was meeting planned? |
| `participants[].status` | Did they accept/decline? |
| `participants[].joined_at` | Did they actually join? |
| `duration_minutes` | How long was the meeting? |

### Meeting Tracking Insights

```
Alice's Meetings (January):
┌────────────────────────────────────────┐
│ Scheduled: 4                           │
│ Attended: 4 (100%)                     │
│ Total Meeting Time: 2.5 hours          │
│                                        │
│ Types:                                 │
│ • Weekly 1:1 with Mentor: 4            │
│ • Team Standup: 12 (not tracked)       │
│ • Project Review: 1                    │
└────────────────────────────────────────┘
```

---

## Module 10: Communication

### Why It Exists in a Tracking System

Communication tracking answers:
- Is the intern engaged?
- Are they asking questions?
- Are they responsive?

### What We Track

| Data | Tracking Purpose |
|------|------------------|
| `message count` | How active are they? |
| `response time` | How quickly do they reply? |
| `questions asked` | Are they proactive? |

### Communication Insights

```
Alice's Communication (January):
┌────────────────────────────────────────┐
│ Messages Sent: 45                      │
│ Messages Received: 52                  │
│ Avg Response Time: 15 mins             │
│                                        │
│ Task Comments: 23                      │
│ Questions Asked: 8                     │
│                                        │
│ Status: ✓ Actively engaged             │
└────────────────────────────────────────┘
```

---

## Module 11: Report System

### Why It Exists in a Tracking System

Reports **present the tracked data** in a meaningful way.

All the tracking we do is useless if we can't:
- Show it to managers
- Compare across interns
- Track progress over time

### Report Types

| Report | Contains | For Whom |
|--------|----------|----------|
| **Intern Report** | Individual performance summary | Mentor, Intern, HR |
| **Team Report** | Compare all interns | Manager |
| **Attendance Report** | Time tracking summary | HR |
| **Progress Report** | Weekly/monthly progress | Everyone |
| **Exit Report** | Full internship summary | HR, Intern |

### Universal Report Structure

Every report follows this template:

```
┌─────────────────────────────────────────────────────────────────┐
│                     INTERN PROGRESS REPORT                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  HEADER                                                         │
│  • Intern: Alice Johnson                                        │
│  • Period: January 2026                                         │
│  • Mentor: John Smith                                           │
│                                                                 │
│  SUMMARY METRICS                                                │
│  ┌──────────┬──────────┬──────────┬──────────┐                  │
│  │  Tasks   │Attendance│  Score   │ On-Time  │                  │
│  │   8/10   │   91%    │   8.2    │   90%    │                  │
│  └──────────┴──────────┴──────────┴──────────┘                  │
│                                                                 │
│  TASK BREAKDOWN                                                 │
│  • Development: 5 tasks, avg 8.5                               │
│  • Documentation: 2 tasks, avg 7.8                             │
│  • Research: 1 task, avg 8.0                                   │
│                                                                 │
│  STRENGTHS (from AI + Human reviews)                           │
│  • Consistent code quality                                     │
│  • Meets deadlines                                             │
│  • Good communication                                          │
│                                                                 │
│  AREAS FOR IMPROVEMENT                                         │
│  • Documentation could be more detailed                        │
│  • Should ask more clarifying questions                        │
│                                                                 │
│  ATTENDANCE CALENDAR                                            │
│  [Visual representation]                                        │
│                                                                 │
│  MENTOR COMMENTS                                                │
│  "Great progress this month..."                                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Module 12: Certificate System

### Why It Exists in a Tracking System

The certificate is the **final output of all tracking**.

It proves:
- The intern completed the program
- They achieved certain scores
- They worked for a specific duration
- Their work was verified

### Certificate Contains Tracked Data

```
┌─────────────────────────────────────────────────────────────────┐
│                   INTERNSHIP CERTIFICATE                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  This certifies that                                            │
│                                                                 │
│               ALICE JOHNSON                                     │
│                                                                 │
│  successfully completed an internship at                        │
│  ACME CORPORATION                                               │
│                                                                 │
│  TRACKED DATA INCLUDED:                                         │
│  ─────────────────────                                          │
│  • Duration: January - March 2026                               │
│  • Tasks Completed: 25                                          │
│  • Overall Score: 87%                                           │
│  • Attendance: 95%                                              │
│  • Mentor: John Smith                                           │
│  • Department: Engineering                                      │
│                                                                 │
│  Certificate No: CERT-2026-001234                               │
│  Verification: verify.ourplatform.com/abc123xyz                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Module 13: Dashboard & Widgets

### Why It Exists in a Tracking System

Dashboards show **real-time tracking data** at a glance.

Different roles see different tracking data:

### Intern Dashboard (Sees Their Own Tracking)

```
┌─────────────────────────────────────────────────────────────────┐
│  ALICE'S DASHBOARD                                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
│  │ My Score │  │ Tasks    │  │Attendance│  │ Days Left│         │
│  │ 85%      │  │ 3 pending│  │ 91%      │  │ 45       │         │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘         │
│                                                                 │
│  MY PENDING TASKS          │  ATTENDANCE TODAY                  │
│  ○ Build Login API         │  ┌──────────────────┐              │
│  ○ Write API Docs          │  │ Clocked In: 9:05 │              │
│  ○ Code Review PR #42      │  │ Hours: 4.5       │              │
│                            │  │ [ Clock Out ]    │              │
│                            │  └──────────────────┘              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Alice sees: HER OWN tracking data only
```

### Mentor Dashboard (Sees Their Interns' Tracking)

```
┌─────────────────────────────────────────────────────────────────┐
│  JOHN'S MENTOR DASHBOARD                                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
│  │ Interns  │  │ Tasks    │  │ Reviews  │  │ Avg Score│         │
│  │ 3        │  │ 15 active│  │ 5 pending│  │ 82%      │         │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘         │
│                                                                 │
│  INTERN TRACKING             │  PENDING REVIEWS                 │
│  ┌───────────────────────┐   │  ○ Alice - Auth API              │
│  │ Alice: 85% ⬆️         │   │  ○ Bob - Database Schema         │
│  │ Bob: 78% →            │   │  ○ Alice - API Docs              │
│  │ Charlie: 72% ⬇️        │   │                                  │
│  └───────────────────────┘   │  [ Review Next ]                 │
│                                                                 │
│  TODAY'S ATTENDANCE                                             │
│  ✓ Alice (9:05 AM)  ✓ Bob (9:00 AM)  ? Charlie (not yet)        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

John sees: His 3 interns' tracking data
```

### Admin Dashboard (Sees All Tracking)

```
┌─────────────────────────────────────────────────────────────────┐
│  ADMIN DASHBOARD                                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
│  │ Interns  │  │ Mentors  │  │ Tasks    │  │ Avg Score│         │
│  │ 25       │  │ 5        │  │ 150      │  │ 79%      │         │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘         │
│                                                                 │
│  COMPANY-WIDE TRACKING                                          │
│  ┌────────────────────────────────────────────────┐             │
│  │ Department     │ Interns │ Avg Score │ Attend %│             │
│  │ Engineering    │ 15      │ 82%       │ 94%     │             │
│  │ Design         │ 5       │ 78%       │ 91%     │             │
│  │ Data Science   │ 5       │ 75%       │ 88%     │             │
│  └────────────────────────────────────────────────┘             │
│                                                                 │
│  [ Generate Company Report ]  [ Export Data ]                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

Admin sees: EVERYONE'S tracking data
```

---

## Tech Stack

### Why This Stack for a Tracking System?

| Choice | Tracking Benefit |
|--------|------------------|
| **Node.js** | Fast API responses for real-time tracking |
| **MongoDB** | Flexible schema for different tracking needs per company |
| **Python AI** | Consistent, unbiased automated review scoring |
| **WebSocket** | Real-time attendance, presence tracking |

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                   TRACKING SYSTEM ARCHITECTURE                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  NODE.JS BACKEND                PYTHON SERVICES                 │
│  ────────────────                ───────────────                │
│  • User actions                 • AI Review scoring            │
│  • Task assignment              • Report PDF generation        │
│  • Attendance clock             • Certificate generation       │
│  • Real-time updates            • Email notifications          │
│                                                                 │
│             │                           │                       │
│             ▼                           ▼                       │
│       ┌───────────────────────────────────────┐                 │
│       │              MONGODB                  │                 │
│       │                                       │                 │
│       │  All tracking data stored here:       │                 │
│       │  • Task records                       │                 │
│       │  • Submission + reviews               │                 │
│       │  • Attendance logs                    │                 │
│       │  • Leave records                      │                 │
│       │  • Meeting history                    │                 │
│       │  • Communication logs                 │                 │
│       └───────────────────────────────────────┘                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Summary: How Everything Connects for Tracking

```
┌─────────────────────────────────────────────────────────────────┐
│                    THE TRACKING FLOW                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. COMPANY sets up tracking rules (attendance, leave types)   │
│                         │                                       │
│                         ▼                                       │
│  2. ROLES define who tracks whom                               │
│                         │                                       │
│                         ▼                                       │
│  3. USERS are created (interns to track, mentors to track them)│
│                         │                                       │
│                         ▼                                       │
│  4. TASKS are assigned (what to track)                         │
│                         │                                       │
│                         ▼                                       │
│  5. SUBMISSIONS received (track what they did)                 │
│                         │                                       │
│                         ▼                                       │
│  6. REVIEWS score the work (track how well)                    │
│                         │                                       │
│                         ▼                                       │
│  7. ATTENDANCE tracks time (when they work)                    │
│                         │                                       │
│                         ▼                                       │
│  8. LEAVE tracks absences (when they don't work)               │
│                         │                                       │
│                         ▼                                       │
│  9. REPORTS summarize all tracking data                        │
│                         │                                       │
│                         ▼                                       │
│  10. CERTIFICATES prove the tracked outcomes                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

*Document Version: 4.0*
*Focus: Intern Tracking System*
*Last Updated: January 13, 2026*
