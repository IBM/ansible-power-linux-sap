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
4)	**Installing Services**

### Ansible Roles Summary

<table>
    <thead>
        <tr>
            <th>Role Name</th>
            <th>Input Variable Name</th>
		<th>Mandatory or Optional</th>
			<th>Variable description</th>
			<th>Variable Values</th>
        </tr>
    </thead>
    <tbody>
        <tr>
		<td rowspan=3><b><a href="./roles/powervs_prepare_sles_sap">powervs_prepare_sles_sap</a></b><br /></td>
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
            <td><b>3. suse_subscription : { <br />username: "",<br />key: "", <br />release: "" <br />}</td>
  	    <td><b>Optional</b></td>
            <td>SUSE subscription information. It is a dictionary. Should be set only if subscription is not already set or subscription has to be updated</td>
            <td>e.g.: { <br />username: "XYZ",<br />key: "ABC" ,<br />release: "12"<br />}</td>
        </tr>
         <tr>
		 <td rowspan=4><b><a href="./roles/powervs_prepare_rhel_sap">powervs_prepare_rhel_sap</a></b><br /></td>
            <td rowspan=1><b>1. sap_solution</b></td>
	    <td><b>Mandatory</b></td>
            <td rowspan=1>RHEL community roles for HANA or NETWEAVER will be executed</td>
            <td rowspan=1><b>HANA or NETWEAVER</b></td>
        </tr>
        <tr>
            <td><b>2. host_ip</b></td>
    	    <td><b>Optional</b></td>
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
            <td><b>4. rhel_subscription : { <br />username: "",<br />password: "" ,<br />release: ""<br />}</td>
  	    <td><b>Optional</b></td>
            <td>RHEL subscription information. It is a dictionary. Should be set only if subscription is not already set or subscription has to be updated</td>
            <td>e.g.: { <br />username: "XYZ",<br />password: "ABC" ,<br />release: "8.2"<br />}</td>
        </tr>
		<tr>
         <td rowspan=2><b><a href="./roles/powervs_fs_creation">powervs_fs_creation</a></b><br /></td>
            <td rowspan=1><b>1.a. disks_configuration: { counts: [ ], names: [ ], paths: [ ], wwns: [ ] }<br />1.b. disks_configuration: [ { name: "", path: "", wwns: ""}...]</b></td>
 	    <td><b>Mandatory</b></td>
            <td>Disks configuration value to create and mount filesystems. Supports 2 data structures. First data structure is a single dictionary. Second data structure is a list of dictionaries.</td>
            <td rowspan=1>see <b><a href="README.md#example-a-data-structure-for-disks_configuration-variable-as-dictionary-value-example">example A </a></b> and <b><a href="README.md#example-b-data-structure-for-disks_configuration-variable-as-list-value-example">example B</a></b> below</td>
        </tr>
	<tr>
            <td><b>2. stripe_size</b></td>
	    <td><b>Optional</b></td>
            <td>stripe size for disks  </td>
            <td><b>Default is 64K</b></td>
        </tr>
	<tr>
		<td rowspan=1><b><a href="./roles/powervs_swap_creation">powervs_swap_creation</a></b><br /></td>
            <td><b>swap_disk_wwn</b></td>
	    <td><b>Mandatory</b></td>
            <td>wwn id of swap disk</td>
	    <td><b>wwn value</b></td>
        </tr>
	<tr>
		<td rowspan=3><b><a href="./roles/powervs_install_services">powervs_install_services</a></b><br /></td>
            <td rowspan=1><b>1. dns_servers</b></td>
	    <td><b>Optional</b></td>
            <td rowspan=1>dns servers IPs to be configured, ;(semicolon) separated. Please note values should end with a semicolon too.</td>
            <td rowspan=1><b>e.g.: "161.26.0.7; 161.26.0.8; 9.9.9.9;"</b></td>
        </tr>
        <tr>
            <td><b>2. nfs_directory</b></td>
	    <td><b>Optional</b></td>
            <td>One or more directories name to be exported as mountable, ;(semicolon) separated</td>
            <td>e.g.: "/NFS; /hana/software"</td>
        </tr>
	<tr>
            <td><b>3. enable</b></td>
	    <td><b>Mandatory</b></td>
            <td>For each service to be enabled. Default is False</td>
            <td>True or False</td>
	    </tr>
    </tbody>
</table>

***

