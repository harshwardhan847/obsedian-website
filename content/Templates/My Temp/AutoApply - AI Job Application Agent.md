# PRD — AutoApply: AI Job-Application Agent (SaaS)

## 1. One-line summary

A SaaS web app where a job seeker connects their Gmail, uploads a base resume, defines the roles they want, and an AI agent discovers matching LinkedIn jobs/posts daily, scores each against the user's profile, and — after the user approves — sends a personalised, human-feeling application email with an ATS-optimised resume attached, all tracked on an analytics dashboard.

The product is **human-in-the-loop by design**: the agent does discovery, scoring, drafting, and preparation automatically, but a human approves before anything is sent.

---

## 2. Problem & target user

**Target user:** Active job seekers (India-first: freshers, early-career, and mid-career switchers), plus students using Internshala-type sources later.

**Core problem:** Applying is repetitive and slow. Finding the right jobs, tailoring a resume for each one to pass ATS keyword filters, finding the right person to email, and writing a non-generic message — done 30–100 times a month — is exhausting and error-prone. People give up or spray identical resumes that never get read.

**What we do:** Compress the discover → tailor → find contact → write → send loop from ~20 minutes per application to one approval tap, while keeping quality high enough that each email reads like a human wrote it individually.

---

## 3. Scope decisions (locked)

|Decision|Choice|
|---|---|
|Outreach channel|**Channel A only** — cold email to a discovered address. (No ATS-form auto-apply in v1.)|
|Sources v1|**LinkedIn jobs + "we're hiring" posts** via Apify. Internshala and others added later behind a pluggable source layer.|
|Automation model|**Human-in-the-loop.** Auto: discover → score → draft. Manual: user approves, then send fires. Never silent full-auto in v1.|
|Resume input|User **uploads a base resume**; we parse it into a structured profile. User can also fill/edit fields manually.|
|Resume library|User stores **2–5 prebuilt resume variants**. Agent reuses the best-matching variant when it already fits; only tailors when needed.|
|Tailoring depth|**Keyword optimisation / reordering** against the JD (not a full rewrite). ATS-safe.|
|Output format|**PDF** (single-column, ATS-parseable, no tables/multi-column).|
|Email body|**Agent-generated** cover note using the user's written instructions/voice.|
|Match scoring|**Both** keyword filters _and_ semantic (embedding) match of JD vs profile, combined into one score.|
|Sending|**Gmail API via Google OAuth** (not SMTP). Conservative caps, throttling, "feels human."|
|Analytics|Full dashboard: discovered, scored, approved, sent, delivered, bounced, replied, trends. Multiple tabs.|
|Monetisation|Free tier + paid tiers via **Razorpay** (Stripe later).|
|Tone|Must **feel human**, not like cold outreach or automation.|

**Explicitly out of scope for v1:** ATS-form submission, LinkedIn DMs/InMail, WhatsApp outreach, interview scheduling, multi-language emails, team/recruiter accounts.

---

## 4. The pipeline (core user flow)

```
1. DISCOVER   →  Apify pulls matching LinkedIn jobs + hiring posts (daily cron or manual run)
2. SCORE      →  Each item scored 0–100 (keyword + semantic) vs user profile & preferences
3. QUEUE      →  Scored items land in the "Review" tab, ranked by fit
4. APPROVE    →  User approves the ones they want (bulk-select or one at a time)
5. PREPARE    →  For each approved item: pick best resume variant → keyword-tailor if needed →
                 find recipient email → generate personalised email body
6. FINAL OK   →  User sees the drafted email + attached resume; edits if wanted; hits Send
7. SEND       →  Queued into the throttled sender; goes out from user's Gmail with jitter
8. TRACK      →  Delivery, bounce, and reply status update on the dashboard
```

Steps 1–3 and 5 run automatically. Steps 4 and 6 are the human gates. (v1.1 option: let users pre-authorise auto-send above a score threshold, still with daily caps.)

