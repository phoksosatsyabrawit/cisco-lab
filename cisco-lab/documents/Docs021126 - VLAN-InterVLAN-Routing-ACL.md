Network Lab: VLAN Configuration, Inter-VLAN Routing, and Access Control Lists (ACL)

1. Scenario

A company wants to organize its internal network into separate departments to improve network management, reduce unnecessary broadcast traffic, and strengthen security.

The network has two departments:

VLAN 10 — IT Department: 10.1.10.0/24

VLAN 20 — Staff Department: 10.1.20.0/24

A Cisco Catalyst 3650-24PS Layer 3 switch (S2) is used to connect the VLANs and provide inter-VLAN routing.

Initially, both departments are allowed to communicate. However, the company later introduces a security policy requiring the Staff Department to be prevented from accessing the IT Department's network.

An extended Access Control List (ACL) is implemented to enforce this policy.

2. Scope

This lab covers the following networking concepts and configurations:

Creating and configuring VLAN 10 and VLAN 20.

Assigning switch access ports to the appropriate VLANs.

Configuring 802.1Q trunk links between switches.

Configuring Switch Virtual Interfaces (SVIs) on a Layer 3 switch.

Enabling inter-VLAN routing.

Configuring and applying an extended named ACL.

Testing network connectivity and verifying ACL behavior.

Network devices and software:

Cisco Packet Tracer

Cisco Catalyst 3650-24PS Layer 3 switch

Access switches

End devices (PCs)

3. Objectives

The objectives of this lab are to:

Understand how VLANs logically separate devices within a network.

Configure access and trunk ports for VLAN communication.

Understand how a Layer 3 switch performs inter-VLAN routing.

Configure gateway addresses for different VLANs.

Implement network security policies using extended ACLs.

Verify connectivity and troubleshoot network communication using Cisco CLI commands and ping tests.

4. Network Scheme

VLAN and IP Addressing Plan

VLAN

Department

Network

Default Gateway

10

IT

10.1.10.0/24

10.1.10.1

20

Staff

10.1.20.0/24

10.1.20.1

Example End-Device Configuration

Device

IP Address

Subnet Mask

Default Gateway

SALE PC

10.1.20.11

255.255.255.0

10.1.20.1

IT PC

10.1.10.11

255.255.255.0

10.1.10.1

Switch Configuration

The Layer 3 switch S2 uses two SVIs:

VLAN 10 SVI: 10.1.10.1/24

VLAN 20 SVI: 10.1.20.1/24

Two trunk uplinks are configured:

GigabitEthernet 0/1

GigabitEthernet 0/2

Both trunk links allow VLANs 10 and 20 using IEEE 802.1Q encapsulation.

Logical Network Design

End devices connect to access ports.

Switch-to-switch uplinks operate as trunk ports.

S2 performs Layer 3 routing between VLANs.

The ACL controls traffic entering S2 from VLAN 20.

5. Configuration and Implementation

5.1 VLAN Configuration

VLANs 10 and 20 are created to separate the IT and Staff departments.

configure terminal
vlan 10
name IT
exit

vlan 20
name STAFF
exit

Access ports are assigned to their respective VLANs.

Example:

interface gigabitEthernet1/0/3
switchport mode access
switchport access vlan 10

5.2 Trunk Configuration

The switch uplinks are configured to carry VLAN 10 and VLAN 20 traffic.

interface gigabitEthernet1/0/1
switchport mode trunk
switchport trunk allowed vlan 10,20

interface gigabitEthernet1/0/2
switchport mode trunk
switchport trunk allowed vlan 10,20

5.3 Layer 3 Switch Configuration

SVIs are configured to provide default gateways for both VLANs.

configure terminal

interface vlan 10
ip address 10.1.10.1 255.255.255.0
no shutdown

interface vlan 20
ip address 10.1.20.1 255.255.255.0
no shutdown

ip routing

The ip routing command enables routing between the connected VLAN networks.

5.4 Access Control List Configuration

A security policy is introduced:

Staff VLAN 20 must not access the IT VLAN 10 network. Other IP traffic is permitted by this ACL.

An extended named ACL called ACC_TO_IT is created.

configure terminal

ip access-list extended ACC_TO_IT
deny ip 10.1.20.0 0.0.0.255 10.1.10.0 0.0.0.255
permit ip any any
exit

The ACL is applied inbound on the VLAN 20 SVI:

interface vlan 20
ip access-group ACC_TO_IT in
end

ACL rule explanation:

deny ip: Blocks IP traffic matching the specified source and destination networks.

10.1.20.0 0.0.0.255: Staff source network.

10.1.10.0 0.0.0.255: IT destination network.

permit ip any any: Allows other IP traffic that does not match the deny rule.

The ACL is processed from top to bottom. The first matching rule determines the action.

6. Testing and Verification

The following Cisco commands are used to verify the network configuration:

Command

Purpose

show ip interface brief

Checks interface status and IP addresses

show interfaces trunk

Verifies trunk links and allowed VLANs

show ip route

Displays the Layer 3 routing table

show running-config

Reviews the switch configuration

show access-lists

Checks ACL rules and match counters

show ip interface vlan 20

Checks whether the ACL is applied inbound

6.1 Inter-VLAN Routing Verification

Before implementing the ACL, connectivity tests confirmed that the VLAN gateways and end devices were reachable.

The routing table showed:

C 10.1.10.0 is directly connected, Vlan10
C 10.1.20.0 is directly connected, Vlan20

The C indicates a directly connected network.

6.2 ACL Verification

After applying the ACL, tests were performed from the Staff PC (10.1.20.11).

Test

Expected Result

Observed Result

Ping 10.1.20.1

Successful

Successful

Ping 10.1.10.11

Blocked

Blocked

Ping 10.1.10.1

Blocked

Blocked

The Staff PC successfully reached its own gateway but could no longer reach the IT network.

The blocked tests returned:

Reply from 10.1.20.1:
Destination host unreachable.

6.3 ACL Match Counter Verification

The final ACL output was:

Extended IP access list ACC_TO_IT

10 deny ip 10.1.20.0 0.0.0.255 10.1.10.0 0.0.0.255 (12 match(es))

20 permit ip any any (8 match(es))

The counters indicate that:

12 packets matched the deny rule.

8 packets matched the permit rule.

This confirms that the ACL processed traffic matching both rules during testing.

7. Summary and Conclusion

This lab successfully demonstrated the configuration and implementation of VLANs, trunk links, inter-VLAN routing, and extended Access Control Lists using Cisco Packet Tracer.

Two separate departmental networks were created using VLAN 10 and VLAN 20. A Cisco Catalyst 3650-24PS Layer 3 switch was configured with SVIs to provide default gateways and enable communication between the VLANs.

After confirming inter-VLAN connectivity, an extended named ACL was implemented to prevent the Staff Department from accessing the IT Department's network.

The final connectivity tests confirmed that Staff devices could still reach their own gateway, while traffic from Staff to the IT subnet was blocked. ACL match counters provided additional evidence that the security rules were functioning.

Through this exercise, practical knowledge was gained in VLAN segmentation, trunk configuration, Layer 3 switching, IP routing, ACL implementation, and network troubleshooting.

Learning outcome: Gained hands-on experience configuring and verifying a segmented departmental network and implementing basic network access restrictions using Cisco IOS commands.

8. Possible Future Improvements

Introduce VLAN 30 for company servers.

Allow Staff access to approved server services only.

Configure DHCP for automatic IP address assignment.

Implement additional ACL rules to control access between departments.

Configure a default route for external network connectivity.

Document network topology, IP addressing, and security policies.
