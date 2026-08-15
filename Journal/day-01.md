# journal/day-01-project-setup.md

# Day 1 - Project Setup

## Objectives

The objective of Day 1 was to prepare the virtualization environment and deploy the first components of the project homelab.

The following tasks were completed:

* Installed VMware Workstation Pro.
* Downloaded all required operating system images.
* Configured VMware virtual networks.
* Installed pfSense Community Edition.
* Created the first Windows 11 client virtual machine.
* Troubleshot a Windows Server installation issue.

## Software Downloaded

| Software                         | Purpose                            |
| -------------------------------- | ---------------------------------- |
| VMware Workstation Pro           | Virtualization platform            |
| Windows Server Evaluation        | Domain Controller operating system |
| Windows 11 Enterprise Evaluation | Client workstation                 |
| Ubuntu Server LTS                | Linux server                       |
| pfSense Community Edition 2.8.1  | Firewall and router                |

## VMware Virtual Networks

### VMnet8 - NAT

Used to provide Internet access to the virtual machines.

* Network: `192.168.56.0/24`
* DHCP enabled

### VMnet1 - Host-Only

Used as the internal laboratory network.

* Initially configured as `192.168.244.0/24`
* Used by pfSense LAN, servers, and clients

## pfSense Deployment

Created a virtual machine named `PFSENSE01`.

Network adapters:

* WAN → VMnet8
* LAN → VMnet1

### Installation Issue

The first installation used the Netgate installer.

The installer displayed a message indicating that the device did not have an active pfSense Plus subscription.

The issue was resolved by installing pfSense Community Edition 2.8.1.

### Final Network Configuration

| Interface | Address                    |
| --------- | -------------------------- |
| WAN       | `192.168.56.128/24` (DHCP) |
| LAN       | `192.168.10.1/24`          |

The LAN network was configured as `192.168.10.0/24`.

pfSense DHCP was enabled with the following range:

* Start: `192.168.10.100`
* End: `192.168.10.200`

## CLIENT01 Deployment

Created a Windows 11 Enterprise Evaluation virtual machine named `CLIENT01`.

### Hardware

* 2 vCPU
* 4 GB RAM
* 64 GB disk
* UEFI
* Secure Boot enabled
* TPM 2.0 enabled

### Network

* Connected to the pfSense LAN network.
* Received an IP address through pfSense DHCP.

## Windows Server Installation Troubleshooting

The first Windows Server installation failed with the error:

> Windows cannot find the Microsoft Software License Terms.

The issue was traced to VMware Easy Install.

The virtual machine was recreated using a custom installation:

* Install the operating system later.
* Mount the Windows Server ISO manually.
* Complete the installation normally.

The installation completed successfully.

## Validation

* pfSense was reachable from CLIENT01.
* CLIENT01 received a DHCP lease.
* Internet connectivity was available.
* The internal LAN was operational.

## Lessons Learned

* pfSense Plus and pfSense CE now use the same installation package.
* VMware Easy Install can interfere with Windows Server installation.
* Separating WAN and LAN networks simplifies homelab troubleshooting.
* Documenting configuration decisions helps reproduce and troubleshoot the environment.
