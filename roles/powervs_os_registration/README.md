# Sections

1. [Introduction](README.md#1-Introduction)
2. [Role Description](README.md#2-Role-description)
3. [Configuration Variables](README.md#3-Edit-parameters-in-the-monitoring-configuration-file)
4. [Installation Guide](README.md#4-Installation-Guide)

# 1. Introduction

The `powervs_os_registration` Ansible role is responsible for activating the necessary subscription for the Linux operating system, which includes:
- Full Linux Subscription: [Learn more](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux)
- RHEL subscription
- SUSE subscription

# 2. Role Description

This role will dynamically determine the subscription type to activate based on the values set for the `byol` or `fls` fields in the configuration file. Specifically, it will:
- Activate a Full Linux Subscription if the `byol` field is set
- Activate a RHEL/SLES subscription if the `fls` field is set

# 3. Edit parameters in the monitoring configuration file
Edit the configuration file - `playbooks/vars/sample-variables-powervs-os-registration.yml` with the required inputs to register the OS.

| Name       | Type       |Example                                     | Description                        |
|------------|------------|--------------------------------------------|-----------------------------------|
| byol  | Object   | <br>byol:<br>&nbsp;&nbsp;username:`username`<br> &nbsp;&nbsp;password:`password`<br> | BYOL for RHEL/SLES. For RHEL provide username and password. For SLES provide email as username and activation code as password. Do not provide it for Full Linux Subscription |
| fls   | Boolean  | fls: true | if true, performs Full Linux Subscription. Do not provide if for BYOL|

# 4. Installation Guide

## 4.1 Edit the configuration file

To register a PowerVS LPAR OS, you need to specify either `fls` or `byol` in the `playbooks/vars/sample-variables-powervs-os-registration.yml` configuration file, depending on whether you need to activate a Full Linux Subscription (FLS) or Bring Your Own License (BYOL) for RHEL/SLES.

Please ensure to specify the correct option in the configuration file to ensure successful registration of your PowerVS OS.

## 4.2 Execute the Ansible playbook

To prpeare the OS for SAP system, execute the ansible playbook:
`ansible-playbook --connection=local -i "localhost," playbooks/sample-powervs-os-registration.yml`

# Notes

Ansible playbook may report **Failure/Warning**, if scripts analyze reboot is required for settings applied by it. User should reboot their LPAR, in that case.

# Dependencies

None.

# License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).