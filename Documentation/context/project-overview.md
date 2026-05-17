# [ S.E.A System ] Project Overview

## Overview

The S.E.A System (Smart Emergency Alert System) is a multi-role emergency response platform designed to connect civilians in distress with the nearest available help in the least time possible. Built across a Flutter mobile app and a MERN web dashboard, the system supports six roles — users, helpers, ambulances, police, firefighters, and admins — each with a distinct, purpose-built experience. At its core is a smart dispatch engine that automatically identifies the nearest responder within a 5 km radius, enforces a 10-minute response window, and escalates intelligently if no one responds — routing medical emergencies to helpers when ambulances are unavailable, and keeping crime and fire incidents strictly within their respective authorities. Users can trigger a silent SOS via their device's side button or an in-app panic button, with a 10-second cancellation window to prevent false dispatches. All incidents are tracked in real time, fully logged, and visible to admins through a live web dashboard that also handles responder approvals and system-wide announcements.



## Goals
Here are the goals for the S.E.A System:

---

### Primary Goals

1. **Reduce emergency response time** — Get the right help to the right person as fast as possible by automating dispatch and eliminating manual coordination.

2. **Make emergency assistance accessible** — Any registered civilian can summon help in seconds, regardless of technical literacy, with as few as 3 taps or a single button press.

3. **Maximize responder utilization** — Ensure no available responder within range is missed, and no responder is overloaded with multiple simultaneous assignments.

4. **Build a smart escalation safety net** — No emergency goes unanswered. If the primary responder can't make it, the system automatically finds the next best option.

5. **Establish accountability at every step** — Every action from alert to resolution is timestamped and logged, creating a transparent, auditable record of every incident.

---

### Secondary Goals

6. **Empower civilians to be first responders** — By bringing helpers (cabs, bikes) into the network, ordinary people with vehicles become part of the emergency response ecosystem.

7. **Give admins full situational awareness** — A real-time dashboard means no incident goes unnoticed and system health is always visible.

8. **Build a foundation for scale** — The architecture should be ready to onboard trusted organizations like Rescue 1122 and Edhi Foundation without a system redesign.

9. **Earn public trust** — Role-based access, strict dispatch rules, and zero data leakage between roles ensures the platform is safe, fair, and trustworthy.

---




Core User Flows

---

### 11.1 User (Civilian) Flow

#### A — Registration & Onboarding
1. User downloads the S.E.A app
2. User opens the app and taps "Sign Up"
3. User enters name, phone number, email, and password
4. Phone number is verified via OTP
5. User is taken to the home screen
6. App requests location permission — user must grant it for SOS to work
7. App requests notification permission

#### B — In-App Panic Button (Medical / Fire / Crime)
1. User feels unsafe or witnesses an emergency
2. User opens the app and taps the panic button
3. User selects emergency type: **Medical**, **Fire**, or **Crime**
4. A 10-second cancellation countdown appears on screen
5. User can tap "Cancel" within 10 seconds to abort — no dispatch occurs
6. If not cancelled, alert is locked and dispatched
7. User sees a status screen: "Help is on the way"
8. User sees live map with responder's location moving toward them
9. User sees estimated arrival time (ETA) updating in real time
10. Responder arrives — user sees "Responder has arrived" notification
11. Incident is closed (by responder or admin — TBD)
12. User receives an incident summary notification

#### C — Silent SOS (Physical Side Button)
1. User is in a dangerous situation (e.g. robbery in progress)
2. User presses the physical side button on their device
3. No sound, no visible screen change — fully discreet
4. A 10-second silent cancellation window begins (no visible UI)
5. If not cancelled, alert is silently dispatched to nearest police mobile unit
6. User's live GPS location is shared with police
7. In the background, the app shows a minimal "SOS Sent" indicator if user checks their screen

#### D — Cancellation
1. User triggers SOS (either mode)
2. Within 10 seconds, user taps "Cancel" (in-app) or re-presses side button (silent SOS)
3. Alert is aborted — no responder is notified
4. Cancellation is logged silently in the system

---

### 11.2 Responder Flow (Ambulance / Police / Firefighter)

