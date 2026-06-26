# Eurowings · Project Management Cockpit

A professional, **multi-project** project-management cockpit in Eurowings branding —
the **Eurowings Project Management Cockpit · Trainings Department (TD)**. A general
multi-project tool that works for any kind of training or project.

The whole app is a single self-contained file: **`index.html`**.

> **No build, no server, no install.** Just open `index.html` in any modern browser
> (laptop or iPad), or use the hosted version on GitHub Pages. Everything runs in the
> browser; the interface is in English.

**Live:** `https://thornapplication-coder.github.io/Eurowings-Project-Management/`

---

## Features

- **Portfolio home page** — every project as a status card (RAG health, progress ring,
  open / critical / milestone / task counts, status mini-bar, target date) plus
  portfolio-level KPIs. Create / rename / delete projects.
- **Project templates** — Blank, New Aircraft Type – Entry Into Service, Differences /
  Conversion Training, Recurrent / Refresher Cycle, ATO / Authority Approval and Generic
  Phased Project. Each template pre-fills workstreams plus a skeleton of tasks,
  dependencies and milestones (dates relative to today), with a **Review & edit** step.
- **Per-project workspace** — independent Dashboard, Tasks table, Gantt timeline,
  To-Do checklists and Report for each project.
- **Master task list** — 20 editable columns; add / duplicate / delete rows.
- **Full status set, live statistics, status automation, critical path, milestones.**
- **One-page status reports** (project + portfolio), print-ready.
- **Import** existing Excel/CSV lists and **export** to multi-sheet `.xlsx` / CSV / PDF.
- **Backup & restore** the whole portfolio as JSON.
- **Responsive** for laptops and iPad.

## Cloud sync — one login, all devices (optional)

Open the app and **sign in** (email + password) to sync your portfolio automatically and
in real time across all your devices under a single account.

- Uses a **Supabase** backend; the whole portfolio is stored as one row in the `kv` table,
  protected by Row-Level Security per user.
- The Supabase URL + anon key are embedded in `index.html`.

### Supabase setup (once)

1. In your Supabase project: **SQL Editor** → paste the contents of `supabase-setup.sql` → **RUN**
   (creates the `kv` table, RLS policies and realtime).
2. **Authentication → Providers → Email** → turn off *Confirm email* so sign-in works
   without a confirmation mail.

Without signing in, data is stored **locally in the browser** (`localStorage`); use
**Backup (JSON)** / **Restore** to move a portfolio between devices.

---

## Deployment

GitHub Actions publishes `index.html` to **GitHub Pages** automatically on every push to
`main` (`.github/workflows/deploy.yml`). To enable it once:
**Settings → Pages → Source: "GitHub Actions"**.

## Quick start

1. Open `index.html` (double-click) — or visit the hosted URL above.
2. On the **Portfolio** page, open a project or create a new one.
3. Edit tasks in the **Tasks** view; watch the Dashboard / Gantt update live.
4. Generate a **Report**, **Export** to Excel/PDF, or **Import** an existing Excel list.
