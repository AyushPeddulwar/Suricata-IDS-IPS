<h1>Implementing IPS with Suricata</h1>

Enable IP Forwarding
```
# Enable IP forwarding temporarily
sudo sysctl -w net.ipv4.ip_forward=1

# Make it permanent
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```
Configure NFQueue for IPS Mode
```
# Set up iptables rules to send traffic to NFQueue
sudo iptables -I FORWARD -j NFQUEUE --queue-num 0

# For all traffic (input, output, forward):
sudo iptables -I INPUT -j NFQUEUE --queue-num 0
sudo iptables -I OUTPUT -j NFQUEUE --queue-num 0
```
Create IPS Rules
```
sudo vim /etc/suricata/rules/local.rules
# Drop ICMP ping 
drop icmp any any -> any any (msg:"ICMP Ping Dropped"; sid:1000002; rev:1;)
```
Start Suricata in IPS Mode
```
# Test configuration
sudo suricata -T -c /etc/suricata/suricata.yaml

# Run Suricata in IPS mode with NFQueue
sudo suricata -c /etc/suricata/suricata.yaml -q 0
```
### Testing Your IPS Setup
From Attacker Machine:
```
# Test ICMP blocking
ping <target-ip>  # Should be blocked
```
Monitor Logs:
```
sudo tail -f /var/log/suricata/fast.log
```
Troubleshooting Tips
```
# Check if Suricata is running
sudo systemctl status suricata

# Verify NFQueue is working
sudo iptables -L -v -n | grep NFQUEUE
```

### IPS vs IDS Rule Actions
| Action | Description |
|------|-------------|
| alert | Generate alert only (IDS mode) |
| drop | Drop packet and generate alert (IPS mode) |
| reject | Drop packet, send RST/ICMP, and alert |
| pass | Allow packet without further inspection |