#### A — Registration & Onboarding
1. Responder organization downloads the S.E.A app
2. Taps "Register as Responder" and selects type: Ambulance / Police / Fire
3. Fills in organization name, unit ID, contact details, and service area
4. Submits registration — account is marked **Pending**
5. Admin reviews and approves the registration
6. Responder receives approval notification and can now log in
7. App requests location permission (mandatory) and notification permission

#### B — Going On Duty
1. Responder logs in
2. Responder toggles availability to **Available**
3. Responder's live location is now tracked by the system
4. Responder is now eligible to receive alerts within their 5 km radius

#### C — Receiving & Responding to an Alert
1. An emergency is triggered by a nearby user
2. Responder receives a push notification with alert type and distance
3. Responder opens the app — sees incident details and user location on map
4. Responder has **10 minutes** to respond
5. Responder taps **Accept**
6. System confirms assignment — other responders for this incident are notified
7. Responder sees:
   - Live map of user's location (updates in real time)
   - Location of nearest hospital (medical only)
   - Other responders also en route to the same incident
8. Responder navigates to user
9. Responder arrives on scene — taps **Mark Arrived**
10. Incident handled — responder taps **Resolve Incident** (TBD — see Open Items)
11. Responder is freed up and returns to Available status

#### D — Declining or Not Responding
1. Responder receives alert
2. Responder taps **Decline** — system immediately searches for next nearest responder
3. Or responder does not respond within 10 minutes — system auto-escalates
4. Responder remains in their current availability state

#### E — Going Off Duty
1. Responder toggles availability to **Unavailable**
2. Responder no longer receives dispatch assignments
3. Responder still receives **area notifications** if an incident occurs nearby (informational only)

---

### 11.3 Helper Flow (Cab / Bike / Public Transport Volunteer)

#### A — Registration & Onboarding
1. Helper downloads the S.E.A app
2. Taps "Register as Helper"
3. Fills in name, vehicle type, vehicle registration number, phone number
4. Submits registration — account marked **Pending**
5. Admin reviews and approves the registration
6. Helper receives approval notification and can now log in

#### B — Going Online
1. Helper logs in
2. Toggles status to **Available**
3. Helper's live location is now visible to the dispatch system
4. Helper is eligible to receive medical emergency alerts only (not crime or fire)

#### C — Receiving & Responding to an Alert
1. A medical emergency occurs and no ambulance is available
2. Helper receives a push notification with incident location and distance
3. Helper opens the app — sees user location and nearest hospital on map
4. Helper has **10 minutes** to accept or decline
5. Helper taps **Accept**
6. Helper sees:
   - Live map of user's location (updates in real time)
   - Location of nearest hospital
   - Any ambulance or other responder also en route (for coordination)
7. Helper navigates to user, assists with transport to hospital
8. Incident resolved — helper taps **Mark Complete**

#### D — Declining
1. Helper receives alert and taps **Decline**
2. System moves to next nearest available helper
3. Helper remains online and available for future alerts

#### E — Going Offline
1. Helper toggles status to **Unavailable**
2. Helper no longer receives dispatch alerts
3. Helper does not receive area notifications (unlike ambulance/fire)

---

### 11.4 Admin Flow (Web Dashboard)

#### A — Login
1. Admin navigates to the S.E.A web dashboard
2. Logs in with admin credentials
3. Lands on the main dashboard — overview of active incidents, responder statuses, and recent activity

#### B — Approving Registrations
1. Admin sees a pending registrations list (helpers, ambulances, police, fire, orgs)
2. Admin reviews submitted details for each applicant
3. Admin taps **Approve** or **Reject**
4. Applicant receives a push notification with the decision
5. Approved accounts go live immediately

#### C — Posting a Live Announcement
1. Admin taps "New Announcement"
2. Admin types the announcement (e.g. flood warning, road closure, emergency advisory)
3. Admin selects target audience: All Users / Responders Only / Helpers Only / Everyone
4. Admin publishes — notification is sent instantly to all targeted users
5. Announcement appears as a banner in the app for all targeted roles

#### D — Monitoring Active Incidents
1. Admin sees all active incidents on a live map
2. Each incident shows: type, user location, assigned responder, elapsed time, status
3. Admin can view full incident detail — timeline of events, responder movements
4. Admin can intervene manually if needed (e.g. force-reassign a responder — TBD)

