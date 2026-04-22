# Demos Fleet Audit — Per-Node Instructions

Workflow: SSH from Mac to each node → run the audit block → check for secrets → paste in chat → wait for Claude's analysis → proceed to next node.

---

## SECRETS TO WATCH FOR

Before pasting any output, scan for these patterns. If any appear, redact the VALUE (keep the variable name).

Never paste values for:
- MNEMONIC, SEED_PHRASE, SEED
- PRIVATE_KEY, PRIVKEY
- WALLET_KEY, WALLET_SECRET, KEYSTORE
- API_KEY, ACCESS_TOKEN
- PASSWORD, PASSWD
- DNO_ADMIN_TOKEN (n3 only)
- Any long random-looking hex or base64 string

Safe to paste: PROD=true, ports, URLs, flags, public keys (short 0x peerlist entries), container names, block heights.

When in doubt: redact value, keep variable name. Example: MNEMONIC=<REDACTED>

---

## THE AUDIT COMMAND BLOCK

This is the reference block. Use it per node, change `NODENAME` in the first/last echo to identify which node.

    echo "=== AUDIT: NODENAME ==="
    hostname; uptime
    cat /etc/os-release | head -4
    which bun && bun --version
    which docker && docker --version
    whoami; pwd
    ls -la ~/ | head -20
    for dir in ~/node ~/demos-node /home/deploy/node /opt/node; do
      [ -d "$dir" ] && echo "FOUND: $dir" && cd "$dir" && pwd && break
    done
    git rev-parse --abbrev-ref HEAD 2>/dev/null
    git log -1 --oneline 2>/dev/null
    git status --short 2>/dev/null | head -10
    ls -la .env* 2>/dev/null
    cat .env 2>/dev/null
    cat demos_peerlist.json 2>/dev/null || echo "no peerlist"
    ls -la run fnode.sh 2>/dev/null
    cat fnode.sh 2>/dev/null
    systemctl list-units --type=service --state=running | grep -iE "demos|node"
    cat /etc/systemd/system/demos*.service 2>/dev/null
    ps auxf | grep -E "bun|node|docker|postgres" | grep -v grep | head -20
    docker ps 2>/dev/null | head -10
    ls -lad postgres_5332 2>/dev/null
    du -sh postgres_5332 2>/dev/null
    ss -ltn | grep -E ":53550|:53551|:53552|:54550|:60001|:5332"
    df -h ~ | tail -2
    free -h | head -2
    curl -s --max-time 3 http://127.0.0.1:53550/info | head -c 500; echo
    curl -s --max-time 3 http://127.0.0.1:53551/info | head -c 500; echo
    curl -s --max-time 3 http://127.0.0.1:54550/info | head -c 500; echo
    echo "=== END AUDIT: NODENAME ==="

---

## NODE 1 — n4 (193.77.50.180) — FIRST, SAFEST

From your Mac:

    ssh deploy@193.77.50.180

Then run the audit block above with NODENAME replaced by n4.

After: check for secrets, paste in chat, wait for Claude. Exit SSH with `exit`.

---

## NODE 2 — n5 (193.77.50.180, port 53550)

Same box as n4. SSH same way. Run audit block with NODENAME=n5.

---

## NODE 3 — n1 (193.77.44.160) — ORACLE VALIDATOR INSTANCE

From your Mac:

    ssh deploy@193.77.44.160

Run audit block with NODENAME=n1. Additionally run:

    echo "--- Oracle instance on n1 ---"
    systemctl status node-health-agent 2>/dev/null | head -10
    ls -la /home/deploy/supercolony-node-health-agent 2>/dev/null | head -5

---

## NODE 4 — n2 (193.77.44.160, port 54550)

Same box as n1. Run audit block with NODENAME=n2.

---

## NODE 5 — n6 (193.77.169.106, port 54550)

Same box as n3 (primary Oracle). Read-only only.

From your Mac:

    ssh deploy@193.77.169.106

Run audit block with NODENAME=n6. Additionally:

    systemctl status node-health-agent 2>/dev/null | head -5

---

## NODE 6 — m1 (82.192.52.254) — ORACLE BACKUP

From your Mac:

    ssh deploy@82.192.52.254

Run audit block with NODENAME=m1. Additionally:

    systemctl status node-health-agent 2>/dev/null | head -10

---

## NODE 7 — n3 (193.77.169.106) — ANCHOR + PRIMARY ORACLE

HIGHEST CAUTION. Read-only only. Do not restart anything.

From your Mac:

    ssh deploy@193.77.169.106

(or use alias: ssh n3)

Run audit block with NODENAME=n3. Additionally:

    echo "--- Oracle state on n3 (DO NOT modify) ---"
    systemctl status node-health-agent | head -15
    curl -s --max-time 3 http://localhost:55225/organism | head -c 300
    ls -la /home/deploy/supercolony-node-health-agent 2>/dev/null | head -10

---

## AFTER ALL 7

Paste all outputs in order. Claude produces the drift register. Then stop. Next session: fix planning.

---

## TROUBLESHOOTING

| Problem | Action |
|---|---|
| SSH timeout | Retry. Skip if persistent, return later. |
| Permission denied | Tell Claude what user you normally use for that node. |
| Command not found (bun/docker) | Fine, missing tool is data. Proceed. |
| Secrets in output | Redact values, keep variable names, paste rest. |
| Node appears dead | Paste what you have. Flag the issue. Move on. |
| Want to stop mid-audit | Stop. Partial data is useful. |

---

## REMINDER

This session: audit only. No fixes. No config changes. No restarts. No deletions. Full picture first.
