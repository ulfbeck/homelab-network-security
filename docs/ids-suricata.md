# Homelab – IDS & Attack Simulation

## Overview

This lab extends the homelab with:

- Suricata IDS for traffic analysis
- Kali Linux as attacker machine
- Realistic attack simulation and detection

The goal is to understand how network traffic looks during normal behavior vs attack activity, and how IDS detection works in practice.

---

## Architecture

### Physical host
- Lenovo mini-PC → Proxmox VE (hypervisor)

### Virtual machines
- rtr-pi (Router + Suricata IDS)
- dns-pi (Pi-hole DNS)
- Ubuntu Server (target system)
- Kali Linux (attacker)

### Network design
- Lab network: 192.168.10.0/24
- Segmented from home network
- Traffic routed via rtr-pi

---

## Suricata Setup

Suricata is running on rtr-pi and analyzing routed traffic.

Logs are written to:

/var/log/suricata/eve.json

Traffic is analyzed using:

- flow (network connections)
- http (application data)
- alert (IDS triggers)
- fileinfo (payload visibility)

Example command:

jq -r 'select(.event_type=="alert")' /var/log/suricata/eve.json

---

## Attack Simulation

Kali Linux is used to simulate attacker behavior inside the lab network.

IP address:
192.168.10.183

---

### Nmap Scan

Command:

nmap -sS 192.168.10.1

Result:

- High number of TCP flows (~6000)
- Many short-lived connections
- Several failed connections
- Few IDS alerts

Interpretation:

Behavior consistent with reconnaissance / port scanning.

Key learning:

Scanning generates large amounts of traffic, but does not always trigger alerts.

---

### IDS Test (Payload-based detection)

Command:

curl http://testmyids.com

Result:

Suricata alert triggered:

GPL ATTACK_RESPONSE id check returned root

Interpretation:

- This is a known test payload
- Confirms IDS detection is working
- Shows that content-based detection is effective

---

## Traffic Analysis

Observed traffic included:

- TCP connections to gateway (192.168.10.1)
- ICMP (ping)
- HTTP requests to external servers
- APT traffic (system updates)

Example alerts:

- ET INFO GNU/Linux APT User-Agent
- Possible Kali Linux hostname (DHCP)
- IDS test alert

Key learning:

Not all alerts indicate attacks. Many are informational (“noise”).

---

## Key Insight – IDS Visibility

Suricata did NOT detect all attack traffic.

Explanation:

Traffic between hosts in the same VLAN does not pass through the router.

Therefore:

- Internal (east-west) traffic is not always visible
- IDS visibility depends on network placement

Key learning:

Detection is not only about tools – it is about where the sensor is placed in the network.

---

## SSH Hardening

Basic SSH hardening was implemented.

Changes:

- Disabled root login over SSH

Rationale:

Root accounts are common targets for brute force attacks. Disabling direct root login reduces risk and improves security posture.

---

## Current Status

- VLAN segmentation working
- Routing working
- DNS via Pi-hole working
- Suricata IDS active
- Kali attacker VM active
- Detection pipeline verified

---

## Next Steps

- Improve detection (trigger more IDS rules)
- Analyze flows in more detail (ports, states)
- Add Windows + Active Directory (IAM lab)
- Create detection use cases (SOC-style analysis)