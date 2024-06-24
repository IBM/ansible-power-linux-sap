## Role: monitoring_sap

Configuring SAP monitoring for upto 99 different SAP Systems

This module supports SLES only. RHEL does not supply necessary packages.

This role requires some prerequisites that are not covered by the Ansible module:

* a IBM Cloud monitoring instance

- SAP DB user with ReadOnly permissions
- VSI host with a specific SLES SAP applications image
- SAP-HANA-Client as SAR-file and SAPCAR utility in the host directory <sap_tools_directory>
- when running on localhost: activate Repo and install Ansible and Git (not required when executing from a remote host)
- git clone https://github.com/IBM/ansible-power-linux-sap.git
- configuration file playbooks/vars/sample-monitoring-sap-parameters.yml with SAP and monitoring parameters

This role performs the following tasks:

- deleting or adding the monitoring configuration for one specific SAP System
  Upto 99 different SAP Systems can be configured using a different <sap_monitoring_nr> for each configuration.Monitoring HA SAP Systems is not covered in this release.

Adding a SAP monitoring configuration includes:

- checks if monitoring configurations already exist with the same <sap_monitoring_nr>
- checks all required variables in the config file causing Ansible to fail if missing any
- checks the modus config_override (true or false), if existing configurations shall be overwritten
- installs the SAP HANA client only if not already present to /usr/sap/hdbclient
- installs the package python3-pip
- installs the SAP HANA python driver using pip
- checks on minimum OS version SLES15.5, python version 3.6 due to compatibility to prometheus
- checks connectivity to HANA DB and SAP services using netcat causing Ansible to fail if any ports are not reachable by netcat
- activates the OS repositories that deliver the prometheus packages
- configures prometheus-sap_host_exporter

  - Installs prometheus-sap_host_exporter
  - creates configuration files /etc/sap_host_exporter/sap_host_exporter-{{ sap_monitoring_nr }}-
  - HANA + ASCS + DI(n).yaml  from the template file
  - with dynamic values from the variables
  - extracting metrics from SAP services HANA, ASCS and all DI-service
  - create the systemd file  /etc/systemd/system/sap_host_exporter@.service from the template to make use of the < sap_monitoring_nr> in systemd to allow multiple daemons on sap_host_exporter
  - reload systemd daemon
  - start all sap-host-exporter daemons, the systemd daemon file comes with the prometheus exporter package
- configures prometheus-hanadb_exporter
  - installs the package prometheus-hanadb_exporter
  - copies metrics.json from exporter-package to /etc/hanadb_exporter
  - creates a sap-hana-keystore for the specific <sap_monitoring_nr> using the SAP HANA client tool hdbuserstore with the HANA-DB readonly-user
  - checks the sap-hana-keystore
  - creates the hanadb_exporter config file /etc/hanadb_exporter/config-{{ sap_monitoring_nr }}-SQL.json with dynamic values from the variables and the recently created key file
  - starts the systemd daemon of hanadb_exporter
- configures the prometheus agent
  - installs the package golang-github-prometheus-prometheus
  - creates and sets permissions to prometheus agent directory /opt/prometheus
  - creates a systemd onfig file /etc/systemd/system/prometheus@.service from the template with dynamic values to allow multiple prometheus agents
  - reload systemd daemon
  - sets permissions to the  directory /etc/prometheus/
  - creates the prometheus agent configuration file /etc/prometheus/{{ sap_monitoring_nr }}.yml from the template with dynamic values
  the prometheus agent collects all metrics from the hanadb-exporter and all sap-host-exporters and forwards all metrics to the IBM monitoring instance

## Introduction

Each exporter uses a different port number to expose the metrics,
each configuration file and systemd daemon includes the <sap_monitoring_nr>.

Each Ansible run with a different <sap_monitoring_nr> adds another monitoring configuration collecting metrics from a different SAP System.
All metrics are forwarded to your IBM monitoring instance, this instance is not accessible by other customers.
In the IBM monitoring instance you can create Dashboards for HANA DB and SAP Services.

All parameter varaibles are in one configuration file  playbooks/vars/sample-sap-monitoring-parameters.yml.
You can add or delete a SAP monitoring configuration by setting the variable sap_monitoring_action to  "add" or "delete".

The variable "config_override" controls whether to overwrite exiting configurations with the same <sap_monitoring_nr>  (set to "true")
or to protect from overwriting (set to "false").

With each different SAP System to monitor you have to use a different <sap_monitoring_nr> and the specific SAP parameters.
You may keep track of these <sap_monitoring_nr> and corresponding SAP Systems,
on the command line it is always possible to check how many configurations are running by
ps aux|grep  -E "prom|hana|sap"

Adding a monitoring configuration requires all SAP parameters,
deleting a SAP monitoring configuration only requires the <sap_monitoring_nr>, all other parameters will not be checked.



## Short overview



This documentation covers how to configure SAP monitoring
to monitor multiple Power SAP Systems on a Web Dashboard.
Supported are multiple SAP Systems, but not yet HA SAP Systems.

Monitoring configuration prerequirements:

• Create an IBM Cloud Monitoring instance
• Collect SAP parameters of your SAP System
Each SAP System monitoring requires the whole configuration setup
Each SAP-SYSTEM to monitor will be addressed by a unique ${SAP_SYSTEM_NR}
Using these SAP parameters on an additional VPC VirtualServer

• Setup or use a VPC VirtualServer with the SLES image
"SUSE Linux Enterprise Server 15 SP5 for SAP Applications amd64"
