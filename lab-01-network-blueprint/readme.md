# Lab 001 - pmg network blueprint
## Objective
design an ipv4 addressing plan for a small panda media group office network using variable length subnetting
the network must support 4 seperate groups
- Guest wifi 40 host
- production 25 host
- management 10 host
- servers    8  host

## Starting Network
The network uses private ipv4 range

- 192.168.10.0/24

A/24 provides 256 total ipv4 addresses

## Subnet design
The largest network was allocated first to reduce wasted addresses

Network     Host needed  cidr  subnetmask      Network Address     usable host range            brodcast        default
Guest Wifi      40       /26   255.255.255.192   192.168.10.0    192.168.10.1-192.168.10.62  192.168.10.63   192.168.10.1
Production      25       /27   255.255.255.224   192.168.10.64   192.168.10.65-192.168.10.94 192.168.10.95  192.168.10.65
Management      10       /28   255.255.255.240   192.168.10.96   192.168.10.97-192.168.10.110 192.168.10.111 192.168.10.97
Servers         8        /28   255.255.255.240   192.168.10.112  192.168.10.113-192.168.10.126 192.168.10.127 192.168.10.113

## Address Space Remaining
The assigned subnets consume addresses from:
192.168.10.0-192.168.10.127
The following addresses remain available for future expansion
192.168.10.128-192.168.10.255

## Skills practiced
ipv4 addressing
cidr notation
subnet masks
variable lengh subnet mask (vlsm)
network addresses
broadcast addresses
usable host ranges
default gateway planning
private ipv4 addressing
network capacity planning

## Verification
The addressing plan was checked to ensure
- no subnets overlapped
- every subnet supported the required number of host
- network and broadcast addresses were not assigned to host
- the first usable address of each subnet is reserved for the default gateway
- unused address space remained available for the future network expansion



