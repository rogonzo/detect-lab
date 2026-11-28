# Sudo Privilege Escalation Attempt (T1548.003)

## What it detects
More than 3 sudo authentication failures from a single user within
10 minutes. Signals a user (or process) trying passwords or escalating
privileges they don't have.

## Why it matters
Sudo is the primary privilege-escalation path on Linux. A burst of failed
sudo attempts is either a confused user, a stale automation job, or an
attacker who has a low-priv shell and is trying to go root.

## Test it (lab only)

On a lab VM, as a non-root user:

```bash
for i in $(seq 1 5); do
  echo wrongpassword | sudo -S id 2>/dev/null
done

## Proof / validation

Sample log: [`tests/sudo_abuse_sample.log`](../tests/sudo_abuse_sample.log)

Five `incorrect password` failures from `alice` within 20 seconds, followed
by one successful sudo command.

Rule condition: `selection_fail | count() by user > 3` over `10m`.
5 failures > 3 → **rule fires** for user `alice`.

Validate locally:

```bash
grep -c 'incorrect password' tests/sudo_abuse_sample.log
# expected: 5  (threshold 3 → fires)
