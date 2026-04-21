# Demos Network Oracle — Complete Handoff v10

**Date:** 2026-04-21 (late evening CEST)
**Latest commit:** `63c37e0` (C1.4: footer cleanup — remove broken Scan link + XM33 attribution)
**Status:** Live at demos-oracle.com. Final product polish pass complete. Unified header across all 6 surfaces, watermark anchored to document, hero redundancies removed, attestation line relocated, footer cleaned. 5 commits shipped and pushed to origin this session.
**Code:** Single file `src/agent.mjs` (~3,500 lines) on n3 (193.77.169.106) + 5 supporting HTML files
**Session type:** Coherence pass — observation-driven polish, no canonical changes, no new features. Session ended deliberately at observation window.

This handoff supersedes v9. Paste §19 into any new Claude/ChatGPT/Grok chat to resume.

---

## 1. WHAT THIS IS

The **Demos Network Oracle (DNO)** is a public, watch-only network intelligence service for the Demos blockchain testnet. It monitors public validator nodes, tracks network agreement, detects incidents, publishes attested health data on-chain via SuperColony, and explains why it says what it says.

**Core principle:** Strictly watch-only. Observe → interpret → summarize → explain → publish. Never commands, never predicts, never gives operational advice.

**Built by:** XM33 (independently, as a community project — NOT an official Demos team product). Attribution now visible in homepage footer.

**Live URLs:**
- Homepage: https://demos-oracle.com
- Dashboard (legacy): https://demos-oracle.com/dashboard
- Health API: https://demos-oracle.com/health
- Organism: https://demos-oracle.com/organism
- Methodology: https://demos-oracle.com/methodology
- Agent Guide: https://demos-oracle.com/agent
- Sources: https://demos-oracle.com/sources
- Submit Node: https://demos-oracle.com/submit
- Community Onboarding: https://demos-oracle.com/community
- Admin: https://demos-oracle.com/admin/submissions?token=TOKEN
- GitHub: https://github.com/xm33/demos-fleet-oracle (pending rename)
- SuperColony: https://supercolony.ai/agent/0xbdb3e8189a62dce62229bf3badbf01e5bdb3fbeb22f6f59f4c7c2edafe802a45

---

## 2. THIS SESSION — WHAT SHIPPED

5 commits, all pushed to origin. Each addressed an observed problem, not speculative polish.

| Commit | Name | Files | Intent |
|---|---|---|---|
| 4df2f84 | C1+C2: unified header + watermark anchored | 5 files | Remove STATUS pill, relocate ORACLE LIVE to header, unify nav across home/doc pages, anchor watermark to document |
| b44f287 | C1.1: fix submit.html doc-nav + dead .nav-pill CSS | 2 files | submit.html was skipped by C1 due to missing CSS anchor — completed alignment |
| 8231494 | C1.2: remove hero trustline + Trust intro + mobile nav refinements | 6 files | Hero duplication gone; Trust intro paragraph duplicated Attested card; mobile nav METHODOLOGY no longer truncates; ORACLE LIVE shows on mobile row 1 |
| 644ccd9 | C1.3: relocate attestation line + fix orphan </section> | 1 file | SuperColony attestation moved to free-floating transition between Incidents and API; orphan closing tag removed; font 11→12px |
| 63c37e0 | C1.4: footer cleanup — remove broken Scan + XM33 attribution | 1 file | Scan link target did not resolve; Built by XM33 appended to footer-meta |

### Design system — locked

- Logo geometry (all surfaces): 22px, currentColor, 4-dot consensus triad SVG
- Nav links (all 6 surfaces): Methodology · Agent · Sources · Community
- Current-page: aria-current="page" + 50% opacity, no underline
- ORACLE LIVE: steady green dot + uppercase, navbar right, all 6 surfaces
- STATUS pill: REMOVED
- Hero kicker (homepage): [tagline] + [OPEN: NODE SUBMISSION]
- Watermark: position:absolute, desktop right:48px bottom:72px 120px 0.10, mobile right:24px bottom:56px 88px 0.08
- Attestation line: free-floating between Incidents and API, 12px mono, border-bottom
- Footer ecosystem: demos.sh · Faucet · Submit Node
- Footer meta: Demos Network Oracle · API v1.0 · Methodology · GitHub · Built by XM33

