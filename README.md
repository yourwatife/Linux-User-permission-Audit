 Project Overview
This project is a hands-on Linux security audit focused on reviewing user accounts, group memberships, administrative privileges, file permissions, and potentially risky permission configurations.

The objective was to determine whether the Linux system contained unnecessary privileges, insecure permissions, or suspicious user/account configurations.

 Objectives

* Identify normal and system user accounts
* Review user group memberships
* Identify users with administrative privileges
* Examine home directory permissions
* Review sensitive account-file permissions
* Check for world-writable files and directories
* Review account login-shell configuration
* Assess the overall security of the system’s user and permission configuration

 Environment

Operating System: Kali Linux
Environment: Virtual Machine
Primary User: kali
Privilege Level During Audit: Root

⸻

Investigation

1. Identify Current User

Command

whoami

Result

root

Analysis

The investigation was performed with root privileges, allowing protected system files and account configurations to be examined.

2. Enumerate User Accounts

Command

cut -d: -f1 /etc/passwd

This command lists the accounts configured on the Linux system.

Further analysis was performed using:

awk -F: '$3 >= 1000 {print $1, $3, $4}' /etc/passwd

Finding

The relevant accounts identified were:

nobody 65534 65534
kali 1000 1000

Analysis

kali is the primary normal user account.

The nobody account is a standard restricted system account used by Linux services.

Screenshot 1 — User Accounts

<img width="164" height="46" alt="whoamiroot" src="https://github.com/user-attachments/assets/e82f9279-41f3-4892-80bc-aa898f4ff328" />



3. Review Sudo Group Membership

Command

getent group sudo

Finding

The kali account was identified as a member of the sudo group.

Analysis

Membership in the sudo group allows the user to perform administrative actions through sudo.

This is expected for a Kali Linux workstation but represents elevated privileges.

 Screenshot 2 — Sudo Group
 
<img width="582" height="66" alt="ludo1" src="https://github.com/user-attachments/assets/ed28a316-9e0d-4ba7-b6f0-e72c6451b241" />




4. Review User Home Directories

Commands

ls -la /home
ls -la /home/kali

Analysis

The ownership and permissions of the home directories and their contents were reviewed for inappropriate access.

No obvious permission issue was identified during the review.

 Screenshot 3 — Home Directory Permissions


Include the screenshot showing /home and/or /home/kali permissions.

<img width="238" height="26" alt="ludo3" src="https://github.com/user-attachments/assets/35bd2e95-e0ef-4556-99e6-e949e69b155e" />


5. Review Sensitive Account Files

Command

ls -l /etc/passwd /etc/shadow

Findings

/etc/passwd:

rw-r--r--

/etc/shadow:

rw-------

Analysis

/etc/passwd is readable by regular users, which is expected because it contains general account information.

/etc/shadow was restricted to root access. This is important because it contains password-hash information.

The permissions observed were appropriate.

 Screenshot 4 — Sensitive Account Files

<img width="236" height="29" alt="ludo5" src="https://github.com/user-attachments/assets/d5ee22b6-48b8-4021-97b6-08149eb5aa49" />

6. Review User Privileges

Command

id kali

Finding

The kali account belongs to the sudo group.

Analysis

This confirms that the account has access to administrative functionality through sudo.
<img width="959" height="44" alt="ludo2" src="https://github.com/user-attachments/assets/2c5fef3f-cf08-4b27-9f02-e1cde10eb085" />

7. Check for World-Writable Files

Command

find /home -type f -perm -002 -ls 2>/dev/null

Result

No output was returned.

Analysis

No world-writable files were identified under /home.


8. Check for World-Writable Directories

Command

find /home -type d -perm -002 -ls 2>/dev/null

Result

No output was returned.

Analysis

No world-writable directories were identified under /home.



9. Check for Empty Password Accounts

Command

awk -F: '($2 == "") {print $1}' /etc/shadow

Analysis

This check was performed to identify accounts with empty password fields.

No account was identified as requiring investigation from this check.


