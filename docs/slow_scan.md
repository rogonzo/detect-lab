# Slow Port Scan (T1046)

## What it detects
A single source touching more than 50 distinct destination ports over
10 minutes, where the firewall blocked the connection. Catches throttled
scans that evade short-window rate detection.

## Why it matters
The `port_scan_burst` rule catches fast scans (>20 ports in 1m). An attacker
who paces the scan defeats it. This rule looks at **total distinct ports
over a longer window**, so pacing doesn't help — a 200-port scan at any
speed still crosses this threshold.

## Test it (lab only)

Using netrecon's throttled mode:

```bash
python3 netrecon.py LAB_IP -p 1-200 -d 3
Expected ufw log entries spaced several seconds apart:

text
UFW BLOCK ... SRC=192.168.1.10 DST=192.168.1.20 PROTO=TCP DPT=23
UFW BLOCK ... SRC=192.168.1.10 DST=192.168.1.20 PROTO=TCP DPT=24
UFW BLOCK ... SRC=192.168.1.10 DST=192.168.1.20 PROTO=TCP DPT=25
False positives
Authorized slow vulnerability scans (suppress scanner IPs)

Distributed monitoring from a single collector hitting many backends

Legitimate service discovery over long periods

Tuning
count(dst_port) > 50 over 10m is chosen so a 200-port scan at 3s/port
(~10 min total) clearly exceeds it, while routine traffic doesn't. If your
environment has heavy legitimate multi-port activity, raise the threshold.

ATT&CK
Technique: T1046 — Network Service Discovery

Tactic: Discovery

## Proof / validation

Sample log: [`tests/slow_scan_sample.log`](../tests/slow_scan_sample.log)

60 `ufw-block` lines from one source (`192.168.1.10`) across 60 distinct
destination ports, spread over ~9 minutes (one every 9 seconds).

This is the evasion case from [write-up #003](../../purple-writeups/writeups/003-slow-scan-evasion/):
too slow to trip `port_scan_burst` (>20 in 1m), but total distinct ports
still far exceeds `slow_scan`'s `>50 in 10m`.

Validate locally:

```bash
grep 'ufw-block' tests/slow_scan_sample.log | \
  awk '{print $NF}' | sort -u | wc -l
# expected: 60  (threshold 50 → fires)

# And confirm port_scan_burst would NOT fire: max ports in any 1-minute window
grep 'ufw-block' tests/slow_scan_sample.log | \
  awk '{print $3}' | cut -d: -f1-2 | sort | uniq -c | sort -nr | head -1
# expected: ~7 per minute  (below 20 → port_scan_burst misses it)
That contrast — slow_scan fires, port_scan_burst does not — is the proof
that the two rules are complementary, not redundant
