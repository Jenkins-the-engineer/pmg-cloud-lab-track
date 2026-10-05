# Lab 06: Secure Network Device Administration

## Objective

Secure the administrative access paths for the PMG network infrastructure and create a dedicated management plane using VLAN 40.

This lab focused on securely administering Cisco routers and switches using local administrator accounts, SSH, management SVIs, VTY restrictions, inactivity timeouts, and unused-port hardening.

## Environment

- Cisco Packet Tracer
- Cisco router-on-a-stick topology
- Layer 2 access switches
- Management VLAN 40
- Git / GitHub for documentation and version control

## Network Architecture

The existing PMG network uses router-on-a-stick for inter-VLAN routing.

Production VLANs:

- VLAN 10 - PUBLIC
- VLAN 20 - APP
- VLAN 30 - DATABASE
- VLAN 40 - MANAGEMENT

Management addressing:

| Device | Management IP |
|---|---|
| PMG-R1 | 10.10.40.1 |
| SW-CORE | 10.10.40.2 |
| SW-PUBLIC | 10.10.40.3 |
| SW-APP | 10.10.40.4 |
| SW-DB | 10.10.40.5 |
| SW-MGMT | 10.10.40.6 |

Subnet mask:

`255.255.255.0`

Default gateway for managed Layer 2 switches:

`10.10.40.1`

## Skills Practiced

- Cisco IOS navigation
- Privileged EXEC mode
- Cisco privilege levels
- Local administrator accounts
- Enable secrets
- Console authentication
- Login banners
- Session inactivity timeouts
- Management VLANs
- Switch Virtual Interfaces (SVIs)
- Layer 2 switch default gateways
- Trunk configuration
- SSH remote administration
- RSA key generation
- SSH version 2
- VTY configuration
- Blocking Telnet
- Unused-port hardening
- Network verification
- Troubleshooting
- Configuration persistence

## Administrative Security

A local administrator account was created using privilege level 15.

The lab used:

- Administrator account: `pmgadmin`
- Privilege level: `15`

Privilege level 15 provides full administrative access to Cisco IOS.

The enable secret protects privileged EXEC access separately from the local administrator credentials.

## Console Security

Console access was configured to authenticate against the device's local user database.

An inactivity timeout was configured so unattended administrative sessions automatically terminate.

A message-of-the-day banner was also configured to warn against unauthorized access.

## Secure Remote Administration

SSH was configured for remote administration.

The devices were configured with:

- Local authentication
- RSA cryptographic keys
- SSH version 2
- SSH-only VTY access
- Five-minute inactivity timeout

The VTY configuration was restricted using:

`transport input ssh`

This prevents Telnet from being used for remote management.

Telnet connectivity was deliberately tested and confirmed to fail while SSH remained functional.

## Management VLAN and SVIs

VLAN 40 was used as the dedicated management VLAN.

Each managed Layer 2 switch received an SVI in VLAN 40.

An SVI, or Switch Virtual Interface, gives a Layer 2 switch a Layer 3 IP address that can be used for administrative access.

Example:

`SW-CORE -> 10.10.40.2`

The switches use `10.10.40.1` as their default gateway.

## VTY Lines

VTY stands for Virtual Teletype.

VTY lines control remote CLI sessions to Cisco devices.

The lab configured VTY lines 0 through 4, providing five possible remote terminal sessions.

The VTY configuration required:

- Local authentication
- SSH-only connections
- Session inactivity timeout

## Trunk Management

Some access-switch trunks initially carried only their production VLAN.

For example, a switch serving VLAN 20 might initially carry:

`1,20`

VLAN 40 was added to the existing allowed VLAN list so management traffic could travel between the access switch and SW-CORE.

The `add` keyword was important because it preserved existing VLANs rather than replacing the trunk's allowed VLAN list.

## Unused-Port Hardening

Unused ports on SW-CORE were placed into:

`VLAN 999 - UNUSED`

Confirmed-unused ports were:

- Assigned to VLAN 999
- Configured as access ports
- Given the description `UNUSED-DISABLED`
- Administratively shut down

Before hardening interfaces, port roles were verified using commands such as:

`show interfaces status`

`show interfaces trunk`

This prevented active uplinks or trunks from being disabled accidentally.

## Troubleshooting

During the lab, the SW-CORE uplink to the router was accidentally configured as an unused access port and administratively shut down.

The affected interface was identified as the router trunk and restored by returning it to trunk mode and issuing `no shutdown`.

Connectivity and trunk operation were then verified.

A second troubleshooting issue occurred when SW-APP could not be reached at its management IP.

Investigation showed that VLAN 40 and its management SVI had not been completely configured.

The management path was rebuilt by:

1. Creating VLAN 40
2. Allowing VLAN 40 across both sides of the trunk
3. Creating the VLAN 40 SVI
4. Assigning the management IP
5. Configuring the switch default gateway
6. Verifying the interface was `up/up`

This restored management connectivity and SSH access.

## Verification

The following management addresses were successfully tested:

- `10.10.40.1`
- `10.10.40.2`
- `10.10.40.3`
- `10.10.40.4`
- `10.10.40.5`
- `10.10.40.6`

SSH authentication was successfully tested against all managed devices.

Telnet access was tested and rejected.

Useful verification commands included:

`show vlan brief`

`show interfaces trunk`

`show ip interface brief`

`show interfaces status`

`show users`

`show running-config`

## Key Takeaways

This lab demonstrated that secure device administration requires more than assigning an IP address.

A secure management workflow includes:

1. Establish administrative identity
2. Protect privileged access
3. Secure console access
4. Create a dedicated management path
5. Configure SSH
6. Restrict remote access through VTY lines
7. Disable insecure protocols such as Telnet
8. Harden unused interfaces
9. Save the configuration
10. Verify that security controls actually work

## Interview Explanation

In this lab, I secured the administrative plane of a segmented Cisco network.

I created local privilege-15 administrator accounts, protected privileged EXEC access, secured console sessions, configured inactivity timeouts, and restricted remote management to SSH version 2.

I implemented a dedicated management network using VLAN 40 and switch virtual interfaces. I extended VLAN 40 across the required trunks so each infrastructure device could be centrally managed.

I also hardened unused switch ports by moving them to an unused VLAN and administratively disabling them.

During troubleshooting, I recovered an accidentally disabled router trunk and diagnosed a missing management VLAN path on an access switch.

The lab gave me practical experience securing, managing, verifying, and troubleshooting network infrastructure rather than only learning the concepts theoretically.