# Power LPAR Configuration for SAP HANA and SAP NetWeaver using Ansible collection


# Sections
 1. [Introduction](README.md#1-introduction)
 2. [Roles Description](README.md#2-roles-description)
 3. [Installation Guide](README.md#3-installation-guide)
 4. [Execution Details](README.md#4-execution-details)
 5. [Requirements, Dependencies and Testing](README.md#5-requirements-dependencies-and-testing)

# 1. Introduction

This ansible collection simplifies IBM PowerVS LPAR configuration for installing SAP HANA and SAP NetWeaver on SLES and RHEL environments. It doesn't install SAP HANA or NETWEAVER applications but, prepares the OS with correct configurations for SAP HANA/NetWeaver installations for best performance. They can be executed on same LPAR or different LPARs.

This collection has 4 modules, which are independent of each other and can be run individually.
1)	**Preparing Operating System for SAP installations.**
2)	**Creating Filesystems for SAP installations.**
3)	**Configuring SWAP spaces.**
4)	**Installing Management Services (SQUID, NTP, NFS, DNS)**

### Ansible Roles Summary

## Contents

Within this Ansible Collection, there are various Ansible Roles and no custom Ansible Modules.

### Ansible Roles

| Name | Summary |
| :--- | :--- |
| [powervs_install_services](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_install_services)| Installs Squid, NTP, NFS and DNS services and configures it as a server|
| [powervs_client_enable_services](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_client_enable_services) | Configures proxy and connects to NTP and DNS services and mounts the provided NFS path. |
| [powervs_storage_setup](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_storage_setup)| Configures storage |
| [powervs_swap_creation](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_swap_creation)| Configures swap disk |
| [powervs_prepare_rhel_sap](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_prepare_rhel_sap)| configure general RHEL OS settings for SAP software |
| [powervs_prepare_sles_sap](https://github.com/IBM/ansible-power-linux-sap/tree/main/roles/powervs_prepare_sles_sap)| configure general SLES OS settings for SAP software |


# 3. Installation Guide

Install **[collection](https://galaxy.ansible.com/ibm/power_linux_sap)** from Ansible Galaxy using below command

```ansible-galaxy collection install ibm.power_linux_sap```

A folder will be created in root directory : ```/root/.ansible/collections/ansible_collections/ibm/power_linux_sap/```

To install RHEL system roles, follow the steps provided [here](https://access.redhat.com/articles/6857351#installation)

***

# 4. Execution Details

Sample playbooks are provided in the playbooks directory of collection.

## Local Host Execution

```ansible-playbook --connection=local -i "localhost," sample-powervs-configure-os-for-sap.yml```

## Target Host Execution

```ansible-playbook -i "<target-host>," sample-powervs-configure-os-for-sap.yml```


# 5. Requirements, Dependencies and Testing

### 5.1 Operating System requirements

Designed for Linux operating systems, RHEL and SLES.

This role has not been tested and amended for SAP NetWeaver Application Server instantiations on IBM AIX or Windows Server.

Assumptions for executing this role include:
- Registered OS license and OS package repositories are available (from the relevant content delivery network of the OS vendor)

### 5.2 Python requirements

Python 3 from the execution/controller host.

### 5.3 Testing on execution/controller host

**Tests with Ansible Core release versions:**

- Ansible Core 2.9.19 community edition

**Tests with Python release versions:**

- Python 3.6.8

### Testing on target/remote host

**Tests with Operating System release versions:**

- RHEL 8.2, 8.4 and 8.8 for SAP
- SLES 12, 15 for SAP

## License

- [Apache 2.0](./LICENSE)

## Contributors

Contributors to the Ansible Roles within this Ansible Collection, are shown within [/docs/contributors](./docs/CONTRIBUTORS.md).
