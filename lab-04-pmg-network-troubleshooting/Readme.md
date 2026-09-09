# Lab 04 - PMG Network Troubleshooting

## Estimated Completion Time

Approximately 90-120 minutes

## Objective

The objective of this lab was to practice structured network troubleshooting using a previously built multi-tier Packet Tracer network.

Instead of building a new topology from scratch, I reused the Lab 03 PMG multi-tier network and intentionally introduced several faults. I then used command-line tools and Cisco switch configuration commands to identify, isolate, repair, and verify each problem.

This lab focused on understanding that similar connectivity symptoms can have very different root causes depending on the OSI layer involved.

---

## Concepts Practiced

* CompTIA Network+ troubleshooting methodology
* IPv4 addressing
* Default gateway troubleshooting
* VLAN configuration
* Access port configuration
* 802.1Q trunking
* Router-on-a-stick
* DHCP
* DNS
* ICMP
* ARP behavior
* Inter-VLAN routing
* Layer 2 vs Layer 3 troubleshooting
* Baseline testing
* Post-repair verification

---

## Environment

### Software

* Cisco Packet Tracer
* Git Bash
* Visual Studio Code
* Git
* GitHub

### Existing Network

The lab reused the PMG multi-tier network created in Lab 03.

The network contained separate VLANs/subnets for:

* Web Tier
* Application Tier
* Database Tier
* Management Tier

The router used a router-on-a-stick configuration to provide inter-VLAN routing.

---

## Network Addressing

| Network Role     | Network         |
| ---------------- | --------------- |
| Web Tier         | 192.168.10.0/24 |
| Application Tier | 192.168.20.0/24 |
| Database Tier    | 192.168.30.0/24 |
| Management Tier  | 192.168.40.0/24 |

### Key Devices

| Device              | IP Address    |
| ------------------- | ------------- |
| Web PC              | 192.168.10.10 |
| Web Gateway         | 192.168.10.1  |
| Application PC      | 192.168.20.10 |
| Application Gateway | 192.168.20.1  |
| Database Server     | 192.168.30.10 |
| Management Gateway  | 192.168.40.1  |
| DHCP/DNS Server     | 192.168.40.30 |
| DHCP Client         | DHCP Assigned |

---

# Baseline Testing

Before introducing faults, I verified that the network was operating according to its intended design.

The Web PC successfully reached:

```text
192.168.10.1
192.168.20.10
```

The Web tier was intentionally blocked from directly accessing the Database tier.

The Application tier was allowed to access the Database tier.

A successful trace from the Application tier to the Database tier showed:

```text
192.168.20.1
192.168.30.10
```

This established a known-good baseline before troubleshooting began.

---

# Incident 1 - Incorrect Default Gateway

## Problem

The Application PC was configured with the incorrect default gateway:

```text
192.168.20.254
```

The correct gateway was:

```text
192.168.20.1
```

## Symptoms

The Web PC could no longer successfully communicate with the Application PC.

```text
ping 192.168.20.10
```

Result:

```text
100% packet loss
```

A traceroute reached the Web gateway but did not successfully complete the connection.

## Diagnostic Command

On the Application PC:

```text
ipconfig
```

The output revealed:

```text
IPv4 Address:    192.168.20.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.20.254
```

## Root Cause

The Application PC could communicate locally, but traffic destined for another subnet required a valid default gateway.

Because the configured gateway did not exist, return traffic could not be routed correctly.

## Fix

Changed the default gateway to:

```text
192.168.20.1
```

## Verification

```text
ping 192.168.20.10
```

Result:

```text
4 sent
4 received
0 lost
```

---

# Incident 2 - Incorrect VLAN Assignment

## Problem

The switch access port connected to the Application PC was intentionally moved into the wrong VLAN.

Application PC connection:

```text
PC Fa0/0 -> Switch0 Fa0/2
```

Fa0/2 was incorrectly assigned to VLAN 10 instead of VLAN 20.

## Symptoms

The Application PC retained the correct IPv4 configuration but became unreachable from the Web tier.

## Diagnostic Commands

```text
show vlan brief
```

and:

```text
show interfaces fa0/2 switchport
```

These commands identified that Fa0/2 belonged to the wrong access VLAN.

## Root Cause

The device had a correct Layer 3 configuration but was connected to the wrong Layer 2 broadcast domain.

## Fix

```text
enable
configure terminal
interface fa0/2
switchport access vlan 20
end
```

## Verification

The Web PC successfully reached the Application PC again:

```text
4 sent
4 received
0 lost
```

---

# Incident 3 - DHCP Failure

## DHCP Environment

A DHCP server and DHCP test client were added to VLAN 40.

