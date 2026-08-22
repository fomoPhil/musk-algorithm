# Case study: the AI-drafted microservice design doc

**Persona:** backend engineer at a 200-person company, presenting a proposal at sprint planning in two days.

**The request:** an AI-drafted design doc for extracting notifications from the monolith, with seven components: a standalone microservice, a Kafka event bus, a template rendering service, a user preference center API, a provider rate limiter, delivery analytics into the warehouse, and a support admin dashboard. The ask: "review the design and tighten it up before I present it."

**Context the assistant knew:** the monolith sends email and push fine today. The incident that started all this was one bad deploy where a migration locked the notifications table for 20 minutes. Marketing has asked for copy edits twice this year.

**What happened:** the assistant, running this skill, refused to tighten the doc. It deleted all seven components, traced the real problem to migration practice, and proposed a one-page fix. Below is its actual response, unedited.

---

**Bottom line: the design doc is a comprehensive answer to a question nobody asked.** One deploy locked one table for 20 minutes. Nothing in that incident points to a microservice, and six of the seven components have no owner and no evidence behind them. What should exist is a migration safety fix (roughly a one-page proposal), and the strongest thing you can bring Thursday is the deletion list, not the architecture.

**The uncomfortable part first:** extracting notifications into a separate service does not fix your incident. A migration that locks a table locks it wherever that table lives. You would carry the same failure mode into a new service, plus dual writes, deploy skew, and a Kafka consumer to keep alive at 3am. That is optimizing a thing that should not exist.

## Requirement audit (bring this table Thursday)

| Component | Named owner | Observed evidence | Verdict |
|---|---|---|---|
| Standalone notifications service | AI draft | None. Email and push send fine today | **Delete.** Does not address the lock |
| Kafka event bus | AI draft | None. Platform team owns the cluster and has not been asked | **Delete.** New topic, consumer, ordering, dedupe, dead-letter, on-call |
| Template rendering service | Marketing (implied) | Two copy edits in a year | **Delete.** A service for two edits per year |
| User preference center API | Nobody | None | **Delete.** Most likely to legitimately come back (see below) |
| Provider rate limiter | Nobody | No throttling incident cited. Providers already rate limit | **Delete** until a provider actually 429s you |
| Delivery analytics to warehouse | Nobody | None | **Delete.** Instrumentation for an unproven system |
| Support admin dashboard | Nobody (not support) | None. Your provider dashboard already shows delivery status | **Delete.** Ask support if they ever asked |

Only one requirement survives contact with the incident, and it was never in the doc: **notification sends must degrade, not disappear, when a migration takes a lock.**

## The five steps

| Step | Finding |
|---|---|
| 1. Question requirements | Real problem, one sentence: a migration held a lock on the notifications table and users got nothing for 20 minutes. Six of seven components trace to the AI draft or an assumption, not to a person with an observation. |
| 2. Delete | All seven components deleted. What survives is migration practice, which is a repo-wide problem, not a notifications problem. Your next migration on any hot table does this again. |
| 3. Simplify | The actual fix: set `lock_timeout` on migrations so a blocked migration fails fast instead of holding the line, build indexes concurrently, use expand-then-contract for column changes, and give the background job retry with a backlog drain so a lock delays sends instead of dropping them. Small, boring, protects every table you own. |
| 4. Accelerate | Prove it, do not argue it. Reproduce the incident on staging (hold a lock on that table for 20 minutes), then show sends draining afterward instead of vanishing. That demo is worth more Thursday than any diagram. |
| 5. Automate | Not yet. Review migrations by hand for a few sprints. Once you have caught three or four risky ones manually, then write the CI check. A lint rule written today locks in guesses about what "risky" means. |

**The 10% that probably comes back:** the preference center, but only if marketing starts sending real campaigns (unsubscribe handling is a legal obligation, not a nice-to-have). Park it with that trigger written down. Everything else needs a person and an incident before it earns a line of code.

**A note on the politics:** proposing seven components reads as thorough. Proposing one fix plus a documented list of six things you considered and rejected reads as senior. Your tech lead can approve the second one in the meeting. The first one becomes a quarter of work and a new on-call rotation for a problem you could have closed this sprint.

**Your one next action:** rewrite the doc as a one-pager titled "Migration lock safety for notifications," with the fix in step 3 as the proposal and the audit table above as an appendix of explicit deletions. If you want, paste me the current doc and I will do that rewrite with you.
