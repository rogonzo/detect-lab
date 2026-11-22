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
