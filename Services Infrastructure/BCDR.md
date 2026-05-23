# BCDR.md

**Written By:** Liam Saunders L00187849
**Module:** Hybrid Cloud Technology  
**Project:** Electric-Petrol.ie Infrastructure Design  
**Date:** 23 May 2026  
**Audience:** Technical Audience  

---

# 1. Introduction

This document outlines the Backup, Disaster Recovery and Business Continuity (BCDR) strategy for the Electric-Petrol.ie Hyper-V infrastructure environment.

The purpose of this plan is to ensure:
- Protection of critical infrastructure services
- High availability of virtual workloads
- Recovery from hardware or software failure
- Reduced downtime
- Continuity of EV charging operations

The BCDR strategy is designed specifically for the dual-host Hyper-V infrastructure deployed within the Electric-Petrol.ie environment.

---

# 2. Backup Strategy

## 2.1 Backup Objectives

The backup strategy is designed to:
- Protect critical virtual machines
- Protect Active Directory services
- Allow rapid service restoration
- Minimise data loss
- Support disaster recovery procedures

Critical infrastructure services include:
- Active Directory
- DNS
- DHCP
- NTP
- Hyper-V virtual machines
- Infrastructure configuration data

---

# 2.2 Hyper-V Virtual Machine Backups

All Hyper-V virtual machines are backed up regularly using Windows Server Backup and Volume Shadow Copy Service (VSS).

## Backup Schedule

| Backup Type | Frequency |
|---|---|
| Full VM Backup | Weekly |
| Incremental Backup | Daily |
| System State Backup | Weekly |
| Configuration Backup | Weekly |

Backups are retained for:
- 180 Days

---

## 2.3 Backup Storage Locations

Backups are stored:
- On local backup storage
- On replicated secondary storage
- On encrypted offsite backup media

Future expansion may include:
- Azure Backup
- AWS Storage Gateway
- Cloud replication services

This improves resilience against:
- Hardware failure
- Ransomware
- Site-level disaster
- Data corruption

---

# 2.4 Active Directory Backup Strategy

Both domain controllers are backed up regularly using:
- System State Backup
- Windows Server Backup
- Volume Shadow Copy Service (VSS)

The following components are protected:
- Active Directory database
- DNS zones
- Group Policy Objects
- DHCP configuration
- SYSVOL

System State backups are critical for restoring:
- Authentication services
- DNS services
- Group Policy configuration

---

# 2.5 Backup Security

Backup security is important to prevent unauthorised access and ransomware attacks.

The following controls are implemented:
- Encrypted backup storage
- Restricted administrative access
- Offsite backup copies
- Separate backup credentials
- Regular backup testing

This reduces the risk of backup compromise.

---

# 3. Disaster Recovery Strategy

## 3.1 Disaster Recovery Objectives

The disaster recovery strategy is designed to:
- Restore critical services quickly
- Reduce downtime
- Maintain authentication services
- Restore EV charging infrastructure management
- Protect business operations

---

# 3.2 Scenario – Hyper-V Host Failure

If a single Hyper-V host fails:

- Hyper-V Failover Clustering automatically migrates workloads
- Virtual machines restart on the remaining host
- Live Migration reduces service interruption
- Infrastructure services remain operational

Each host is sized to independently run the full workload of the environment.

This provides:
- High availability
- Fault tolerance
- Reduced downtime

---

# 3.3 Scenario – Virtual Machine Failure

If a virtual machine becomes corrupted or unavailable:

- Restore from latest backup
- Import VM into Hyper-V
- Restore configuration files
- Verify network connectivity
- Validate Active Directory replication if required

Critical infrastructure VMs are prioritised during recovery.

---

# 3.4 Scenario – Domain Controller Failure

The environment uses:
- DC1
- DC2

If one domain controller fails:
- Authentication continues through the remaining DC
- DNS services remain available
- Group Policy remains operational

