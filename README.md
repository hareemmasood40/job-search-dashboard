# Job Search Dashboard

A Power BI dashboard tracking my own UK Data/AI job search — built on real data from my
application tracker, not a sample dataset.

## Why this project
Most portfolio dashboards use the same handful of public datasets (sales, retail, movies).
This one uses something I actually understand inside out: my own job search. It's also
genuinely useful — I update the source data every time I apply somewhere, so the dashboard
stays current.

## What it shows
- **Applications by Status** — how many applications are Applied, Rejected, Interview
  Scheduled, etc.
- **Applications over Time** — when I actually applied, so I can see the pace of my search
- **Response Rate %** — a DAX measure showing what percentage of my outreach messages got
  any kind of response

![Job search dashboard showing applications by status, over time, and outreach response rate](dashboard_screenshot.png)

## Tools
Power BI Desktop, Power Query (for cleaning the source data), DAX (for custom measures)

## How it works
1. Connects directly to `06_applications/application_tracker.xlsx` (Applications and
   Outreach sheets)
2. Power Query cleans the data on load — the biggest fix needed was converting date columns
   from raw Excel serial numbers (e.g. `46291`) into actual dates
3. Two DAX measures do the real calculation work rather than relying on Power BI's automatic
   aggregation:
   ```
   Total Applications = COUNTROWS(Applications)

   Response Rate % = DIVIDE(
       CALCULATE(COUNTROWS(Outreach), Outreach[Reply?] <> "Waiting"),
       COUNTROWS(Outreach)
   )
   ```
   `Response Rate %` filters the Outreach table down to messages that got any response,
   counts them, and safely divides by the total sent — `DIVIDE` returns a blank instead of
   an error if there's ever nothing to divide by.
4. Visuals are built from the cleaned tables and measures, and refresh automatically from
   the source file

## A real problem I ran into and fixed
Excel dates often import into Power BI as plain numbers rather than dates, because of how
Excel stores dates internally. I had to manually set the correct data type on each date
column in Power Query before the visuals could use them properly — a good reminder that
raw data rarely arrives ready to use.

## What I'd do next
- Build a proper data model: relate the Applications and Outreach tables so they can be
  filtered together, rather than treating them as two separate tables
- Add an application-side response rate too (percentage of applications that got any reply,
  not just outreach)
- Refresh this as my application count grows, to see real trends over a longer period
