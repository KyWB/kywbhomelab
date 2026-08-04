# Phase 2: Edge Gateway & Server Integration
**Core Hardware:** Raspberry Pi (IP: 10.0.0.122), 64GB Class 10 MicroSD
**Objective:** Deploy a Linux server to act as the primary Wi-Fi bridge to the home internet, while installing a graphical desktop environment for remote management.

---

### Problem 1: GUI Installation Failure & System Freeze
Attempted to access the Raspberry Pi via Windows Remote Desktop Connection (XRDP). The RDC window immediately crashed after sign-in. Terminal logs revealed that `raspberrypi-ui-mods` failed to install because of a strict file collision with `pi-greeter` over the file `/usr/share/xgreeters/pi-greeter-labwc.desktop`. The `dpkg` package manager returned an error code (1) and locked the system in a broken state. Furthermore, during the re-installation, the progress hung indefinitely at 97%.

### Justification & Reasoning
The system explicitly required `pi-greeter` as a dependency but simultaneously refused to let both packages own the same greeter file. High-level `apt` commands failed to bypass the logic loop. When the installation froze at 97%, `htop` analysis showed CPU usage at 0%; the Class 10 SD card's poor Random Write (IOPS) speeds caused the background configuration process to deadlock. We utilized low-level `dpkg` commands to forcefully wedge the package into place, clear the collision, and restart the stalled configuration.

### Solution & Commands Used
1. Removed the conflicting package and cleared old dependencies:
   `sudo apt remove pi-greeter -y`
   `sudo apt autoremove -y`
2. Forcefully deleted the ghost configuration file:
   `sudo rm -f /usr/share/xgreeters/pi-greeter-labwc.desktop`
3. Terminated the deadlocked 97% installation (`Ctrl+C`) and repaired the configuration:
   `sudo dpkg --configure -a`
4. Rebooted to load the GUI into memory:
   `sudo reboot`

**Result:** Successful installation of the Raspberry Pi OS Desktop environment, accessible headlessly via Windows RDC.

---

### Problem 2: Network Speed Verification & 5GHz Optimization
Needed to verify if routing traffic through the enterprise Cisco lab was bottlenecking the connection, as initial speed tests showed a maximum of ~56 Mbps Download. 

### Justification & Reasoning
Ran baseline speed tests through the lab environment versus a direct connection to the home Wi-Fi network. Both tests returned identical results (~56 Mbps). To attempt to raise the physical ceiling, we shifted the Pi from the congested 2.4GHz band to the 5GHz band.

### Solution & Commands Used
1. Scanned local Wi-Fi frequencies to locate the 5GHz broadcast (`2-Much` on Channel 157):
   `sudo nmcli device wifi rescan`
   `nmcli device wifi list`
2. Forced the Pi to authenticate specifically to the 5GHz BSSID to bypass the 2.4GHz automatic assignment:
   `sudo nmcli device wifi connect "2-Much" bssid 5A:D7:75:F1:E3:52 password "YourWiFiPassword"`

**Result:** The Pi successfully locked onto the 5GHz access point. Speeds remained identical (~59 Mbps / 41 Mbps / 10ms Ping), confirming the ceiling is a hard limit set by the ISP plan or physical wall penetration, and verifying the Cisco routing hardware operates with zero bottlenecks and near-zero latency.