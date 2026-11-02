# Campus Connect: Refined Spec Based on the CIA Triad

This builds on `campus-connect-ui-spec.md`. It applies **Confidentiality, Integrity and Availability** to each screen, based on these decisions:

| Decision | Choice |
|---|---|
| Roles | Student, Faculty / HOD, Admin (Admin also manages drives, sees applicant data, approves posts, manages users and roles) |
| Sign-in | Password only |
| Announcement publishing | Admin-only |
| Scale | Small college, under 3,000 users (current: 2,480) |

---

## 1. Roles and Permissions (Confidentiality)

Principle: **least privilege**. Every role sees and does only what it needs. Permissions are enforced on the server, never only by hiding menu items.

| Capability | Student | Faculty / HOD | Admin |
|---|---|---|---|
| View announcements for their audience | ✅ | ✅ | ✅ |
| View own timetable, own applications and registrations | ✅ | ✅ (own timetable) | ✅ |
| Register for events / apply to drives | ✅ | ❌ | ❌ |
| Create **draft** announcements and circulars (own department) | ❌ | ✅ | ✅ |
| Edit or delete own drafts | ❌ | ✅ | ✅ |
| **Publish / approve** announcements | ❌ | ❌ | ✅ |
| Create events | ❌ | Submit for approval | ✅ |
| Create / manage placement drives | ❌ | ❌ | ✅ |
| View applicant data (name, CGPA, applications) | ❌ (own only) | ❌ | ✅ |
| Manage users and roles | ❌ | ❌ | ✅ |
| View audit log | ❌ | ❌ | ✅ |

**Sidebar changes by role**

- Students and Faculty do **not** see the Admin Panel link. The route also returns "403 Forbidden" if opened directly.
- Faculty get a limited "My Drafts" view instead of the full Admin Panel.
- The Login role dropdown is removed. Role is assigned by an Admin and read from the account, so a user cannot pick "Admin" at sign-in.

**Audience targeting:** the "Audience" field (All students, by department, by year) is enforced when fetching announcements, so a CSE-only circular is never sent to other departments.

---

## 2. Screen-by-Screen Refinements

### Login

| Concern | Change |
|---|---|
| C | Remove the Role dropdown; role comes from the account |
| C | Fix the password field (single line, masked, show/hide toggle) |
| C | Generic error: "Invalid register number or password" (never say which was wrong) |
| C | Lock the account for 15 minutes after 5 failed attempts; rate-limit by IP |
| C | Enforce HTTPS only; session cookie is `HttpOnly`, `Secure`, `SameSite=Lax` |
| C | Session expires after 30 minutes idle (shorter for Admin); sign-out ends the session on the server |
| A | "Forgot password?" sends a time-limited, single-use reset link to the registered college email |
| I | First login forces a password change if the account was created by an Admin |

> **Risk note (password only):** a single password is the weakest control here, and Admin accounts can see applicant data and publish to everyone. Recommended compensating controls: minimum 10 characters, check against common/breached passwords, lockout and rate limiting (above), and login alerts. **Strongly consider adding an email OTP for Admin accounts only**, which is a small change with a large benefit.

### Student Dashboard

| Concern | Change |
|---|---|
| C | Show only data for the signed-in student; greeting and counts come from the server, not the client |
| C | Counts (announcements, events, placements, pending notices) are computed per user and audience |
| I | Announcement source labels ("Examination Cell", "Placement Cell") come from the approved author record, so they cannot be typed in or spoofed |
| A | Cache the announcements list for 1 to 2 minutes; show cached data with a "last updated" note if the database is slow |

### Events

| Concern | Change |
|---|---|
| I | Registration is idempotent: one registration per student per event; double-clicking `Register` does not create duplicates |
| I | Enforce capacity and registration deadline on the server; button changes to `Registered` / `Full` / `Closed` |
| I | Students can cancel their own registration; changes are logged |
| C | Event organisers see registrant counts only; the registrant list is Admin-only |
| A | Registration endpoint is rate-limited and queued so a rush for Tech Fest or Hackathon does not crash the site |
| A | Search and filters run on indexed fields; paginate results |
| ➕ | `+ Create Event` is visible only to Faculty (submit for approval) and Admin |

### Placements & Internships

| Concern | Change |
|---|---|
| C | Eligibility (CGPA) is checked **on the server** against the student's record, so editing the page cannot bypass it |
| C | Applicant data (CGPA, resume, application list) is visible only to the student and Admin |
| C | Uploaded resumes are stored privately and served through short-lived signed links; allow PDF only, size limit, virus scan |
| I | One application per student per drive; applications close automatically at the deadline (server time) |
| I | Applications show a status (Applied, Shortlisted, Rejected) that only Admin can change, with every change logged |
| A | Deadline-day spike: apply endpoint is rate-limited; show a clear "Application received" confirmation and send an email receipt |
| ➕ | Show a "Not eligible" reason instead of a silent disabled button |

