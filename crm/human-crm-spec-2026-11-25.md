# Human CRM — Spec (built 2026-11-25)

**Sheet:** Job Applications Tracker — `1TBbg_GWqIC-lPPsaI4TuAjs-EAL9URY9PnCBjyW-Z5c`
**Rule:** The Sheet is the live source. The GitHub dashboard (`ryanaustinanderson/job-dashboard`) is a snapshot — re-sync and push after CRM edits.
**Existing tabs untouched:** Applications, People, Role Types, LAMP List. No messages sent, nothing pushed to GitHub, no crons created.

## Tabs added (10)

| Tab | Rows | Contents |
|---|---|---|
| CRM People | 66 people + header | Master person pipeline (cols A–U, see schema) |
| CRM Companies | 64 employers + header | LAMP employers as a company pipeline (cols A–K) |
| CRM Interactions | 3 seeded + header | Touch log — log every email/DM/call/coffee chat here |
| Active | QUERY, max 15 | Pipeline Stage = Active, by Priority Score desc |
| Overdue | QUERY | Overdue? = Overdue, by Next Action Date asc |
| This Week | QUERY + SORT, max 15 | Next Action Date within 7 days, not Done/Parked; sorted Tier asc, Priority Score desc, Next Action Date asc |
| Nurture | QUERY | Pipeline Stage = Nurture, by Priority Score desc |
| Template Bank | 5 templates + header | New, After Call, Referral Nudge, Nurture Value Add, Close the Loop — all under 120 words |
| CRM Rules | 21 rows | Cadence, follow-up timing, Sunday 1-1-1, monthly promote/demote/batch, Active cap |
| Weekly Summary | 10 metrics + header | Live formulas (see below) — this is what gets pushed to GitHub weekly |

## CRM People schema (A–U)

A Name, B Company, C Role, D Tier (1/2/3 dropdown), E Warmth (Cold/Warm/Hot dropdown),
F Pipeline Stage (New/Active/Nurture/Overdue/Done/Parked dropdown),
G Last Touch (yyyy-mm-dd, copied from People tab where present, else blank),
H Days Since Last Touch `=IF(G2="","",TODAY()-G2)`,
I Cadence Days `=IF(D2=1,14,IF(D2=2,30,90))`,
J Next Action Date `=IF(G2="",TODAY(),G2+I2)`,
K Overdue? `=IF(J2="","",IF(J2<TODAY(),"Overdue","On track"))`,
L Priority Score `=IF(D2="","",(4-D2)*10+IF(E2="Hot",10,IF(E2="Warm",5,0))+MIN(30,MAX(0,IF(H2="",0,H2-I2))))`,
M Touches `=COUNTIF('CRM Interactions'!$B:$B,A2)`,
N Next Action (from People Next step), O How We Met / Commonality (from People How we met),
P Contact Method (Email/LinkedIn/Instagram/Text/Call dropdown),
Q Email, R LinkedIn URL, S Phone, T LAMP Motivation (from LAMP List where the company matches),
U Notes. Row 1 frozen. Dropdowns validated for D, E, F, P.

### Tier / Warmth are INITIAL DRAFTS for Ryan to adjust — not his decisions
- **Tier 1 (28):** people at Top 5 LAMP companies (Google, Capital One, RBC, BlackRock, Amazon) + the 6 overdue ROMBA follow-ups (Jack Erbs, Chad Plenge, Pfizer / Chase / BofA / Accenture recruiters). Note: Top-5 company membership takes precedence, so Tyler Ryan (Done, BlackRock) and the three "[Pick] @ Google" placeholders are Tier 1.
- **Tier 2 (36):** Active / To follow up at other companies.
- **Tier 3 (2):** Andrei C (Prep done, Microsoft) and Ernie Garcia (Parked). Everyone else landed in Tier 1 or 2 under the rules above — Ryan should re-tier by hand.
- **Warmth:** Hot (2) = last contact in Nov 2026 (Nate, Tyler Ryan). Warm (35) = met at ROMBA/NBMBAA or a coffee chat. Cold (29) = never contacted / To-pick / blank last contact with no event.
- **Pipeline Stage:** Overdue (6 — the ROMBA six), Done (1 — Tyler Ryan), Active (4 — Nate, Chris, Kunal Abichandani, David Mendez), New (4 — Andrei C + 3 [Pick] placeholders), Parked (1 — Ernie Garcia), Nurture (50 — everything else, incl. all "To follow up").

