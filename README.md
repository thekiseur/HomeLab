# HomeLab
# Enterprise Homelab — Infrastructure & Network Security Lab

> A hands-on virtual enterprise environment designed to develop practical skills in system administration, networking, identity management, cybersecurity, troubleshooting, and automation.

---

## Project Overview

This project is a virtualized enterprise-style homelab designed to simulate a small organization's IT infrastructure.

The environment combines Windows, Linux, networking, virtualization, identity management, firewalling, security monitoring, and automation into a single reproducible lab.

The main objective is to build an environment where infrastructure components can be deployed, configured, secured, monitored, and intentionally broken for troubleshooting and learning purposes.

The lab is designed to progressively evolve from a basic Windows and network infrastructure into a more complete enterprise-style environment.

---

# Target Architecture

The lab is designed around pfSense as the network firewall and gateway, Windows Server as the core identity infrastructure, Windows 11 as the corporate workstation environment, and Ubuntu Server as the Linux infrastructure platform.


                              INTERNET
                                  │
                                  │
                         VMware VMnet8
                              NAT
                                  │
                                  │
                         ┌────────▼────────┐
                         │    PFSENSE01   │
                         │                 │
                         │ Firewall        │
                         │ NAT             │
                         │ Gateway         │
                         │ DHCP            │
                         └────────┬────────┘
                                  │
                                  │
                         LAN 192.168.10.0/24
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
      ┌───────▼───────┐   ┌───────▼───────┐   ┌──────▼───────┐
      │      DC01     │   │    CLIENT01   │   │   UBUNTU01   │
      │ Windows Server│   │   Windows 11  │   │ Ubuntu Server│
      │               │   │               │   │              │
      │ AD DS         │   │ Domain Member  │   │ SSH          │
      │ DNS           │   │               │   │ Web Server   │
      │ DHCP          │   │               │   │ Docker       │
      └───────────────┘   └───────────────┘   └──────────────┘
              │                   │                   │
              └───────────────────┼───────────────────┘
                                  │
                             LAB.HOME


---

#  Network Architecture

The lab uses a dedicated internal network separated from the host and external network.

## Network Design

| Component | Configuration |
| ----------------------- | --------------------------------- |
| Internal LAN            | `192.168.10.0/24`                 |
| pfSense LAN             | `192.168.10.1`                    |
| DC01                    | `192.168.10.10`                   |
| Ubuntu Server           | `192.168.10.20`                   |
| Windows Clients         | DHCP                              |
| DHCP Pool               | `192.168.10.100 - 192.168.10.200` |
| DNS                     | `192.168.10.10`                   |
| Active Directory Domain | `lab.home`                        |

---

# Virtualization Architecture

The entire environment runs inside VMware Workstation Pro.


                         HOST PC
                            │
                  VMware Workstation Pro
                            │
              ┌─────────────┴─────────────┐
              │                           │
           VMnet8                       VMnet1
            NAT                       Host-Only
              │                           │
              │                           │
        ┌─────▼─────┐                     │
        │ PFSENSE01 │                     │
        │    WAN    │                     │
        └─────┬─────┘                     │
              │                           │
              │ LAN                       │
              └──────────────┬────────────┘
                             │
                     192.168.10.0/24
                             │
                ┌────────────┼────────────┐
                │            │            │
               DC01       CLIENT01     UBUNTU01


### VMware Networks

| Network | Type | Purpose |
| ------------------ | --------- | -------------------------------- |
| VMnet8             | NAT       | External / Internet connectivity |
| VMnet1             | Host-Only | Internal lab network             |

---

# pfSense — PFSENSE01

pfSense acts as the primary network security and routing platform.

### Responsibilities

- Firewall
- NAT
- Default gateway
- DHCP
- Network traffic filtering
- LAN routing
- Network segmentation
- VPN
- Firewall rule management
- Traffic monitoring

### Network Interfaces


WAN → VMware VMnet8
LAN → VMware VMnet1


pfSense provides the security boundary between the external network and the internal lab network.

---

# Windows Server — DC01

DC01 provides the core Windows infrastructure services.

### Responsibilities

- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- User management
- Security group management
- Computer management
- Authentication
- File services
- Permission management

### Active Directory Domain


lab.home


