# HostConfiguration.md

**Written By:** Liam Saunders L00187849
**Module:** Hybrid Cloud Technology  
**Project:** Electric-Petrol.ie Infrastructure Design  
**Date:** 23 May 2026  
**Audience:** Technical Audience  

---

# 1. Introduction

This document outlines the physical host configuration for the Electric-Petrol.ie Hyper-V infrastructure environment. The infrastructure is designed to support a virtualised environment for core infrastructure services including:

- Active Directory
- DNS
- DHCP
- NTP
- Infrastructure management services

The design focuses on:
- High availability
- Redundancy
- Performance
- Scalability
- Ease of administration

Two identical Dell PowerEdge R670 servers are deployed in a dual-host Hyper-V configuration.

---

# 2. Physical Host Infrastructure

## 2.1 Hyper-V Host Servers

The environment uses two Dell PowerEdge R670 servers configured as Hyper-V hosts.

Both hosts are sized to independently support the full workload in the event of a host failure. This improves resilience and ensures business continuity.

### Host Specifications

| Component | Specification |
|---|---|
| Server Model | Dell PowerEdge R670 |
| Quantity | 2 |
| CPU | 2 x Intel Xeon 6520P |
| Total Cores | 48 Cores Per Host |
| Memory | 512GB RAM |
| Operating System Drives | 2 x NVMe 240GB (RAID 1) |
| Data Storage | RAID 6 (23.04TB Usable) |
| Network Interfaces | 8 x 25Gb SFP28 |
| Power Supplies | Dual 1100W Hot-Plug PSU |
| Remote Management | Dell iDRAC / BMC |

The Dell PowerEdge R670 platform was selected due to:
- Enterprise reliability
- High compute density
- Strong virtualisation performance
- Scalability
- Redundant hardware support

---

# 3. Physical Connectivity Design

The Hyper-V hosts are connected using dedicated physical networks for management, storage and virtual machine traffic.

Network segregation improves:
- Security
- Performance
- Traffic management
- Fault isolation

---

## 3.1 Management Network

### VLAN 2 – Host Management

Host management traffic operates on VLAN 2 using dedicated 1Gb management interfaces.

This network is used for:
- Hyper-V management
- Windows administration
- Monitoring
- Cluster management

Separating management traffic from VM traffic improves security and prevents congestion from affecting host administration.

---

## 3.2 VM Network

### Trunked VM Network

Virtual machine traffic is carried across dedicated 25Gb SFP28 trunk interfaces.

These interfaces allow:
- Multiple VLANs
- Tagged traffic
- High bandwidth
- VM network scalability

The use of trunked SFP28 interfaces provides sufficient bandwidth for:
- Infrastructure services
- Virtual machine communication
- Future workload expansion

---

## 3.3 Storage Network

### VLAN 9 – Storage Network

Storage traffic operates on a dedicated storage VLAN.

The storage network uses:
- 2 x 25Gb SFP28 interfaces
- Isolated VLAN traffic
- Dedicated switching infrastructure

Separating storage traffic improves:
- Storage performance
- Replication efficiency
- Backup performance
- Reduced latency

This prevents storage traffic from impacting production VM traffic.

---

## 3.4 iDRAC / BMC Management Network

The iDRAC/BMC interfaces are connected to a dedicated isolated management switch.

This network:
- Has no default gateway
- Is isolated from production traffic
- Provides out-of-band management

The isolated management network improves security by preventing direct access from production systems.

The iDRAC interfaces provide:
- Remote console access
- BIOS management
- Power management
- Hardware monitoring
- Remote recovery functionality

---

# 4. Power Infrastructure

## 4.1 Dual Power Design

Each Hyper-V host includes dual 1100W hot-plug power supplies.

The power supplies connect to:
- Power Rail A
- Power Rail B

This removes a single point of failure from the power infrastructure.

---

## 4.2 UPS Protection

Each power rail connects to separate UPS/PDU infrastructure.