### Contact info — nothing invented
- Emails on file (8, from the old-tracker audit): Liz Barnum (lizbarnum@google.com), Michael Toole (mtoole@microsoft.com), Andrew Clark (andrewclark@microsoft.com), Allie Bennet (allie.bennett@blackrock.com), Jelicza Thomas (jelicza.thomas@blackrock.com), Raquel Duran (raquel.duran@pfizer.com), Ryan Carty (ryancarty@meta.com), Ariane Owens (aowens@adobe.com).
- LinkedIn only (4): Chad Plenge (linkedin.com/in/chadplenge/), Ernie Garcia, Kate Haney, Jaclyn Lomax.
- Phones: Michael Toole 425-703-3242, Teona Avaliani (917) 288-2978, Kenny Wang +1 504-717-1588.
- Teona Avaliani: email teona.avaliani@pwc.com in Q with note "verify before using — from Nov 2025 DM". Lives in Sarasota; mentor target.
- Kenny Wang: Instagram as contact method; mentor target; birthday May 20 in Notes.
- Everyone else: email / LinkedIn / phone blank — "not in tracker, add it". Do not guess.

## CRM Companies schema (A–K)
A Company, B Tier (1/2/3 dropdown — 1 = Top 5, 2 = Motivation 2, 3 = rest),
C Motivation (0–3), D Posting (1–3), E Advocate Y/N, F Named Advocate,
G Target Roles, H Location / Office, I Pipeline Stage (Active/Nurture/Parked dropdown),
J Next Action, K Notes (full LAMP Notes copied).
G/H/J filled only where the LAMP Notes state a role/location/action (e.g. Capital One MBA PM 2027 Chicago $169k; Google GTM + YouTube Marketing Chicago; Amazon MLDP / Pets referral-only); otherwise blank. Parked: Motivation 0, Accenture, Dynasty, JPMorgan Chase (no live FT posting).

## CRM Interactions — seeded rows only (verified, nothing invented)
1. 2026-11-05 — Tyler Ryan, BlackRock, Coffee Chat, Outbound — informational interview Nov 5; note sent Nov 23 — Replied.
2. 2026-10-03 — Jack Erbs, Amazon, Coffee Chat, Outbound — Hilton Anatole, ROMBA Booth 222EX — No reply; follow-up was due 2026-10-09.
3. 2026-11-22 — Teona Avaliani, PwC, Instagram DM, Outbound — lives in Sarasota; mentor target — Replied.
- Chad Plenge: deliberately NO row — no interaction yet. J Touches Count `=COUNTIF($B$2:B2,B2)` (running count per person).

## Views (live QUERYs, no hard-coded copies)
- **Active:** `=QUERY('CRM People'!A2:U,"select A,B,C,D,E,G,J,K,L,N where F='Active' order by L desc limit 15",0)`
- **Overdue:** `=QUERY('CRM People'!A2:U,"select A,B,C,D,E,G,J,L,N where K='Overdue' order by J asc",0)` — currently 3 rows: Chris, Jack Erbs, Tyler Ryan (see caveat below).
- **This Week:** `=SORT(QUERY('CRM People'!A2:U,"select A,B,C,D,E,G,J,K,L,N where J<=date '"&TEXT(TODAY()+7,"yyyy-mm-dd")&"' and F<>'Done' and F<>'Parked' limit 15",0),4,TRUE,9,FALSE,7,TRUE)` — sorted Tier asc, Priority Score desc, Next Action Date asc. Currently 15 rows, led by Jack Erbs (score 65).
- **Nurture:** `=QUERY('CRM People'!A2:U,"select A,B,C,D,E,G,J,K,L,N where F='Nurture' order by L desc",0)`

