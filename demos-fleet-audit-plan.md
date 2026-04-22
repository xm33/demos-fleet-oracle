# Demos Fleet Audit — Step-by-Step Plan

**Session type:** Read-only audit, all 7 nodes, against Kynesys `join-fixnet.md` spec
**Reference spec:** https://github.com/kynesyslabs/node/blob/stabilisation/documentation/join-fixnet.md
**Branch target:** `stabilisation`
**Target network:** fixnet (entry point, then Kynesys promotes to devnet → testnet)

**Workflow:** You SSH from Mac → one node at a time → paste output in chat → Claude analyzes → next node
**Fixes:** NOT this session. Audit only. Full picture first, then decide.

---

## THE 7 NODES (from handoff v10 §8)

| # | Node | IP | Port (handoff) | Role | Audit order |
|---|---|---|---|---|---|
| 1 | n4 | 193.77.50.180 | 54550 | non-anchor | **First** (safest) |
| 2 | n5 | 193.77.50.180 | 53550 | non-anchor | Second |
| 3 | n1 | 193.77.44.160 | 53550 | validator (Oracle inst 2) | Third |
| 4 | n2 | 193.77.44.160 | 54550 | non-anchor | Fourth |
| 5 | n6 | 193.77.169.106 | 54550 | on n3's box | Fifth |
| 6 | m1 | 82.192.52.254 | 53550 | backup (Oracle inst 3) | Sixth |
| 7 | n3 | 193.77.169.106 | 53550 | **anchor, primary Oracle** | **Last** |

Order rationale: smallest blast radius first. n3 is last because it hosts the Oracle. m1 is second-to-last because it's the backup Oracle instance.

---

## PHASES

### Phase 1 — Audit setup (now)

- Kynesys `join-fixnet.md` spec fetched and analyzed (done)
- Scope confirmed: full depth, all 7 nodes, audit-only
- SSH model confirmed: Mac → node, per-node manual
- Secrets policy confirmed: Claude flags before display
- **You:** open a fresh scratchpad on your Mac to collect outputs per node

### Phase 2 — Per-node audits (this session)

For each of the 7 nodes, in audit order:

1. SSH into the node from Mac
2. Run the audit command block from `demos-fleet-node-audit-instructions.md`
3. Read the output — if you see `mnemonic`, `SEED_PHRASE`, `PRIVATE_KEY`, `WALLET_KEY`, `PASSWORD`, stop and redact
4. Paste the full output into chat
5. Claude analyzes, asks follow-up if needed
6. Move to next node

**Estimated time:** 5–10 min per node. ~60 min total.

### Phase 3 — Drift register

After all 7 audits are in, Claude produces:

- Per-node status card
- Master drift table vs fixnet spec
- Action recommendations

### Phase 4 — Fix planning (NEXT SESSION)

Do not fix anything this session. After audit lands:
1. Sleep on it
2. Open new session
3. Paste continuation prompt + drift register
4. Decide per-item: fix now, fix at migration, accept

---

## FIXNET SPEC (what we're comparing against)

From `join-fixnet.md`:

**Prerequisites:** Docker installed + user in docker group, Bun installed

**Code:** `git clone https://github.com/kynesyslabs/node.git`, branch `stabilisation`

**Config:**
- `.env` contains `PROD=true`
- `demos_peerlist.json`:
**Startup:** `./run` or `fnode.sh` wrapper with `-c false -u http://<public-ip>:53550 -t true`

**Ports:** 53550 (RPC), 53551 (P2P)

**PostgreSQL:** Dockerized, data dir `postgres_5332`

**Runtime:** Node responds on `/info`, block height advancing, syncing against `node3.demos.sh:60001`

**Expected drift:** Fleet is on stalled ~59K test chain. Almost certainly NOT on fixnet. Audit will confirm massive drift across all 7.

---

## WHAT THIS SESSION DOES NOT DO

- No fixes on any node
- No config modifications
- No postgres_5332 deletions
- No service restarts
- No peerlist changes
- No git branch switches
- No touching Oracle on n3

Read-only.

---

## SECRETS POLICY

Never paste if present:
- MNEMONIC, SEED_PHRASE, PRIVATE_KEY, WALLET_KEY, WALLET_SECRET
- API_KEY, ACCESS_TOKEN, DNO_ADMIN_TOKEN
- PASSWORD, PASSWD
- Any long unfamiliar hex/base64

Safe to paste:
- PROD=true, ports, URLs, flags, public keys (66-char 0x identifiers), container names, block heights

If in doubt: redact value, keep key. Example:---

## OUTPUT EXPECTATIONS

Each audit: 80–150 lines. Paste whole thing if clean. Redact secret values, keep variable names.

---

## IF SOMETHING GOES WRONG

- SSH fails → try again, skip if persistent, return later
- Command errors → paste error, Claude adjusts
- Node dead → paste what you have, flag, move on
- Need to stop → stop, partial data is still useful