### Connections

```text
DHCP Server -> Switch0 Fa0/7
DHCP Client -> Switch0 Fa0/8
```

Both ports were configured as VLAN 40 access ports.

The DHCP server used the static address:

```text
192.168.40.30
```

The DHCP pool was configured to begin at:

```text
192.168.40.100
```

with:

```text
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.40.1
```

## Initial DHCP Issue

Packet Tracer's default DHCP pool initially assigned an unintended address.

The default pool was modified so the desired pool began at:

```text
192.168.40.100
```

The DHCP client then successfully received:

```text
IPv4 Address:    192.168.40.100
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.40.1
```

## Fault

The DHCP service was intentionally disabled.

## Symptoms

After requesting a new lease, the client displayed:

```text
IPv4 Address:    0.0.0.0
Subnet Mask:     0.0.0.0
Default Gateway: 0.0.0.0
```

The client could not reach:

```text
192.168.40.1
192.168.40.30
```

## Troubleshooting Lesson

On real Windows systems, DHCP failure commonly results in an APIPA address from:

```text
169.254.0.0/16
```

Packet Tracer instead displayed `0.0.0.0` in this simulation.

Both indicate that the client failed to obtain a usable DHCP lease.

## Fix

The DHCP service was turned back on and the client requested a new lease.

## Verification

The client successfully reached both:

```text
192.168.40.30
192.168.40.1
```

with:

```text
4 sent
4 received
0 lost
```

---

# Incident 4 - DNS Failure

## DNS Configuration

The server at:

```text
192.168.40.30
```

was also configured as a DNS server.

A DNS record was created:

```text
pmgserver.local -> 192.168.40.30
```

The DHCP pool was updated so clients used:

```text
192.168.40.30
```

as their DNS server.

## Fault

DNS service was intentionally turned off while normal IP connectivity remained operational.

## Symptoms

The client could reach the server by IP address but could not successfully resolve the hostname.

This demonstrated the difference between:

```text
ping 192.168.40.30
```

and:

```text
ping pmgserver.local
```

## Root Cause

Layer 3 connectivity was functioning correctly, but hostname resolution was unavailable.

## Troubleshooting Lesson

If communication by IP address succeeds but communication using a hostname fails, DNS should be investigated.

## Fix

DNS service was restored.

## Verification

The hostname successfully resolved again.

---

# Incident 5 - Router-on-a-Stick Trunk Failure

## Network Connection

The router-on-a-stick connection used:

```text
Switch0 Gi0/1 -> Router0 Gi0/0
```

## Fault

The switch interface was intentionally changed from trunk mode to access mode.

```text
interface gi0/1
switchport mode access
```

## Symptoms

The Web PC could still communicate with itself:

```text
ping 192.168.10.10
```

but could no longer reach its default gateway:

```text
ping 192.168.10.1
```

Result:

```text
100% packet loss
```

## Root Cause

The router used subinterfaces to provide routing for multiple VLANs.

Changing the switch port from trunk mode prevented the switch from carrying multiple 802.1Q tagged VLANs to the router.

This disrupted communication between the VLANs and their router gateway interfaces.

## Fix

The interface was restored to trunk mode:

```text
enable
configure terminal
interface gi0/1
switchport mode trunk
end
```

## Verification

After the trunk recovered, the first ping experienced temporary packet loss while the network reconverged and Layer 2 information refreshed.

The second test returned:

```text
4 sent
4 received
0 lost
```

The router-on-a-stick connection was fully operational again.

---

# Troubleshooting Commands Practiced

### Host Commands

```text
ipconfig
ping
tracert
```

### Cisco IOS Commands

```text
show vlan brief
show interfaces trunk
show interfaces fa0/2 switchport
```

### Configuration Commands

```text
enable
configure terminal
interface
switchport mode access
switchport access vlan
switchport mode trunk
```

---

# Key Lessons Learned

This lab demonstrated that identical symptoms can originate from completely different causes.

A failed ping does not automatically indicate a routing problem.

Potential causes included:

* incorrect default gateway
* incorrect VLAN membership
* unavailable DHCP service
* unavailable DNS service
* broken trunk configuration

Troubleshooting should therefore follow evidence instead of immediately changing configurations.

I also learned the importance of determining whether a failure is actually unexpected. For example, the Web tier was intentionally prevented from directly accessing the Database tier, so failed communication between those two networks represented correct security behavior rather than a fault.

---

# Troubleshooting Methodology

The general process used throughout the lab was:

1. Identify the problem
2. Establish a theory of probable cause
3. Test the theory
4. Establish a plan of action
5. Implement the solution
6. Verify full system functionality
7. Document findings and results

---


