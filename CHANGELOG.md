# Release Notes

<a name="v3.2.1"></a>

# [v3.2.1](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v3.2.1) - 24 Jan 2025

## Release Summary
* fix: Add the custom DNS entry to the beginning of /etc/resolv.conf

<a name="v3.2.0"></a>

# [v3.2.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v3.2.0) - 14 Jan 2025

## Release Summary
* fix: enhanced matching for rps_flw_cnt rule
* fix: check rps_sock_flow_entries value using sysctl command and then modify the value if not equal to 32768
* fix: refactor monitoring roles for sap, add assertions
* feat: introduce new optional variables for proxy for rhel and sles os registration

<a name="v3.1.1"></a>

# [v3.1.1](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v3.1.1) - 11 Dec 2024

## Release Summary
* fix: Requires community.general version 10.1.0 to use community.general.filesystem


<a name="v3.1.0"></a>

# [v3.1.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v3.1.0) - 05 Dec 2024

## Release Summary
* fix: Checks for NFS client on RHEL
* fix: RFS settings on RHEL and SLES

<a name="v3.0.0"></a>

# [v3.0.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v3.0.0) - 05 Nov 2024

## Release Summary
* Breaking changes: Previous roles powervs_prepare_rhel_sap and powervs_prepare_sles_sap are now powervs_os_registration and powervs_configure_os_for_sap
Previous roles powervs_storage_setup and powervs_swap_creation now combined to one role powervs_storage_and_swap_setup
Previous roles powervs_client_enable_services and powervs_install_services now combined to one role configure_network_management_services
Please take a look at the READMEs for the updated variable names.

<a name="v2.2.1"></a>

# [v2.2.1](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v2.2.1) - 18 Jul 2024

## Release Summary
* fix: private ingestion endpoints for sap monitory role

<a name="v2.2.0"></a>

# [v2.2.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v2.2.0) - 10 Jul 2024

## Release Summary
* New monitoring role for SAP supported on SLES

<a name="v2.1.1"></a>

# [v2.1.1](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v2.1.1) - 30 May 2024

## Release Summary
* Fix missing distro condition to skip a proxy task for SLES.

[Changes][v2.1.1]

<a name="v2.1.0"></a>

# [v2.1.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v2.1.0) - 18 May 2024

## Release Summary
* Allow user to specify `opts` and `fstype` when mounting a `NFS` share.
* README.md update for powervs_client_enable_services.

[Changes][v2.1.0]


<a name="v2.0.0"></a>

# [v2.0.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v2.0.0) - 16 May 2024

## Release Summary
* Added checks using ansible-lint.
* Renamed **powervs_fs_creation** role to **powervs_storage_setup**
* Updated sample playbook files with sample variables files included.

* README.md has been reworked.
[Changes][v2.0.0]


<a name="v1.1.5"></a>

# [v1.1.5](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.1.5) - 16 Feb 2024

## Release Summary
* Proxy client configuration on RHEL OS
* Adhere to Ansible lint

[Changes][v1.1.5]


<a name="v1.1.4"></a>

# [v1.1.4](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.1.4) - 13 Oct 2023

## Release Summary
* support ntp services for all private ipv4 CIDRs
* include tests for NTP, NFS and DNS client configuration
* Correct the DNS forwarder configuration by making right configurations in /etc/named.conf file
* Change the way disk is mounted , so that even if the disk fails , on reboot os comes up without being stuck. This is modified in /etc/fstab file

[Changes][v1.1.4]


<a name="v1.1.3"></a>

# [v.1.1.3](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.1.3) - 21 Aug 2023

## Release Summary
* Chrony fix on ntp server

[Changes][v1.1.3]


<a name="v1.1.2"></a>

# [v1.1.2](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.1.2) - 03 Jul 2023

## Release Summary
* Updated playbook power-linux-configure.yml

[Changes][v1.1.2]


<a name="v1.1.0"></a>

# [v1.1.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.1.0) - 28 Jun 2023

## Release Summary
* Updated playbook power-linux-configure.yml

[Changes][v1.1.0]


<a name="v1.0.11"></a>

# [v1.0.11](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.11) - 09 Jun 2023

## Release Summary
* Updated roles to work with new variable names.
* Removed awscli usage
* Removed specific OS-related playbooks, to have one universal for any Linux OS.
* Updated README

[Changes][v1.0.11]


