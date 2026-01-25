# Firewall Policy (iptables)

## Purpose
The firewall on the lab router (`rtr-pi`) is designed to strictly isolate the lab environment from the production home network, while still allowing controlled internet access and secure management.

The policy follows the principle of **least privilege** and is implemented using stateful iptables rules.

---

## High-Level Policy
- Allow lab network → internet
- Block lab network → home network
- Allow established/related traffic back
- Restrict management access (SSH)
- Log denied traffic for visibility and troubleshooting

---

## Traffic Flow Overview

| Source            | Destination        | Action | Reason |
|-------------------|--------------------|--------|--------|
| Lab (192.168.10.0/24) | Internet          | Allow  | Required for testing and updates |
| Lab (192.168.10.0/24) | Home (192.168.0.0/24) | Drop   | Protect production network |
| Home (192.168.0.0/24) | Router (SSH)      | Allow  | Management access |
| Lab (192.168.10.0/24) | Router (SSH)      | Drop   | Prevent lab lateral movement |
| Established flows | Originator         | Allow  | Stateful firewall behavior |

---

## FORWARD Chain Rules (Order Matters)

The FORWARD chain is evaluated **top to bottom**.  
Rules are ordered intentionally to ensure correct behavior.

### 1. Allow established and related connections

```bash
-A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT


### 2. Log blocked lab → home traffic

-A FORWARD -s 192.168.10.0/24 -d 192.168.0.0/24 -j LOG --log-prefix "LABB->HEM BLOCK: "

### 3. Drop lab → home traffic

-A FORWARD -s 192.168.10.0/24 -d 192.168.0.0/24 -j DROP

### Allow lab → internet traffic (via WAN interface)

-A FORWARD -s 192.168.10.0/24 -o eth0 -j ACCEPT

### INPUT Chain Rules (Router Protection)
Allow SSH from home network

-A INPUT -p tcp -s 192.168.0.0/24 --dport 22 -j ACCEPT

Drop SSH from lab network

-A INPUT -p tcp -s 192.168.10.0/24 --dport 22 -j DROP

This prevents lab devices from accessing the router directly.

### Logging and Verification

Blocked traffic is logged to the kernel log and verified using:

journalctl -k | grep "LABB->HEM BLOCK"

This confirms that firewall rules are active and functioning as intended.

### Persistence

Firewall rules are made persistent across reboots using iptables-persistent.

This ensures the security policy survives restarts and power outages.

### Key Learning Outcomes

Firewall rules are evaluated top-down

Stateful filtering is critical for functionality

Logging is essential for validation and troubleshooting

Persistence is required for production-like stability







