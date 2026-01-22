# Homelab – Network Segmentation, Firewall and IDS

## Overview
This project documents the design and implementation of a secure homelab built in parallel with a production home network.  
The goal is to enable safe experimentation with networking and security without impacting critical household services such as online banking, etc or IoT devices.

The project is created as a portfolio for upcoming LIA applications and entry-level roles within IT and IT security.

---

## Network Architecture
The environment is split into two logical zones using VLAN segmentation:

- **Home Network (VLAN 1):** 192.168.0.0/24  
- **Lab Network (VLAN 10):** 192.168.10.0/24  

A managed switch is used for Layer 2 separation, while routing, firewalling and monitoring are handled by a dedicated router.

---

## Components
- TP-Link Home Router (production network)
- Managed Switch (VLAN capable)
- Raspberry Pi – Router (routing, NAT, firewall, DHCP)
- Raspberry Pi – DNS (Pi-hole)
- Suricata IDS

---

## Routing and NAT
The lab network uses private addressing and is routed through a dedicated router.  
Source NAT (MASQUERADE) is applied to allow lab clients to access the internet without exposing internal addresses or requiring changes to the home network design.

---

## DNS Architecture
DNS for the lab network is handled by a dedicated Pi-hole instance.  
The lab router distributes the DNS server via DHCP, while DNS services on the router itself are disabled to maintain clear separation of roles.

---

## Firewall and Security
Stateful firewall rules are implemented on the lab router to control traffic between networks:

- Lab network traffic to the internet is allowed
- Traffic from the lab network to the home network is blocked
- Management access (SSH) to the router is restricted to the home network
- Denied traffic is logged to provide visibility

---

## Intrusion Detection
A lightweight IDS using Suricata is deployed on the router to monitor lab traffic.  
Suspicious activity such as scanning behavior is detected and logged, providing insight into potential misconfigurations or security issues.

---

## Project Structure
Detailed documentation and configurations are available in the following directories:

- `docs/` – Technical documentation and design explanations  
- `configs/` – Example configuration files  
- `screenshots/` – Verification screenshots  

---

## Learning Outcomes
This project demonstrates practical knowledge of:

- VLAN-based network segmentation
- Routing and NAT
- DHCP and DNS separation
- Stateful firewall design and rule ordering
- Logging and traffic visibility
- Intrusion detection using Suricata

---

## Future Improvements
- Centralized log collection
- Expanded IDS rule tuning
- Additional monitoring and alerting
