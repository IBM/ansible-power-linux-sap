# Introduction details

This Ansible role will add or delete one SAP monitoring configuration on a IBM Cloud Virtual Server
to monitor one specific SAP System running on IBM Cloud Power Workspace.
HA SAP Systems are not supported yet.

Upto 99 permanently activated SAP monitoring configurations are supported to monitor 99 different SAP Systems.
This module supports SLES only. RHEL does not supply necessary packages.
The Ansible role does not cover the creation of a IBM Cloud Monitoring Instance,nor the changes on the SAP Systems.

For each SAP System this Ansible playbook has to be executed with a different
configuration file `playbooks/vars/sample-sap-monitoring-parameters.yml` that determines the SAP host parameters, IBM monitoring instance and action parameters.
The configuration file controls which Ansible tasks will be taken,
whether to add or delete a monitoring configuration.
A monitoring configuration will be active permanently by enabled daemons.
Upto 99 different SAP Systems can be monitored.

Each monitoring configuration must use a different `sap_monitoring_nr` in `playbooks/vars/sample-sap-monitoring-parameters.yml`,
The `sap_monitoring_nr` will be part of the configuration filenames and process names.
The variables in `playbooks/vars/sample-sap-monitoring-parameters.yml` will be used in the
prometheus exporter configuration files to collect metrics from the specific SAP System.
There is a maximum of 99 different `sap_monitoring_nr`.

Each Ansible execution with a different `sap_monitoring_nr` adds another monitoring configuration collecting metrics from a different SAP System.
All metrics are forwarded to your IBM monitoring instance, this instance is not accessible by other customers.
In the IBM monitoring instance you can create Dashboards for HANA DB and SAP Services.

All parameter variables are in one configuration file `playbooks/vars/sample-sap-monitoring-parameters.yml`.
You can add or delete a SAP monitoring configuration by setting the variable sap_monitoring_action to  "`add`" or "`delete`".

The variable "`config_override`" controls whether to overwrite exiting configurations with the same `sap_monitoring_nr`  (set to "true")
or to protect from overwriting (set to "false").

You may keep track of these `sap_monitoring_nr` and corresponding SAP Systems,
on the command line it is always possible to check how many configurations are running by
`ps aux|grep  -E "prom|hana|sap"`

A successful configuration will forward the SAP metrics to your IBM monitoring instance
where you can add Dashboards for Views on HANA DB and SAP Services.


Adding a monitoring configuration requires all SAP parameters,

Deleting a SAP monitoring configuration only requires the `sap_monitoring_nr` in the configuration file (all other parameters will not be checked) to

* Stop and disable all daemons of prometheus-agent, hanadb-exporter and all sap-host-exporters
* Delete all configuration files of the prometheus-agent, hanadb-exporter and all sap-host-exporters
