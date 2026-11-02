# Campus Connect: UI Screens Reference

A campus portal for students and administrators covering announcements, events, placements and academic updates. This document describes the six screens in the design, with their content and components, so it can be shared with developers, designers and stakeholders.

---

## 1. Global Layout

**Sidebar navigation (all screens except Login)**

| Item | Purpose |
|---|---|
| Dashboard | Student home / summary |
| Events | Browse and register for campus events |
| Placements | Placement and internship opportunities |
| Academics | Circulars, departments, timetable |
| Admin Panel | Publish and manage announcements |

**Header (all screens):** page title and subtitle on the left; avatar chip with user initials (**AK**) on the right. Some screens also show a primary action button.

**Visual style:** blue primary color, light grey-blue background, white rounded cards, small colored status/category pills.

---

## 2. Login

**Purpose:** Sign in to a campus account.

| Field | Details |
|---|---|
| Role | Dropdown, default `Student` |
| Register No. / Email | Text input, placeholder `e.g. 21CS1001` |
| Password | Password input |

**Actions:** `Sign in` (primary button), `Forgot password?` (text link).

---

## 3. Student Dashboard

**Header:** "Good morning, Arun. Here is what is happening on campus." Includes a Notifications indicator.

**Summary cards**

| Card | Value |
|---|---|
| New announcements | 6 |
| Upcoming events | 4 |
| Open placements | 12 |
| Pending notices | 2 |

**Latest announcements**

| Title | Source | Time | Tag |
|---|---|---|---|
| Internal assessment schedule released | Examination Cell | 2 hrs ago | Academic |
| Annual Tech Fest registrations open | Student Affairs | Today | Event |
| TCS campus drive on 12 Nov | Placement Cell | Yesterday | Placement |
| Library timing change | Administration | 2 days ago | Notice |

**Upcoming events**

| Event | Date | Venue | Status |
|---|---|---|---|
| Tech Fest 2026 | 15 Nov | Main Auditorium | Open |
| Guest lecture: AI in Industry | 18 Nov | Seminar Hall | Open |
| Sports Day | 22 Nov | Ground | Soon |

---

## 4. Events

**Purpose:** Discover and register for campus activities.

**Controls:** search box (`Search events`), `All departments` filter, `All categories` filter, `+ Create Event` button.

**Event cards (3-column grid, each with a `Register` button)**

| Event | Category | Date & Time | Venue |
|---|---|---|---|
| Tech Fest 2026 | Technical | 15 Nov, 9:00 AM | Main Auditorium |
| Guest Lecture: AI in Industry | Seminar | 18 Nov, 2:00 PM | Seminar Hall |
| Sports Day | Sports | 22 Nov, 8:00 AM | College Ground |
| Cultural Night | Cultural | 28 Nov, 6:00 PM | Open Air Theatre |
| Hackathon 24h | Technical | 2 Dec, 10:00 AM | CS Block |
| Blood Donation Camp | Social | 5 Dec, 9:00 AM | Health Centre |

---

## 5. Placements & Internships

**Purpose:** Opportunities shared by the Placement Cell.

**Summary cards**

| Card | Value |
|---|---|
| Open drives | 12 |
| Internships | 8 |
| Applied by you | 3 |

**Open opportunities**

| Company | Role | Type | Eligibility | Deadline | Action |
|---|---|---|---|---|---|
| TCS | Systems Engineer | Placement | CGPA ≥ 6.5 | 10 Nov | Apply |
| Infosys | Intern - Web Dev | Internship | CGPA ≥ 7.0 | 14 Nov | Apply |
| Zoho | Software Developer | Placement | CGPA ≥ 7.5 | 20 Nov | Apply |
| Wipro | Project Engineer | Placement | CGPA ≥ 6.0 | 25 Nov | Apply |
| Startup Hub | Intern - UI/UX | Internship | Open to all | 30 Nov | Apply |

---

## 6. Academics

**Purpose:** Department news, circulars and timetables.

**Circulars**

| Title | Issuer | Date | Tag |
|---|---|---|---|
| Internal Assessment I timetable | CSE Department | 27 Oct | PDF |
| Lab record submission deadline | CSE Department | 25 Oct | Notice |
| Semester fee payment reminder | Accounts | 22 Oct | Notice |
| Revised syllabus - Data Structures | Academic Council | 20 Oct | PDF |

**Departments** (each has a `View updates` link): CSE, ECE, EEE, MECH, CIVIL.

**Today's timetable**

| Time | Subject | Room | Type |
|---|---|---|---|
| 9:00 | Data Structures | Hall 204 | Lecture |
| 11:00 | DBMS Lab | Lab 3 | Lab |
| 2:00 | Software Engineering | Hall 204 | Lecture |

---

## 7. Admin Panel

**Purpose:** Manage announcements, events and placements.

**Summary cards**

| Card | Value |
|---|---|
| Total users | 2,480 |
| Posts this month | 4 (see note below) |
| Pending approvals | 5 |

**New announcement form**

| Field | Details |
|---|---|
| Title | Text, placeholder `Announcement title` |
| Category | Dropdown, default `Academic` |
| Audience | Dropdown, default `All students` |
| Message | Textarea, placeholder `Write details...` |
| Publish | Primary button |

**Recent posts**

| Title | Author | Status | Actions |
|---|---|---|---|
| IA-I timetable | Exam Cell | Published | Edit · Delete |
| Tech Fest | Student Affairs | Pending | Edit · Delete |
| TCS drive | Placement Cell | Published | Edit · Delete |
| Library timing | Admin | Draft | Edit · Delete |

**Status pills:** Published (green), Pending (orange), Draft (blue).

---

## 8. Design Issues Noticed

These are visible in the screenshots and worth fixing before handoff:

1. **Admin Panel, "Posts this month":** the values `6` and `4` overlap, so the real number is unclear. The Dashboard shows "New announcements 6" and "Upcoming events 4", so this card may have picked up stray text.
2. **Login, password field:** the placeholder wraps onto two lines ("passw / ord") and is clipped. It should be a single line or masked dots.
3. **Content width:** on all screens the content stops at roughly two-thirds of the viewport, leaving a large empty area on the right.
4. **Placements:** the data table renders without visible column alignment in places; confirm Type, Eligibility and Deadline columns line up with their headers.
5. **Terminology:** the Dashboard says "Open placements" while the Placements screen says "Open drives". Pick one term.

---

## 9. Suggested Next Steps

- Confirm the data model: Announcement, Event, Placement/Internship, Circular, Timetable entry, User (with role).
- Define roles and permissions (Student vs. Admin, plus Faculty/Placement Cell if needed).
- Specify the approval flow behind the Draft → Pending → Published statuses.
- Decide how the filters (department, category) and search behave on Events.