#### E — Viewing Incident Logs
1. Admin navigates to Logs section
2. Filters by date, incident type, responder, or status
3. Views full incident record for any past incident
4. Can export logs for reporting (TBD — format to be decided)

---

1. Admin logs in via the web dashboard with elevated credentials
2. System loads the admin overview — live gate feed, active alerts, open reports, and daily statistics
3. Admin manages users — creates, edits, or deactivates accounts for guards, students, and staff
4. Admin pre-registers visitors on behalf of hosts and monitors all active visitor passes
5. Admin reviews and updates suspicious activity reports — changes status from open to investigating to resolved
6. Admin monitors the lost and found board and manually confirms matches if auto-match is uncertain
7. Admin broadcasts a live announcement — types message, selects severity level (info, warning, emergency), and publishes
8. In an emergency, admin receives the panic alert with full details and coordinates response from the dashboard
9. Admin exports gate logs or incident reports to CSV for record keeping
10. Admin logs out or stays on session for continuous monitoring



---

### User (Civilian)
- Register & login with OTP phone verification
- In-app panic button with emergency type selection (Medical / Fire / Crime)
- Silent SOS via physical side button
- 10-second cancellation window for both alert modes
- Live map tracking of incoming responder
- Real-time ETA updates
- "Responder arrived" notification
- Incident summary after resolution
- Location permission & background GPS

---

### Responder (Ambulance / Police / Firefighter)
- Register with unit details — admin approval required
- Availability toggle (Available / Unavailable)
- Push notification on new alert within 5 km
- Accept or decline incoming alert
- 10-minute response window before auto-escalation
- Live map of user's location (real-time updates)
- Nearest hospital pin (medical incidents only)
- See other responders en route to same incident
- Area notification even when unavailable
- Mark arrived & resolve incident

---

### Helper (Cab / Bike / Volunteer Transport)
- Register with vehicle details — admin approval required
- Availability toggle
- Receives medical emergency alerts only (never crime or fire)
- Accept or decline with 10-minute window
- Live map of user location + nearest hospital
- See other responders en route (coordination)
- Mark complete on resolution
- No area notifications when offline (unlike ambulances/fire)

---

### Admin (Web Dashboard — MERN)
- Login with admin credentials
- Approve or reject registrations (all role types)
- Post live announcements with audience targeting
- Live incident map — all active incidents at once
- Full incident detail view with responder timeline
- Incident logs with filtering (date / type / responder / status)
- System-wide responder availability overview

---

### System / Backend (Cross-cutting)
- 5 km radius search for nearest responder
- Conflict prevention — no double-assignment of same responder
- Auto-escalation chain per incident type (medical → fire → crime)
- Full incident logging (every action timestamped)
- Real-time location tracking during active incidents
- Push notifications via FCM
- Role-based access control (RBAC)
- Trusted org registration flow (future-ready)





## Scope

Based on everything we've confirmed, here's what's **in scope**:

---

### In Scope

**Platform**
- Flutter mobile app (all roles except admin)
- MERN web app (admin dashboard only)

**Authentication**
- Role-based login (User, Helper, Ambulance, Police, Fire, Admin)
- OTP phone verification for users
- Admin approval flow for helpers & responders

**Alert System**
- In-app panic button with 3 emergency types
- Silent SOS via physical side button
- 10-second cancellation window
- Push notifications via FCM

**Dispatch Engine**
- 5 km radius search
- 10-minute response timer with auto-escalation
- Conflict prevention (no double-assignment)
- Role-restricted dispatch (helpers excluded from crime & fire)

**Live Tracking**
- Real-time GPS tracking during active incidents
- Responder sees user location live
- User sees responder location live
- Multi-responder visibility on same incident

**Availability System**
- Toggle for ambulances, fire, helpers
- Area notifications for unavailable responders (ambulance & fire only)

**Admin Dashboard**
- Registration approvals
- Live incident map
- Announcements with audience targeting
- Incident logs with filtering

**Data & Logging**
- Full incident lifecycle logging
- Timestamped records for every action

---

### Explicitly Out of Scope (for now)

