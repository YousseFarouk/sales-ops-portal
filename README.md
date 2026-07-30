# Sales Ops Portal

A full-stack field sales visit tracking system built on Google Apps Script and Google Sheets. It replaces manual, paper-based rep visit logging with a GPS-verified, real-time system that gives management live visibility into rep coverage, visit outcomes, and revenue per client.

## Problem

Sales reps were logging client visits manually (paper or messaging apps), which meant:
- No reliable proof a visit actually happened
- No way to see, in real time, how many clients were visited today vs. remaining
- No structured record of *why* a visit didn't happen
- Manual, error-prone tracking of invoice totals per client

## Solution

A lightweight web app backed entirely by Google Sheets (no external database), with:
- A mobile-friendly frontend for reps to log visits with one tap
- GPS capture on every visit, converted into a clickable map link
- Automatic duplicate prevention — logging the same client twice in one day updates the existing record instead of creating a new one
- A management dashboard with live KPIs

## Features

- **Daily client assignment** — each client is auto-assigned to a day of the week; the app shows only today's route
- **GPS-tagged visit logging** — captures latitude/longitude and generates a Google Maps link automatically
- **Visit outcome tracking** — records visited / not visited, with a reason code for missed visits
- **Revenue tracking** — logs invoice value per visit and maintains a running total per client, matched by phone number
- **Client management** — add new clients, edit existing ones, with duplicate-phone validation
- **Auto-updating client records** — a client's last known address/region is updated automatically from field GPS data
- **Management dashboard** — completion rate, visits done/not done, top clients by revenue, unvisited clients, top reasons for missed visits, and full visit history

## Architecture

```
Frontend (HTML/JS)  →  Google Apps Script (doGet API)  →  Google Sheets (data store)
```

- **Backend:** Google Apps Script, exposed as a single web app endpoint with an action-based router
- **Database:** Two Google Sheets — one for client master data, one for visit logs
- **Frontend:** HTML/JS client that calls the API and renders the rep's daily route and the management dashboard

No external server or database is required — the entire system runs on Google's infrastructure, which keeps hosting cost at zero and deployment to a single "Deploy as Web App" step in Apps Script.

## API Reference

All requests go through a single `doGet(e)` endpoint, dispatched by an `action` query parameter.

| Action | Description |
|---|---|
| `getTodayClients` | Returns the list of clients assigned to the current day |
| `getClientDetails` | Returns full details for a given client (category, address, region, invoice history, phone) |
| `updateClientDetails` | Updates a client's name, category, address, or phone |
| `addNewClient` | Adds a new client, rejecting duplicate phone numbers |
| `saveVisit` | Logs a visit (status, GPS coordinates, reason, invoice value); updates instead of duplicating if the client was already logged today |
| `getDashboardData` | Returns aggregated KPIs and visit history for the management dashboard |

## Data Schema

**Clients sheet** (`بيانات العملاء`)
| Column | Field |
|---|---|
| A | Assigned day of week |
| B | Client name |
| C | Category |
| D | Manual address |
| E | Last auto-recorded address |
| F | Region |
| G | Last invoice value |
| H | Total invoices (running sum) |
| I | Phone number (unique key) |

**Visits sheet** (`زيارات المندوبين`)
| Column | Field |
|---|---|
| A | Visit date |
| B | Client name |
| C | Category |
| D | Visit status (yes/no) |
| E, F | GPS latitude, longitude |
| G | Google Maps link |
| H | Reason not visited |
| I | Region |
| J | Detailed address |
| K | Invoice value |
| L | Phone number |

## Tech Stack

Google Apps Script (JavaScript), Google Sheets API, HTML/JS frontend

## Setup

1. Create a Google Sheet with two tabs matching the schema above.
2. Open **Extensions → Apps Script** and paste in `Code.gs`.
3. Update the `VISITS_SHEET` and `CLIENTS_SHEET` constants if your tab names differ.
4. Deploy as a **Web App** (Execute as: Me, Access: Anyone with the link).
5. Point the frontend's API base URL to the deployed web app URL.

## Author

**Youssef Mohamed Farouk**
[GitHub](https://github.com/YousseFarouk) · [LinkedIn](https://www.linkedin.com/in/youssef-mohamed-f)