### What is NOT on homepage anymore

- STATUS pill in navbar
- ORACLE LIVE in hero kicker (moved to navbar)
- Overview / Nodes / Incidents in-page anchor nav links
- Hero trustline (risk/confidence/data/incidents row)
- Trust section intro paragraph
- Scan link in footer (broken target)
- Discovered Validators table (moved to /community in prior session)

---

## 3. ARCHITECTURE

Unchanged from v9.

- Single file `src/agent.mjs` (~3,500 lines)
- Supporting HTML files: homepage.html, methodology.html, agent-guide.html, sources.html, submit.html
- Runtime: Bun
- DB: SQLite — real path `logs/marketplace.db` (NOT agent.db)
- Service: node-health-agent via systemd
- Deploy path: /home/deploy/supercolony-node-health-agent on n3
- SSH: deploy@n3 (alias `ssh n3`)
- Restart: `sudo systemctl restart node-health-agent`
- Monitoring: 20s poll, 20 min on-chain publish
- Domain: Cloudflare + nginx → port 55225
- Oracle instances: 3 (n3 primary, n1 validator, m1 backup)

Oracle Instances:

| Instance | Node | Port | Wallet | SuperColony |
|----------|------|------|--------|-------------|
| Primary | n3 | 55225 | 0xbdb3e8... | Linked @CypherX33 |
| Validator | n1 | 55226 | 0x5f3c6d... | Linked @CypherX33 |
| Backup | m1 | 55227 | 0x163c55... | Linked @CypherX33 |

---

## 4. TRUTH MODEL (Layer 1 — LOCKED)

Unchanged from v9. 7 orthogonal concepts:

| Concept | Question | Values |
|---|---|---|
| Status | Is the network usable right now? | stable · degraded · unstable · unknown |
| Trend | Is the condition changing? | improving · stable · worsening · unknown |
| Risk | How fragile is the current state? | low · elevated · high |
| Data quality | Can the Oracle see properly? | sufficient · insufficient |
| Confidence | Do signals agree? | clear · uncertain |
| Agreement | Are validators in consensus? | strong · moderate · weak · unknown |
| Incidents | What specific problems are active? | severity: info · warning · critical |

### Critical Semantic Decisions (SETTLED — DO NOT TOUCH)

- Status = network operability — NOT observer coverage
- Risk = resilience / safety margin
- Agreement = reachable consensus — computed only from reachable public nodes
- Confidence ≠ network health — signal consistency
- Incidents = active problems only
- Every categorical field has a paired reason string (except trend)

### Status Logic---

## 5. HOMEPAGE STRUCTURE (v1.7 — post-C1.4)

1. Navbar (identical across all 6 surfaces)
2. Hero — kicker [tagline + OPEN: NODE SUBMISSION] → big STATUS → status_reason → staleness
3. Network Assessment — 4 cards (trend, risk+factors, confidence+reason, data quality)
4. Agreement — state pill + agreement_reason, Aligned Nodes, Block Spread, Median Block
5. Network View — growth cards + Monitored Public Nodes table
6. Incidents — count + list
7. Attestation line (free-floating) — SuperColony cadence + attestation link
8. API — 3 endpoint pills + live JSON example
9. Trust — title + 4 cards + disclaimer
10. Footer
11. Watermark

### Design System
- Background: #0a0a0a, Surface: #101010, Border: #1a1a1a
- Brand: #2B36D9 (Demos blue — accents only)
- Three-tier signal colors: #2dd4a0 / #d97706 / #EF4444
- Typography: Source Code Pro (mono values), Inter (sans labels)
- 980px content grid, mobile responsive @640px

---

## 6. PAGES AND HEADERS (v10 — UNIFIED)

Major change from v9: all 6 pages now use same navbar geometry.