- Suspicious activity reports — deferred
- Offline / weak signal fallback — deferred
- SMS fallback for SOS — deferred
- False alarm penalty system — deferred
- Incident closure rules — deferred (stakeholder decision)
- Crime escalation timer — deferred
- Radius auto-expansion logic — deferred
- Helper rating / trust score — deferred
- Trusted org onboarding (Rescue 1122, Edhi) — future release
- Log export / reporting formats — deferred
- Admin force-reassign responder — deferred
- In-app chat or calling between user and responder — not planned

---


## Success Criteria

Here are the success criteria for the S.E.A System:

---

### Technical Success Criteria

**Alert & Dispatch**
- SOS alert is dispatched within **3 seconds** of the 10-second cancellation window ending
- Nearest responder within 5 km is correctly identified 100% of the time
- Auto-escalation triggers accurately at the **10-minute mark** — no early, no late
- Double-assignment of the same responder never occurs
- Silent SOS reaches police without any visible UI change on the user's device

**Real-time Tracking**
- Responder and user locations update with a maximum delay of **2 seconds**
- Live map remains stable and accurate throughout the entire incident
- ETA recalculates correctly as the responder moves

**Notifications**
- Push notifications delivered within **5 seconds** of dispatch
- Area notifications reach unavailable responders reliably
- Announcements from admin reach all targeted users within **10 seconds**

**System Reliability**
- App handles **simultaneous incidents** without cross-assignment or data leakage between incidents
- No incident is silently dropped — every alert either reaches a responder or escalates
- All incident data is logged with correct timestamps and zero data loss

---

### Product Success Criteria

**User Side**
- A civilian can trigger an SOS and see a responder on the map within **60 seconds**
- Cancellation within 10 seconds results in zero dispatch — every time
- A user with no technical knowledge can trigger an SOS in under **3 taps**

**Responder Side**
- A responder can accept an alert and begin navigation in under **5 taps**
- Availability toggle reflects in the system in **real time**
- Responders can clearly distinguish incident type, user location, and hospital location on one screen

**Admin Side**
- Admin can approve or reject a registration in under **30 seconds**
- All active incidents are visible on the dashboard live map with no refresh needed
- Admin can post a system-wide announcement in under **1 minute**

---

### Business Success Criteria

- Every registered and approved responder receives alerts relevant to their role — no missed dispatches
- No helper is ever dispatched to a crime or fire incident
- Every incident has a complete, unbroken log from trigger to resolution
- Admin has full visibility of every active incident at all times
- Zero unauthorized access across roles — a user cannot see responder management, a helper cannot see police dispatches, etc.

---

### Out of Scope for Success Measurement (Deferred)
- Response time SLAs for police (escalation timer not yet defined)
- False alarm rate thresholds (policy not yet defined)
- Helper reliability scoring (not yet in scope)

---

Want these added to the business logic document, or keep it separate as a dedicated success criteria doc?

- Every registered student and staff member can be scanned and logged at a gate in under 3 seconds from camera open to confirmation
- Visitor QR passes are generated, delivered, and validated end-to-end without any manual intervention from security personnel
- A panic alert triggered by any guard or admin reaches all connected users within 5 seconds regardless of how many users are online simultaneously
- Live announcements broadcast by an admin appear on all active dashboards and mobile screens within 3 seconds of being published
- The gate log captures 100% of scan events with no missing entries even under simultaneous multi-gate scanning activity
- Suspicious activity reports submitted by guards are received and visible to admins in real time with no manual refresh required
- Lost and found auto-matching correctly identifies and notifies owners for clear description matches without admin intervention
- Role-based access is strictly enforced — no user can access a route, screen, or data point outside their assigned permission level under any circumstance
- The Flutter mobile app functions as a fully capable field tool — scanning, reporting, and receiving alerts — without requiring a desktop fallback
- The admin dashboard loads a live operational overview with current gate activity, open reports, and active alerts within 2 seconds of login
- Offline QR scanning on the mobile app queues and syncs all missed entries accurately upon reconnection with zero data loss
- The system handles concurrent users across web and mobile simultaneously without performance degradation or data inconsistency
- All panic events, gate logs, and incident reports are permanently stored and retrievable by administrators at any point after the fact
- A non-technical administrator can create a user account, register a visitor, and broadcast an announcement without any training or documentation



## Must Discuss Before Development
With Stakeholders

Who marks an incident as resolved?
What is the false alarm policy?

Does police dispatch have a 10-minute escalation timer?
