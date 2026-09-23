Day 06 — DNS Troubleshooting, Repair & Internet Connectivity

Objective

Restore reliable DNS resolution and Internet connectivity for the Windows domain environment without rebuilding Active Directory or changing the existing GPO/security configuration.

Starting Point

The lab uses:

VMware Workstation Pro

pfSense as the network gateway/firewall

DC01 — Windows Server / Active Directory Domain Controller

CLIENT01 — Windows 11 domain client

Domain: lab.home

DC01: 192.168.10.10

pfSense LAN: 192.168.10.1

The existing Active Directory structure, users, groups, permissions and GPO configuration were preserved.

Problem Identified

Internet connectivity worked from pfSense, but DNS resolution from the domain machines was failing.

Observed symptoms included:

ping 8.8.8.8 worked from pfSense.

DC01 and CLIENT01 could communicate with the LAN gateway.

DNS queries from the clients timed out.

nslookup google.com initially returned DNS timeouts.

DNS queries against 127.0.0.1 on DC01 worked, while queries against 192.168.10.10 initially failed.

Packet capture showed DNS requests reaching the DNS service but responses were not being returned correctly.

The issue began after the original DNS configuration was introduced.

Troubleshooting

The following components were checked during troubleshooting:

pfSense LAN firewall rules

pfSense WAN DHCP configuration

pfSense outbound NAT

pfSense DNS Resolver / Unbound

DNS port 53

DNS listening interfaces

DC01 DNS server configuration

DNS forwarders

DNS root hints

Active Directory-integrated DNS

DNS SRV records

Local and remote DNS resolution

A direct test using drill on pfSense confirmed that Unbound itself could resolve external DNS names.

DNS Repair

Because the issue was isolated to the Windows DNS service configuration and the problem had appeared after the DNS setup, DNS on DC01 was rebuilt without removing Active Directory Domain Services.

Preserved

Active Directory Domain Services

lab.home

DC01 identity and IP configuration

Organizational Units

Security groups

Users

GPOs

File/share permissions

pfSense routing and firewall configuration

Rebuilt

Windows DNS Server role

DNS service configuration

DNS forwarding configuration

Final DNS Configuration

DC01 uses itself as its DNS server:

DC01: 192.168.10.10
Preferred DNS: 192.168.10.10

Configured forwarders:

192.168.10.1   → pfSense01.lab.home
1.1.1.1        → Cloudflare DNS
8.8.8.8        → Google DNS

All configured forwarders validated successfully.

Validation

DNS resolution from DC01 was confirmed with:

Resolve-DnsName google.com -Server 192.168.10.10
Resolve-DnsName google.com -Server 1.1.1.1

Both returned successfully.

CLIENT01 was then tested using:

nslookup google.com

The lookup returned a non-authoritative answer and successfully resolved google.com.

The message:

Server: Unknown
Address: 192.168.10.10

was observed, but this does not indicate a DNS failure. The DNS server was reachable and successfully resolved the requested external name.

Final Architecture

                         INTERNET
                            |
                            v
                     +-------------+
                     |   pfSense   |
                     | 192.168.10.1|
                     |   Gateway   |
                     +------+------+
                            |
                            |
                     +------+------+
                     |    DC01     |
                     |192.168.10.10|
                     |             |
                     | AD DS       |
                     | DNS         |
                     | GPO         |
                     +------+------+
                            |
                            |
                     +------+------+
                     |  CLIENT01   |
                     | Windows 11  |
                     +-------------+

DNS flow:

CLIENT01
   |
   v
DC01 DNS (192.168.10.10)
   |
   +--> lab.home → AD-integrated DNS
   |
   +--> Internet names → DNS forwarders
                           |
                           +--> pfSense
                           +--> 1.1.1.1
                           +--> 8.8.8.8

Result

AD DS preserved: PASS

Internal lab.home DNS: PASS

DC01 external DNS resolution: PASS

CLIENT01 external DNS resolution: PASS

Internet connectivity restored: PASS

Existing AD/GPO configuration preserved: PASS

Lessons Learned

A successful ping to an external IP does not prove DNS is working.

DNS troubleshooting should distinguish between:

network connectivity,

DNS transport,

local DNS service,

forwarding,

and client configuration.

Domain clients should use the Active Directory DNS server rather than public DNS directly.

Rebuilding a single broken role can be preferable to rebuilding an entire lab when the scope of the failure is known.

DNS changes should be validated from both the domain controller and the client.

Screenshots — Major Milestones Only

Do not capture every troubleshooting command. The journal contains the detailed troubleshooting history.

Add only these important screenshots:

1. Rebuilt DNS / Forwarders

Capture:

DNS Manager
→ DC01
→ Properties
→ Forwarders

The screenshot should show:

192.168.10.1
1.1.1.1
8.8.8.8

with successful validation visible if possible.

Save as:

screenshots/active-directory/05-dns-forwarders-working.png

2. Client DNS Resolution

On CLIENT01, capture the successful:

nslookup google.com

showing:

Server: 192.168.10.10

and a successful non-authoritative answer.

Save as:

screenshots/networking/02-client-dns-working.png

3. Internet Restored

Capture the browser on CLIENT01 successfully accessing an external website.

Save as:

screenshots/networking/03-client-internet-working.png

These three screenshots are sufficient for Day 06.

Status

Day 06 completed.

The DNS problem is resolved and the lab is ready to continue with the next major infrastructure milestone.