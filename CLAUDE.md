# SCO Monitoring Dashboard

## Project Overview

A single-file HTML prototype for monitoring Self-Checkout (SCO) machines across retail stores.
Built for internal demos — no build tools, no backend, no external libraries required.

Live URL: https://ypoons0602-dotcom.github.io/SCO-Monitoring-Dashboard/
GitHub: https://github.com/ypoons0602-dotcom/SCO-Monitoring-Dashboard

---

## File Structure

```
monitoring-prototype/
└── index.html     # Entire application — HTML + CSS + JavaScript in one file
```

Everything lives in `index.html`. There are no dependencies, config files, or build steps.

---

## How to Open and Test

**Locally:**
```bash
open index.html
```
Or drag `index.html` into any browser.

**Via GitHub Pages (shareable link):**
```
https://ypoons0602-dotcom.github.io/SCO-Monitoring-Dashboard/
```

**To deploy updates:**
```bash
git add index.html
git commit -m "your message"
git push
```
GitHub Pages auto-deploys within ~1 minute after push.

---

## Key Features and Components

### 1. Store Overview
- Displays 2 stores: **Store A** and **Store B**
- Each store has a colored status indicator:
  - Green — all SCOs operational
  - Yellow — 1 SCO in alert
  - Red — 2+ SCOs in alert
- Clicking a store card switches the detail view below

### 2. Store Detail
- Shows 3 SCO rows per store
- Each row displays:
  - **Overall** status dot (green / red)
  - Individual issue flag dots: **No Transaction**, **No Stream**, **No AI Event**, **No Popup**
- **Simulate Failure** button — randomly activates one issue flag, creates an incident
- **Recover** button — clears all flags, resolves open incidents

### 3. Floating Alert (top-right)
- Triggered whenever `Simulate Failure` is clicked
- Shows: Store, SCO, Issue type, Incident ID
- Auto-dismisses after 8 seconds; also has a manual close button
- Multiple alerts stack vertically

### 4. Client Email Notification (UI simulation only)
- Triggered when the issue type is one of: `No Transaction`, `No AI Event`, `No Popup`
- Shows a centered **Email Preview Modal** with full subject + body
- On **Recover**, shows a second modal with a resolution email
- No real emails are sent — purely visual simulation

### 5. Incident Log
- Auto-populated on every `Simulate Failure`
- Columns: Incident ID, Store, SCO, Issue, Status, Create Time, Last Updated, Client Email Sent
- Status badge: **Open** (red) → **Resolved** (green) after recovery
- Email column: **Yes** (blue) or **Internal Only** (grey)

---

## Architecture Notes

### State
All application state lives in a single `state` object in JavaScript:
```
state.stores         — SCO flag data per store
state.incidents      — incident log entries
state.selectedStore  — currently displayed store
state.nextIncNum     — auto-incrementing incident ID counter
```

### Key Constants
```
ISSUE_TYPES           — ['No Transaction', 'No Stream', 'No AI Event', 'No Popup']
STORE_NAMES           — ['Store A', 'Store B']
SCO_IDS               — ['SCO-1', 'SCO-2', 'SCO-3']
EMAIL_TRIGGER_ISSUES  — subset of ISSUE_TYPES that trigger client email simulation
```

### Render Pattern
The UI is fully re-rendered on every state change by calling `render()`, which calls:
- `renderStoreGrid()`
- `renderStoreDetail()`
- `renderIncidentLog()`

Alert and email modals are injected into the DOM independently (not part of `render()`).

---

## Future Development Guidelines

### Adding a New Store
Add the store name to `STORE_NAMES`:
```js
const STORE_NAMES = ['Store A', 'Store B', 'Store C'];
```

### Adding a New SCO per Store
Add the ID to `SCO_IDS`:
```js
const SCO_IDS = ['SCO-1', 'SCO-2', 'SCO-3', 'SCO-4'];
```

### Adding a New Issue Type
Add to `ISSUE_TYPES`. If it should trigger client email, also add to `EMAIL_TRIGGER_ISSUES`:
```js
const ISSUE_TYPES = ['No Transaction', 'No Stream', 'No AI Event', 'No Popup', 'Hardware Error'];
const EMAIL_TRIGGER_ISSUES = new Set(['No Transaction', 'No AI Event', 'No Popup', 'Hardware Error']);
```

### General Guidelines
- Keep everything in `index.html` — do not split into separate files unless the project graduates to a proper build system
- Do not introduce external libraries (React, Vue, Chart.js, etc.) without intentional scope change
- All new UI sections should follow the existing `.card` + `.card-title` pattern
- Status logic (green / yellow / red) is derived, not stored — always compute from flags, never hardcode
- The `render()` function is the single source of truth for the DOM — avoid direct DOM mutations outside of alerts and modals
