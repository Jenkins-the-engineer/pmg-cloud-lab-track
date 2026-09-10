Lab 05 - PMG Cloud Network Design

Objective:
Design and configure a segmented cloud-style network using VLANs, subnets, trunking, router-on-a-stick, and ACLs.

Network:
10.10.0.0/16

Subnets:
PUBLIC      10.10.10.0/24
APP         10.10.20.0/24
DATABASE    10.10.30.0/24
MANAGEMENT  10.10.40.0/24

Key Tasks:
- Created four VLANs
- Configured access and trunk ports
- Configured router subinterfaces
- Assigned default gateways
- Verified inter-subnet routing
- Applied ACL 110
- Blocked PUBLIC access to DATABASE
- Allowed APP and MANAGEMENT access to DATABASE

Verification:
- All hosts successfully reached their default gateways
- Inter-subnet routing worked before ACL enforcement
- PUBLIC to DATABASE traffic was blocked
- PUBLIC to APP traffic remained allowed
- APP to DATABASE traffic remained allowed
- MANAGEMENT to DATABASE traffic remained allowed

What I Learned:
This lab showed how a large private network can be divided into smaller subnets and security zones. I also practiced how VLANs, trunks, routing, gateways, and ACLs work together to create a segmented network architecture.