DC01 is designed to act as the central identity and management platform for Windows clients.

---

# Active Directory Structure

The Active Directory environment is designed to simulate a small enterprise organization.


LAB.HOME
│
├── Admin
│
├── Employees
│
├── Servers
│
├── Workstations
│
├── Groups
│
└── Service Accounts


### Identity Management

The environment will be used to practice:

- User creation
- Security group management
- Organizational Unit design
- Authentication
- Role-based access
- Permission management
- Account administration
- Service accounts

---

# Windows 11 — CLIENT01

CLIENT01 represents a standard corporate workstation.

The workstation will be joined to:


lab.home


### Use Cases

- Active Directory domain authentication
- Group Policy testing
- Windows security configuration
- Network resource access
- File permission testing
- Remote administration
- Troubleshooting
- Endpoint security testing

Additional Windows clients can be added later to simulate a larger organization.

---

# Ubuntu Server — UBUNTU01

Ubuntu Server provides the Linux component of the lab.

### Planned Responsibilities

- SSH
- Web server
- Docker
- Bash scripting
- Linux service management
- User management
- Permission management
- Log analysis
- Network administration
- Linux firewall configuration

Ubuntu will also provide opportunities to practice interoperability between Windows and Linux environments.

---

# Group Policy

Group Policy will be used to centrally manage Windows users and computers.

### Security and Administration Policies

The lab will include GPOs for:

- Windows security configuration
- Windows Firewall
- User restrictions
- Password and account policies
- Desktop configuration
- Browser configuration
- Network drive mapping
- Access restrictions
- System configuration
- Workstation hardening

Each GPO will be configured and tested independently to understand its scope, inheritance, filtering, and interaction with Active Directory.

---

# File Services & Permissions

The Windows infrastructure will include shared network resources for practicing access control.

### Example Shares


\\DC01\Public
\\DC01\IT
\\DC01\HR
\\DC01\Finance


Access control will be implemented using:

- Active Directory security groups
- NTFS permissions
- Share permissions
- Role-based access

The objective is to simulate real-world departmental file access.

---

# Network & System Security

Security is implemented in multiple layers.

## Network Layer

pfSense will provide:

- Firewall rules
- NAT
- Traffic filtering
- Network segmentation
- VPN
- Network monitoring

## Windows Layer

Windows security will include:

- Active Directory security
- Group Policy
- Windows Defender Firewall
- User permissions
- Security policies
- Event logging
- Endpoint hardening

## Linux Layer

Linux security will include:

- SSH hardening
- File permissions
- User and group management
- Firewall configuration
- Service security
- Log analysis

---

# Monitoring & Security

The lab will progressively integrate security monitoring and analysis tools.

### Planned Technologies

- Wireshark
- Nmap
- Sysmon
- Wazuh
- Centralized logging
- Vulnerability scanning
- SIEM concepts
- Security event analysis

These tools will be integrated progressively rather than deployed all at once.

The goal is to understand how security monitoring works across network, Windows, and Linux environments.

---

# Automation

Automation will be introduced progressively to reduce repetitive administrative tasks.

## PowerShell

Potential automation tasks:

- User creation
- Security group creation
- OU creation
- Active Directory administration
- System information gathering
- GPO administration
- Account management
- Administrative tasks

## Bash

Potential automation tasks:

- User management
- Service management
- Log collection
- System maintenance
- Linux configuration
- Automated deployments

---

# Troubleshooting & IT Support Scenarios

The homelab will be used to reproduce realistic enterprise IT problems.

### Example Scenarios


1. A user cannot authenticate to the domain.
2. A workstation does not receive an IP address.
3. DNS cannot resolve the Active Directory domain.
4. A Group Policy is not being applied.
5. A user cannot access a shared folder.
6. A workstation cannot access the Internet.
7. A firewall rule blocks a required service.
8. A Windows service fails.
9. An Ubuntu service becomes unavailable.
10. SSH access fails.
11. A permission configuration prevents resource access.
12. A network configuration causes connectivity problems.


Every troubleshooting exercise follows the same methodology:


Symptom
   ↓
Possible Causes
   ↓
Tests
   ↓
Diagnosis
   ↓
Correction
   ↓
Verification
   ↓
Documentation


This methodology is intended to reproduce the workflow used in professional IT support and system administration.

