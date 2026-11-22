# SSH Authentication Failure Burst (T1110.001)

## What it detects
More than 10 SSH authentication failures from a single source IP within
5 minutes. Classic brute-force signature.

## Why it matters
SSH is the most-targeted Linux service. A burst of failures from one IP
means either a brute-force attempt or a misconfigured automation job.
Distinguishing those two is the analyst's job — the rule just surfaces it.

## Test it (lab only)

On a lab VM running sshd, from a second lab machine:

```bash
for i in $(seq 1 15); do
  ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no \
      wronguser@LAB_IP 2>/dev/null
done
