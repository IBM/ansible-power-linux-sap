# Sections

1. [Introduction](#introduction)
2. [Role Description](#role-description)
3. [Role Dependencies](#role-dependencies)
4. [Role Variables](#role-variables)
5. [Installation Guide](#installation-guide)
6. [Gather SAP Parameters](#gather-sap-parameters)
7. [Troubleshooting](#troubleshooting-monitoring)

# Introduction

This ansible role is configuring or deleting one SAP monitoring on a IBM Cloud Virtual Server. <br>
A maximum of 99 SAP monitoring configurations can be deployed targeting 99 different SAP Systems located in the same security group. Monitoring HA SAP Systems is not covered in this release.<br>
Each deployment has to use a different `<sap_monitoring_nr>` in the configuration file `playbooks/vars/sample-variables-monitoring-sap-parameters.yml` <br>
The Ansible role is only a part of the monitoring workflow model as described in [docs/Introduction-details.md](docs/Introduction-details.md).

# Role Description

This role requires some prerequisites that are not covered by the Ansible module: <br>
A IBM Cloud monitoring instance, SAP DB user with ReadOnly permissions, VSI host with a specific SLES SAP applications image, the SAP-HANA-Client as SAR-file and SAPCAR utility in the host directory `<sap_tools_directory>`

This role performs the following tasks:

- Deleting or adding the monitoring configuration for one specific SAP System

Adding a SAP monitoring configuration includes:

- Check of existing monitoring configurations with the same `<sap_monitoring_nr>`
- Installing the SAP HANA client and python3-driver if not already present
- Installing and configuring the prometheus-sap_host_exporter to collect metrics from SAP services
- Installing and configuring the prometheus-hanadb_exporter using the SAP HANA client tool hdbuserstore
  to collect metrics from HANA DB
- Installing and configuring the golang-github-prometheus-prometheus to forward metrics to the IBM Cloud monitoring instance
- Enabling and activating systemd daemons to all listed prometheus exporters and prometheus agent

Deleting a SAP monitoring configuration requires only the `<sap_monitoring_nr>` in the configuration file to execute:

- Stopping and disabling all daemons of prometheus-agent, hanadb-exporter and all sap-host-exporters
- Deleting all configuration files of the prometheus-agent, hanadb-exporter and all sap-host-exporters

# Role Dependencies

Install the below collections.


| Collection        | Version |
| ----------------- | ------- |
| community.general | 10.1.0  |

# # Role Variables

Edit the configuration file `playbooks/vars/sample-sap-monitoring-parameters.yml`.  <br>
Any additional monitoring configuration will be added with executing the same command but different variables:


| VARIABLE NAME                                   | example value                                                                       | comment                                                                                                                                     |
| ----------------------------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                 |                                                                                     |                                                                                                                                             |
| # variable group: monitoring meta parameters    |                                                                                     |                                                                                                                                             |
| sap_monitoring_nr                               | "03"                                                                                | Quotation is required.<br>Two-digit incremental number, starting with 01 up to 99. <br>This is not a existing SAP ID, but a pure virtual NR |
| sap_monitoring_solution_name                    | "PM2 6.1 (S/4 2022)"                                                                | Optional. If set, quotation is required.<br> A virtual arbitrary short name to recognize your SAP System                                    |
| sap_monitoring_action                           | add  OR delete                                                                     | This controls the Ansible action to add or delete a configuration                                                                           |
| config_override                                 | false OR true                                                                       | Boolean. Default: false.<br>if true, an existing configuration will be overwritten                                                          |
| sap_tools_directory                             | /root/sap                                                                           | Full directory path, create and copy SAP-HANA-Client into this directory                                                                    |
|                                                 |                                                                                     |                                                                                                                                             |
| # variable group: IBM cloud parameters          |                                                                                     |                                                                                                                                             |
| ibmcloud_monitoring_instance_url                | https://ingest.prws.private.br-sao.monitoring.cloud.ibm.com/prometheus/remote/write | URL of the IBM Cloud monitoring instance,<br> the keyword private is added to the URL, <br>used in prometheus agent configuration           |
| ibmcloud_monitoring_authorization_credentials   | a123-b123-c123-12345678ab1232                                                       | redentials to access the IBM Cloud monitoring instance                                                                                      |
|                                                 |                                                                                     |                                                                                                                                             |
| # variable group: hana parameters               |                                                                                     |                                                                                                                                             |
| sap_hana_ip                                     | 10.0.0.2                                                                            | IPv4 address of HANA-DB Server                                                                                                              |
| sap_hana_http_port                              | 50213                                                                               | Port of SAPStartService on HANADB Server                                                                                                    |
| sap_hana_sql_systemdb_port                      | 30213                                                                               | SQL Port of SYSTEMDB on HANADB Server                                                                                                       |
| sap_hana_sql_systemdb_user                      | monitoring-user                                                                     | SYSTEMDB-User with ReadOnly permissions                                                                                                     |
| sap_hana_sql_systemdb_password                  | password123                                                                         | Password of SYSTEMDB-User                                                                                                                   |
|                                                 |                                                                                     |                                                                                                                                             |
| # variable group: application server parameters |                                                                                     |                                                                                                                                             |
| sap_ascs_ip                                     | 10.0.0.3                                                                            | IPv4 address of first (primary) Application Server                                                                                          |
| sap_ascs_http_port                              | 50013                                                                               | HTTP Port of SAP service ASCS on Application Server                                                                                         |
| sap_app_server                                  | Example:see the following code box                                                  | One or several DI-servers  as a yaml-dictionary <br>or a JSON object list with incremental number, IPV4 and Port            |


```

# examples of the variable sap_app_server as a JSON list
sap_app_server: [{"ip":"10.0.0.4","port":"50113","sap_app_server_nr":"01"}]

# several apps servers:
sap_app_server: [ {"ip":"10.51.0.30","port":"50113","sap_app_server_nr":"01"},{"ip":"10.51.0.30","port":"50113","sap_app_server_nr":"02"} ]

# yaml-formatted:
sap_app_server:
  - sap_app_server_nr: 01
    ip: 10.0.0.4
    port: 50113
  - sap_app_server_nr: 02
    ip: 10.0.0.5
    port: 50113
  - sap_app_server_nr: 03
    ip: 10.0.0.6
    port: 50113

```

These Variables will be used by Ansible on the monitoring host in configuration files and exposed ports:


| Port exposed on the monitoring host | Service                                              |
| ----------------------------------- | ---------------------------------------------------- |
| 5<sap_monitoring_nr>01              | prometheus agent                                     |
| 5<sap_monitoring_nr>02              | hanadb_exporter                                      |
| 5<sap_monitoring_nr>03              | sap_host_exporter_HANA                               |
| 5<sap_monitoring_nr>04              | sap_host_exporter_ASCS                               |
| 5<sap_monitoring_nr>05              | sap_host_exporter_DI_01 (primary/ first APPS Server) |
| 5<sap_monitoring_nr>06              | sap_host_exporter_DI_02 (second APPS Server)         |
| 5<sap_monitoring_nr>07              | sap_host_exporter_DI_03 (third APPS Server)          |

All parameter variables are in one configuration file `playbooks/vars/sample-sap-monitoring-parameters.yml`. <br>
You can add or delete a SAP monitoring configuration by setting the variable `<sap_monitoring_action>` to  "`add`" or "`delete`".

The variable `<config_override>` controls whether to overwrite existing configurations with the same `<sap_monitoring_nr>`  (`<config_override>` set to `true`)
or to protect from overwriting (`<config_override>` set to `false`).

Adding a monitoring configuration requires all SAP parameters, <br>
deleting a SAP monitoring configuration only requires the `<sap_monitoring_nr>`, all other parameters will not be checked.

# Installation Guide

## 5.1. Prerequisites

#### 5.1.1 Create an IBM Cloud Monitoring Instance

Create an IBM Cloud Monitoring Instance with your IBM IAM account in the same region as the monitoring host and extract URL and Credentials
as described in the file [docs/HOWTO-create-IBM-Cloud-monitoring-instance.md](docs/HOWTO-create-IBM-Cloud-monitoring-instance.md)

#### 5.1.2 Changes on the SAP System:

- Create a SAP DB user with ReadOnly permissions
  as instructed in the file [docs/HOWTO-create-HANA-DB-User-ReadOnly-permissions.md](docs/HOWTO-create-HANA-DB-User-ReadOnly-permissions.md)
- Check the SAPstart services on the HANA DB host and all Application Server hosts
  and change according to the file [docs/HOWTO-configure-SAP-system-services.md](docs/HOWTO-configure-SAP-system-services.md)
  to allow HTTP-queries without user/passwords

#### 5.1.3 Create or use an existing IBM Cloud VPC VirtualServer as monitoring host

Create an IBM Cloud VPC VirtualServer as described in [docs/HOWTO-create-IBM-Cloud-VPC-VirtualServer.md](docs/HOWTO-create-IBM-Cloud-VPC-VirtualServer.md)

## 5.2 Installation steps on the monitoring host:

#### 5.2.1  Copy the SAP-HANA-Client to the monitoring host

Copy the SAP-HANA-Client to the monitoring host as SAR-file and SAPCAR utility in the host directory `<sap_tools_directory>`

Note: Make sure inside the directory `<sap_tools_directory>` there is only one SAP-HANA-Client as SAR-file and SAPCAR utility.
Not all SAP-HANA-Client downloads include the required Python-HANADB-Driver. <br>
This version is verified to deliver the required driver: IMDB_CLIENT20_020_23-80002082.SAR (version 2.01) <br>
You may try your existing version, Ansible will tell you if it is successful.

#### 5.2.2 Install packages and repositories on the command line

On the monitoring host execute these steps to activate the repository, install packages and clone the Ansible repository:

```
# Activate the PackageHub Repository
/usr/sbin/SUSEConnect -p PackageHub/15.5/x86_64

# Update all packages
zypper update

# Restart the monitoring host
shutdown -r now

# after the restart:
# Install the packages ansible and git-core

zypper install ansible git-core

mkdir <sap_tools_directory>

# Copy the SAP-HANA-Client SAR-File and SAPCAR-XXX.exe to <sap_tools_directory>

# Clone the ansible-power-linux-sap repository from github
git clone https://github.com/IBM/ansible-power-linux-sap.git
cd ansible-power-linux-sap
```

#### 5.2.3 Edit the monitoring configuration file

Edit the configuration file `playbooks/vars/sample-variables-monitoring-sap-parameters.yml`
with your variables as described in chapter 2. <br>
This document  [docs/HOWTO-gather-SAP-parameters.md](docs/HOWTO-gather-SAP-parameters.md) describes how to gather these SAP parameters.

#### 5.2.4 Execute the Ansible playbook

Ansible actions are controlled by the configuration file `playbooks/vars/sample-variables-monitoring-sap-parameters.yml`, <br>
no changes are required in the file `playbooks/sample-monitoring-sap.yml`. <br>
Each execution of the Ansible playbook will add or delete a monitoring configuration: <br>
`ansible-playbook --connection=local -i "localhost," playbooks/sample-monitoring-sap.yml`

## 5.3.  Post installation steps

Create, duplicate and edit SAP Dashboards in the IBM Cloud Monitoring Instance to visualize SAP metrics as
explained in the file [docs/HOWTO-SAP-Dashboards.md](docs/HOWTO-SAP-Dashboards.md)

# Gather SAP parameters

Each monitoring configuration requires the parameters of your targeted SAP System

in the configuration file `playbooks/vars/sample-variables-monitoring-sap-parameters.yml`. <br>

This document  [docs/HOWTO-gather-SAP-parameters.md](docs/HOWTO-gather-SAP-parameters.md) describes how to gather these SAP parameters.

# Troubleshooting monitoring

The Ansible run may fail. <br>
This document [docs/troubleshooting-monitoring.md](docs/troubleshooting-monitoring.md) is listing several reasons for failing and how to scrutinize the causes.

To list existing monitoring configurations execute:
`ps aux|grep  -E "prom|hana|sap"`
