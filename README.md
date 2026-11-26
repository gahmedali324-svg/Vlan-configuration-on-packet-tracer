All four PCs share the subnet 192.168.100.0/24, but VLANs separate them at Layer 2:

Hosts in the same VLAN can communicate, even when they are connected to different switches (the trunk carries both VLANs).
Hosts in different VLANs cannot communicate, even though they share an IP subnet.
Topology

Topology

VLAN	Name	Hosts
10	IT	192.168.100.2 (PC0), 192.168.100.4
20	HR	192.168.100.3, 192.168.100.5 (PC3)
Two switches (Sw1 and Sw2), each with one host in VLAN 10 and one in VLAN 20
Fa0/2 is an access port for VLAN 10 and Fa0/3 is an access port for VLAN 20 on both switches
Fa0/1 on each switch is the trunk link between them
Tools Used
Cisco Packet Tracer
Catalyst switches and PC-PT end devices
Configuration

Applied on both Sw1 and Sw2:

vlan 10
 name IT
vlan 20
 name HR
exit

interface fa0/2
 switchport mode access
 switchport access vlan 10
 exit
interface fa0/3
 switchport mode access
 switchport access vlan 20
 exit
interface fa0/1
 switchport mode trunk
 exit

do write memory

Verify with:

show vlan brief
show interfaces trunk
Verification

Ping tests from PC0 and PC3:

Source	Destination	Expected	Result
PC0	192.168.100.4	Reachable (same VLAN, across trunk)	✅ 4/4 replies, 0% loss
PC0	192.168.100.3	Blocked (different VLAN)	✅ 100% loss
PC0	192.168.100.5	Blocked (different VLAN)	✅ 100% loss
PC3	192.168.100.3	Reachable (same VLAN, across trunk)	✅ 4/4 replies, 0% loss
PC3	192.168.100.2	Blocked (different VLAN)	✅ 100% loss
PC3	192.168.100.4	Blocked (different VLAN)	✅ 100% loss

Screenshots:

PC0 ping results
PC3 ping results
Sw1 configuration
Sw2 configuration
Troubleshooting Notes
While bringing up the trunk, Sw2 logged an Inconsistent port type spanning-tree message on Fa0/1. It taught me to configure both ends of the link explicitly as trunk ports and to check the result with show interfaces trunk.
switchport mode trunk only works in interface configuration mode. Typing it in global configuration returns % Invalid input detected.
What I Learned
How VLANs isolate traffic at Layer 2, even inside one IP subnet
The difference between access ports and trunk ports, and why a trunk is needed to carry several VLANs between switches
How to read spanning-tree and line-protocol log messages when a link comes up
How to prove isolation and reachability with structured ping tests
Possible Improvements
Add inter-VLAN routing (router-on-a-stick or a Layer 3 switch) so the VLANs can talk in a controlled way
Restrict the trunk with an allowed-VLAN list
Add more VLANs and more hosts
Files
vlan-topology.pkz: the Packet Tracer project file
screenshots/: topology, configurations, and ping results
Author

Syed Ahmed Ali Gillani LinkedIn# Vlan-configuration-on-packet-tracer
VLAN &amp; Trunking Lab (Cisco Packet Tracer)  A hands-on lab that splits one IP subnet into two VLANs across two switches, joined by a trunk link. Built and tested in Cisco Packet Tracer.
