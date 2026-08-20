# Day 3 - DHCP, DNS, and Domain Join

## Objectives

The objective of Day 3 was to prepare the client workstation for Active Directory and join it to the domain.

## DHCP Configuration

DHCP remained temporarily managed by pfSense.

Configuration:

| Setting         | Value            |
| --------------- | ---------------- |
| DHCP server     | pfSense          |
| Pool start      | `192.168.10.100` |
| Pool end        | `192.168.10.200` |
| Default gateway | `192.168.10.1`   |

Initially, pfSense distributed its own address as the DNS server.

This was changed so that clients received:

* DNS server: `192.168.10.10` (DC01)

This allowed Windows clients to resolve the Active Directory domain correctly.

## DNS Validation

From CLIENT01, the following tests were performed:

powershell
ping dc01.lab.home


powershell
nslookup lab.home


powershell
nslookup -type=SRV _ldap._tcp.dc._msdcs.lab.home


The DNS tests were successful.

The `nslookup` output initially displayed `Server: Unknown`. This was determined to be caused by the absence of a reverse DNS (PTR) record and did not prevent forward DNS resolution or Active Directory functionality.

## Domain Join

CLIENT01 was joined to:

`lab.home`

The workstation was restarted after joining the domain.

After reboot, domain authentication was tested using a domain account.

## Validation

The following commands confirmed domain membership:

powershell
whoami


powershell
systeminfo | findstr /B /C:"Domain"


CLIENT01 also appeared in Active Directory.

The computer object was later moved from the default `Computers` container to the `Workstations` Organizational Unit.

## Lessons Learned

* DHCP and DNS are separate services.
* A client can receive DHCP from pfSense while using DNS from a Windows Domain Controller.
* Active Directory domain joins depend on correct DNS configuration.
* A reverse DNS record is not required for normal domain functionality, although it improves name resolution and troubleshooting.

