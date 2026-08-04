# Phase 3: Network Monitoring & LibreNMS
**Core Software:** LibreNMS (Web UI: 10.0.0.122:8000), SNMP
**Objective:** Deploy centralized monitoring to graph interface bandwidth, CPU load, and memory usage across all lab hardware in real-time.

---

### Problem Encountered
LibreNMS was successfully installed and polling devices via SNMP, but the dashboard graphs were largely flat, making it difficult to verify if the monitoring system was accurately capturing network bottlenecks or hardware stress.

### Justification & Reasoning
LibreNMS relies on SNMP polling on strict 5-minute intervals. To validate that the software is correctly graphing interface utilization and device strain, realistic heavy traffic needed to be pushed through the entire routing chain (End Device ➔ Switch ➔ Router ➔ NAT Gateway ➔ Internet).

### Solution Implemented
1. **Bandwidth Stress Testing:** Connected end-user devices (laptops/phones) to the Catalyst switch. Ran multiple simultaneous 4K video streams (UDP traffic) and maximum-capacity speed tests (TCP traffic) to saturate the internal links.
2. **CPU & NAT Stress Testing:** Initiated heavy peer-to-peer downloads (e.g., Linux ISO torrents) on a connected endpoint. This forces the Cisco Router and Raspberry Pi to instantly calculate and track hundreds of simultaneous, fragmented NAT translations.
3. **Data Verification:** Monitored the LibreNMS dashboard over a 15-minute period to allow multiple SNMP polling cycles to complete and render the data.

**Result:** Live data successfully populated the LibreNMS dashboard, proving the SNMP configurations on the Cisco hardware are accurately reporting interface utilization and hardware resource spikes.