<a name="v1.0.10"></a>

# [v1.0.10](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.10) - 30 Jan 2023

## Release Summary
* Updated playbooks to use RHEL system calls, instead of community roles for SAP configurations

[Changes][v1.0.10]


<a name="v1.0.9"></a>

# [v1.0.9](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.9) - 05 Jan 2023

## Release Summary
* updated NFS setting on server side, to create new filesystem for mountable NFS directories
* updated playbooks to run filesystem creation first

[Changes][v1.0.9]


<a name="v1.0.8"></a>

# [v1.0.8](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.8) - 14 Oct 2022

## Release Summary
* squid.conf updated
* pip installation removed

[Changes][v1.0.8]


<a name="v1.0.7"></a>

# [v1.0.7](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.7) - 22 Sep 2022

## Release Summary
* Updated Readme

[Changes][v1.0.7]


<a name="v1.0.6"></a>

# [v1.0.6](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.6) - 14 Sep 2022

## Release Summary
* Added new role powervs_client_enable_services
* Updated Readme

[Changes][v1.0.6]


<a name="v1.0.4"></a>

# [v1.0.4](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.4) - 26 Aug 2022

## Release Summary
* Updated Chrony/NTP tasks
* Updated Readme

[Changes][v1.0.4]


<a name="v1.0.3"></a>

# [v1.0.3](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.3) - 25 Aug 2022

## Release Summary
This release will add the following capabilities:

- Full Linux subscription automation
- Install services like Squid, DNS, NTP, and NFS on server hosts.
- Install awscli packages on server host.
- Setup proxy on clients
- Bug fixing.

[Changes][v1.0.3]


<a name="v1.0.2"></a>

# [v1.0.2](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.2) - 06 Jul 2022

## Release Summary
* Updated Ansible Galaxy Readme

[Changes][v1.0.2]


<a name="v1.0.0"></a>

# [v1.0.0](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v1.0.0) - 06 Jul 2022

## Release Summary
* Change related to TSO
* Removing all reference to terraform_wrapper variable, as it is no more used.
* Updated Readme and updated few file names
* Created sample variable files inside playbooks/vars directory for SLES and RHEL
* Updated README.md to reflect current modifications

[Changes][v1.0.0]


<a name="v0.0.1"></a>

# [Initial Release (v0.0.1)](https://github.com/IBM/ansible-power-linux-sap/releases/tag/v0.0.1) - 17 Jun 2022

## Release Summary
Initial beta release of IBM Power Virtual Systems SAP with RISE collection, referred to as power_linux_sap

[Changes][v3.2.1]
[v3.2.1]: https://github.com/IBM/ansible-power-linux-sap/compare/v3.2.0...v3.2.1
[v3.2.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v3.1.1...v3.2.0
[v3.1.1]: https://github.com/IBM/ansible-power-linux-sap/compare/v3.1.0...v3.1.1
[v3.1.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v3.0.0...v3.1.0
[v3.0.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v2.2.1...v3.0.0
[v2.2.1]: https://github.com/IBM/ansible-power-linux-sap/compare/v2.2.0...v2.2.1
[v2.2.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v2.1.1...v2.2.0
[v2.1.1]: https://github.com/IBM/ansible-power-linux-sap/compare/v2.1.0...v2.1.1
[v2.1.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v2.0.0...v2.1.0
[v2.0.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.1.5...v2.0.0
[v1.1.5]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.1.4...v1.1.5
[v1.1.4]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.1.3...v1.1.4
[v1.1.3]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.1.2...v1.1.3
[v1.1.2]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.1.0...v1.1.2
[v1.1.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.11...v1.1.0
[v1.0.11]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.10...v1.0.11
[v1.0.10]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.9...v1.0.10
[v1.0.9]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.8...v1.0.9
[v1.0.8]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.7...v1.0.8
[v1.0.7]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.6...v1.0.7
[v1.0.6]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.4...v1.0.6
[v1.0.4]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.3...v1.0.4
[v1.0.3]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.2...v1.0.3
[v1.0.2]: https://github.com/IBM/ansible-power-linux-sap/compare/v1.0.0...v1.0.2
[v1.0.0]: https://github.com/IBM/ansible-power-linux-sap/compare/v0.0.1...v1.0.0
[v0.0.1]: https://github.com/IBM/ansible-power-linux-sap/tree/v0.0.1
