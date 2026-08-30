
# Day 4 - Group Policy and Security

## Objectives

The objective of Day 4 was to organize Active Directory security groups and implement Group Policy Objects for workstation security.

## Active Directory Security Groups

The existing Active Directory structure was retained.

LAB.HOME
├── Admin
│   ├── GG-Helpdesk
│   ├── GG-IT-Admins
│   └── GG-Server-Admins
└── Employees
    └── GG-Employees

These groups are Global Security Groups and are used to manage permissions through group membership rather than assigning permissions directly to individual users.

## User Accounts

Existing user accounts included:

* m.admin
* john

m.admin was associated with the IT administration group.

john was created as a regular employee account.

## Workstations OU

The CLIENT01 computer account was moved into:

LAB.HOME
└── Workstations
    └── CLIENT01

This allows workstation-specific Group Policies to be applied cleanly.

## GPO-Workstation-Security

An existing GPO named GPO-Workstation-Security was linked to the Workstations OU.

The GPO configured:

* Interactive logon: Machine inactivity limit
* Value: 900 seconds

This enforces automatic workstation locking after 15 minutes of inactivity.

The policy was validated using:

powershell
gpupdate /force

and:

powershell
gpresult /scope computer /r

## GPO-Workstation-Firewall

A workstation firewall GPO named GPO-Workstation-Firewall was linked to the Workstations OU.

The Domain firewall profile was configured as follows:

| Setting                 | Value |
| ----------------------- | ----- |
| Firewall state          | On    |
| Default inbound action  | Block |
| Default outbound action | Allow |

## Troubleshooting GPO Application

The firewall GPO did not initially appear in gpresult.

The following items were verified:

* CLIENT01 was in the Workstations OU.
* The GPO was linked to the OU.
* Authenticated Users was present in Security Filtering.
* The workstation was DomainAuthenticated.

A second issue occurred because gpresult was initially executed without administrative elevation.

The local administrator account on CLIENT01 was used to run the computer-scope report.

After elevation, the GPO appeared in the applied Group Policy list.

## Firewall Validation

The active firewall policy was checked using:

powershell
Get-NetFirewallProfile -PolicyStore ActiveStore |
Format-List Name,Enabled,DefaultInboundAction,DefaultOutboundAction

The resulting configuration was:

Domain
Enabled               : True
DefaultInboundAction  : Block
DefaultOutboundAction : Allow

Private
Enabled               : True
DefaultInboundAction  : Block
DefaultOutboundAction : Allow

Public
Enabled               : True
DefaultInboundAction  : Block
DefaultOutboundAction : Allow

The network profile was also verified as:


DomainAuthenticated


This confirmed that the workstation recognized the Active Directory domain and that the firewall policy was active.

## GPOs Present

At the end of Day 4, the domain contained the following Group Policy Objects:


Default Domain Controllers Policy
Default Domain Policy
GPO-Workstation-Firewall
GPO-Workstation-Security

## Lessons Learned

* Computer Configuration and User Configuration affect different scopes.
* GPOs must be linked to the correct Organizational Unit.
* Security Filtering can prevent a GPO from applying.
* gpresult should be run with appropriate privileges when troubleshooting computer policies.
* ActiveStore reflects the firewall policy currently active on the workstation.
* Group-based administration is preferable to assigning permissions directly to users.
* Layered security using pfSense and Windows Defender Firewall provides defense in depth.