# Phase 1: Cisco Foundation & Routing
**Core Hardware:** Cisco Router, Cisco Catalyst Switch
**Objective:** Establish an isolated, enterprise-grade lab network that routes traffic independently from the main home network while maintaining internet access.

---

### Problem Encountered
Needed to create an isolated lab environment that could accommodate multiple end-user devices, provide dynamic IP addressing, and route traffic to the internet without relying on the primary home ISP router for internal lab switching.

### Justification & Reasoning
To simulate a true enterprise environment, the lab required a dedicated Cisco Router to handle Network Address Translation (NAT) and DHCP, and a Catalyst Switch to handle Layer 2 endpoint connectivity. This ensures that any configurations, spanning tree elections, or broadcast storms remain entirely contained within the lab environment.

### Solution Implemented
1. **Physical Topology:** Connected the Cisco Catalyst switch to the Cisco Router's internal interface.
2. **DHCP Services:** Configured the Cisco Router to act as a DHCP server, leasing out IP addresses to any devices plugged into the Catalyst switch.
3. **NAT Configuration:** Configured Port Address Translation (PAT/NAT) on the Cisco router's external-facing interface to translate all internal lab IP addresses to a single exit IP.
4. **Endpoint Integration:** Plugged end-user devices (laptops, PCs, or a wireless Access Point) directly into the Catalyst switch to populate the network.

**Result:** A fully functional, isolated Layer 2 / Layer 3 Cisco environment capable of routing internal traffic and reaching the external edge gateway.