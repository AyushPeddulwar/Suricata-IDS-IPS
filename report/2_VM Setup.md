# VM Setup

This step involves installing and configuring Suricata IDS IPS on an Ubuntu virtual machine. The VM is placed on the same network as the attacker and target machines so it can monitor all traffic. For this project, Suricata is used as the primary network-based intrusion detection & prevention system.

| Component | Role | Location | OS
| --- | --- | --- | ---- |
| `Suricata` | IDS IPS | VMware | Ubuntu |
| `attack vm ` | offensive | VMware | kali linux |
| `user endpoint vm` | authorise user | VMware | linux |

---

### ⚙️ Deployment Breakdown

### 💻 **Ubuntu VM**

![image.png](media/VM%20&%20EDR%20Setup/image1.png)

### ☁️ **Kali Linux**

![image.png](media/VM%20&%20EDR%20Setup/image3.png)
