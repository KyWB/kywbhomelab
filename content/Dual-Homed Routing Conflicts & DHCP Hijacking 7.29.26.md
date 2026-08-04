## Problem Description

When connecting the Windows management laptop or the Raspberry Pi to the isolated Cisco lab network via Ethernet, the devices would completely lose internet access.

**Observed Symptoms:**

- **Windows Laptop:** Complete loss of internet connectivity immediately upon plugging into the Cisco switch (FastEthernet0/3).
    
- **LibreNMS:** Graphs showed massive, perfectly symmetrical traffic spikes (Packets In matching Packets Out), visually mimicking a Layer 2 switching loop.
    
- **Raspberry Pi:** SSH sessions (PuTTY) became highly unstable, freezing, and dropping connections. The Pi was unable to ping external addresses like `google.com`.
    
- **Cisco Switch:** Logs showed no Spanning Tree (STP) blocks or MAC flap errors, ruling out a physical Layer 2 loop.
    

## Root Cause Analysis

The issue was caused by **Windows and Linux Routing Metric Confusion** combined with a **DHCP Gateway Hijack**.

Because both the laptop and the Raspberry Pi were configured to use DHCP on their Ethernet adapters, the Cisco router handed them an IP address _and_ declared itself as the Default Gateway.

1. The operating systems inherently prioritized the wired Ethernet connection over the wireless (Wi-Fi) connection.
    
2. The OS updated its internal routing table to send all external web traffic into the Cisco router.
    
3. Because the lab network is isolated and not performing NAT out to the internet, the traffic hit a dead end.
    
4. The massive spikes in LibreNMS were caused by the laptop attempting to route high volumes of background web traffic through the switch, which promptly dropped or bounced the packets.
    

## Resolution: Windows Management Laptop

To prevent Windows from sending internet traffic into the lab, the Ethernet adapter must be assigned a static IP with the Default Gateway intentionally left blank.

**Steps:**

1. Open **Network Connections** (`ncpa.cpl`).
    
2. Right-click the Ethernet adapter connected to the lab and select **Properties**.
    
3. Double-click **Internet Protocol Version 4 (TCP/IPv4)**.
    
4. Select **Use the following IP address** and apply the following settings:
    
    - **IP address:** `10.10.10.50` _(A memorable, dedicated management IP)_
        
    - **Subnet mask:** `255.255.255.0`
        
    - **Default gateway:** _(Leave entirely blank)_
        
    - **DNS servers:** _(Leave entirely blank)_
        
5. Click **OK** to apply. Windows will now strictly use this interface for `10.10.10.x` traffic and rely on Wi-Fi for standard internet routing.
    

## Resolution: Raspberry Pi (OS Bookworm / NetworkManager)

To permanently shield the Raspberry Pi from DHCP hijacks while maintaining its dual-homed status, we used `nmcli` to set a static IP, clear the gateway, and apply the `never-default` flag.

**Steps:**

1. Identify the active Ethernet connection name:
    
    Bash
    
    ```
    nmcli connection show
    ```
    
    _(In this environment, the connection was named `netplan-eth0`)_.
    
2. Modify the connection to use a static IP, clear the gateway, and explicitly forbid it from becoming the default route:
    
    Bash
    
    ```
sudo nmcli connection modify "netplan- eth0" ipv4.method manual ipv4.addresses 10.10.10.11/24  ipv4.gateway "" ipv4.never-default yes
    ```
    
3. Restart the interface to apply the changes immediately:
    
    Bash
    
    ```
    sudo nmcli connection up "netplan-eth0"
    ```
    

## Verification

To validate the Raspberry Pi is routing traffic correctly, check the routing table:

Bash

```
ip route
```

**Expected Output:**

Plaintext

```
default via 10.0.0.1 dev wlan0 proto dhcp src 10.0.0.122 metric 600
10.0.0.0/24 dev wlan0 proto kernel scope link src 10.0.0.122 metric 600
10.10.10.0/24 dev eth0 proto kernel scope link src 10.10.10.11 metric 100
```

- **Validation 1:** Only one `default` route exists, pointing to the home Wi-Fi gateway (`10.0.0.1`).
    
- **Validation 2:** The lab subnet (`10.10.10.0/24`) is strictly mapped to `eth0`, allowing direct communication with lab equipment without hijacking internet traffic.
    
- **Validation 3:** Running `ping google.com` succeeds via the `wlan0` interface.