---

## 5. Feature specification

### 5.1 Onboarding & profile

- Sign up (Clerk) → connect Google account (OAuth, Gmail send scope) → upload base resume.
- **Resume parsing:** PDF/DOCX → structured profile (name, contact, summary, experience, education, skills, projects, links). Parsed via an LLM extraction pass into a typed schema.
- User reviews/edits every parsed field. Manual entry supported for users without a resume.
- Profile completeness meter (drives match quality).

### 5.2 Resume library (2–5 variants)

- User can store up to 5 named resume variants (e.g. "Frontend", "Data Analyst", "Backend").
- Each variant has its own emphasis (skills order, summary, highlighted projects).
- When preparing an application, the agent **first checks if an existing variant already matches the JD well** (≥ a similarity threshold). If yes, reuse as-is (zero generation cost). If no, run keyword-tailoring on the closest variant.

### 5.3 Job & post discovery

- **Sources (pluggable):** Apify LinkedIn Jobs actor + LinkedIn Posts actor (keyword/hashtag search for hiring posts). Source adapters normalise output to a common `DiscoveredItem` schema so Internshala etc. slot in later.
- **User targeting inputs:** role/title keywords, seniority, location(s), remote/hybrid/onsite, must-have skills, exclude keywords, min score to surface.
- **No user LinkedIn login/cookies ever used** (account-ban risk). Cookieless Apify actors + residential proxies only.
- **De-duplication:** by company + role + posting URL, so the same job discovered on multiple days isn't re-surfaced or re-applied.

### 5.4 Match scoring engine

- **Stage 1 — cheap semantic filter:** embed the JD and compare to the user's profile embedding (cosine similarity) to drop obvious non-fits before any LLM call.
- **Stage 2 — keyword coverage:** % of JD must-have/skill keywords present in the profile/resume.
- **Stage 3 — LLM judgment (cheap model):** a scoring call returns a 0–100 fit score + a one-line "why" + a list of matched/missing keywords.
- Final score = weighted blend (semantic + keyword + LLM). Shown with a short rationale so the user understands _why_ it's a good fit.
- Batch-scored so a day's discoveries are processed together.

### 5.5 Review / approve queue

- "Review" tab lists scored items, sorted by fit, with: company, role, score, rationale, matched/missing keywords, source link, discovered date.
- Actions: **Approve**, **Skip**, **Save for later**. Bulk-select supported.
- Approving triggers the Prepare step (5.6) in the background.

### 5.6 Application preparation

- **Resume selection/tailoring** per 5.2.
- **Email discovery:** given the person/company, resolve a recipient address. Primary path: Apify actors that bundle email discovery (pattern-matching + Hunter.io fallback). Store a **confidence score**; low-confidence addresses are flagged and can be held back or user-confirmed. Hit rates are realistically 20–60%; items with no findable email are surfaced as "no contact found" rather than sent to a guess.
- **Email generation:** agent writes a personalised body using the user's saved voice/instructions, the JD, and the resume highlights — short, specific, referencing the role and one concrete reason of fit. No spammy template feel.

### 5.7 Final review & send

- User sees the full drafted email + rendered resume PDF, can inline-edit the body, then **Send**.
- Send is **queued**, not immediate — the sender applies throttling and jitter (see 5.9).

### 5.8 Gmail sending

- Send via **Gmail API** using the user's OAuth refresh token (stored encrypted). Attachment = tailored resume PDF.
- Threading, real "From" = the user, real signature. Replies land in the user's actual inbox.

### 5.9 Deliverability & "feels human" (critical — this is what makes or breaks the product)

This directly addresses the "must not look like cold outreach" requirement.

