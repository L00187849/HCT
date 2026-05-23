# ServicesDescription.md

**Written By:** Liam Saunders L00187849  
**Module:** Hybrid Cloud Technology  
**Project:** Electric-Petrol.ie Infrastructure Design  
**Date:** 23 May 2026  
**Audience:** Technical Audience  

---

# 1. Introduction

This document outlines the infrastructure services deployed within the Electric-Petrol.ie Hyper-V environment.

The infrastructure is designed to support:
- EV charging management systems
- Internal authentication services
- Infrastructure servers
- Technical staff devices
- Monitoring and management systems

The environment is hosted across two Hyper-V hosts running Windows Server 2022 Datacenter Edition.

The core services deployed are:
- Active Directory Domain Services (AD DS)
- Domain Name System (DNS)
- Dynamic Host Configuration Protocol (DHCP)
- Network Time Protocol (NTP)

The design focuses on:
- High availability
- Redundancy
- Centralised management
- Scalability
- Security

---

# 2. Active Directory Domain Services (AD DS)

## 2.1 Active Directory Design

Active Directory Domain Services provides:
- Centralised authentication
- User and device management
- Security policy enforcement
- Group Policy management
- Service authentication

The Active Directory domain for the environment is:

```text
corp.electric-petrol.ie
```

Two Domain Controllers are deployed:
- DC1 (Primary Domain Controller)
- DC2 (Secondary Domain Controller)

The domain controllers are virtualised across separate Hyper-V hosts for redundancy.

---

## 2.2 Domain Controller Placement

| VM Name | Role |
|---|---|
| DC1 | Primary Domain Controller and DNS |
| DC2 | Secondary Domain Controller and DNS |

Both domain controllers provide:
- Authentication services
- DNS services
- Time synchronisation
- Directory replication

This ensures the environment remains operational if one domain controller fails.

---

## 2.3 VLAN Association

The domain controllers operate within:
- VLAN 6 – Infrastructure Servers VLAN

Management traffic is accessible through:
- VLAN 2 – Host Management VLAN

Separating infrastructure services from client traffic improves security and fault isolation.

---

## 2.4 Active Directory Configuration

### AD DS Installation Steps

1. Open Server Manager
2. Select:
   - Manage
   - Add Roles and Features
3. Select:
   - Active Directory Domain Services
4. Click:
   - Add Features
5. Complete installation

---

## 2.5 Promote Server to Domain Controller

### Domain Controller Promotion Steps

1. Open Server Manager
2. Select:
   - Promote this server to a domain controller
3. Select:
   - Add a new forest
4. Enter the domain name:

```text
corp.electric-petrol.ie
```

5. Configure Directory Services Restore Mode password
6. Accept default DNS configuration
7. Complete installation and reboot

---

# 3. Domain Name System (DNS)

## 3.1 DNS Design

DNS provides hostname resolution for:
- Hyper-V hosts
- Domain controllers
- Infrastructure servers
- EV charging management systems
- Technical support devices

The DNS environment uses:
- AD-integrated DNS zones
- Secure dynamic updates
- Internal and external name resolution

---

## 3.2 DNS Redundancy

DNS is hosted on:
- DC1
- DC2

This provides:
- Fault tolerance
- Redundancy
- Improved availability

If one DNS server becomes unavailable, clients automatically use the remaining DNS server.

---

## 3.3 DNS Zones

| Zone Type | Purpose |
|---|---|
| corp.electric-petrol.ie | Internal infrastructure domain |

Forwarders are configured to:
- 1.1.1.1 (Cloudflare)
- 8.8.8.8 (Google DNS)

This allows external name resolution for:
- Updates
- Cloud services
- Vendor support systems

---

## 3.4 VLAN Association

DNS services operate within:
- VLAN 6 – Infrastructure Servers VLAN

Management traffic uses:
- VLAN 2 – Host Management VLAN

---

## 3.5 DNS Configuration Steps

### Install DNS Server Role

1. Open Server Manager
2. Select:
   - Manage
   - Add Roles and Features
3. Select:
   - DNS Server
4. Complete installation

---

### Create DNS Zone

1. Open:
   - DNS Manager
2. Expand:
   - Forward Lookup Zones
3. Select:
   - New Zone
4. Configure:
   - Primary Zone
   - Store zone in Active Directory
   - Allow secure dynamic updates
5. Set zone name:

