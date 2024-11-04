# Sections

1. [Introduction](README.md#1-Introduction)
2. [Role Description](README.md#2-Role-description)
3. [Configuration Variables](README.md#3-Edit-parameters-in-the-monitoring-configuration-file)
4. [Installation Guide](README.md#4-Installation-Guide)

# 1. Introduction

The `powervs_storage_and_swap_setup` Ansible role performs various tasks related to disk configuration and swap space management for both SLES and RHEL systems. It is specifically tailored for SAP Netweaver instances, which require additional configuration steps.

# 2. Role Description

This role is responsible for the following tasks:

- Creating file systems with user-defined stripe size using Ansible's built-in LVM logical volumes modules
- Mounting the file systems on provided mount points
- Adding an entry to /etc/fstab for automount on reboot
- Optionally, converting the input data structure of disks_configuration variable from a dictionary to a general data structure
- Removing previous swap device configured (SAP Netweaver instance)
- Creating a new swap device on disk provided with swap_disk_wwn variable (SAP Netweaver instance)

A separate task, disks-dict2list.yml, is used to handle disks_configuration variable values passed as a dictionary to execute this role. The disks_configuration input variable supports two data structures, and only then will it convert the disks_configuration variable data structure from example A to example B below.

**Example A**. Data structure for **disks_configuration** variable as **dictionary** value example:
```
disks_configuration:
{
counts: [2,2,1],
names: [data,log,shared],
mounts: [/hana/data,/hana/log,/hana/shared],
wwns: [600507681082018bc8000000000057e4,600507681082018bc8000000000057e8,600507681082018bc8000000000057e5,600507681082018bc8000000000057e6,600507681082018bc8000000000057e7]
}
```

**Example B**. Data structure for **disks_configuration** variable as **list** value example:
```
disks_configuration: [
{
name: data,
mount: /hana/data,
wwns: 600507681082018bc8000000000057e4,600507681082018bc8000000000057e8
},
{
name: log,
mount: /hana/log,
wwns: 600507681082018bc8000000000057d9,600507681082018bc8000000000057ed7
},
{
name: shared,
mount: /hana/shared,
wwns: 600507681082018bc8000000000057f1
}
.
.
.
]
```

# 3. Edit parameters in the monitoring configuration file

Edit the configuration file - playbooks/vars/sample-variables-powervs-storage-setup.yml with the required inputs for Power LPARs' swap disk and file system creation.

| Name  | Type  |Example  | Description |
|-------|-------|---------|-------------|
|stripe_size|String|stripe_size: 64K| the size of the smallest unit of data that is written to a disk. In the example stripe size is set to 64KB and the module will write 64KB of data to the disk at a time. |
|swap_disk_wwn|String|swap_disk_wwn: "60050768108002DC6800000000029801"| the identifier of the disk that will be used to create a new swap device |
|disks_configuration|Object|disks_configuration:<br>&nbsp;&nbsp;- name: data<br>&nbsp;&nbsp;&nbsp;&nbsp; mount: /hana/data<br>&nbsp;&nbsp;&nbsp;&nbsp; wwns:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    - 600507681082018bc8000000000057e4<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    - 600507681082018bc8000000000057e8<br>&nbsp;&nbsp;- name: log<br>&nbsp;&nbsp;&nbsp;&nbsp; mount: /hana/log<br>&nbsp;&nbsp;&nbsp;&nbsp; wwns:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    - 600507681082018bc8000000000057d9<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    - 600507681082018bc8000000000057ed7<br>&nbsp;&nbsp;- name: shared<br>&nbsp;&nbsp;&nbsp;&nbsp; mount: /hana/shared<br>&nbsp;&nbsp;&nbsp;&nbsp; wwns:<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    - 600507681082018bc8000000000057f1<br>| **_disk_configuration_** is a list of volume identifiers along with how they must be mounted **_disks_configuration[\*].name_**: Identifier for the logical volume and volume group.<br> **_disks_configuration[\*].mount_**: The directory path where the filesystem is mounted on the operating system.<br> **_disks_configuration[\*].wwns_**:  World Wide Names used to uniquely identify storage devices. Can be obtained using multipath -ll |

# 4. Installation Guide

## 4.1 Prerequisites 
To create file systems, retrieve the wwns of the storage volumes from the PowerVS workspaces. 

## 4.2 Edit the network services server/client configuration file
Provide required inputs for Power LPARs' swap disk and file system creation in the configuration file - playbooks/vars/sample-variables-powervs-storage-setup.yml

## 4.3. Execute the Ansible playbook

To do the storage setup, execute the ansible playbook:
`ansible-playbook --connection=local -i "localhost," playbooks/sample-powervs-storage-setup.yml`

# Note:
For RHEL, **swap disk of size >= 24GB** is required for community role **[sap-netweaver-preconfigure](https://github.com/linux-system-roles/sap-netweaver-preconfigure)** to succeed.

# Dependencies

None.

# License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