Replacement steps:
1. Restore failed VM if possible
2. If unrecoverable, deploy new VM
3. Promote replacement to domain controller
4. Restore replication
5. Validate DNS functionality

---

# 3.5 Scenario – Full Rack Failure

A full rack failure may occur due to:
- Fire
- Flood
- Power failure
- Major hardware fault

Recovery steps include:
1. Restore Hyper-V hosts on replacement hardware
2. Restore domain controllers first
3. Restore DNS services
4. Restore DHCP services
5. Restore remaining infrastructure VMs
6. Validate network connectivity
7. Restore monitoring services

Offsite backups are required for this recovery scenario.

---

# 3.6 Recovery Priority Order

| Priority | Service |
|---|---|
| 1 | Active Directory |
| 2 | DNS |
| 3 | DHCP |
| 4 | Hyper-V Management |
| 5 | Monitoring Services |
| 6 | Remaining Infrastructure Services |

Authentication and DNS services are restored first because all other infrastructure depends on them.

---

# 4. Business Continuity Strategy

## 4.1 High Availability Design

The infrastructure includes:
- Dual Hyper-V hosts
- Hyper-V Failover Clustering
- Redundant networking
- RAID protected storage
- Dual power supplies
- UPS protection

These features reduce the likelihood of downtime.

---

# 4.2 Power Continuity

Each Hyper-V host uses:
- Dual 1100W hot-plug PSUs
- Separate Power Rail A and Power Rail B
- UPS/PDU redundancy

This protects against:
- PSU failure
- Power outages
- Electrical faults

The UPS systems provide approximately:
- 30 minutes runtime

This allows:
- Continued operation during short outages
- Graceful shutdown during extended outages

---

# 4.3 Network Continuity

The infrastructure uses:
- Dedicated management networks
- Dedicated storage VLANs
- Trunked VM traffic
- NIC Teaming

NIC Teaming provides:
- Redundancy
- Load balancing
- Failover protection

If a network interface fails, connectivity continues through remaining interfaces.

---

# 4.4 Operational Continuity

The following operational controls are implemented:
- Change management procedures
- Infrastructure monitoring
- Patch management
- Recovery documentation
- Failover procedures
- Backup verification

All infrastructure changes are documented to reduce operational risk.

---

# 5. Testing and Validation

## 5.1 Backup Testing

Backups are tested quarterly to ensure:
- Backup integrity
- Successful restoration
- Recovery reliability

Test restores are performed within an isolated lab environment.

---

## 5.2 Disaster Recovery Testing

Full disaster recovery testing is performed annually.

Testing includes:
- Hyper-V host recovery
- VM restoration
- Active Directory recovery
- DNS validation
- DHCP validation
- Network connectivity verification

Testing ensures recovery procedures remain effective.

---

# 6. Justification

The BCDR strategy was designed to provide:
- High availability
- Infrastructure resilience
- Reduced downtime
- Rapid recovery
- Protection against hardware failure

Hyper-V Failover Clustering ensures workloads remain operational during host failure.

Redundant networking, RAID storage and dual power infrastructure reduce single points of failure.

The use of multiple domain controllers ensures authentication and DNS services remain available during outages.

Offsite backups improve protection against site-level disaster scenarios.

---

# 7. Future Improvements

Possible future enhancements include:
- Cloud-based disaster recovery replication
- Azure Site Recovery integration
- Secondary site failover
- Automated backup verification
- SIEM integration for security monitoring
- Immutable backup storage
- Automated failover testing

These improvements would further strengthen business continuity and disaster recovery capabilities.

---

# 8. Conclusion

The proposed Backup, Disaster Recovery and Business Continuity strategy provides a resilient and scalable solution for the Electric-Petrol.ie Hyper-V environment.

The combination of Hyper-V failover clustering, redundant infrastructure, regular backups and offsite recovery procedures ensures critical services remain available and recoverable during failure scenarios.

The design supports both current infrastructure requirements and future expansion of Electric-Petrol.ie operations.