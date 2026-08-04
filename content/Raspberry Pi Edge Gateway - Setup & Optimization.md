
**Date:** July 28, 2026
**Device:** Raspberry Pi (IP: 10.0.0.122)
**Role:** LibreNMS Server & NAT Gateway

---

## Part 1: GUI Installation & Package Conflict Resolution

### Problem Encountered
Attempted to access the Raspberry Pi via Windows Remote Desktop Connection (XRDP). The RDC window immediately crashed after sign-in. Terminal logs revealed that `raspberrypi-ui-mods` failed to install because of a strict file collision with `pi-greeter` over the file `/usr/share/xgreeters/pi-greeter-labwc.desktop`. The `dpkg` package manager returned an error code (1) and locked the system in a broken state. 

Furthermore, during the re-installation, the progress hung indefinitely at 97% due to the SD card's poor Random Write IOPS performance, causing the background configuration process to freeze.

### Justification & Reasoning
The system explicitly required `pi-greeter` as a dependency but simultaneously refused to let both packages own the same greeter file. High-level `apt` commands failed to bypass the logic loop. We utilized low-level `dpkg` commands to forcefully wedge the package into place and manually clear the collision. When the installation froze at 97%, we terminated the deadlocked process and used a configuration repair command to finish building the boot images.

### Solution & Commands Used
1. Removed the conflicting package and cleared old dependencies:
   `sudo apt remove pi-greeter -y`
   `sudo apt autoremove -y`
2. Forcefully deleted the ghost configuration file:
   `sudo rm -f /usr/share/xgreeters/pi-greeter-labwc.desktop`
3. Repaired the interrupted installation after the 97% freeze:
   `sudo dpkg --configure -a`
4. Rebooted to load the GUI into memory:
   `sudo reboot`

**Result:** Successful installation of the Raspberry Pi OS Desktop environment, accessible headlessly via Windows RDC.

---

## Part 2: Network Speeds & 5GHz Wi-Fi Optimization

### Problem Encountered
Needed to verify if routing traffic through the enterprise Cisco lab (Catalyst Switch ➔ Cisco Router ➔ Pi NAT Gateway) was bottlenecking the end-user internet connection.

### Justification & Reasoning
Ran baseline speed tests through the lab environment versus a direct connection to the home Wi-Fi network. Both tests returned identical results (~59 Mbps Download / ~41 Mbps Upload / 10ms Ping). This verified the Cisco hardware and Pi NAT translations were operating at maximum physical efficiency with near-zero latency added. To attempt to raise the physical ceiling, we shifted the Pi from the congested 2.4GHz band to the 5GHz band.

### Solution & Commands Used
1. Scanned local Wi-Fi frequencies to locate the 5GHz broadcast of the home network (`2-Much` on Channel 157):
   `sudo nmcli device wifi rescan`
   `nmcli device wifi list`
2. Forced the Pi to authenticate specifically to the 5GHz BSSID to bypass the 2.4GHz automatic assignment:
   `sudo nmcli device wifi connect "2-Much" bssid 5A:D7:75:F1:E3:52 password "YourWiFiPassword"`

**Result:** The Pi successfully locked onto the 5GHz access point. Speeds remained identical, confirming the 60/40 Mbps cap is a hard limit set by the ISP plan or physical wall penetration, and not a limitation of the lab's routing hardware.