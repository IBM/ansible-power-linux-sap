Version 0.0.1
Notes
       * Initial beta release of IBM Power Virtual Systems SAP with RISE collection, referred to as power_linux_sap
Availability
       * GitHub v0.0.1(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.0
Notes
       * Change related to TSO
       * Removing all reference to terraform_wrapper variable, as it is no more used. Updated Readme and also updated few file names
       * Created sample variable files inside playbooks/vars directory for SLES and RHEL
       * Updated README.md to reflect current modifications
Availability
       * GitHub v1.0.0(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.1
Notes
       * Updated Ansible Galaxy Readme
Availability
       * GitHub v1.0.1(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.2
Notes
       * Updated Ansible Galaxy Readme
Availability
       * GitHub v1.0.2(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.3
Notes
       * Full Linux subscription automation
       * Install services like Squid, DNS, NTP, and NFS.
       * Install awscli packages
       * Setup proxy on clients
       * Bug fixing.
Availability
       * GitHub v1.0.3(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.4
Notes
       * Updated Chrony/NTP tasks
       * Updated Readme
Availability
       * GitHub v1.0.4(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.5
Notes
       * Bug Fixing
Availability
       * GitHub v1.0.5(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.6
Notes
       * Added new role powervs_client_enable_services
	* Updated Readme
	* Bug Fixing
Availability
       * GitHub v1.0.6(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.7
Notes
        * Updated Readme
        * Bug Fixing
Availability
       * GitHub v1.0.7(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.8
Notes
        * squid.conf updated
        * pip installation removed
Availability
       * GitHub v1.0.8(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.9
Notes
        * updated NFS setting on server side, to create new filesystem for mountable NFS directories
        * updated playbooks to run filesystem creation first
Availability
       * GitHub v1.0.9(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.10
Notes
       * Updated playbooks to use RHEl system calls, instead of community roles for SAP configurations
Availability
       * GitHub v1.0.10(https://github.com/IBM/ansible-power-linux-sap)

Version 1.0.11
Notes
       * Updated roles to work with new variable names.
       * Removed awscli usage
       * Removed specific OS related playbooks, to have one universal for any Linux OS.
       * Updated README
Availability
       * GitHub v1.0.11(https://github.com/IBM/ansible-power-linux-sap)

Version 1.1.0
Notes
       * Updated playbook power-linux-configure.yml
Availability
       * GitHub v1.1.0(https://github.com/IBM/ansible-power-linux-sap)

Version 1.1.2
Notes
       * Updated playbook power-linux-configure.yml
Availability
       * GitHub v1.1.2(https://github.com/IBM/ansible-power-linux-sap)

Version 1.1.3
Notes
       * Fix for chrony/NTP
Availability
       * GitHub v1.1.3(https://github.com/IBM/ansible-power-linux-sap)

Version 1.1.4
Notes
       * Add all private ip address range for NTP service
       * Include tests for NTP, DNS and NFS services
Availability
       * GitHub v1.1.4(https://github.com/IBM/ansible-power-linux-sap)

Version 2.0.0
Notes
       * Added checks using ansible-lint.
       * Renamed **powervs_fs_creation** role to **powervs_storage_setup**
       * Updated sample playbook files with sample variables files included.
       * README.md has been reworked.
Availability
       * GitHub v2.0.0(https://github.com/IBM/ansible-power-linux-sap)


Version 2.1.0
Notes
       * Allow user to specify `opts` and `fstype` when mounting a `NFS` share.
       * README.md update for powervs_client_enable_services.

Version 2.1.1
Notes
       * Add missing distro condition to skip a proxy task for SLES.

Version 2.2.0
Notes
       * New monitoring role for SAP supported on SLES.

Version 2.2.1
Notes
       * fix: private ingestion endpoints for sap monitory role

Version 3.0.0
Notes
       * Previous roles powervs_prepare_rhel_sap and powervs_prepare_sles_sap are now powervs_os_registration and powervs_configure_os_for_sap
       * Ansible role powervs_os_registration supports Full Linux Subscription and BYOL for RHEL/SLES.
       * Ansible role powervs_configure_os_for_sap sets up RHEL and SLES for SAP deployment, enabling required services and configurations.
       * Previous roles powervs_storage_setup and powervs_swap_creation now combined to one role powervs_storage_and_swap_setup
       * Ansible role powervs_storage_and_swap_setup configures disk and manages swap space on both SLES and RHEL systems.
       * Previous roles powervs_client_enable_services and powervs_install_services now combined to one role configure_network_management_services
       * Ansible role configure_network_management_services installs and configures network services on SLES and RHEL, based on the instance's role as client or server.

Version 3.1.0
Notes
       * RFS configuration setup
       * NFS client check fix

Version 3.1.1
Notes
       * Minimum community.general version 10.1.0
       * Skip pause for services only if status changed
