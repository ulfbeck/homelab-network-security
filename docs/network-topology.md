# Network Topology

## Goal
Build a homelab that is isolated from the production home network to ensure critical services (e.g., online banking, IPTV) remain stable while enabling security and networking experiments.

---

## Logical Segmentation
Two zones are separated using VLANs:

- **VLAN 1 – Home Network:** `192.168.0.0/24`
- **VLAN 10 – Lab Network:** `192.168.10.0/24`

---

## Physical Topology (High-Level)

[ TP-Link Home Router / Deco ]
LAN: 192.168.0.1/24
|
| (VLAN 1 - Home)
v
[ Managed Switch ]
| |
| Port 3 (VLAN 1) | Port 4 (VLAN 10)
v v
[ rtr-pi (Router) ]
eth0 (WAN/Home): 192.168.0.10
eth1 (LAN/Lab): 192.168.10.1
|
| (VLAN 10 - Lab)
v
[ dns-pi (Pi-hole) ]
192.168.10.2
|
v
[ Lab Client (Port 6) ]
DHCP from rtr-pi, DNS via dns-pi


---

## Roles and Responsibilities

### TP-Link Home Router (Production)
- Provides internet access to the home network
- DHCP and DNS remain on the home router for home clients
- Contains a static route to reach the lab network via `rtr-pi`

### Managed Switch
- Enforces Layer 2 separation via VLANs
- Provides dedicated access ports for home and lab devices

### rtr-pi (Lab Router)
- Routes between VLAN 10 (lab) and VLAN 1 (home uplink)
- Performs NAT for lab clients to reach the internet
- Runs DHCP for the lab network
- Enforces firewall rules (lab → home blocked, lab → internet allowed)

### dns-pi (Pi-hole)
- Provides DNS filtering and visibility for lab clients
- Used only inside VLAN 10 to avoid impacting home services

---

## Switch Port Plan (Current)
- **Port 3:** rtr-pi `eth0` (Home / VLAN 1)
- **Port 4:** rtr-pi `eth1` (Lab / VLAN 10)
- **Port 5:** dns-pi (Lab / VLAN 10)
- **Port 6:** Lab Client (Lab / VLAN 10)

(Other ports remain in VLAN 1 for the production home network.)
