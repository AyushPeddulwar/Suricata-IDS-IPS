# Installing Suricata IDS on Ubuntu

This section covers the full setup of the Suricata Network IDS on Ubuntu to monitor traffic User Endpoint VM (Linux/Windows).

```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt-get update
sudo apt-get install suricata -y

sudo systemctl enable suricata.service
sudo systemctl status suricata
```

### Basic Configuration
```
sudo nano /etc/suricata/suricata.yaml
```
Set your network range
```
vars:
  address-groups:
    HOME_NET: "[192.168.0.0/24]"
```
Configure Interface (IDS Mode)
```
af-packet:
  - interface: eth0 or ensp0s3
```
Update Rules
```
sudo suricata-update
ls /var/lib/suricata/rules/
```
Custom rules
```
rules-files:
  - suricata.rules
  - /etc/suricata/rules/local.rules
```
Test Configuration
```
sudo suricata -T -c /etc/suricata/suricata.yaml
sudo systemctl start suricata.service
sudo systemctl status suricata
```
Important files: <br>
eve.json → alerts, events (used by SIEMs)<br>
fast.log → simple alert log <br>
stats.log → performance data <br>
Example: sudo tail -f /var/log/suricata/fast.log
<br><br>
### Test Detection (Safe Test)
```
curl http://testmynids.org/uid/index.html
```

## Custom Rules
Open the Local Rules File
``` 
sudo vim /etc/suricata/rules/local.rules
```
Add a Simple Rule
```
alert icmp any any -> $HOME_NET any (msg:"ICMP Ping Detected"; sid:1000001; rev:1;)
```
Test the Configuration
```
sudo suricata -T -c /etc/suricata/suricata.yaml
```
Trigger the Rule (Test)
```
ping <ip>
```
View the Alert
```
sudo tail -f /var/log/suricata/fast.log
```




