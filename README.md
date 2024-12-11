# Power LPAR Configuration for SAP HANA and SAP NetWeaver using Ansible collection


# Sections
 1. [Introduction](#introduction)
 1. [Role Description](#role-description)
 1. [Role Dependencies](#role-dependencies)
 1. [Installation Guide](#installation-guide)
 1. [Execution Details](#execution-details)
 1. [Requirements, Dependencies and Testing](#requirements-dependencies-and-testing)

# Introduction

This ansible collection simplifies IBM PowerVS LPAR configuration for installing SAP HANA and SAP NetWeaver on SLES and RHEL environments. It doesn't install SAP HANA or NETWEAVER applications but, prepares the OS with correct configurations for SAP HANA/NetWeaver installations for best performance. They can be executed on same LPAR or different LPARs.

This collection has 5 modules, which are independent of each other and can be run individually.
1.	**Preparing Operating System for SAP installations.**
1.	**Creating Filesystems for SAP installations.**
1.	**Installing and Configuring Management Services (SQUID, NTP, NFS, DNS)**
1.	**Creating and deleting SAP monitoring configurations**
1.  **OS registrations**

# Roles Description

| Name | Summary |
| :--- | :--- |
| [configure_network_management_services](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/configure_network_management_services)| Installs and configures network services on SLES and RHEL |
| [monitoring_sap](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/monitoring_sap)| Configures/deletes a SAP monitoring instance on a IBM Cloud Virtual Server |
| [powervs_configure_os_for_sap](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_configure_os_for_sap)| Configure general RHEL/SLES OS settings for SAP software |
| [powervs_os_registration](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_os_registration)| Full Linux Subscription and BYOL support for RHEL/SLES  |
| [powervs_storage_and_swap_setup](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_storage_and_swap_setup)| Configures disk and manages swap space on both SLES and RHEL systems |

# Role Dependencies

Install the below collections.

|Collection|Version|
|----------|-------|
|redhat.sap_install| >= 1.3.7|
|community.general| >= 10.1.0|
|ansible.posix| >= 1.5.4|
|ansible.utils| >= 4.1.0|


# Installation Guide

Install **[collection](https://galaxy.ansible.com/ibm/power_linux_sap)** from Ansible Galaxy using below command

```ansible-galaxy collection install ibm.power_linux_sap```

A folder will be created in root directory : ```/root/.ansible/collections/ansible_collections/ibm/power_linux_sap/```

To install RHEL system roles, follow the steps provided [here](https://access.redhat.com/articles/6857351#installation)

***

# Execution Details

Sample playbooks are provided in the playbooks directory of collection.

## Local Host Execution

```ansible-playbook --connection=local -i "localhost," sample-powervs-configure-os-for-sap.yml```

## Target Host Execution

```ansible-playbook -i "<target-host>," sample-powervs-configure-os-for-sap.yml```


# Requirements, Dependencies and Testing

### Operating System requirements

Designed for Linux operating systems, RHEL and SLES.

This role has not been tested and amended for SAP NetWeaver Application Server instantiations on IBM AIX or Windows Server.

Assumptions for executing this role include:
- Registered OS license and OS package repositories are available (from the relevant content delivery network of the OS vendor)

### Python requirements

Python 3 from the execution/controller host.

### Testing on execution/controller host

**Tests with Ansible Core release versions:**

- Ansible Core 2.9.19 community edition

**Tests with Python release versions:**

- Python 3.6.8

### Testing on target/remote host

**Tests with Operating System release versions:**

- RHEL 8.2, 8.4, 8.8, 9.2 for SAP
- SLES 12, 15 for SAP

## License

- [Apache 2.0](./LICENSE)

## Contributors

Contributors to the Ansible Roles within this Ansible Collection, are shown within [/docs/contributors](./docs/CONTRIBUTORS.md).