### UPS Benefits

The UPS systems protect the hosts from:
- Power outages
- Brownouts
- Voltage spikes
- Sudden shutdowns

This allows continued operation during short-term outages and safe shutdown during extended power failures.

---

# 5. Storage Configuration

## 5.1 Operating System Storage

The host operating systems are installed on:
- 2 x NVMe 240GB drives configured in RAID 1

### RAID 1 Benefits

RAID 1 provides:
- Redundancy
- Fast recovery
- Protection against OS drive failure

This ensures the Hyper-V host remains operational if one operating system drive fails.

---

## 5.2 Virtual Machine Storage

Virtual machine data is stored on:
- RAID 6 storage arrays
- 23.04TB usable capacity

### RAID 6 Benefits

RAID 6 was selected because it provides:
- Fault tolerance
- Large storage capacity
- Protection against multiple drive failures

This is suitable for virtual machine storage and infrastructure services.

---

# 6. Host Operating System

## 6.1 Windows Server 2022 Datacenter

Both hosts run:
- Windows Server 2022 Datacenter Edition

### Justification

Windows Server 2022 Datacenter was selected because it includes:
- Hyper-V virtualisation
- Failover clustering
- Unlimited Windows Server VM licensing
- Cluster-Aware Updating
- Advanced security features

Datacenter licensing is cost effective within highly virtualised environments.

---

# 7. Hyper-V Virtualisation Platform

## 7.1 Microsoft Hyper-V

Microsoft Hyper-V is used as the hypervisor platform for the environment.

### Hyper-V Benefits

| Benefit | Justification |
|---|---|
| Cost Efficiency | Included within Windows Server |
| Integration | Native Microsoft ecosystem integration |
| High Availability | Supports failover clustering |
| Live Migration | Allows VM migration with minimal downtime |
| Scalability | Supports future infrastructure growth |
| Management | Managed through Windows Admin Center |

Hyper-V was selected instead of VMware due to lower licensing and operational costs.

---

# 8. High Availability Design

## 8.1 Hyper-V Failover Cluster

The two Hyper-V hosts are configured as a Microsoft Failover Cluster.

This provides:
- Live migration
- Automatic failover
- Workload balancing
- Reduced downtime
- Maintenance without service interruption

If one host fails, workloads automatically restart on the remaining host.

---

## 8.2 Host Redundancy

Each host is sized to independently run the full workload of the environment.

This ensures:
- High availability
- Fault tolerance
- Business continuity

The environment can continue operating during:
- Hardware failure
- Maintenance operations
- Unexpected outages

---

# 9. Virtual Machine Layout

The environment contains several virtual machines providing infrastructure services.

| VM Name | Purpose |
|---|---|
| DC1 | Primary Active Directory and DNS |
| DC2 | Secondary Active Directory and DNS |
| DHCP1 | DHCP Services |
| MGMT1 | Infrastructure Monitoring |
| FILE1 | Backup and File Storage |

Separating services into multiple virtual machines improves:
- Security
- Troubleshooting
- Scalability
- Service isolation

---

# 10. Operations and Management

## 10.1 Monitoring

Infrastructure monitoring is performed using:
- Windows Admin Center
- Hyper-V Manager
- Failover Cluster Manager
- Dell iDRAC Enterprise

Monitoring includes:
- Hardware health
- CPU utilisation
- Memory usage
- Network performance
- Storage health

---

## 10.2 Patch Management

Patch management is performed using:
- Windows Server Update Services (WSUS)

Cluster-Aware Updating minimises downtime during maintenance operations.

---

# 11. Conclusion

The proposed host infrastructure provides a resilient and scalable Hyper-V environment for Electric-Petrol.ie.

The use of dual Hyper-V hosts, dedicated storage networking, redundant power infrastructure and failover clustering ensures high availability and operational continuity.

The infrastructure is designed to support current infrastructure services while allowing future expansion as Electric-Petrol.ie grows its EV charging operations across Ireland.