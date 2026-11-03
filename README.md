# Wi-Fi Range Extender Gateway Resolution & Bridging Protocols

This technical reference documents the operational transitions of embedded Customer Premises Equipment (CPE) operating as wireless range extenders, Wi-Fi boosters, and Layer-2 bridging nodes. 

It specifically addresses the network architectural shift where default management IP addresses (predominantly `192.168.188.1` across Wavlink, Pix-Link, Joowin, and MediaTek-based hardware) become unreachable immediately following uplink pairing.

---

## 1. Network Topology Shift: Standalone vs. Bridged State
# Wi-Fi Range Extender Gateway Resolution & Bridging Protocols

This technical reference documents the operational transitions of embedded Customer Premises Equipment (CPE) operating as wireless range extenders, Wi-Fi boosters, and Layer-2 bridging nodes. 

It specifically addresses the network architectural shift where default management IP addresses (predominantly `192.168.188.1` across Wavlink, Pix-Link, Joowin, and MediaTek-based hardware) become unreachable immediately following uplink pairing.

---

## 1. Network Topology Shift: Standalone vs. Bridged State

---

## 2. Dynamic Protocol Mechanics

### A. DHCP Server Teardown
To prevent catastrophic DHCP contention (two competing DHCP servers offering overlapping leases on the same local network segment), the repeater firmware automatically halts its internal DHCP daemon as soon as a 4-way wireless handshake establishes a bridge to an upstream root AP.

### B. Subnet Re-Addressing
The client workstation releases its transient `192.168.188.x` lease and issues a `DHCPDISCOVER` broadcast. The upstream primary router answers with a `DHCPOFFER` in its native subnet (e.g., `192.168.1.0/24` or `192.168.0.0/24`).

### C. Packet Routing Blackholes
When a user attempts to open `http://192.168.188.1` while connected to the bridged network:
1. The host operating system compares the destination IP against its local subnet mask (`255.255.255.0`).
2. The destination belongs to an external subnet.
3. The host forwards the packet to its Default Gateway (the primary router at `192.168.1.1`).
4. The primary router has no static route for `192.168.188.0/24` and drops the packet.

For hardware-specific setup procedures, factory credential lists, and manufacturer login URLs, reference the [technical 192.168.188.1 repeater setup guide](https://routeripguide.com/ip/192-168-188-1).

---

## 3. Command-Line Discovery and Access Workflows

To access the extender administration console without initiating a factory reset, identify the dynamic IP allocated by the root gateway.

### Method 1: Local ARP Cache Inspection
After generating traffic through the repeater, inspect the ARP table to locate the device MAC address:

```bash
# Windows Command Prompt
arp -a | findstr 192.168.1.

# Linux / macOS
arp -an | grep 192.168.1
Method 2: Active Subnet Sweep via Nmap

Perform an ICMP ping sweep across your active local subnet to enumerate all connected hosts:
nmap -sn 192.168.1.0/24
Identify the entry with open HTTP ports (TCP 80 / 443) that does not match your primary gateway or personal devices.  
4. Hardware Factory Reset Protocol

If the extender was misconfigured with an incorrect Wi-Fi passphrase or static IP conflict, hardware reset restores standalone gateway mode:

Maintain continuous power delivery to the wall adapter.
Depress the recessed Reset microswitch using an unbent paperclip for 8 to 10 seconds.
Release the switch immediately upon rapid flashing of the System/WPS LED.
Allow 90 seconds for NVRAM reconstruction. The device will revert to factory defaults and restore management access at http://192.168.188.1.

Maintained by Router IP Guide - Independent Hardware Networking & Gateway Diagnostic Suite.