---

#  Documentation

The project is documented progressively so that the environment can be reproduced and understood by another administrator.


docs/
│
├── architecture/
├── networking/
├── active-directory/
├── windows/
├── linux/
├── pfsense/
├── security/
└── troubleshooting/


---

# Project Journal

The journal contains the chronological history of the project.


journal/
│
├── day-01.md
├── day-02.md
└── ...


Each journal entry documents:

- Objectives
- Configuration
- Implementation
- Problems encountered
- Troubleshooting
- Tests
- Solutions
- Lessons learned
- Next steps

The README describes the overall architecture and direction of the project, while the journal records the actual implementation history.

---

# Screenshots

Important implementation screenshots will be stored separately.


screenshots/
│
├── vmware/
├── pfsense/
├── active-directory/
├── windows/
├── ubuntu/
└── networking/


Screenshots will demonstrate important configurations, tests, and results without exposing passwords, private keys, or sensitive information.

---

# Technologies

| TechnologyPurpose      |                                     |
| ---------------------- | ----------------------------------- |
| VMware Workstation Pro | Virtualization                      |
| pfSense CE             | Firewall / Gateway                  |
| Windows Server         | Windows Infrastructure              |
| Active Directory       | Identity & Access Management        |
| DNS                    | Name Resolution                     |
| DHCP                   | IP Address Management               |
| Windows 11             | Client Workstation                  |
| Ubuntu Server          | Linux Infrastructure                |
| PowerShell             | Windows Administration & Automation |
| Bash                   | Linux Administration & Automation   |
| Git                    | Version Control                     |
| GitHub                 | Project Hosting & Documentation     |
| Wireshark              | Network Analysis                    |
| Nmap                   | Network Discovery                   |
| Sysmon                 | Windows Monitoring                  |
| Wazuh                  | Security Monitoring                 |

---

# Project Roadmap

## Phase 1 — Virtualization

- VMware Workstation Pro
- VMware virtual networking
- pfSense deployment
- Windows Server deployment
- Windows 11 deployment
- Ubuntu Server deployment

## Phase 2 — Networking

- WAN configuration
- LAN configuration
- DHCP
- DNS
- Firewall rules
- NAT
- Network segmentation
- Connectivity testing

## Phase 3 — Active Directory

- Active Directory Domain Services
- `lab.home` domain
- DNS integration
- Organizational Units
- Security groups
- User accounts
- Computer accounts
- Domain joining

## Phase 4 — Windows Administration

- Group Policy
- Windows Firewall
- Security hardening
- File sharing
- NTFS permissions
- Share permissions
- Remote administration
- User and computer management

## Phase 5 — Linux Administration

- Ubuntu Server
- SSH
- Web services
- Docker
- Linux permissions
- Service management
- Log analysis
- Linux firewall

## Phase 6 — Automation

- PowerShell
- Bash
- Active Directory automation
- System administration scripts
- Automated configuration

## Phase 7 — Security

- Network analysis
- Network discovery
- Windows security monitoring
- Sysmon
- Wazuh
- Vulnerability scanning
- Centralized logging
- Security hardening
- Incident investigation exercises

## Phase 8 — Advanced Infrastructure

- Additional clients
- Additional servers
- Network segmentation
- Advanced firewall rules
- VPN
- Monitoring dashboards
- Centralized logging
- Advanced troubleshooting scenarios

---

# Final Goal

The final objective is to create a documented, reproducible, and realistic virtual enterprise environment that demonstrates practical skills in:

- IT infrastructure
- System administration
- Network administration
- Windows Server
- Active Directory
- DNS and DHCP
- Linux administration
- Firewall administration
- Identity and access management
- Group Policy
- Troubleshooting
- Automation
- Network security
- Security monitoring

The completed lab will serve as both a hands-on learning environment and a technical portfolio project demonstrating practical IT infrastructure and cybersecurity skills.

---

# Disclaimer

This lab is a personal learning environment.

Security testing, network scanning, vulnerability testing, and other experiments are performed only against systems that are part of this labo or systems for which explicit authorization has been granted.

No confidential, personal, or sensitive information should be stored in this repository.

---

# Author

Maxime MBE

Enterprise Homelab — Infrastructure, Networking & Cybersecurity