| Page | URL | Header |
|---|---|---|
| Homepage | / | Unified nav + DEMOS button |
| Methodology | /methodology | Unified nav |
| Agent Guide | /agent | Unified nav |
| Sources | /sources | Unified nav |
| Submit | /submit | Unified nav |
| Community | /community | Unified nav |
| Admin | /admin/submissions | doc-nav (not unified — internal) |
| Dashboard | /dashboard | Legacy |

**ORACLE LIVE semantic (LOCKED):** "System is actively observing and updating." Does NOT mean network is healthy. Re-evaluate if ≥3 users misread it as health signal.

---

## 7. PUBLIC NODES MONITORED

| Node | Type | URL | Status |
|---|---|---|---|
| kyne-node2 | Kynesys | node2.demos.sh:53550 | Online |
| kyne-node3 | Kynesys | node3.demos.sh:53550 | Variable |
| kyne-node3b | Kynesys | node3.demos.sh:53540 | Offline |
| community-node1 | Community | 107.131.170.202:53552 | Variable |
| community-node2 | Community | 65.7.20.194:53552 | Variable |

Discovered (on /community only): 5.189.144.254:53552, 84.32.22.26:53552.

---

## 8. FLEET NODES (Reference Layer)

7 validator nodes owned by XM33. Currently on stalled test chain at block ~59K. Public at ~2.1M+. Fleet never influences public status.

| Node | IP | Port | Identity |
|---|---|---|---|
| n1 | 193.77.44.160 | 53550 | 0x8f3abd... |
| n2 | 193.77.44.160 | 54550 | 0xbfda23... |
| n3 | 193.77.169.106 | 53550 | 0x4ba486... (anchor, primary oracle) |
| n4 | 193.77.50.180 | 54550 | 0x848ae0... |
| n5 | 193.77.50.180 | 53550 | 0x95cbd7... |
| n6 | 193.77.169.106 | 54550 | 0x3ab336... |
| m1 | 82.192.52.254 | 53550 | 0x56b46b... |

---

## 9. KEY LESSONS (v10 additions)

Lessons 1–50 from v9 §13 remain. New this session:

51. Python heredoc multi-file scripts must report per-file success AND check exit code. C1 script crashed silently on submit.html (assert fired mid-loop). Fix: wrap per-file in try/except and emit summary.
52. Reference grep counts in verify blocks need to be exact, not guessed. Multiple verify asserts this session were wrong because I miscounted string occurrences in context.
53. `git commit --amend` only amends HEAD. Said "amend into C1" when HEAD was actually C1.1. Check `git log --oneline -1` before every amend.
54. Outside-model advisory documents frequently lie about current state. Treat any pasted advisory as stale until verified against commit log.
55. "Ship → look → spot another → ship" is a convergence failure. Recognized mid-session and stopped. Observation window is now.
56. Orphan `</section>` tags are invisible until you grep for tag balance. `grep -c '<section'` vs `grep -c '</section'` surfaces them in one line.
57. `position: fixed` tracks the viewport, reading as "floating over content." `position: absolute` on a `position: relative` body anchors to the document. Rule for brand marks: absolute, not fixed.
58. Don't expand data fields before the data justifies them. "+N this month" with 5 days of history would display identical to "+N this week" — misleading. Wait for data to make the display meaningful.

---

## 10. UPSTREAM ISSUES

Unchanged from v9. Live chain migration still pending.

---

## 11. WHAT TO DO NEXT

### IMMEDIATE — Observation window

**Posture: DON'T SHIP CODE.** Observe for 2–4 weeks.

1. Post community outreach messages (v9 §20 text, unchanged, ready). Single highest-leverage action.
2. Purge Cloudflare cache for all 6 paths.
3. Clean up .pre-* backup files on n3.

### OBSERVATION TARGETS

- Anyone asks "what's the current state?" from a doc page? ≥3 instances → revisit Path 2
- Anyone misreads ORACLE LIVE as "network is healthy"? ≥3 instances → change dot to neutral gray
- Anyone reads /community as canonical? ≥3 instances → ship C3 banner
- Visual confusion on attestation line? → drop border-bottom or absorb into API subtitle

### LIVE CHAIN MIGRATION (pending)

