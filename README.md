# PALO-ALTO-firewall
# Palo Alto Firewall Configuration Project

## Overview
This project demonstrates comprehensive configuration of Palo Alto Networks firewalls in a lab environment. It covers a variety of security scenarios, including zone-based security, routing, NAT/PAT, DMZ configuration, VPN setup, and advanced security features.

![Network Topology](network-topology.png)

## Network Architecture
The lab consists of the following components:
- **Palo Alto Firewalls**: Two firewalls (PaloAlto1 and PaloAlto2) for site-to-site VPN
- **Client Networks**: 
  - Internal network (10.1.1.0/24)
  - Secondary site (10.2.0.0/24)
- **DMZ**: 172.16.1.0/24 with web server
- **Internet-facing Network**: 23.1.2.0/24
- **Router R1**: Provides internet connectivity

## Configuration Components

### Basic Setup
- Initial bootstrap configuration
- Interface configuration
- Security zones (inside_zone, outside_zone, DMZ_zone)
- Virtual router configuration

### Routing
- Static routing
- Dynamic routing (OSPF)
- Default route configuration

### NAT/PAT Implementation
- Source NAT for outbound traffic
- Destination NAT for inbound DMZ access

### Access Control
- Security policies between zones
- Application-based security rules
- Port-based rules

### Advanced Features
- SSL decryption with self-signed certificates
- User-ID integration with Active Directory
- Application groups and filters
- Virtual wire interfaces
- Layer 2 security

### VPN Configuration
- Site-to-site VPN tunnel setup
- IKE and IPSec configuration
- Security rules for VPN traffic

## Configuration Sections

1. [Initial Configuration](#initial-configuration)
2. [Zone and Interface Setup](#zone-and-interface-setup)
3. [Routing Configuration](#routing-configuration)
4. [NAT/PAT Setup](#natpat-setup)
5. [Security Policies](#security-policies)
6. [DMZ Configuration](#dmz-configuration)
7. [Advanced Interface Types](#advanced-interface-types)
8. [Application-based Controls](#application-based-controls)
9. [SSL Decryption](#ssl-decryption)
10. [Site-to-Site VPN](#site-to-site-vpn)
11. [User-ID and AD Integration](#user-id-and-ad-integration)

## Initial Configuration
The initial bootstrap configuration includes basic system settings, management interface configuration, and DNS settings.

```
configure 
set deviceconfig system ip-address 192.168.1.11 netmask 255.255.255.0
set deviceconfig system default-gateway 192.168.1.1
set deviceconfig system dns-setting servers primary 8.8.8.8
set deviceconfig system type static
commit  
```

## Zone and Interface Setup
Security zones are created to segment the network, with interfaces assigned to appropriate zones:

- inside_zone (internal network): eth1/1 - 10.1.0.11/24
- outside_zone (internet-facing): eth1/3 - 23.1.2.11/24
- DMZ_zone (demilitarized zone): eth1/2 - 172.16.1.11/24

## Routing Configuration
Both static and dynamic routing are configured:

### Static Route Example
```
network -> Virtual routers -> Our Virtual router -> static routers -> IPV4 -> Add
Name=Our_default_Router, Destination=0.0.0.0/0, next hop=IP Address, 23.1.2.1
```

### OSPF Configuration
```
network -> Virtual routers -> Our Virtual router -> OSPF
Enable, Router ID=11.11.11.11, Area ID=0.0.0.0, Interface=ethernet1/3
```

## NAT/PAT Setup
Source NAT is configured for outbound traffic from the internal network, and destination NAT for inbound access to the DMZ server:

### Source NAT
```
Policies -> NAT -> Add -> Name=Our-Source-NAT/PAT
Original Packet: source zone=inside-zone, destination zone=Outside-zone
Translated Packet: Translation Type=Dynamic IP and Port, Interface=ethernet1/3
```

### Destination NAT
```
Policies -> NAT -> Add -> Name=OUT-TO-DMZ-server
Original Packet: source zone=Outside-zone, destination=23.1.2.100/32
Translated Packet: Translation Type=Static IP, Translate to=172.16.1.100
```

## Security Policies
Various security policies are implemented to control traffic between zones:

### Basic Outbound Access
```
Policies -> Security -> Add -> Name=Simple-security-role
Source: inside_zone, Source Address=HQ-NET10.1.0.0
Destination: Outside-Zone, Destination Address=any
Application: any
Action: allow
```

## DMZ Configuration
A DMZ is set up to host web servers with controlled access from both internal and external networks.

## Advanced Interface Types
The project demonstrates different interface configurations:
- Tap interfaces
- Virtual wire interfaces
- Layer 2 interfaces

## Application-based Controls
Advanced application controls demonstrate Palo Alto's App-ID technology:

- Application filters
- Application groups
- Port vs. application-based rules

## SSL Decryption
SSL forward proxy decryption is configured with custom certificates:

```
Policies -> Decryption -> Add -> Name=Our_Decryption_Rule
Source: inside-zone
Destination: outside-zone
Action: Decrypt, Type=SSL Forward Proxy
```

## Site-to-Site VPN
A site-to-site VPN connects two locations:

```
network -> IPSec Tunnels -> Add -> Name=Tunnel-To-FW2
Tunnel interface=Tunnel6783, Type=Auto key
IKE Gateway=Peering-Over-To-FW2
```

## User-ID and AD Integration
Active Directory integration demonstrates user and group-based policy control:

```
Device -> User identification -> Group Mapping Settings -> Add
Name=Our_Group_Mapping, Server Profile=Our_LDAP_Profile
```

## Usage
This configuration can be used as a reference for:
1. Building a secure network architecture with Palo Alto firewalls
2. Implementing advanced security features
3. Setting up site-to-site VPN connectivity
4. Configuring user-based access controls

## Requirements
- Palo Alto Networks firewall (physical or virtual)
- Network environment with multiple segments (internal, DMZ, external)
- Client devices for testing
- Optional: Active Directory server for User-ID integration
