# Enterprise Network Homelab

```text
Enterprise Network Homelab
Cisco + MikroTik + Proxmox Infrastructure (Sanitized)
High Level Topology
                                            Internet
                                               │
                                        MikroTik Router
                                     (Edge firewall + VPN)
                                               │
                                         WireGuard VPN
                                               │
                         ┌─────────────────────┴─────────────────────┐
                         │                                           │
                 Admin VPN Clients                         Restricted VPN Clients
                     (IT access)                             (limited access)
                         │                                           │
                         └───────────────────┬───────────────────────┘
                                             │
                                    Core Routing Layer
                           ┌─────────────────┴─────────────────┐
                           │                                   │

---

                        CORE1                               CORE2
                    Cisco Catalyst                     Cisco Catalyst

---

                        3560G                             3560G
                    L3 Switch                           L3 Switch
                           │                                   │
                           └──────────────┬────────────────────┘
                                          │

---

                                   ACCESS SWITCH
                                   Cisco Catalyst
                                       3560(layer2)
                                          │
         ┌──────────────────────┬──────────────────────┬──────────────────────┐
         │                      │                      │
     Admin PC               Client PCs            Proxmox Server
     VLAN 10                 VLAN 10               Virtualization
                                                          │
                                  ┌───────────────────────┼───────────────────────┐
                                  │                       │                       │
                         Windows Server VM            Lab VMs                Mgmt VM
                         Active Directory             VLAN 30               VLAN 99

---

                         DNS + DHCP
Technologies Used
Category	Technology
core routing	Cisco Catalyst 3560G
access switching	Cisco Catalyst 2960
firewall/router	MikroTik RouterOS
VPN	WireGuard
virtualization	Proxmox VE
identity services	Windows Server Active Directory
DNS	Windows Server DNS
DHCP	Windows Server DHCP
routing protocol	OSPF
redundancy	HSRP
switching protocol	Rapid PVST
management access	SSH
Network Segmentation Model
VLAN	Purpose	Example Systems
VLAN 10	client network	desktops, laptops
VLAN 20	server network	domain controller
VLAN 30	lab network	test VMs
VLAN 40	expansion segment	future wireless
VLAN 99	management network	switch interfaces
VPN-ADMIN	full IT access	administrator devices
VPN-RESTRICTED	limited client VPN	remote users

Segmentation isolates broadcast domains and enforces least-privilege access.

VLAN Architecture (Sanitized)
VLAN 10   10.10.10.0/24   Client Devices
VLAN 20   10.20.20.0/24   Servers
VLAN 30   10.30.30.0/24   Lab Environment
VLAN 40   10.40.40.0/24   Expansion
VLAN 99   10.99.99.0/24   Management Interfaces

VPN Admin     10.201.201.0/24   Full access remote IT
VPN Restricted 10.200.200.0/24   Restricted remote clients
Core Layer Design (Cisco Catalyst 3560G)

The dual-core architecture provides high availability routing between VLANs.

Core capabilities:

• Inter-VLAN routing
• HSRP gateway redundancy
• OSPF dynamic routing
• management VLAN separation
• ACL-based access restrictions

Example logical gateway model (Sanitized)

---

CORE1 VLAN10 IP 10.10.10.2

---

CORE2 VLAN10 IP 10.10.10.3

Virtual Gateway
10.10.10.1

Clients use the virtual IP as their default gateway.

If CORE1 fails, CORE2 automatically takes over.

Access Layer (Cisco Catalyst 2960)

The access switch provides endpoint connectivity.

Access port configuration features:

• VLAN membership assignment
• PortFast enabled for fast link initialization
• BPDU Guard enabled for loop prevention

Example endpoint connection flow:

Endpoint connects to switch
        │
Port assigned VLAN 10
        │
DHCP request forwarded
        │
Device receives configuration
Routing Protocol (OSPF)

OSPF advertises networks between core switches and the MikroTik router.

Benefits:

• dynamic route discovery
• automatic failover
• scalable architecture

Edge Router (MikroTik RouterOS)

The MikroTik router provides:

• internet connectivity
• firewall filtering
• VPN termination
• route advertisement
• NAT services

WireGuard VPN Design

WireGuard provides encrypted remote access.

Two access tiers implemented.

Administrative VPN

Full internal access for IT administration.

Access includes:

• Cisco switches
• Proxmox host
• Active Directory server
• management VLAN
• lab systems

Restricted VPN

Limited client access.

Restrictions prevent access to:

• management interfaces
• infrastructure configuration
• core routing devices

Allowed access:

• client VLAN resources
• permitted internal services

VPN Segmentation Model
Admin VPN Client
        │
Full infrastructure access
        │
Switch management
Server management
Lab systems


Restricted VPN Client
        │
Limited access
        │
Client VLAN resources only
Virtualization Platform (Proxmox VE)

Proxmox hosts infrastructure servers in isolated virtual machines.

Benefits:

• flexible infrastructure deployment
• resource isolation
• safe testing environment
• enterprise simulation capability

Proxmox VM Layout
Proxmox Hypervisor
        │
        ├── Windows Server
        │     Active Directory

---

        │     DNS

---

        │     DHCP
        │
        ├── Lab VM(s)
        │     testing systems
        │
        └── Management VM
              monitoring tools
Domain Controller (Windows Server)

Windows Server provides centralized identity services.

Roles installed
Role	Purpose
Active Directory Domain Services	identity management
DNS Server	hostname resolution
DHCP Server	automatic IP assignment
DHCP Architecture

DHCP provides automatic network configuration for endpoints.

DHCP scope example:

• network range assigned dynamically
• default gateway assigned automatically
• DNS server configured automatically
• domain suffix assigned

DHCP Relay (IP Helper)

Since DHCP server resides in VLAN 20, relay forwarding is configured on core switches.

Flow:

Client VLAN 10
        │
Broadcast DHCP request
        │
Core switch forwards request
        │
DHCP server responds
DNS Architecture

DNS allows devices to locate resources using hostnames.

Example:

core1.homelab.local
dc.homelab.local
proxmox.homelab.local

DNS supports:

• domain join
• service discovery
• simplified administration

Domain Join Workflow
Client connects to VLAN 10
        │
Receives DHCP configuration
        │
Finds domain controller via DNS
        │
Joins Active Directory domain
        │
Receives centralized policies
Security Controls

Security features implemented:

• management VLAN isolation
• ACL restrictions for switch access
• segmented VPN permissions
• SSH-only administrative access
• restricted routing between networks
• separation between user and infrastructure traffic

Data Flow Overview
Client connectivity
Client PC
     │
Access switch
     │
Core routing layer
     │
Domain controller
Administrative workflow
Remote IT device
        │
WireGuard VPN tunnel
        │
MikroTik router
        │
Core switches
        │
Infrastructure devices
Enterprise Skills Demonstrated
Networking

• VLAN design
• trunk configuration
• inter-VLAN routing
• redundant gateway configuration
• dynamic routing implementation
• hierarchical network architecture

Systems Administration

• Windows Server deployment
• Active Directory configuration
• DNS integration
• DHCP scope configuration

Security

• segmented remote access
• ACL implementation
• least privilege architecture
• management network isolation

Virtualization

• Proxmox deployment
• VM segmentation
• infrastructure simulation
• lab environment creation

Real World Environment Simulation

This architecture reflects environments commonly used by:

• Managed Service Providers
• small enterprise networks
• branch offices
• education institutions
• engineering teams
• healthcare offices

Final Summary

This lab demonstrates the ability to deploy and support an enterprise-style environment integrating:

Cisco switching infrastructure
MikroTik firewall and VPN services
Proxmox virtualization platform
Windows Server identity services
segmented VLAN architecture
secure remote access design

The environment demonstrates understanding of both networking and systems engineering principles used in real-world IT environments.
```