Plan unchanged from v9. Fleet rejoins public monitoring when Demos team invites. Post-migration: reconsider "+N this month" growth field.

### WHEN nibor83 SYNCS CLOSER TO HEAD

Approve via /admin/submissions?token=TOKEN. Node joins as community-node-4.

### GITHUB RENAME

Plan unchanged from v9 §24. Ready when you are.

---

## 12. DEFERRED WORK

### Still deferred from v9
- Startup reload of unresolved incidents
- .gitignore for *.pre-* / *.bak* files
- Mobile tables as card-stacked rows
- Sources page content review
- Trust card voice strengthening
- Layer 1/2/3 copy additions

### v10 completions (no longer deferred)
- Header unification ✅ C1, C1.1
- Hero trustline redundancy ✅ C1.2
- Trust intro paragraph ✅ C1.2
- Mobile nav METHODOLOGY truncation ✅ C1.2
- ORACLE LIVE on mobile row 1 ✅ C1.2
- Attestation line relocation ✅ C1.3
- Orphan section fix ✅ C1.3
- Broken Scan link ✅ C1.4
- Built by XM33 ✅ C1.4

### New — deferred this session
- **C3** — /community non-canonical banner. Defer until user misreads /community as canonical.
- **C4** — vertical rhythm tuning. No observed signal. Defer.
- **Doc-page state references.** Path 1 held. Revisit on observed trigger.
- **"+N this month" growth field.** Defer until post-migration (≥30 days of data).

---

## 13. FEEDBACK TRIGGERS (unchanged)

- Trigger A — Confusion: "What is this?" / "Is this a validator?" / "Who runs this?"
- Trigger B — Misinterpretation: Users believe Oracle controls/predicts/endorses
- Trigger C — Trust hesitation: "Where is this data from?" / "Why believe this?" / "Is this official?"

Act only on 3+ independent instances.

---

## 14. CRITICAL PRINCIPLES

1. Watch-only
2. Public-first
3. Status = operability
4. Risk = resilience
5. Agreement = reachable consensus
6. Publish state, not policy
7. Honest uncertainty
8. No scoring
9. No prediction
10. The Organism is external
11. Stable vocabulary
12. Observation ≠ endorsement
13. Manual approval required
14. Fleet on /community is temporary
15. Reason strings never lie
16. Every categorical canonical field has a paired reason string
17. "Good copy" is not enough — copy must solve observed user problem
18. **(NEW in v10)** "Ship → look → spot another → ship" is a convergence failure

---

## 15. STACK

- Runtime: Bun
- Chain: Demos Network testnet
- Oracle layer: SuperColony
- DB: SQLite (logs/marketplace.db)
- Domain: Cloudflare + nginx
- Monitoring: 20s poll, 20 min on-chain publish
- Admin auth: DNO_ADMIN_TOKEN env var
- Code: single file src/agent.mjs

---

## 16. COMMUNITY MESSAGES

Unchanged from v9 §20. Ready to post. Public Telegram announcement, DM to ChadX, DM to nibor83, DM to Demos team member.

---

## 17. SECURITY

No new injection attempts this session.

---

## 18. GITHUB RENAME

Plan unchanged from v9 §24.

---

## 19. CONTINUATION PROMPT FOR NEXT SESSION

Paste this into a new chat to continue:---

## 20. GIT LOG---

## 21. SUMMARY

**Shipped this session:** 5 commits covering full header unification, hero redundancy removal, attestation relocation, and footer cleanup. All pushed to origin.

**Not shipped (intentionally):** C3, C4, doc-page state references, month growth field. Deferred per observation-first discipline.

**Not done (housekeeping):** Post outreach, purge Cloudflare, clean backups, GitHub rename, migration prep.

**The Oracle's state:** Production-grade. Coherent across all 6 surfaces. Hero lean. Navigation unified. Footer honest. Truth model stable. Mobile UX verified.

**The main thing:** Session deliberately ended at observation window. Next milestone: live chain migration. Until then — distribute (outreach), observe (triggers), prepare (migration).

**End of handoff v10.**
