# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

`kat-planner.html` is a single-file, mobile-first personal planning app. It has no build system, no dependencies, and no server — open the file directly in a browser or serve it statically. All logic, styles, and markup live in one file.

## Running the app

Open `kat-planner.html` directly in a browser, or serve it locally:

```powershell
# Python (any directory)
python -m http.server 8080
# Then open http://localhost:8080/kat-planner.html
```

On first load, the setup screen prompts for an Airtable Personal Access Token (stored in `localStorage` as `kat-token`). The Base ID is hardcoded as `appgnlShBLca0Xe6Y`.

## Architecture

The entire app is vanilla JS + CSS in one HTML file (~1260 lines). No frameworks, no bundler.

**State** — a single `state` object holds all runtime data. Per-day state (level, priorities, pillars, reflection, custom meetings) is persisted to `localStorage` under the key `kat-day-YYYY-MM-DD`. Airtable is the source of truth for tasks and daily logs.

**Airtable backend** — the `AT` object wraps all API calls to three tables:
- `Tasks` — fields: Name, Status, Priority (Q1–Q4), Area, Estimated Minutes, Actual Minutes, Target Date, Date Added, Person
- `Daily Logs` — fields: Date, Day Level, Mental Focus, Emotional Energy, Physical Energy, Life Fuel, Community, What Worked, What to Change, Notes, Day Rating, Work Minutes
- `Symptoms` — fields: Date, Time, Symptom, Severity, Duration, Impact on day, Possible Trigger, Actions Taken

**Four tabs**: Day, Tasks, Log, Week — each a `<div class="tab-panel">` toggled via `switchTab()`.

**Day Levels** control work-hour ceiling and how many priorities/tasks are shown:
- Level 1 Full: 7.5 hrs, 3 priorities
- Level 2 Moderate: 5 hrs, 3 priorities
- Level 3 Light: 3 hrs, 2 priorities
- Level 4 Survival: 1.5 hrs, 1 priority (Q1 only)

The Decision Matrix modal (`showMatrix()`) suggests a level based on energy, physical state, and deadline answers.

**Standing meetings** are hardcoded in the `STANDING` object (Mon–Fri). Custom meetings are stored in `state.customMeetings` and persisted to localStorage.

**Tasks shown on the Day tab** are filtered to: non-done, non-deferred, with a Target Date ≤ today. Further filtered by Day Level.

## Sensitive files

`Kat Planning_airtable_config.txt` contains a live Airtable PAT token — do not commit this file or include its contents anywhere.