10. Review Account Lock Status

Command

passwd -S -a

Analysis

Account status information was reviewed to identify locked or unusual account states.

 Screenshot 6 — Account Status

Screenshot should show the output of passwd -S -a.


<img width="236" height="29" alt="ludo5" src="https://github.com/user-attachments/assets/36900aa6-852b-48e7-a99c-9b87225b3cb7" />

11. Review Login-Capable Accounts

Command

awk -F: '$7 !~ /(nologin|false)$/ {print $1, $7}' /etc/passwd

Analysis

This check identified accounts configured with usable login shells.

The purpose was to determine whether unexpected system/service accounts had interactive login capability.

No obvious suspicious account configuration was identified.

 Screenshot 7 — Login-Capable Accounts

Screenshot  shows the accounts and their configured login shells.

<img width="318" height="272" alt="ludo6" src="https://github.com/user-attachments/assets/9557438e-7568-4a37-80a3-977e167c7732" />

12. Review Important Directory Permissions

Command

ls -ld /home/* /tmp /var/tmp 2>/dev/null

Analysis

Ownership and permissions of important user and temporary directories were reviewed.

No obvious dangerous permission configuration was identified.

Screenshot 8 — Directory Permissions

Screenshot  show the permissions and ownership of /home, /tmp, and /var/tmp entries returned by the command.



<img width="222" height="40" alt="ludo7" src="https://github.com/user-attachments/assets/28c90716-2bcf-4c8f-9418-6648d60aa61c" />

13. Check /etc for World-Writable Files

Command

find /etc -maxdepth 1 -type f -perm -002 -ls 2>/dev/null

Result

No output was returned.

Analysis

No world-writable files were identified directly inside /etc.

 Findings Summary

Area	Finding	Risk
Normal user accounts	kali identified	Low
nobody account	Standard restricted system account	Low
Sudo membership	kali has sudo privileges	Medium
Home files	No world-writable files found	Low
Home directories	No world-writable directories found	Low
/etc/passwd	rw-r--r--	Low
/etc/shadow	rw-------	Low
/etc world-writable files	None identified	Low
Login-capable accounts	Reviewed	Low

 SOC Analyst Documentation

The Linux user and permission audit did not identify an obvious high-risk permission misconfiguration within the areas examined.

The primary privileged account identified was kali, which belongs to the sudo group. This provides administrative capabilities but is consistent with the expected configuration of a Kali Linux workstation.

The audit also confirmed that /etc/shadow was appropriately restricted and that no world-writable files or directories were identified in the locations examined.

 Conclusion

Based on the evidence reviewed, the system’s user and permission configuration appeared appropriately restricted in the areas tested.

No clear indicator of privilege abuse or dangerous file-permission configuration was identified during this audit.

This investigation demonstrates practical SOC and Linux security skills including:

* Linux account enumeration
* Privilege analysis
* Sudo group investigation
* File permission analysis
* Sensitive-file protection
* World-writable file detection
* Login-shell analysis
* Security finding documentation
* SOC-style assessment

Screenshot placement map

01-user-accounts.png	Section 2 — Enumerate User Accounts
02-sudo-group.png	Section 3 — Review Sudo Group Membership
03-home-permissions.png	Section 4 — Review User Home Directories
04-sensitive-files.png	Section 5 — Review Sensitive Account Files
05-user-privileges.png	Section 6 — Review User Privileges
06-account-status.png	Section 10 — Review Account Lock Status
07-login-shells.png	Section 11 — Review Login-Capable Accounts
08-directory-permissions.png	Section 12 — Review Important Directory Permissions

 Tools & Technologies

* Kali Linux
* Bash
* /etc/passwd
* /etc/shadow
* sudo
* awk
* find
* ls
* passwd
* Linux file permissions
* Linux user/group management
  
Skills Demonstrated


SOC Skills

* Evidence collection
* Security finding identification
* Permission auditing
* Risk assessment
* Investigation documentation
* SOC analyst reporting
