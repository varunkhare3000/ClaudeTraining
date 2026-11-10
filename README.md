# IT PMO Project Board

[![CI](https://github.com/varunkhare3000/ClaudeTraining/actions/workflows/ci.yml/badge.svg)](https://github.com/varunkhare3000/ClaudeTraining/actions/workflows/ci.yml)

A single-page Kanban board for a fictitious bank's internal IT PMO. It is a demo/training tool: vanilla HTML, CSS and JavaScript in one file, no build step, no dependencies, no real bank branding.

**Live demo:** https://varunkhare3000.github.io/ClaudeTraining/

## Features
- Four columns: Backlog, In Progress, Blocked, Done, each with a live count.
- Drag and drop cards between columns, plus a keyboard-accessible "Move ▸" control.
- Priority colour coding, overdue badges, and inline "Delete? Yes / No" confirmation.
- Add Task form with client-side validation and an optimistic UI.
- Filters by project, assignee and priority; live summary strip.

## Run it
Open `index.html` in a browser. No server needed.

## Data
Board state lives in memory only. Refreshing the page resets it to the seeded demo data (nothing is stored in the browser).

## Email notifications (FormSubmit)
New tasks are sent via [FormSubmit](https://formsubmit.co) from the browser. To use it:
1. Edit `FORMSUBMIT_ENDPOINT` at the top of the script in `index.html` and replace `YOUR_EMAIL@example.com`. Do not commit your real address to a public repository.
2. The first submission triggers a one-time confirmation email to that address. Notifications are delivered only after you click the link in it.

If the call fails, the card still appears and a warning toast is shown.
