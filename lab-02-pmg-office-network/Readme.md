# Lab 02 - PMG Office Network

## Objective
Build and verify a segmented office network for Panda Media Group using Cisco Packet Tracer.

## Network Design
The network contains:

- 1 Cisco 2911 router
- 2 Cisco 2960 switches
- 4 departmental PCs
- 1 internal server

## VLANs

| VLAN | Department | Network |
|---|---|---|
| 10 | Publishing | 192.168.10.0/24 |
| 20 | Creative | 192.168.20.0/24 |
| 30 | Production | 192.168.30.0/24 |
| 40 | Administration | 192.168.40.0/24 |
| 50 | Servers | 192.168.50.0/24 |

## Technologies Configured

- VLAN segmentation
- Access ports
- 802.1Q trunking
- Router-on-a-stick
- Inter-VLAN routing
- DHCP
- DHCP relay using ip helper-address
- Static server addressing
- DNS
- Cisco running-config and startup-config management

## Addressing

PMG-SERVER:
- IP: 192.168.50.10
- Gateway: 192.168.50.1
- DNS: 192.168.50.10

Department PCs receive addresses dynamically through DHCP.

## Verification

Successful tests included:

- Client-to-default-gateway connectivity
- Inter-VLAN connectivity
- Client-to-server connectivity
- DHCP address assignment
- DNS resolution using pmg-server.local

Example successful tests:

```text
ping 192.168.40.100
ping pmg-server.local
