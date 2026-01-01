<h1>Detection Rules</h1>

Detection rules are used in IDS mode to monitor network traffic for suspicious or malicious activity.
They analyze packets based on defined signatures and conditions without blocking the traffic.
When a match is found, the system generates alerts to help administrators identify and investigate threats.

```
alert icmp any any -> $HOME_NET any (msg:"ICMP Ping"; sid:1; rev:1;)
alert tcp any any -> $HOME_NET 22 (msg:"TCP Connection to SSH Port 22 Detected"; flow:to_server; flags:S; classtype:attempted-recon; sid:1000001; rev: 1;)

# Rule 2 - Will trigger for nmap -p 22 <ip>
alert tcp any any -> $HOME_NET 22 (msg:"Nmap Connect Scan on SSH Port 22"; flow:to_server,stateless; flags: S; threshold:type both, track by_src, count 1, seconds 60; classtype: attempted-recon; sid:1000002; rev:1;)
pass tcp <ip> any -> $HOME_NET 22 (msg:"Allowed SSH"; sid:999999; rev:1;)

# 1. DROP - Blocks:  nmap -p 22 <ip> and nmap <ip> (when scanning port 22)
drop tcp any any -> $HOME_NET 22 (msg:"IPS:  Blocked Nmap Scan on SSH Port 22"; flags:S; flow:to_server,stateless; classtype:attempted-recon; sid: 1000003; rev:1;)

# --- ICMP Flood Protection ---
drop icmp any any -> $HOME_NET any (msg:"IPS: ICMP Flood Detected"; itype:8; detection_filter:track by_src, count 50, seconds 10; sid:1000004; rev:1;)
#drop tcp any any -> $HOME_NET 22 (msg:"IPS: SSH Brute Force Blocked"; flow:to_server,established; threshold:type both,track by_src,count 5,seconds 120; sid:1000005; rev:1;)

# --- SYN Flood Protection ---
drop tcp any any -> $HOME_NET any (msg:"IPS: SYN Flood Detected"; flags:S; threshold: type both,track by_src,count 100,seconds 10; sid:1000006; rev:1;)

# --- Block NULL and XMAS Scans ---
drop tcp any any -> $HOME_NET any (msg:"IPS: NULL Scan Detected"; flags:0; sid:1000007; rev:1;)
drop tcp any any -> $HOME_NET any (msg:"IPS: XMAS Scan Detected"; flags:FPU; sid:1000008; rev:1;)

#drop tcp any any -> $HOME_NET 22 (msg:"IPS:  Hydra Rapid SSH Connections"; flow:to_server,stateless; flags:S; threshold:type threshold,track by_src,count 5,seconds 10; sid:1000009; rev:1;)
```
