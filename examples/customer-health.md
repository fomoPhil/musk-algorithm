# Case study: the customer-health system

**Persona:** backend engineer at a 6-person B2B SaaS startup with 40 customers.

**The request:** a customer health system with six components: an event tracking SDK, a warehouse sync, a 12-signal health-score model, a React dashboard, Slack anomaly alerts, and a nightly recompute cron. The assistant also knew two things. The CSM already runs a manual check-in spreadsheet every Monday. And churn keeps getting noticed too late.

**What happened:** it deleted all six components and proposed one SQL query in the spreadsheet the CSM already uses. The full response is below. Unedited.

---

**Verdict: no, not as specced. Six systems, 40 customers, and one human who already does this job every Monday.** Almost all of it gets deleted: the SDK, the warehouse, the 12-signal model, the React dashboard, the Slack alerts, and the cron. What survives is one SQL query against data you already have, pasted into Maria's existing spreadsheet.

The reason I'm not just building it: you have roughly a handful of churn events a year. You cannot fit or validate 12 weights on 5 positive examples. You'd get a model that looks precise and is actually a guess with decimal places, and you'd trust it more than Maria's gut, which is currently your best-performing detector.

| Step | Finding |
|---|---|
| **1. Question every requirement** | The stated problem is "we keep missing churn." Nobody has named the owner or the evidence for a single component. Real questions: which accounts churned, and on what date did you find out versus the date they actually went quiet in existing data? Did Maria flag any of them beforehand? If she did and nothing happened, this is a **response problem, not a detection problem**, and none of the six systems fix it. Also: who chose 12 signals, and against what? "12" has no owner. |
| **2. Try to delete** | Deleted: **event SDK** (you have a Postgres primary with logins, seats, tickets, invoices already, and an SDK is a versioning-and-privacy tail you'll carry forever). **Warehouse sync** (nothing to sync). **Health score** (a scoring layer where a human already reviews the output is the textbook delete: Maria is the review). **React dashboard** (40 rows is a spreadsheet, and she already has one). **Slack anomaly alerts** (no baseline, no history, so early alerts are noise, and noisy Slack alerts permanently train the team to ignore them). **Cron** (see step 5). Survives: three raw facts per account. |
| **3. Simplify what survived** | One query, read replica, no new infra: days since any user last logged in, active seats this week versus contracted seats (and the 4-week direction), days since last human contact. Facts, not a composite score. Ranked by "quietest." Maria pastes it into her Monday sheet. That's the entire build, roughly an hour. |
| **4. Accelerate the cycle** | The loop that matters is signal to save-attempt to outcome, not build speed. Backtest it today: run the query as of 90 days before each of your past churns and see whether those accounts were near the top. If they weren't, the signals are wrong and no amount of dashboard fixes that. If they were, you already had the data and the gap was follow-up. Either answer is worth more than the whole spec. |
| **5. Automate** | Nothing, yet. A cron locks in a definition of "healthy" you haven't validated and makes it invisible once it's wrong. After roughly 8 Mondays of Maria actually using the output and acting on it, schedule it and post to Slack. That's the ~10% I expect to come back, plus maybe one or two extra signals she asks for by name. |

One more thing worth saying out loud: at 40 customers, a CSM calling every account once a month is complete coverage. The tooling case gets strong somewhere north of 150 accounts. You are building for a company you don't have yet.

**Next action:** pull every account that churned in the last 12 months and, for each, write two dates in a sheet: when you found out, and when they actually went quiet in data you already store. Send me that sheet and we'll know whether there's a detection problem to solve at all.