```text
corp.electric-petrol.ie
```

---

### Configure Forwarders

1. Open DNS Manager
2. Right-click server
3. Select:
   - Properties
   - Forwarders
4. Add:
   - 1.1.1.1
   - 8.8.8.8

---

# 4. Dynamic Host Configuration Protocol (DHCP)

## 4.1 DHCP Design

DHCP automatically assigns IP addresses to:
- Staff devices
- Technical support laptops
- Monitoring systems
- Infrastructure devices

This reduces manual configuration and simplifies network administration.

---

## 4.2 DHCP Redundancy

DHCP Failover is configured between:
- DHCP1
- DC2

Failover mode:
- Load Balanced

This ensures DHCP services remain available during outages or maintenance.

---

## 4.3 VLAN Association

DHCP services support:
- VLAN 5 – Technical Clients VLAN
- VLAN 6 – Infrastructure Servers VLAN
- VLAN 12 – Wireless Access Point Management VLAN

---

## 4.4 DHCP Scope Design

### Technical Client Scope

| Setting | Value |
|---|---|
| Scope Name | TechnicalClients |
| Start IP | 10.45.5.100 |
| End IP | 10.45.5.199 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 10.45.5.1 |
| DNS Server | 10.45.6.10 |
| Lease Duration | 8 Days |

---

## 4.5 DHCP Exclusions

Static exclusions are configured for:
- Hyper-V hosts
- Domain controllers
- Switches
- Management interfaces
- Storage interfaces

This prevents address conflicts.

---

## 4.6 DHCP Configuration Steps

### Install DHCP Role

1. Open Server Manager
2. Select:
   - Add Roles and Features
3. Select:
   - DHCP Server
4. Complete installation

---

### Authorise DHCP Server

1. Open:
   - DHCP Console
2. Right-click server
3. Select:
   - Authorise

---

### Create DHCP Scope

1. Open:
   - DHCP Console
2. Expand:
   - IPv4
3. Select:
   - New Scope
4. Configure IP range and settings
5. Activate scope

---

# 5. Network Time Protocol (NTP)

## 5.1 NTP Design

Accurate time synchronisation is critical for:
- Active Directory authentication
- Logging
- Security auditing
- Event correlation
- EV charging transaction timestamps

The domain controllers act as the internal NTP source for the environment.

---

## 5.2 External Time Source

The primary domain controller synchronises with reliable external NTP sources.

Example:
- ie.pool.ntp.org

All infrastructure systems synchronise time through Active Directory hierarchy.

---

## 5.3 VLAN Access

NTP services are accessible across:
- VLAN 5
- VLAN 6
- VLAN 10
- VLAN 12

UDP Port 123 is permitted for NTP traffic.

---

## 5.4 NTP Configuration Steps

### Configure Windows Time Service

1. Open CMD as Administrator
2. Run:

```text
w32tm /config /manualpeerlist:"ie.pool.ntp.org" /syncfromflags:manual /reliable:YES /update
```

3. Restart service:

```text
net stop w32time && net start w32time
```

4. Verify status:

```text
w32tm /query /status
```

---

# 6. Management Services

## 6.1 VLAN 10 – Network Management

VLAN 10 is reserved for:
- Infrastructure monitoring
- Remote administration
- Hyper-V management
- Device troubleshooting

This separates management traffic from production workloads.

---

## 6.2 Monitoring Tools

The infrastructure is monitored using:
- Windows Admin Center
- Hyper-V Manager
- Failover Cluster Manager
- Dell iDRAC Enterprise

Monitoring includes:
- Hardware health
- CPU usage
- Memory usage
- Storage health
- Network utilisation

---

# 7. VLAN Overview

| VLAN | Purpose |
|---|---|
| VLAN 2 | Host Management VLAN |
| VLAN 5 | Technical Clients VLAN |
| VLAN 6 | Infrastructure Servers VLAN |
| VLAN 9 | Storage VLAN |
| VLAN 10 | Network Management VLAN |
| VLAN 12 | Wireless Access Point Management |

---

# 8. Conclusion

The proposed infrastructure services provide a resilient and scalable environment for Electric-Petrol.ie.

The use of redundant domain controllers, DNS redundancy, DHCP failover and centralised time synchronisation ensures reliable infrastructure operations across the Hyper-V environment.

The design also supports future growth as additional EV charging systems and infrastructure services are deployed.