- **Conservative daily caps per user**, ramping up over time (start ~15–25/day, warm up gradually). Never blast.
- **Throttle + jitter:** randomised gaps between sends; respect working hours in the user's timezone; no perfectly-even timing.
- **No tracking pixels / no open-tracking** in v1 — open pixels are a top spam signal and hurt deliverability. Reply detection is done by reading the thread, which is safe.
- **Per-recipient personalisation** enforced (the email references the specific role/company; identical bodies are blocked).
- **Plain, human formatting** — no bulk-email HTML, no unsubscribe-footer look unless legally required for the user's region.
- **Domain reputation is the user's own Gmail**, so we protect it by keeping volume low and content high-quality.

### 5.10 Dashboard & analytics (multi-tab)

- **Overview:** funnel (discovered → scored → approved → sent → delivered → replied), this-week trends, reply rate.
- **Jobs:** every discovered item with status and score, filterable.
- **Applications:** every sent application, its resume variant, email body, delivery/bounce/reply state.
- **Resumes:** variants, usage counts, avg score when used.
- **Deliverability:** sent vs bounced, daily cap usage, any Gmail errors.
- **Account/Billing:** plan, quota used, connected accounts.

### 5.11 Automation / scheduling

- **Daily cron per user** (Convex scheduled functions): runs discovery + scoring, then notifies the user "N new matches ready to review."
- User controls: on/off, run time, sources, daily discovery volume.
- Sending is **never** auto in v1 (human gate). v1.1: optional auto-send above a score threshold, hard-capped by the daily limit.

### 5.12 Billing (Razorpay)

- Plans, quotas, and metering (applications/month + jobs scanned/month). Razorpay Subscriptions for recurring; webhook updates plan state in Convex.
- Quota enforcement: soft warning at 80%, block + upsell at 100%. Optional per-application overage (see pricing).

---

## 6. Agent architecture

A tool-calling loop (Vercel AI SDK over OpenAI). The "agent" is really an orchestrated set of tools with the LLM as the reasoning layer for scoring, tailoring, and writing:

|Tool|Function|Model / service|
|---|---|---|
|`discover_jobs`|Run Apify actor(s), normalise results|Apify|
|`embed_profile` / `embed_jd`|Vector similarity pre-filter|`text-embedding-3-small`|
|`score_job`|0–100 fit score + rationale + keyword gaps|cheap tier (Luna / nano)|
|`select_resume`|Pick best stored variant or flag for tailoring|logic + embeddings|
|`tailor_resume`|Keyword-optimise the chosen variant|mid tier (5.4-mini)|
|`find_email`|Resolve recipient address + confidence|Apify email actor / Hunter|
|`draft_email`|Personalised human-voice body|mid tier|
|`queue_send`|Hand to throttled Gmail sender|Gmail API|