### Academics

| Concern | Change |
|---|---|
| I | Circulars and syllabus PDFs are published only after Admin approval; show version and "Last updated" date |
| I | Timetable changes are logged; when a timetable is revised, mark the old version superseded rather than overwriting it |
| C | Department updates are filtered by the audience set at publish time |
| C | PDF links are served from an authenticated route (no guessable public URLs) |
| A | PDFs are stored with backups; the timetable can be viewed offline (cached) on mobile |

### Admin Panel

| Concern | Change |
|---|---|
| C | Accessible to Admin only, enforced by the server |
| C | Total user count and user management are Admin-only; Admins cannot remove their own Admin role or the last Admin |
| I | Fix the "Posts this month" card (overlapping `6` / `4`); derive it from the database |
| I | **Workflow: Faculty create `Draft` → submit `Pending` → Admin `Publish`.** Publishing is not possible from a Faculty account |
| I | Add a **Reject** action with a required reason, shown to the author |
| I | Edit and Delete on a published post create a new version; **Delete is a soft delete** (hidden, recoverable for 30 days) |
| I | Add confirmation dialogs for Publish and Delete |
| I | Sanitise the Message field to block script injection; allow only safe formatting |
| I | Add an **Audit Log** tab: who did what, to which record, when, from which IP |
| A | Autosave the announcement draft so a timeout or crash does not lose text |

---

## 3. Data Classification (Confidentiality)

| Data | Sensitivity | Who can see it | Handling |
|---|---|---|---|
| Password | Critical | No one | Store only as a salted hash (bcrypt or Argon2) |
| CGPA, resumes, application status | High | Student (own), Admin | Encrypted at rest, private file storage |
| Register no., email, phone | Medium | Student (own), Admin | Mask in lists and logs where possible |
| Event registrations | Medium | Student (own), Admin | Counts only for others |
| Announcements, events, timetables | Low (audience-scoped) | Target audience | Published after approval |
| Audit log | High | Admin | Append-only; cannot be edited from the app |

---

## 4. Integrity Controls (Summary)

- All permission checks and eligibility rules run **on the server**.
- Input validation on every form (length, type, allowed values); parameterised database queries; output encoding to prevent XSS.
- CSRF protection on every state-changing request.
- Unique constraints in the database to prevent duplicate registrations and applications.
- Approval workflow: Draft → Pending → Published; only Admin publishes; rejections require a reason.
- Versioning and soft delete for announcements and circulars.
- Append-only audit log for sign-ins, role changes, publishing, deletions, applicant status changes.
- Uploaded files are checked by type and size and are never executed.

---

## 5. Availability Plan (small college, under 3,000 users)

| Area | Plan |
|---|---|
| Hosting | Single application server and one database; sized for about 3,000 users with headroom for deadline-day peaks (roughly a few hundred concurrent) |
| Backups | **Daily** database backup and file-storage backup, kept 14 to 30 days, stored off the main server |
| Recovery | Documented restore steps; **test a restore every term**. Target: restore within 4 hours, lose at most 24 hours of data |
| Spikes | Rate limiting, caching of read-heavy pages, and queued processing for registrations and applications |
| Monitoring | Uptime check plus alerts for errors, slow responses, disk space and failed backups |
| Maintenance | Schedule updates outside exam, placement-deadline and event-registration windows; show a maintenance banner in advance |
| Failure behaviour | Friendly error pages; cached announcements and timetable remain readable if the database is down |
| Denial of service | Request limits per user and IP; basic web application firewall or CDN in front if available |

---

## 6. Open Risks and Recommendations

| # | Risk | Recommendation |
|---|---|---|
| 1 | **Password-only login** for accounts that see applicant data | Add email OTP at least for Admin accounts |
| 2 | **Admin-only publishing** can become a bottleneck (urgent circulars, exam notices) | Name at least 2 to 3 Admins, show approval queue age, and add an "Urgent" flag that notifies Admins |
| 3 | **Single server**, so one failure means downtime | Keep tested backups and a written restore guide; revisit if usage grows |
| 4 | **Admin is a powerful role** (users, drives, applicant data) | Limit to a few trusted people; every action goes to the audit log; review the Admin list each term |
| 5 | Personal data (CGPA, resumes) stored | Define retention (for example delete applications after the placement season) and follow applicable data-protection rules |

---

## 7. Suggested Build Order

1. Server-side roles, permissions and audience filtering.
2. Login hardening (lockout, rate limits, secure sessions, reset flow) and removal of the role dropdown.
3. Approval workflow with audit log and soft delete.
4. Duplicate prevention and server-side eligibility for events and placements.
5. Backups, monitoring and a tested restore.
6. UI fixes: password field, "Posts this month" card, role-based sidebar, status badges for Registered / Applied / Closed.
