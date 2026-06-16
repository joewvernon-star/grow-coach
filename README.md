# AI Grow Coach
**Improving People & Processes**

A Flask web application prototype for an AI-enabled coaching tool targeting Operations Leads in logistics/distribution environments.

---

## Quick Start

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Run the app
python app.py

# 3. Open your browser
#    http://localhost:5000
```

---

## Project Structure

```
grow-coach/
├── app.py                  # Flask backend — routes, scoring logic, session state
├── requirements.txt        # Python dependencies
├── templates/
│   └── index.html          # Single-page HTML shell (Jinja2 template)
└── static/
    ├── css/
    │   └── main.css        # All styling — layout, components, animations
    └── js/
        └── app.js          # Client logic — flow steps, API calls, UI rendering
```

---

## Architecture

### Backend (`app.py`)
- **Flask** with server-side sessions (cookie-based)
- **REST API** — all coach interactions go through `/api/*` endpoints
- **Scoring engine** — deterministic rules map user taps to ranked focus areas
- **Session state** — all user answers persisted in Flask session across page reloads

### Frontend (`static/js/app.js`)
- Three controller objects: `App` (view routing), `Coach` (flow steps), `UI` (DOM utilities), `Dashboard` (data rendering)
- Tap-first, chip-based interaction — minimal free text
- All API calls are `fetch()` to the Flask backend

### API Endpoints

| Method | Path              | Purpose                              |
|--------|-------------------|--------------------------------------|
| GET    | `/api/state`      | Get current session state + scores   |
| POST   | `/api/state`      | Update session state fields          |
| GET    | `/api/focus`      | Get recommended focus (scored)       |
| POST   | `/api/focus/next` | Cycle to next focus option           |
| POST   | `/api/focus/confirm` | Confirm chosen focus               |
| GET    | `/api/dashboard`  | Get mock dashboard data              |
| GET    | `/api/summary`    | Get manager-ready summary            |
| POST   | `/api/reset`      | Clear session, restart flow          |

---

## Flow Stages

| Step | Stage               | What happens                                                      |
|------|---------------------|-------------------------------------------------------------------|
| 0    | Entry               | User selects "Improve my cost per order"                          |
| 1    | Cost trend          | 3 tap options for current CPO direction                           |
| 2    | Pain area           | 5 tap options for biggest cost driver                             |
| 3    | Operation size      | Orders/day + team size (2 quick questions)                        |
| 4    | Coaching culture    | Team lead behaviour check (3 options)                             |
| 5    | Driver check        | 4 mini-surveys: scheduling, errors, flow, initiative              |
| 6    | Focus selection     | Scored recommendation + "show another option" cycle               |
| 7    | Actions this week   | 2 parallel tasks (process + coaching) with branching sub-tasks    |
| 8    | Wrap-up             | Summary + check-in picker + manager summary + dashboard link      |

---

## Scoring Logic

Each of the three focus areas (errors/rework, shift scheduling, flow/bottlenecks) receives a score based on:

- Severity ratings from the driver check
- Stated pain area (adds weight to the matching focus)
- Coaching culture check (low culture → errors focus weighted slightly higher)

The highest-scoring focus is recommended first. "Show another option" cycles through the ranked list.

---

## What's Real vs. Simulated

| Component              | Status      | Notes                                              |
|------------------------|-------------|----------------------------------------------------|
| Coaching flow          | ✅ Real      | All steps, branching, state management             |
| Scoring engine         | ✅ Real      | Deterministic rules in `app.py`                    |
| Session persistence    | ✅ Real      | Flask server-side sessions                         |
| Manager summary        | ✅ Real      | Generated from actual session answers              |
| Dashboard metrics      | 🟡 Simulated | Mock data in `MOCK_DASHBOARD` dict in `app.py`     |
| CPO / coaching charts  | 🟡 Simulated | Static mock arrays, rendered client-side           |
| HRIS integration       | ❌ Not built | Would connect to Workday/SAP etc. in production    |
| LLM-generated text     | ❌ Not built | All rationale text is pre-authored per focus area  |
| User authentication    | ❌ Not built | Alex Laurent is a hardcoded persona for demo       |

---

## Persona & Scenario

**Alex Laurent** — Operations Lead at a mid-sized logistics/distribution site.
- Responsible for 500–2,000 orders/day with 4–8 team leads
- KPI: Cost per order (currently $4.82 vs $4.25 target)
- Career goal: Promotion to Operations Manager in 12–18 months
- Development focus: Building a coaching culture with team leads

---

## Assumptions

1. Single user/persona — no multi-user auth needed for exec demo
2. Session data is lost on server restart (acceptable for demo; would use a DB in production)
3. Mobile layout collapses sidebar; core flow works on tablet+ screens
4. Focus areas limited to 3 (errors, scheduling, flow) — extensible via `FOCUSES` list in `app.py`