Guardrails: hallucination checks on tailored resumes (never invent experience/skills the user doesn't have — keyword _reordering and surfacing_, not fabrication); email personalisation validator; email-confidence gate.

---

## 7. Data model (Convex, high level)

- `users` — Clerk id, plan, quotas, timezone, settings.
- `googleAccounts` — encrypted OAuth tokens, send scope, daily-cap state.
- `profiles` — structured parsed profile + embedding.
- `resumes` — up to 5 variants, content + embedding + usage stats.
- `preferences` — target roles, locations, filters, email voice/instructions, min score.
- `discoveredItems` — normalised job/post, source, dedupe key, status.
- `scores` — item ↔ user score, rationale, matched/missing keywords.
- `applications` — approved item, resume used, email body, recipient + confidence, send state, delivery/bounce/reply.
- `sendQueue` — throttled outbound queue with scheduled times.
- `runs` — daily cron run logs (for analytics + debugging).
- `usageEvents` — metered events for billing (jobs scanned, applications sent).

---

## 8. Final tech stack

|Layer|Choice|Notes|
|---|---|---|
|Frontend|**Next.js (App Router)** + Tailwind + shadcn/ui||
|Backend / DB / jobs / storage|**Convex**|Scheduled functions for daily cron, actions for external APIs, file storage for resumes.|
|Auth|**Clerk**|Google OAuth handled separately for Gmail send scope.|
|Agent runtime|**Vercel AI SDK** + **OpenAI**|Tool-calling loop.|
|Embeddings|OpenAI `text-embedding-3-small`|Cheap semantic filter.|
|Scraping|**Apify** (cookieless LinkedIn actors)|Pluggable source layer for Internshala later.|
|Email discovery|Apify actor with bundled Hunter.io fallback (or standalone Hunter)|Confidence-scored.|
|Sending|**Gmail API** via user OAuth|Throttled queue.|
|PDF generation|React-PDF / server-side renderer|ATS-safe single column.|
|Payments|**Razorpay**|Stripe migration path later.|
|Hosting|Vercel||

---

## 9. Compliance & risk (do not skip)

- **LinkedIn ToS:** scraping is a gray area. Mitigation: never use user credentials; cookieless actors + proxies; treat scraped fields as personal data; keep the source layer swappable so we survive an actor breaking.
- **Cold email law:** even "human-feeling" email to a discovered business address can fall under CAN-SPAM (US) / GDPR (EU) / India's DPDP Act. Mitigation: accurate sender identity (it _is_ the user), honour opt-outs, region rules, and keep volume low. Consider a subtle, human-appropriate sign-off rather than a bulk unsubscribe footer.
- **Deliverability = user's own Gmail reputation.** Our incentive is aligned: low caps + high quality protect the user's account and our product.
- **Hallucinated resume content** is a legal/ethical risk — enforce "surface, don't fabricate."
- **Email-finding accuracy:** never present a guessed address as verified; always show confidence.

---

## 10. Cost model (unit economics)

> All external pricing verified **July 2026**; API prices move often, re-check before launch. USD→INR at ≈ ₹85.

### 10.1 Per-unit variable costs

**A. Per job discovered + scored** (~₹0.27 / job)

|Item|Cost|
|---|---|
|Apify scrape (LinkedIn jobs/posts, ~$1–3 / 1,000)|≈ $0.0025 (₹0.21)|
|Embedding pre-filter (`text-embedding-3-small`, $0.02/1M)|≈ $0.00003 (negligible)|
|LLM score (Luna $1/$6 or 5.4-nano $0.20/$1.25, ~2.3k in / 150 out)|≈ $0.0006 (₹0.05)|
|**Total per job**|**≈ ₹0.27**|

**B. Per application sent** (~₹3–6 / application; email discovery is the swing factor)

|Item|Cost|
|---|---|
|Email discovery (blended: cheap Apify actor ~$0.005 up to Hunter ~$0.06)|₹0.4 – ₹5|
|Resume tailoring (5.4-mini, only when no variant fits — ~60% of the time)|≈ $0.005 (₹0.4)|
|Email generation (5.4-mini / Luna, ~2k in / 300 out)|≈ $0.005 (₹0.4)|
|PDF render + Gmail send|~₹0|
|**Total per application**|**≈ ₹3 (cheap email path) to ₹6 (premium email path)**|

**Key lever:** email discovery method is the single biggest cost driver. Using Apify's bundled email-discovery actor keeps per-application cost near ₹3; a dedicated Hunter subscription pushes it toward ₹6 but with better hit rates. Recommendation: **start with the cheap bundled path, upgrade discovery only for higher tiers.**

### 10.2 Fixed / platform costs (amortised)

|Service|Cost|Per-user impact|
|---|---|---|
|Clerk|**Free to 50,000 users** (raised Feb 2026), then $0.02/MRU|~₹0 for a long time|
|Convex|Free (1M calls/0.5GB) → Pro $25/mo (25M calls/50GB); **startup program = up to 1 yr free**|negligible per user at scale|
|Vercel|Pro ~$20/mo flat|negligible per user at scale|
|Apify platform|$5 free credit/mo, then plans from ~$29/mo|folded into per-job cost|
|Razorpay|~2% domestic per transaction|~₹10–50 per paid user/mo|

At even a few hundred users, fixed costs are ~₹30–50/user/month. Auth and DB are effectively free early thanks to Clerk's 50K free tier and Convex's startup program.

### 10.3 Blended cost for a "typical active user"

Assume a paid user scans **300 jobs** and sends **40 applications** in a month:

- Jobs: 300 × ₹0.27 = **₹81**
- Applications: 40 × ₹4 (blended) = **₹160**
- Fixed/platform amortised: **~₹40**
- **≈ ₹280 / month total cost to serve.**

A power user (2,500 jobs, 250 applications) ≈ ₹675 (jobs) + ₹1,000 (apps) + ₹40 = **~₹1,700/month.**

---

## 11. Pricing tiers

Structured around two meters — **jobs scanned/month** and **applications sent/month** — since those drive cost. Prices in INR (India-first), with healthy margin after the ~2% Razorpay fee.

|Plan|Price / mo|Jobs scanned|Applications|Resume variants|Auto daily run|Est. cost to serve|Approx. gross margin|
|---|---|---|---|---|---|---|---|
|**Free**|₹0|40|5|1|manual only|~₹35|(acquisition)|
|**Starter**|**₹499**|300|30|3|✅|~₹210|~58%|
|**Pro**|**₹1,299**|1,000|100|5|✅|~₹610|~53%|
|**Power**|**₹2,499**|2,500|250|5|✅ + priority|~₹1,700|~32%|

**Overage (optional):** ₹12 per extra application beyond the quota (well above our ~₹4 cost) — protects margin on heavy users and lets people flex up without a full upgrade.

**Notes on the pricing logic:**

- Free tier is a **funnel**, not cost-recovery — kept tiny (5 applications) so it demonstrates value without bleeding money.
- Starter/Pro carry the margin; Power is a convenience tier where you may route power users to the cheaper email-discovery path and/or rely on overage to keep margin healthy.
- Because Clerk (free to 50K) and Convex (startup program) cost ~₹0 early, **early-stage gross margins are better than the table suggests** — the table is the steady-state view.
- **Annual plans** at ~2 months free improve cash flow and cut Razorpay fee frequency.

---

## 12. Phased roadmap

**MVP (v1) — prove the loop for one user, one source**

- Clerk auth + Google OAuth send scope
- Resume upload → parse → editable profile
- Single resume variant
- Apify LinkedIn jobs discovery + manual "Run now"
- Scoring (embeddings + keyword + LLM)
- Review/approve queue
- Email discovery + agent email draft
- Final review → throttled Gmail send
- Basic dashboard (funnel + applications list)
- Razorpay Free + Starter

**v1.1**

- 2–5 resume variants + smart reuse
- LinkedIn hiring-**posts** source
- Daily cron automation + notifications
- Full analytics tabs + deliverability tab
- Pro / Power tiers + overage

**v2**

- Internshala + additional sources
- Optional threshold-based auto-send (still capped)
- Reply detection & simple follow-up nudges
- Stripe (international expansion)

---

## 13. Open questions still to decide

1. **Email discovery vendor for v1** — start with Apify bundled actor (cheaper, lower hit-rate) vs Hunter (pricier, better). Recommendation: bundled first, measure hit-rate, revisit.
2. **Daily send cap ramp** — exact starting number and warm-up curve (proposal: 15/day → +5/week to a 40/day ceiling on Pro).
3. **Follow-ups** — in scope later, or never? (Follow-ups raise reply rates but also spam risk.)
4. **"No email found" handling** — hold, or let the user manually paste an address?
5. **Resume hallucination policy wording** — exact rules for what tailoring may and may not change.
6. **India-first vs global at launch** — affects compliance defaults (DPDP vs GDPR/CAN-SPAM footers).

---

_End of PRD v1.0._