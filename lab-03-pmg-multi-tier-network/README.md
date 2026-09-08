# Lab 03 - PMG Multi-Tier Network

## Objective

Build and configure a segmented multi-tier network for Panda Media Group using VLANs, trunking, inter-VLAN routing, and access control lists.

## Skills Practiced

* VLAN configuration
* 802.1Q trunking
* Router-on-a-stick
* Inter-VLAN routing
* IPv4 addressing
* Default gateway configuration
* Access Control Lists (ACLs)
* Network segmentation
* Connectivity testing
* Network troubleshooting
* Cisco IOS commands
* Git documentation and version control

## Network Design

The PMG network will be divided into four functional tiers:

| VLAN | Name | Purpose                               |
| ---- | ---- | ------------------------------------- |
| 10   | WEB  | Web-facing systems                    |
| 20   | APP  | Application systems                   |
| 30   | DATA | Database and data systems             |
| 40   | MGMT | Administrative and management systems |

## Security Goals

The network will use segmentation and ACLs to control communication between tiers.

Planned traffic rules:

* WEB devices may communicate with the APP tier.
* APP devices may communicate with the DATA tier.
* Unauthorized devices should not directly access the DATA tier.
* MGMT devices should be able to administer all network tiers.

## Tasks

* Build the network topology in Cisco Packet Tracer.
* Create VLANs 10, 20, 30, and 40.
* Assign switch ports to the correct VLANs.
* Configure an 802.1Q trunk.
* Configure router subinterfaces.
* Configure IP addresses and default gateways.
* Verify inter-VLAN routing.
* Configure ACL security rules.
* Test permitted and denied traffic.
* Troubleshoot configuration problems.
* Save the Packet Tracer topology.
* Document verification results.
* Commit the completed lab to Git.

## Verification

The lab is complete when:

* All required VLANs exist.
* Access ports belong to the correct VLANs.
* The switch-to-router connection is trunking correctly.
* Router subinterfaces are operational.
* Devices have the correct IP addressing.
* Permitted traffic succeeds.
* Restricted traffic is blocked by ACLs.
* Configuration is saved.
* Lab documentation and supporting files are committed to Git.

## Results

Lab 03 was completed successfully.

Verification results:

* VLANs 10, 20, 30, and 40 were created successfully.
* The switch-to-router link operated as an 802.1Q trunk.
* Router subinterfaces for all four VLANs were up/up.
* Inter-VLAN routing worked before ACL enforcement.
* WEB traffic to the DATA VLAN was successfully blocked.
* APP traffic to the DATA VLAN was permitted.
* MGMT traffic to the DATA VLAN was permitted.
* ACL 100 recorded matches on the deny rule.
* Router and switch configurations were saved successfully.

## Key Takeaway

This lab demonstrated that VLANs provide logical segmentation, while ACLs enforce communication policy between network segments. A network can be fully routable while still restricting access based on security requirements.
