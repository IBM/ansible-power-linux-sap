# Sections

1. [Introduction](README.md#1-Introduction)
2. [Role Description](README.md#2-Role-description)
3. [Configuration Variables](README.md#3-Edit-parameters-in-the-configuration-file)
4. [Installation Guide](README.md#4-Installation-Guide)

# 1. Introduction

The `powervs_configure_os_for_sap` Ansible role is designed to prepare the operating system for SAP system deployment on RHEL and SLES platforms. This role ensures that the necessary services and configurations are enabled to support SAP system requirements.

# 2. Role Description

**SAP System Preparation**

This role performs the following tasks to prepare the operating system for SAP system deployment:

- Enables multipathd daemon
- Enables NFS Service
- Enables rpcbind daemon
- Sets MTU value to 9000 for SAP network interfaces
- Enables TSO for SAP network interfaces
- Sets transparent_hugepage to never
- Executes RHEL/SLES specific Ansible roles

On **RHEL**, this role installs and applies **[RHEL system roles](https://access.redhat.com/articles/3050101)**, which streamline system configurations for SAP workloads:

- **`sap_general_preconfigure`**: Provides baseline OS settings for all SAP applications.
- **`sap_hana_preconfigure`**: Adds SAP HANA-specific tuning, optimizing memory and I/O.
- **`sap_netweaver_preconfigure`**: Configures settings tailored to SAP NetWeaver’s application server needs.

All settings applied remain persistent across reboot.

# 3. Edit parameters in the configuration file
To prepare the OS for SAP system deployment edit the configuration file `playbooks/vars/sample-powervs-configure-os-for-sap.yml` with following information.

| Name  | Type  |Example  | Description |
|-------|-------|---------|-------------|
| sap_solution  | String  |sap_solution: "HANA"  | SAP Solution to be installed on Power Virtual Server |
| sap_domain    | String  |sap_domain: "poc.cloud"  | SAP network domain name                              |

# 4. Installation Guide

## 4.1 Prerequisites

In case of RHEL systems, install the following packages

```bash
sudo dnf -y install rhel-system-roles rhel-system-roles-sap
```

## 4.2 Edit the network services server/client configuration file

Edit the configuration file `playbooks/vars/sample-powervs-configure-os-for-sap.yml` with required inputs.

## 4.3 Execute the Ansible playbook

To prpeare the OS for SAP system, execute the ansible playbook:
`ansible-playbook --connection=local -i "localhost," playbooks/sample-powervs-configure-os-for-sap.yml`

# Notes

Ansible playbook may report **Failure/Warning**, if scripts analyze reboot is required for settings applied by it. User should reboot their LPAR, in that case.

# Dependencies

None.

# License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
