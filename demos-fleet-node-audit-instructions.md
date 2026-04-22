# Demos Fleet Audit — Per-Node Instructions

**Workflow:** SSH from Mac → run block → check for secrets → paste in chat → wait for analysis → next node.

---

## SECRETS TO WATCH FOR

Before pasting any output, scan for:
- MNEMONIC, SEED_PHRASE, SEED
- PRIVATE_KEY, PRIVKEY
- WALLET_KEY, WALLET_SECRET, KEYSTORE
- API_KEY, ACCESS_TOKEN
- PASSWORD, PASSWD
- DNO_ADMIN_TOKEN (n3 only)
- Any long random-looking hex/base64

Public keys (short 0x... peerlist entries) are safe. When unsure: ask.

---

## NODE 1 — n4 (193.77.50.180)

SSH from Mac:
Check output for secrets. Paste in chat. Wait for Claude.

Exit SSH: `exit`

---

## NODE 2 — n5 (193.77.50.180, port 53550)

Same box as n4 — you may already see its state in n4's output. SSH same way, change all `n4` references to `n5` in the audit block header/footer, plus:---

## NODE 3 — n1 (193.77.44.160) — ORACLE VALIDATOR INSTANCE

SSH:Run audit block (change n4 → n1). Additionally:---

## NODE 4 — n2 (193.77.44.160, port 54550)

Same box as n1. SSH same way. Run audit block, n4 → n2, plus:---

## NODE 5 — n6 (193.77.169.106, port 54550)

**Same box as n3 (primary Oracle). Read-only only.**

SSH:Run block, n4 → n6. Note Oracle is on this host:---

## NODE 6 — m1 (82.192.52.254) — ORACLE BACKUP

SSH:Run block, n4 → m1. Oracle check:---

## NODE 7 — n3 (193.77.169.106) — ANCHOR + PRIMARY ORACLE

**HIGHEST CAUTION. Read-only.**

SSH (or `ssh n3` alias):Run audit block, n4 → n3. Oracle-specific:---

## AFTER ALL 7

Paste outputs in chat in order. Claude produces drift register. Then stop. Next session = fix planning.

---

## TROUBLESHOOTING

| Problem | Action |
|---|---|
| SSH timeout | Retry. Skip if persistent. |
| Permission denied | Tell Claude what user you normally use. |
| Command not found (bun/docker) | Fine. Proceed. |
| Secrets in output | Redact values, paste rest. |
| Node dead | Paste what you have. Flag it. Move on. |
| Want to stop mid-audit | Stop. Partial = useful. |

---

## REMINDER

This session: **audit only**. No fixes. No config changes. No restarts. No deletions. Full picture first.