## Cadence & follow-up timing (also in CRM Rules tab)
- Tier 1 every 7–14 days (formula target 14), Tier 2 every 21–30 days (target 30), Tier 3 quarterly (90).
- Same-evening thank-you (within 24h) → Day 5–7 ask → Day 3 bump → Day 7 new angle → Day 14 close (then stop).
- **Sunday 1-1-1 Top 3:** 1 New, 1 Active follow-up, 1 Nurture value add. Draft only — never send without Ryan's go-ahead.
- **Monthly:** promote 2–3 from Nurture, demote non-responders after 3 touches, batch 10 new names.
- **Active cap:** 12–15 max (currently 4 — headroom).

## Template Bank (all < 120 words, placeholders [Name] / [Company] / [Commonality])
1. **New** (72 words) — one commonality, one reason ("hear your perspective / understand your journey"), one small ask (15–20 min). Never a job/referral ask.
2. **After Call** (52 words) — same-evening thank-you.
3. **Referral Nudge** (57 words) — Day 5–7, only if they offered/hinted at an intro.
4. **Nurture Value Add** (40 words) — share something useful, no ask.
5. **Close the Loop** (38 words) — Day 14 graceful close.

## Weekly Summary (live formulas, week starting Monday)
- Week Starting `=TODAY()-WEEKDAY(TODAY(),3)` (currently 2026-11-23)
- Touches sent this week — COUNTIFS Interactions date within last 7 days (currently 1)
- Reply rate — Replied / touches this week (currently 1.0)
- Conversations — "Call booked" this week (0); Referrals — "Referral" this week (0)
- Overdue count `=COUNTIF('CRM People'!K:K,"Overdue")` (currently 3)
- Active count (4), Nurture count (50), Total people (66)
- Weekly GitHub push (parent to do): touches sent, reply rate, conversations, referrals, overdue count.

## Formula errors found & fixed
1. **This Week QUERY — `TODAY()+7` inside the QUERY string returns #VALUE!** (QUERY's language has no TODAY()). Fixed by building the date literal with `TEXT(TODAY()+7,"yyyy-mm-dd")` concatenation.
2. **This Week QUERY — `order by … asc` inside QUERY returns empty output** (verified in isolation: `order by D asc`, `order by A asc`, `order by J asc` all returned zero rows, while `desc` variants worked). Fixed by removing `order by` from the QUERY and wrapping with `SORT(…,4,TRUE,9,FALSE,7,TRUE)` — same sort order as requested (Tier asc, Priority Score desc, Next Action Date asc). The Overdue view's `order by J asc` was left as specified — it works (3 rows returned).
3. **Kenny Wang phone `+1 504-717-1588` parsed as a formula (#ERROR!)** — leading `+`. Fixed by writing it as text (leading apostrophe). Teona's phone was briefly overwritten by that fix (wrong row) and restored to (917) 288-2978. Final full-range check: 66 rows, zero errors.

## Caveats for Ryan / parent
- **Tyler Ryan shows as "Overdue"** in the Overdue view and counts toward the Overdue count (3), because his Last Touch (Nov 5) + 14-day Tier 1 cadence = Nov 19, before today — even though his stage is Done. Per the spec formula this is correct; if it bothers Ryan, the Overdue? formula could exclude Done, or his Tier could drop to 3.
- **The 5 no-touch ROMBA overdue contacts** (Chad Plenge, Pfizer/Chase/BofA/Accenture recruiters) show Next Action Date = today and "On track", because the spec formula sets Next Action Date = TODAY() when there is no Last Touch. Their stage is still Overdue in CRM People, so they are not lost — but they do not appear in the Overdue view. Same note as above: a formula tweak could flag no-touch Overdue-stage people as Overdue.
- **No one could not be placed.** All 66 People rows and all 64 LAMP employers are in the CRM. The only "unplaced" items are the 54 people with no email/phone/LinkedIn — fields left blank deliberately (see contact audit).
