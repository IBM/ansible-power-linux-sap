# Sections

1. [Introduction](#introduction)
1. [Role Description](#role-description)
1. [Role Dependencies](#role-dependencies)
1. [Role Variables](#role-variables)
1. [Installation Guide](#installation-guide)

# Introduction

The `powervs_os_registration` Ansible role is responsible for activating the necessary subscription for the Linux operating system, which includes:
- Full Linux Subscription: [Learn more](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux)
- RHEL subscription
- SUSE subscription

# Role Description

This role will dynamically determine the subscription type to activate based on the values set for the `byol` or `fls` fields in the configuration file. Specifically, it will:
- Activate a Full Linux Subscription if the `byol` field is set
- Activate a RHEL/SLES subscription if the `fls` field is set

# Role Dependencies

Install the below collections.

|Collection|Version|
|----------|-------|
|community.general| 10.1.0|

# Role Variables


| Name       | Type       |Example                                     | Description                        |
|------------|------------|--------------------------------------------|-----------------------------------|
| byol  | Object   | <br>byol:<br>&nbsp;&nbsp;username:`username`<br> &nbsp;&nbsp;password:`password`<br> &nbsp;&nbsp;password:`server_proxy_hostname`<br> &nbsp;&nbsp;password:`server_proxy_port`<br>| BYOL for RHEL/SLES. For RHEL provide username and password. If your machine does not have public internet access, you also need to provide a hostname and port of a proxy server with a valid route to the registration servers. For SLES provide email as username and activation code as password. Do not provide it for Full Linux Subscription |
| fls   | Boolean  | fls: true | if true, performs Full Linux Subscription. Do not provide if for BYOL|

# Installation Guide

## Edit the configuration file

To register a PowerVS LPAR OS, you need to specify either `fls` or `byol` in the `playbooks/vars/sample-variables-powervs-os-registration.yml` configuration file, depending on whether you need to activate a Full Linux Subscription (FLS) or Bring Your Own License (BYOL) for RHEL/SLES.

Please ensure to specify the correct option in the configuration file to ensure successful registration of your PowerVS OS.

## Execute the Ansible playbook

To register a PowerVS LPAR OS, execute the ansible playbook:
`ansible-playbook --connection=local -i "localhost," playbooks/sample-powervs-os-registration.yml`

# Notes

Ansible playbook may report **Failure/Warning**, if scripts analyze reboot is required for settings applied by it. User should reboot their LPAR, in that case.

# License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
