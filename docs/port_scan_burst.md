# Port Scan Connection Burst (T1046)

## What it detects
A single source IP hitting more than 20 distinct destination ports within
1 minute, where the firewall blocked the connection.

## Why it matters
This is the network-level signature of reconnaissance. It is the direct
counterpart to the offensive tool in [rogonzo/netrecon](https://github.com/rogonzo/netrecon) —
running netrecon against a monitored host generates exactly this pattern,
which is the point: offense and detection paired.

## Test it (lab only)

Against a lab host you own, using netrecon:

```bash
python3 netrecon.py LAB_IP -p 1-200

## Proof / validation

Sample log: [`tests/port_scan_burst_sample.log`](../tests/port_scan_burst_sample.log)

25 `ufw-block` lines from one source (`192.168.1.10`) across 25 distinct
destination ports, within ~4 seconds.

Rule condition: `selection | count(dst_port) by src_ip > 20` over `1m`.
25 distinct ports > 20 → **rule fires**.

Validate locally:

```bash
grep 'ufw-block' tests/port_scan_burst_sample.log | \
  awk '{print $NF}' | sort -u | wc -l
# expected: 25  (distinct ports; threshold 20 → fires)
