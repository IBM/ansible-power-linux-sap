# POWER LPAR Configuration for SAP HANA and SAP NetWeaver using Ansible


## Introduction

This ansible collection simplifies IBM PowerVS LPAR configuration for installing SAP HANA and SAP NetWeaver on SLES and RHEL environments. It doesn't install SAP HANA or NETWEAVER applications but, prepares the OS with correct configurations for SAP HANA/NetWeaver installations for best performance. They can be executed on same LPAR or different LPARs.

This collection has 3 modules, which are independent of each other and can be run individually.
1)	**Preparing Operating System for SAP installations.**
2)	**Creating Filesystems for SAP installations.**
3)	**Configuring SWAP spaces.**

### Ansible Roles Summary

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Input Variable Name</th>
		<th>Mandatory or Optional</th>
			<th>Variable description</th>
			<th>Variable Values</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3><b>powervs_prepare_sles_sap</b><br /></td>
            <td rowspan=1><b>1. sap_solution</b></td>
	    <td><b>Mandatory</b></td>
            <td rowspan=1>Saptune is executed based on this value</td>
            <td rowspan=1><b>HANA or NETWEAVER or NETWEAVER+HANA</b></td>
        </tr>
        <tr>
            <td><b>2. host_ip</b></td>
	    <td><b>Optional</b></td>
            <td>If this variable is included then mtu 9000 will not be set for this interface IP.</td>
            <td>e.g.: 192.168.1.1</td>
        </tr>
	<tr>
            <td><b>3. suse_subscription : { <br />username:"",<br />key:"", <br />release:"" <br />}</td>
  	    <td><b>Optional</b></td>
            <td>SUSE subscription information. It is a dictionary. Should be set only if subscription is not already set or subscription has to be updated</td>
            <td>e.g.: { <br />username:"XYZ",<br />key:"ABC" ,<br />release:"12"<br />}</td>
        </tr>
         <tr>
            <td rowspan=4><b>powervs_prepare_rhel_sap</b><br /></td>
            <td rowspan=1><b>1. sap_solution</b></td>
	    <td><b>Mandatory</b></td>
            <td rowspan=1>RHEL community roles for HANA or NETWEAVER will be executed</td>
            <td rowspan=1><b>HANA or NETWEAVER</b></td>
        </tr>
        <tr>
            <td><b>2. host_ip</b></td>
    	    <td><b>Mandatory</b></td>
            <td>If this variable is included then mtu 9000 will not be set for this interface IP.</td>
            <td>e.g.: 192.168.1.1</td>
        </tr>
	<tr>
            <td><b>3. sap_domain</b></td>
	    <td><b>Mandatory</b></td>
            <td>sap domain name</td>
            <td>e.g.: xyz.com</td>
        </tr>
		 <tr>
            <td><b>4. rhel_subscription : { <br />username:"",<br />password:"" ,<br />release:""<br />}</td>
  	    <td><b>Optional</b></td>
            <td>RHEL subscription information. It is a dictionary. Should be set only if subscription is not already set or subscription has to be updated</td>
            <td>e.g.: { <br />username:"XYZ",<br />password:"ABC" ,<br />release:"8.2"<br />}</td>
        </tr>
		<tr>
         <td rowspan=2><b>powervs_fs_creation</b><br /></td>
            <td rowspan=1><b>1.a. disks_configuration: { counts: [ ], names: [ ],paths: [ ],wwns: [ ] }<br />1.b. disks_configuration: [ { name: "", path:"", wwns: }...]</b></td>
 	    <td><b>Mandatory</b></td>
            <td>Disks configuration value to create and mount filesystems. Supports 2 data structures. First data structure is a single dictionary. Second data structure is a list of dictionaries.</td>
            <td rowspan=1>see <b>example A</b> and <b>example B</b> below</td>
        </tr>
	<tr>
            <td><b>2. stripe_size</b></td>
	    <td><b>Optional</b></td>
            <td>stripe size for disks  </td>
            <td><b>Default is 64K</b></td>
        </tr>
	<tr>
            <td rowspan=1><b>powervs_swap_creation</b><br /></td>
            <td><b>swap_disk_wwn</b></td>
	    <td><b>Mandatory</b></td>
            <td>wwn id of swap disk</td>
	    <td><b>wwn value</b></td>
        </tr>
    </tbody>