# 2. Roles Description
### 2.1. Preparing Operating System for SAP installations

This module is different for **SLES and RHEL** and hence should be selected as per operating system in use.
 
#### 2.1.1 powervs_prepare_sles_sap: 

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **TSO** is enabled for SAP network interfaces
- **SAPTUNE SOLUTION** for **HANA or NETWEAVER or NETWEAVER+HANA** is applied based on parameter passed. **Only Saptune v3 is supported**.
- **Activates SUSE subscription**

   All settings applied remain persistent across reboot.

#### 2.1.2 powervs_prepare_rhel_sap:

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **Activates RHEL subscription**

This role is followed by execution of following community roles
- **[sap_general_preconfigure](https://github.com/sap-linuxlab/community.sap_install/tree/main/roles/sap_general_preconfigure)** 
- **[sap_hana_preconfigure](https://github.com/sap-linuxlab/community.sap_install/tree/main/roles/sap_hana_preconfigure)**
- **[sap_netweaver_preconfigure](https://github.com/sap-linuxlab/community.sap_install/tree/main/roles/sap_netweaver_preconfigure)** 

These roles are part of ansible galaxy collection **[community.sap_install](https://galaxy.ansible.com/community/sap_install)**

All settings applied remain persistent across reboot.

**Note:** 
Ansible playbook may report **Failure/Warning**, if scripts analyse reboot is required for settings applied by it. User should reboot their LPAR, in that case. 



### 2.2. Creating Filesystems for SAP installations

This module is same for both SLES and RHEL.

#### powervs_fs_creation

This role performs the following tasks:
- **Creates filesystems** with user defined **stripe size** using ansible **built-in** LVM logical volumes modules.
- **Mounts** the filesystems on provided **mount points**
- **Adds an entry to /etc/fstab** for **automount** on reboot.
- **Optional** : Converts the input data structure of disks_configuration variable from **dictionary to a general data structure**.

A separate **task** called **disks-dict2list.yml** is used to handle disks_configuration variable values passed **as dictionary** to execute this role.

The input variable **disks_configuration** for this role supports 2 data structures. Only then disks_configuration will convert the disks_configuration variable data structure in **example A** to general data structure in **example B** below.

#### **Example A**. Data structure for **disks_configuration** variable as **dictionary** value example:
```
disks_configuration: 
{
counts: [2,2,1], 
names: [data,log,shared], 
paths: [/hana/data,/hana/log,/hana/shared], 
wwns: [600507681082018bc8000000000057e4,600507681082018bc8000000000057e8,600507681082018bc8000000000057e5,600507681082018bc8000000000057e6,600507681082018bc8000000000057e7]
}
```

#### **Example B**. Data structure for **disks_configuration** variable as **list** value example:
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


### 2.3. Configuring SWAP spaces

This module configures swap space on LPAR, and is same for both RHEL and SLES.

#### powervs_swap_creation

This role performs the following tasks:
- Removes previous swap device configured
- Creates a new swap device on disk provided with swap_disk_wwn variable. 

#### Note:
For RHEL, **swap disk of size >= 24GB** is required for community role **[sap-netweaver-preconfigure](https://github.com/linux-system-roles/sap-netweaver-preconfigure)** to succeed. 


### 2.4. Installing Services

This module is same for both SLES and RHEL.

This role performs the following tasks:
- Installs **SQUID** package, and configures squid.conf file as described in this link https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux.
- Installs **DNS** packages and configure DNS server based on the **dns_servers** input provided.
- Installs **NTP** packages and updates named.conf file as per the requirement.
- Installs **NFS** server packages, creates NFS directories as provided in input and also exports them as mountable directories.

This role will **start and enable** all above mentioned services.

Additionally it will also install **awscli** package. awscli should be configured manually later.

The input variable **server_config** is needed to be provided for this role to be executed. The variable file is defined as below
```
server_config: {
squid: { enable: false },
ntp: { enable: false },
nfs: { enable: true, nfs_directory: "/NFS; /hana/software" },
dns: { enable: false, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" },
awscli: { enable: false }
}
```

Each service can be chosen to be enabled or not. Disabling is not supported. This variable file enables users, to enable one or many services on one or multiple servers, as desired.

For NFS services, additional variable **nfs_directory** need to be provided. Directories name provided will be created, if not already present and are exported as a mountable directory.

For DNS services, additional variable **dns_servers** is required. These are user-defined DNS servers IPs. Please note, **;** as a separator, in example.

***

# 3. Installation Guide

Install **[collection](https://galaxy.ansible.com/ibm/power_linux_sap)** from Ansible Galaxy using below command

```ansible-galaxy collection install ibm.power_linux_sap```

A folder will be created in root directory : ```/root/.ansible/collections/ansible_collections/ibm/power_linux_sap/```
   
After collection is installed on LPAR, requirements.yml file will be available in location ```/root/.ansible/collections/ansible_collections/ibm/power_linux_sap/requirements.yml```, needs to be used to get other roles/collections which are not part of this collection, but are needed for complete SAP solution. Below command should be used. 

```ansible-galaxy collection install -r requirements.yml```

These community roles are needed, as they configure RHEL LPAR as required for SAP HANA or NetWeaver for Power Systems according to SAP Note [2772999](https://launchpad.support.sap.com/#/notes/2772999).

***

# 4. Execution Details

To execute playbook, **cd** to playbooks directory of collection. 

Sample Ansible Playbook Execution

Local Host Execution for SLES

```ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e "<Variable>"```

Target Host Execution for SLES

```ansible-playbook -i "<target-host>," powervs-sles.yml -e "<Variable>"```

Local Host Execution for RHEL

```ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e "<Variable>"```

Target Host Execution for RHEL

```ansible-playbook -i "<target-host>," powervs-rhel.yml -e "<Variable>"```



### 4.1 Execution examples for SLES

1. To run only **powervs_prepare_sles_sap** role without SUSE subscription variable, 

```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e '{sap_solution: "HANA", host_ip: "192.168.1.1" }'
```

2. To run only **powervs_prepare_sles_sap** role with SUSE subscription variable, 

```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e '{sap_solution: "HANA", host_ip: "192.168.1.1", suse_subscription: { username: "XYZ", key: "ABC", release: "15"} }'
```

3. To run only **powervs_fs_creation** role to create filesystems using **data structure example A** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e '{ disks_configuration: {counts:[8,8,1,1], names:[data,log,shared,usrsap], paths:[/hana/data,/hana/log,/hana/shared,/usr/sap], wwns:[6005076810810261F800000000004094,6005076810810261F800000000004096,6005076810810261F80000000000409D,6005076810810261F8000000000040A3,6005076810810261F80000000000409A,6005076810810261F8000000000040A0,6005076810810261F8000000000040A4,6005076810810261F800000000004097,6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093,6005076810810261F80000000000409C,6005076810810261F800000000004099] } }'
```

4. To run only **powervs_fs_creation** role to create filesystems using **data structure example B** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e '{disks_configuration: [{ name: log, path: /hana/log, wwns: 6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093},{ name: shared, path: /hana/shared, wwns: 6005076810810261F80000000000409C},{ name: usrsap, path: /usr/sap, wwns: 6005076810810261F800000000004099}]}'
```

5. To run only **powervs_swap_creation** role:
```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e '{swap_disk_wwn: 6005076810810261F80000000000409H}'
```

6. To run all roles **powervs_prepare_sles_sap, powervs_fs_creation and powervs_swap_creation** using **data structure example B** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e '{ sap_solution: "NETWEAVER", host_ip: "192.168.1.1", suse_subscription: { username: "XYZ", key: "ABC", release: "15"},  disks_configuration: [{ name: log, path: /hana/log, wwns:   6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093},{ name: shared, path: /hana/shared, wwns: 6005076810810261F80000000000409C},{ name: usrsap, path: /usr/sap, wwns: 6005076810810261F800000000004099}], swap_disk_wwn: 6005076810810261F80000000000409H }'
```

7. To run all roles **powervs_prepare_sles_sap, powervs_fs_creation and powervs_swap_creation** using **variable file sample_sles_variable_file.yml** inside directory playbooks/vars.

```
ansible-playbook --connection=local -i "localhost," powervs-sles.yml -e @vars/sample_sles_variable_file.yml
```

### 4.2 Execution examples for RHEL

1. To run only **powervs_prepare_rhel_sap** role without RHEL Subscription variable, 

```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e '{sap_solution: "NETWEAVER", host_ip: "192.168.1.1" }'
```

2. To run only **powervs_prepare_rhel_sap** role with RHEL Subscription variable, 

```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e '{sap_solution: "NETWEAVER", sap_domain: xyz.com, rhel_subscription: { username: "XYZ",password: "ABC", release: "8.2"}, host_ip: "192.168.1.1" }'
```

3. To run only **powervs_fs_creation** role to create filesystems using **data structure example A** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e '{ disks_configuration: {counts:[8,8,1,1], names:[data,log,shared,usrsap], paths:[/hana/data,/hana/log,/hana/shared,/usr/sap], wwns:[6005076810810261F800000000004094,6005076810810261F800000000004096,6005076810810261F80000000000409D,6005076810810261F8000000000040A3,6005076810810261F80000000000409A,6005076810810261F8000000000040A0,6005076810810261F8000000000040A4,6005076810810261F800000000004097,6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093,6005076810810261F80000000000409C,6005076810810261F800000000004099] } }'
```

4. To run only **powervs_fs_creation** role to create filesystems using **data structure example B** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e '{disks_configuration: [{ name: log, path: /hana/log, wwns: 6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093},{ name: shared, path: /hana/shared, wwns: 6005076810810261F80000000000409C},{ name: usrsap, path: /usr/sap, wwns: 6005076810810261F800000000004099}] }'
```

5. To run only **powervs_swap_creation** role:
```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e '{swap_disk_wwn: 6005076810810261F80000000000409H}'
```

6. To run all roles **powervs_prepare_rhel_sap, powervs_fs_creation and powervs_swap_creation** using **data structure example A** above for disks_configuration:

```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e '{sap_solution: "NETWEAVER", sap_domain: xyz.com, rhel_subscription: { username: "XYZ",password: "ABC", release: "8.2"}, host_ip: "192.168.1.1", disks_configuration: {counts:[8,8,1,1], names:[data,log,shared,usrsap], paths:[/hana/data,/hana/log,/hana/shared,/usr/sap], wwns:[6005076810810261F800000000004094,6005076810810261F800000000004096,6005076810810261F80000000000409D,6005076810810261F8000000000040A3,6005076810810261F80000000000409A,6005076810810261F8000000000040A0,6005076810810261F8000000000040A4,6005076810810261F800000000004097,6005076810810261F800000000004098,6005076810810261F80000000000409E,6005076810810261F80000000000409B,6005076810810261F80000000000409F,6005076810810261F8000000000040A2,6005076810810261F8000000000040A1,6005076810810261F800000000004095,6005076810810261F800000000004093,6005076810810261F80000000000409C,6005076810810261F800000000004099], swap_disk_wwn: 6005076810810261F80000000000409H} }'
```

7. To run all roles **powervs_prepare_rhel_sap, powervs_fs_creation and powervs_swap_creation** using **variable file sample_rhel_variable_file.yml** inside directory playbooks/vars.

```
ansible-playbook --connection=local -i "localhost," powervs-rhel.yml -e @vars/sample_rhel_variable_file.yml
```

### 4.3. Installing services. 


1. To run **powervs_install_services** role, to configure all services on one server, using **variable file sample_services_variable_file.yml** inside directory playbooks/vars. Variable file should be modified like below:
```
server_config: {
squid: { enable: true },
ntp: { enable: true },
nfs: { enable: true, nfs_directory: "/NFS; /hana/software" },
dns: { enable: true, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" },
awscli: { enable: true }
}
```
```
ansible-playbook --connection=local -i "localhost," powervs-services.yml -e @vars/sample_services_variable_file.yml

```

2. To run **powervs_install_services** role, to configure squid service only, using **variable file sample_services_variable_file.yml** inside directory playbooks/vars. Variable file should be modified like below:
```
server_config: {
squid: { enable: true },
ntp: { enable: false },
nfs: { enable: false, nfs_directory: "/NFS; /hana/software" },
dns: { enable: false, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" },
awscli: { enable: false }
}
```
For localhost execution:
```
ansible-playbook --connection=local -i "localhost," powervs-services.yml -e @vars/sample_services_variable_file.yml

```

For remote host ( host on which service has to be enabled ) execution:
```
ansible-playbook -i "remote_host_name," powervs-services.yml -e @vars/sample_services_variable_file.yml

```
***

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

- RHEL 8.2 for SAP
- SLES 15 for SAP
- SLES 12 for SAP

## License

- [Apache 2.0](./LICENSE)

## Contributors

Contributors to the Ansible Roles within this Ansible Collection, are shown within [/docs/contributors](./docs/CONTRIBUTORS.md).
