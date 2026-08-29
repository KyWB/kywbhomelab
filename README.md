# 🖥️ KyWB Homelab Infrastructure

Welcome to my homelab repository. This vault serves as the live documentation, architecture mapping, and version control for my enterprise networking and routing environment.

### 📂 Active Documentation
* [View the Latest Content & Configurations](./content)
---

### ⚙️ Hardware Architecture

| Device          | Model            | Role in Network                              |
| :-------------- | :--------------- | :------------------------------------------- |
| **Edge Router** | Raspberry Pi     | NAT, Inter-VLAN Routing (Router-on-a-Stick)  |
| **Core Switch** | Cisco Catalyst   | Layer 2 Switching, 802.1q Trunking           |
| **Management**  | Windows Laptop   | Network Benchmarking, SSH, Version Control 
|**Auxillary Device**|Mini Mac       |Exploit and Red-teaming lab
| **Branch Node** | Cisco 800 Series | Site-to-Site Routing Protocol Lab (OSPF/BGP) |

---

### 🛠️ Software & Technologies

| Category                  | Technologies Deployed                      |
| :------------------------ | :----------------------------------------- |
| **Networking Protocols**  | 802.1Q, IPv4 Routing, NAT, DHCP, TCP/UDP   |
| **Quality of Service**    | Layer 2 DSCP (Expedited Forwarding)        |
| **Operating Systems**     | Debian Linux, Cisco IOS, Windows 11        |
| **Observability & Tools** | librenms, iperf3, Authentik, Pi-Hole,Firewalld|
| **DevOps**                | Git, GitHub, Obsidian                      |

---

### 🗺️ Network Topology
![Lab Topology Map]
![[topology.png]]

### 🚀 Recent Milestones & Projects

* **Engineered** a router-on-a-stick topology on a Raspberry Pi and Cisco Catalyst switch using 802.1Q VLAN trunking, boosting inter-VLAN throughput to 819 Mbps.
* **Benchmarked** local LAN throughput using iperf3 and tracert to identify physical Layer 1/2 hardware bottlenecks and verify gigabit speeds.
* **Resolved** Windows TCP/IP driver conflicts and virtual adapter DHCP failures by reconfiguring static IP bindings and netsh parameters.
* **Utilized** Industry services for enhanced DNS consistency, SSO for administrator tools, and proxies for organization of services. 