</table>

## Roles Description
### 1. Preparing Operating System for SAP installations

This module is different for **SLES and RHEL** and hence should be selected as per operating system in use.
 
#### 1.1 powervs_prepare_sles_sap: 

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **TSO** is enabled for SAP network interfaces
- **SAPTUNE SOLUTION** for **HANA or NETWEAVER or NETWEAVER+HANA** is applied based on parameter passed.
- **Activates SUSE subscription**

All settings applied remain persistent across reboot.

#### 1.2 powervs_prepare_rhel_sap:

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **TSO** is enabled for SAP network interfaces
- **Activates RHEL subscription**

This role is followed by execution of following community roles
- **[sap-preconfigure](https://github.com/linux-system-roles/sap-preconfigure)** 
- **[sap-hana-preconfigure](https://github.com/linux-system-roles/sap-hana-preconfigure)**
- **[sap-netweaver-preconfigure](https://github.com/linux-system-roles/sap-netweaver-preconfigure)** 

All settings applied remain persistent across reboot.

**Note:** 
Ansible playbook may report **Failure/Warning**, if scripts analyse reboot is required for settings applied by it. User should reboot their LPAR, in that case. 



### 2. Creating Filesystems for SAP installations

This module is same for both SLES and RHEL.

#### 2.1 powervs_fs_creation:

This role performs the following tasks:
- **Creates filesystems** with user defined **stripe size** using ansible **built-in** LVM logical volumes modules.
- **Mounts** the filesystems on provided **mount points**
- **Adds an entry to /etc/fstab** for **automount** on reboot.
- **Optional** :Converts the input data structure from **terraform to a general data structure** (Terraform output support)

A separate **task** called **terraform-wrapper.yml** is used to handle the variable values passed **from terraform output** to execute this role, via **terraform**.

The input variable **disks_configuration** for this role supports 2 data structures. Only then terraform-wrapper.yml will convert the terraform data structure in **example A** to normal data structure in **example B** below. 

**Example A**.**Terraform** Data structure for **disks_configuration** variable value example:
```
disks_configuration: 
{
counts: [2,2,1], 
names: [data,log,shared], 
paths: [/hana/data,/hana/log,/hana/shared], 
wwns: [600507681082018bc8000000000057e4,600507681082018bc8000000000057e8,600507681082018bc8000000000057e5,600507681082018bc8000000000057e6,600507681082018bc8000000000057e7]}
}
```

**Example B**. Data structure for **disks_configuration** variable value example:
```
disks_configuration: [
{
name: data, 
path: /hana/data, 
wwns: 600507681082018bc8000000000057e4,600507681082018bc8000000000057e8
},
{
name: log, 
path: /hana/log, 
wwns: 600507681082018bc8000000000057d9,600507681082018bc8000000000057ed7
},
{
name: shared, 
path: /hana/shared, 
wwns: 600507681082018bc8000000000057f1
}
.
.
.
]
```


### 3. Configuring SWAP spaces

This module configures swap space on LPAR, and is same for both RHEL and SLES.

#### 3.1 powervs_swap_creation

This role performs the following tasks:
- Removes previous swap device configured
- Creates a new swap device on disk provided with swap_disk_wwn variable. 

#### Note:
For RHEL, **swap disk of size >= 24GB** is required for community role **[sap-netweaver-preconfigure](https://github.com/linux-system-roles/sap-netweaver-preconfigure)** to succeed. 


### 4. Installation Guide

Install [git repo](https://github.ibm.com/SAP-Automation/ansible-PowerVS-config) from Ansible Galaxy using below command

```ansible-galaxy collection install ibm.power_linux_sap```
   
   
After git repo is available on LPAR, requirements.yml file present in repo, need to be used to get other roles which are not part of this git repository, but are needed for complete SAP solution. Below command should be used. 

```ansible-galaxy install -r requirements.yml```

These community roles are needed, as they configure RHEL LPAR as required for SAP HANA or NetWeaver for Power Systems according to SAP Note [2772999](https://launchpad.support.sap.com/#/notes/2772999).


### Execution Details

Sample Ansible Playbook Execution

Local Host Execution

```ansible-playbook --connection=local -i "localhost," playbook-sles.yml -e "<Variable>"```

Target Host Execution

```ansible-playbook -i "<target-host>" playbook-sles.yml -e "<Variable>"```



### Execution examples

1. To run only **powervs_prepare_sles_sap** role without SUSE subscription variable, 

```
ansible-playbook --connection=local -i "localhost," playbook-sles.yml -e '{sap_solution: "HANA", host_ip: "192.168.1.1" }'
```

2. To run only **powervs_prepare_rhel_sap** role with RHEL Subscription variable, 

```
ansible-playbook --connection=local -i "localhost," playbook-rhel.yml -e '{sap_solution: "NETWEAVER", sap_domain: xyz.com, rhel_subscription: { username: "XYZ",password: "ABC", release: "8.2"}, host_ip: "192.168.1.1"}}'
```

3. To run only **powervs_fs_creation** role to create filesystems using **data structure example A** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," playbook-sles.yml -e '{ disks_configuration: {counts:[8,8,1,1], names:[data,log,shared,usrsap], paths:[/hana/data,/hana/log,/hana/shared,/usr/sap], wwns:[6005076810810261F800000000004094,6005076810810261F800000000004096,6005076810810261F80000000000409D,6005076810810261F8000000000040A3,6005076810810261F80000000000409A,6005076810810261F8000000000040A0,6005076810810261F8000000000040A4,6005076810810261F800000000004097,6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093,6005076810810261F80000000000409C,6005076810810261F800000000004099]}
```

4. To run only **powervs_fs_creation** role to create filesystems using **data structure example B** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," playbook-sles.yml -e '{disks_configuration: [{ name: log, path: /hana/log, wwns: 6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093},{ name: shared, path: /hana/shared, wwns: 6005076810810261F80000000000409C},{ name: usrsap, path: /usr/sap, wwns: 6005076810810261F800000000004099}]}'
```

5. To run only **powervs_swap_creation** role:
```
ansible-playbook --connection=local -i "localhost," playbook-sles.yml -e '{swap_disk_wwn: 6005076810810261F80000000000409H}'
```

6. To run all roles for **RHEL( powervs_prepare_rhel_sap, powervs_fs_creation and powervs_swap_creation)** using **data structure example A** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," playbook-rhel.yml -e '{sap_solution: "NETWEAVER", sap_domain: xyz.com, rhel_subscription: { username: "XYZ",password: "ABC", release: "8.2"}, host_ip: "192.168.1.1", disks_configuration: {counts:[8,8,1,1], names:[data,log,shared,usrsap], paths:[/hana/data,/hana/log,/hana/shared,/usr/sap], wwns:[6005076810810261F800000000004094,6005076810810261F800000000004096,6005076810810261F80000000000409D,6005076810810261F8000000000040A3,6005076810810261F80000000000409A,6005076810810261F8000000000040A0,6005076810810261F8000000000040A4,6005076810810261F800000000004097,6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093,6005076810810261F80000000000409C,6005076810810261F800000000004099], swap_disk_wwn: 6005076810810261F80000000000409H}'
```

7. To run all roles for **SLES( powervs_prepare_sles_sap, powervs_fs_creation and powervs_swap_creation)** using **data structure example B** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," playbook-sles.yml -e '{ sap_solution: "NETWEAVER", host_ip: "192.168.1.1", suse_subscription: { username: "XYZ", key: "ABC", release: "15"},  disks_configuration: [{ name: log, path: /hana/log, wwns:   6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093},{ name: shared, path: /hana/shared, wwns: 6005076810810261F80000000000409C},{ name: usrsap, path: /usr/sap, wwns: 6005076810810261F800000000004099}], swap_disk_wwn: 6005076810810261F80000000000409H }'
```

Similarly, only RHEL modules can be executed by changing playbook name to playbook-rhel.yml, which is part of this collection.

### Requirements, Dependencies and Testing


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

- RHEL 8.2 for SAP
- SLES 15 for SAP
- SLES 12 for SAP

## License

- [Apache 2.0](./LICENSE)

## Contributors

Contributors to the Ansible Roles within this Ansible Collection, are shown within [/docs/contributors](./docs/CONTRIBUTORS.md).
