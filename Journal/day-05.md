# Day 05 — Active Directory File Sharing & Access Control

## Objectives

- Configure Windows file shares on DC01.
- Implement Active Directory group-based access control.
- Configure NTFS and SMB share permissions.
- Validate access using different domain users.
- Troubleshoot and correct an access-control issue.

---

## 1. Configured the Public File Share

- Created the `Public` shared folder on DC01 and configured SMB access through `\\DC01\Public`.
- Assigned `GG-Employees` the required NTFS permissions to allow authorized employees to create, modify, and delete files.
- Validated access using the `john` domain account, confirming successful read and write operations.

---

## 2. Configured the IT File Share

- Created the `IT` shared folder on DC01 and exposed it through `\\DC01\IT`.
- Restricted SMB share access to the `GG-IT-Admins` security group using Full Control at the share level.
- Assigned `GG-IT-Admins` Modify permissions at the NTFS level to enforce group-based access control.

---

## 3. Tested and Troubleshot Access Control

- Tested `john` against the `IT` share and identified unintended access caused by an overly permissive `Everyone` share permission.
- Removed `Everyone` from the IT share permissions and restricted access to `GG-IT-Admins`.
- Re-tested the configuration and confirmed that authorized IT administrators could access and modify IT files while unauthorized employees were denied access.
- Verified the final configuration using Active Directory group membership, SMB share permissions, NTFS permissions, and user access tests.

---

## Key Skills Practiced

- Active Directory security groups
- SMB file sharing
- NTFS permissions
- Share permissions
- Role-Based Access Control (RBAC)
- Least-privilege access
- Windows file server administration
- Access-control troubleshooting

---

## Current Access Model

| User | Security Group | Public | IT |
|------|----------------|--------|----|
| `john` | `GG-Employees` | Read / Write | Denied |
| `it.admin` | `GG-IT-Admins` | Not specifically assigned | Read / Write |

---

## Result

The lab now demonstrates a functional Active Directory-based file access model where access to shared resources is controlled through security groups rather than individual user permissions.

The `Public` share is available to authorized employees, while the `IT` share is restricted to members of `GG-IT-Admins`.

---

## Next Steps

- Prepare Windows Server DHCP on DC01.
- Validate DHCP configuration before migration.
- Plan the transition of DHCP from pfSense to DC01.
- Maintain pfSense DHCP temporarily until the DC01 DHCP service is fully tested.