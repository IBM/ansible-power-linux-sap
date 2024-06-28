## Role: monitoring_sap

Configuring SAP monitoring for upto 99 different SAP Systems.

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

- Deleting or adding the monitoring configuration for one specific SAP System
  Upto 99 different SAP Systems can be configured using a different <sap_monitoring_nr> for each configuration.Monitoring HA SAP Systems is not covered in this release.

Adding a SAP monitoring configuration includes:

- Checks if monitoring configurations already exist with the same <sap_monitoring_nr>
- Checks all required variables in the config file causing Ansible to fail if missing any
- Checks the modus config_override (true or false), if existing configurations shall be overwritten
- Installs the SAP HANA client only if not already present to /usr/sap/hdbclient
- Installs the package python3-pip
- Installs the SAP HANA python driver using pip
- Checks on minimum OS version SLES15.5, python version 3.6 due to compatibility to prometheus
- Checks connectivity to HANA DB and SAP services using netcat causing Ansible to fail if any ports are not reachable by netcat
- Activates the OS repositories that deliver the prometheus packages
- Configures prometheus-sap_host_exporter

  - Installs the package prometheus-sap_host_exporter
  - Creates configuration files /etc/sap_host_exporter/sap_host_exporter-{{ sap_monitoring_nr }}-HANA + ASCS + DI(n).yaml  from the template file with dynamic values from the variables
  - Extracts metrics from SAP services HANA, ASCS and all DI-services
  - Creates the systemd file  /etc/systemd/system/sap_host_exporter@.service from the template to make use of the < sap_monitoring_nr> in systemd to allow multiple daemons on sap_host_exporter
  - Reloads systemd daemon
  - Starts all sap-host-exporter daemons, the systemd daemon file comes with the package prometheus-sap_host_exporter
- Configures prometheus-hanadb_exporter

  - Installs the package prometheus-hanadb_exporter
  - Copies metrics.json from exporter-package to /etc/hanadb_exporter
  - Creates a sap-hana-keystore for the specific <sap_monitoring_nr> using the SAP HANA client tool hdbuserstore with the HANA-DB readonly-user
  - Checks the sap-hana-keystore
  - Creates the hanadb_exporter config file /etc/hanadb_exporter/config-{{ sap_monitoring_nr }}-SQL.json with dynamic values from the variables and the recently created key file
  - Restarts the systemd daemon of hanadb_exporter
- Configures the prometheus agent

  - Installs the package golang-github-prometheus-prometheus
  - Creates and sets permissions to prometheus agent directory /opt/prometheus
  - Creates a systemd config file /etc/systemd/system/prometheus@.service from the template with dynamic values to allow multiple prometheus agents
  - Reloads systemd prometheus agent daemon
  - Sets permissions to the  directory /etc/prometheus/
  - Creates the prometheus agent configuration file /etc/prometheus/{{ sap_monitoring_nr }}.yml from the template with dynamic values
    the prometheus agent collects all metrics from the hanadb-exporter and all sap-host-exporters and forwards all metrics to the IBM monitoring instance

Deleting a SAP monitoring configuration of only the <sap_monitoring_nr> in the configuration file includes:

* Stops and disables all daemons of prometheus-agent, hanadb-exporter and all sap-host-exporters
* Deletes all configuration files of the prometheus-agent, hanadb-exporter and all sap-host-exporters

## Quickstart

Overview of the whole Setup:

• Create an IBM Cloud Monitoring instance within the same region as the SAP System.

• Create HANA-DB-User with ReadOnly premissions

• Gather SAP parameters of your SAP System

• Create a new (or use an existing) VPC VirtualServer as monitoring host with the SLES image
"SUSE Linux Enterprise Server 15 SP5 for SAP Applications amd64"
with the same security group as the SAP System.

• copy the SAP-HANA-Client SAR-File and SAPCAR-XXX.exe to a subdirectory

on the monitoring host:
* activate the PackageHub Reository
* install the packages Ansible and git-core
* clone the ansible-power-linux-sap reository from github
* edit the configuration file with your variables and execute the Ansible playbook

• in the IBM Cloud monitoring instance copy the SAP Dashboard Templates from the Dashboard Llibrary to your Dashboards

on the monitoring host execute these steps as root:

```
zypper update
shutdown -r now
# after the restart:
/usr/sbin/SUSEConnect -p PackageHub/15.5/x86_64
zypper install ansible git-core
mkdir <sap_tools_directory>   
# copy the SAP-HANA-Client SAR-File and SAPCAR-XXX.exe to <sap_tools_directory>
git clone https://github.com/IBM/ansible-power-linux-sap.git
cd ansible-power-linux-sap
# configure playbooks/vars/sample-monitoring-sap-parameters.yml
# run the playbook:
ansible-playbook --connection=local -i "localhost," playbooks/sample-monitoring-sap.yml
```

edit playbooks/vars/sample-sap-monitoring-parameters.yml
edit all variables: set your parameters to
sap_monitoring_action, config_override, sap_tools_directory,
sap_monitoring_nr, sap_monitoring_solution_name,
ibmcloud_monitoring_instance_url,ibmcloud_monitoring_authorization_credentials
and all SAP parameters.

execute the playbook:

`ansible-playbook --connection=local -i "localhost," playbooks/sample-monitoring-sap.yml `

Any additional monitoring configuration will be added with executing the same command but different
variables in playbooks/sample-monitoring-sap.yml


| VARIABLE NAME                                          | Value and comment                                                                                                                                                                                                                                       |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ‘ monitoring meta parameter‘                         | <br/>                                                                                                                                                                                                                                                   |
| sap\_monitoring\_action                                | <br/>  add  OR delete                                                                                                                                                                                                                                  |
| config\_override                                       | false  OR  true     # boolean                                                                                                                                                                                                                      |
| sap\_tools\_directory                                  | /root/sap			  # full path to created subdirectory                                                                                                                                                                                                       |
|                                                        | <br/>                                                                                                                                                                                                                                                   |
| sap\_monitoring\_nr <br/>                              | two-digit			incremental number, starting with 01 upto 99.<br/>                                                                                                                                                                                          |
|                                                        | This is not a existing SAP ID, but a pure virtual NR                                                                                                                                                                                                    |
| sap\_monitoring\_solution\_name                        | Optional.   A virtual short name to			recognize your SAP System                                                                                                                                                                                         |
| ‘IBM cloud parameters‘                               | <br/>                                                                                                                                                                                                                                                   |
| ibmcloud\_monitoring\_instance\_url <br/>              | URL of the monitoring			instance, the keyword private is added to the URL,used			in prometheus agent configuration,			example:[https://ingest.prws.private.eu-de](https://ingest.prws.private.eu-de/)..monitoring.cloud.ibm.com/prometheus/remote/write |
| ibmcloud\_monitoring\_authorization\_credentials       | credential to access the			monitoring instance,used in prometheus agent configuration			file                                                                                                                                                            |
| ‘hana parameters‘                                    | <br/>                                                                                                                                                                                                                                                   |
| sap\_hana\_ip                                          | IPv4 address of HANA DB			Server                                                                                                                                                                                                                        |
| sap\_hana\_http\_port                                  | Port of SAP Service on			HANADB Server                                                                                                                                                                                                                  |
| sap\_hana\_sql\_systemdb\_port                         | SQL Port of SYSTEMDB on			HANADB Server                                                                                                                                                                                                                 |
| sap\_hana\_sql\_systemdb\_user                         | SQL user of SYSTEMDBuse			a SQL-User with ReadOnly permissions                                                                                                                                                                                          |
| sap\_hana\_sql\_systemdb\_password                     | Password of SYSTEMDB SQL			Userof the SQL user with ReadOnly permissions                                                                                                                                                                                |
| <br/>                                                  | <br/>                                                                                                                                                                                                                                                   |
| ‘netweaver\_parameters‘                              | <br/>                                                                                                                                                                                                                                                   |
| sap\_ascs\_ip                                          | IPv4 address of first			(primary) Application Server                                                                                                                                                                                                    |
| sap\_ascs\_http\_port                                  | HTTP Port of SAP service			ASCS on APPS Server                                                                                                                                                                                                          |
| # a list for the DI-server                             | <br/>                                                                                                                                                                                                                                                   |
| sap\_app\_server: - sap\_app\_server\_nr: 01 ip: port: | <br/>  IPv4			address of Application Server with first DI service HTTP Port			of SAP service DI on APPS Server                                                                                                                                          |
| - sap\_app\_server\_nr: 02 ip: port:                   | <br/>  IPv4			address of Application Server with second DI service HTTP Port			of SAP service DI on APPS Server                                                                                                                                         |
| - sap\_app\_server\_nr: 03 ip: port:                   | <br/>  IPv4			address of Application Server with third DI service HTTP Port			of SAP service DI on APPS Server                                                                                                                                          |
| - sap\_app\_server\_nr: 0n ip: port:                   | <br/>  IPv4			address of Application Server with n-th DI service HTTP Port			of SAP service DI on APPS Server                                                                                                                                           |
|                                                        |                                                                                                                                                                                                                                                         |

These Variables will be used on the monitoring host in configuration files and exposed ports:


| Port exposed on the monitoring host | Service                                             |
| ----------------------------------- | --------------------------------------------------- |
| 5\${SAP\_SYSTEM\_NR}01              | prometheus agent                                    |
| 5\${SAP\_SYSTEM\_NR}02              | hanadb\_exporter                                    |
| 5\${SAP\_SYSTEM\_NR}03              | sap\_host\_exporter HANA                            |
| 5\${SAP\_SYSTEM\_NR}04              | sap\_host\_exporter ASCS                            |
| 5\${SAP\_SYSTEM\_NR}05              | sap\_host\_exporter DI (primary/ first APPS Server) |
| 5\${SAP\_SYSTEM\_NR}06              | sap\_host\_exporter DI (second APPS Server)         |
| 5\${SAP\_SYSTEM\_NR}07              | sap\_host\_exporter DI (third APPS Server)          |

## Introduction

This Ansible role will setup SAP monitoring configuration on a IBM Cloud Virtual Server

to monitor SAP Systems running on IBM Cloud Power Workspace.
HA SAP Systems are not supported yet.

The Ansible role does not cover the creation of a IBM Cloud Monitoring Instance,
nor the changes on the SAP Systems.

For each SAP System this Ansible playbook has to be executed with a different
configuration file playbooks/vars/sample-sap-monitoring-parameters.yml that determines the SAP host parameters, IBM monitoring instance and action parameters.
The configuration file controls which Ansible tasks will be taken,
whether to add or delete a monitoring configuration.
A monitoring configuration will be active permanently by enabled daemons.
Upto 99 different SAP Systems can be monitored.

Each monitoring configuration must use a different <sap_monitoring_nr> in playbooks/vars/sample-sap-monitoring-parameters.yml,
the <sap_monitoring_nr> will be part of the configuration filenames and process names.
The variables in playbooks/vars/sample-sap-monitoring-parameters.yml will be used in the
prometheus exporter configuration files to collect metrics from the specific SAP System.
There is a maximum of 99 different <sap_monitoring_nr>.

Each Ansible execution with a different <sap_monitoring_nr> adds another monitoring configuration collecting metrics from a different SAP System.
All metrics are forwarded to your IBM monitoring instance, this instance is not accessible by other customers.
In the IBM monitoring instance you can create Dashboards for HANA DB and SAP Services.

All parameter varaibles are in one configuration file playbooks/vars/sample-sap-monitoring-parameters.yml.
You can add or delete a SAP monitoring configuration by setting the variable sap_monitoring_action to  "add" or "delete".

The variable "config_override" controls whether to overwrite exiting configurations with the same <sap_monitoring_nr>  (set to "true")
or to protect from overwriting (set to "false").

You may keep track of these <sap_monitoring_nr> and corresponding SAP Systems,
on the command line it is always possible to check how many configurations are running by
ps aux|grep  -E "prom|hana|sap"

Adding a monitoring configuration requires all SAP parameters,
deleting a SAP monitoring configuration only requires the <sap_monitoring_nr>, all other parameters will not be checked.

A successful configuration will forward the SAP metrics to your IBM monitoring instance
where you can add Dashboards for Views on HANA DB and SAP Services.
# SAP Monitoring in detail

## 1. Create an IBM Cloud Monitoring Instance with your IBM IAM account

One IBM Cloud monitoring instance collects and supplies visualized SAP monitoring Dashboards

for several SAP Systems delivered by a monitoring host in the same region using private ingestion endpoints.

We recommend monitoring SAP Systems in different regions with using IBM Cloud monitoring instances in each corresponding region.

You could reach one single IBM Cloud monitoring instance from every region or even from outside IBM Cloud with using public URL endpoints.

In IBM Cloud Web UI navigate to

Observability/Monitoring: click the button ‘create +’

(as described in  [IBM Cloud Doku](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step2)):

- Select location: the monitoring instance must be in the same region as the monitoring host
- Select the resource group
- Select the pricing plan "Graduated Tier"
  (as lite plan does not allow prometheus calls)
- Service name: the name is arbitrary
- IBM platform metrics: Disable

### Enabling VRF and service endpoints in the IBM Cloud IAM account

Enabled VRF and service endpoints are the default settings in the IAM account.
In case creating a monitoring instance is impossible, enable these setting in your IAM account,
which is described in the [IBM cloud document](https://cloud.ibm.com/docs/account?topic=account-vrf-service-endpoint&interface=ui).

Only the owner of the IAM account can change these settings, not an authorized member of
the IAM account.

### Extract the URL and credentials for prometheus access to the IBM Cloud monitoring instance

The URL and credentials are required to authenticate access to the monitoring instance.
Navigate in the IBM Cloud UI:
Observability/Monitoring: choose your monitoring instance, "Open dashboard"

* click "Get Started" on the bottom left panel
* click "Get Connected"
* click "Optional: Connect your Prometheus Servers"
* copy url and authorization:credentials

example result in the UI:

```
remote_write:
- url: "https://ingest.prws.br-sao.monitoring.cloud.ibm.com/prometheus/remote/write"
  authorization:
    credentials: "d92XXXX-f8XX-4fXX-a6XX-34XXX"
```

add ‘ingest.prws.private.’ to the url as private ingestion endpoint
`https://ingest.prws.private.<REGIONtag>.monitoring.cloud.ibm.com/prometheus/remote/write`
use whole URL as value of the variable <ibmcloud_monitoring_instance_url> in the monitoring configuration.

All private ingestion endpoints per region are listed in [IBM Cloud Document](https://cloud.ibm.com/docs/monitoring?topic=monitoring-prometheus_remote_write)

Use the credentials as value of the variable <ibmcloud_monitoring_authorization_credentials>.

## 2. Create HANA-DB-User with ReadOnly premissions

To avoid using admin privileges on the SAP HANA database, create a new user with ReadOnly permissions as
monitoring role: on systemdb + tenant database
Run the next commands as <SAP HANA Database System Administrator> user to create a new user
with monitoring roles (the commands must be executed in all the databases):
`su - >SAP_Instance_User_HANA>`
CONNECT TO SYSTEMDB as DB-Admin via hdbsql on the HANA DB host.
(see Appendix/Troubleshooting how to hdbsql-connect to SYSTEMDB)

```
CREATE USER <sap_hana_sql_systemdb_user> PASSWORD <sap_hana_sql_systemdb_password> \
NO FORCE_FIRST_PASSWORD_CHANGE;
CREATE ROLE HANADB_EXPORTER_ROLE;
GRANT MONITORING TO HANADB_EXPORTER_ROLE;
GRANT HANADB_EXPORTER_ROLE TO <sap_hana_sql_systemdb_user>;
su - ${SAP_Instance_User_HANA}

```

CONNECT TO TENANT-DB as DB-Admin via hdbsql on the HANA DB host

```
CREATE USER sap_hana_sql_systemdb_user PASSWORD sap_hana_sql_systemdb_password
NO FORCE_FIRST_PASSWORD_CHANGE;
CREATE ROLE HANADB_EXPORTER_ROLE;
GRANT MONITORING TO HANADB_EXPORTER_ROLE;
GRANT HANADB_EXPORTER_ROLE TO sap_hana_sql_systemdb_user;
```

## 3. Configure the SAP SYSTEM to allow HTTP-queries without user/passwords

SAPstart services are running on the HANA-DB- host and on the App Server.

To avoid user/passwords inside the sap_host_exporter configuration,
these SAPstart services need to be set with the Parameter:

```
SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList - GetEnvironment -BAPGetSystemWPTable
```

Configure this setting for the following instances:

- HANA on the HANA-DB Server
- ASCS on the Application Server
- DI on all corresponding Application Servers

### Follow this procedure for all SAPstart services:

List the instances and instance numbers:

`/usr/sap/hostctrl/exe/lssap`

Show OS user of the sapstart service
`ps aux|grep sapstart`

Switch to sapstart OS user
`su - <OS-user of SAPstartsrv>`<OS-user of SAPstartsrv>

List the configured webmethods (using the instance numbers extracted with lssap)
`sapcontrol -nr instance-nr -function ParameterValue service/protectedwebmethods`

If this shows the result:
`SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList - GetEnvironment –ABAPGetSystemWPTable`

then no other configuration is required.
Any other result requires to add one line to the configuration file.

Show the configuration files
`sapcontrol -nr instance-nr -function ListConfigFiles`

3 config files of the instance are listed,
edit the file that does not list DEFAULT.PFL or secinfo
(it is listed as well with `ps aux | grep -I sapstart `)

Add these lines:
(The config text must be put in one single line or change the line if an entry already exists)
“service/protectedwebmethods”) after the SETENV parameters (the line service is one line only):

```
# IBM SAP monitoring
service/protectedwebmethods = SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList -GetEnvironment -ABAPGetSystemWPTable
```

Save the config file without changing the file name.
Restart the corresponding services:
`sapcontrol -nr sapstart -function RestartService`

This may take some time, to check the status:
`sapcontrol -nr sapstart -function GetSystemInstanceList`

The status will show GREEN/ GRAY /YELLOW.
If the service does not return to GREEN, the chapter troubleshooting will list some options.
Restarting the Service may fail in some cases.
Then start the service:

```
sapcontrol -nr service -function StartService instance_name
# The value of instance_name is listed in the sapstart configuration file.
```

List again the configured webmethods (using the instance numbers extracted with lssap)
`sapcontrol -nr instance-nr -function ParameterValue service/protectedwebmethods`

This is the desired result:
`SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList - GetEnvironment -ABAPGetSystemWPTable`
## 4. Create or use an existing VPC VirtualServer as monitoring host

The Ansible playbook has to be executed from a IBM Cloud VPC VirtualServer
in  the same region and the same Security Group as your monitored SAP System(s).

Create a new or use an existing VPC VirtualServer as monitoring host
"SUSE Linux Enterprise Server 15 SP5 for SAP Applications amd64"
with the same security group as the SAP System.

Click on  IBM Cloud catalog: search “Virtual server for VPC “, click on the suggested Virtual server for VPC"
as described in this [IBM Cloud Document](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-virtual-servers&interface=ui)

Choose these settings for the new host:

- Location: select the same region in which the existing SAP System is running
- As OS image select the SLES image "ibm-sles-15-5-amd64-sap-applications-2"
- As Profile choose the smallest available sizing (2 cores).
- Add your matching SSH keys as described in this [IBM Cloud Document](https://cloud.ibm.com/docs/vpc?topic=vpc-managing-ssh-keys&interface=ui)
- use a network with the same security group as the SAP System.
  Manage the local network of the host as described in IBM Cloud Docu
  [](https://cloud.ibm.com/docs/vpc?topic=vpc-using-instance-vnics&interface=ui)

  After creating  the host, login with your selected SSH-key.
  As root exute these commands

```
zypper update
shutdown -r now
# after the restart:
/usr/sbin/SUSEConnect -p PackageHub/15.5/x86_64
zypper install ansible git-core
mkdir <sap_tools_directory>   
# copy the SAP-HANA-Client SAR-File and SAPCAR-XXX.exe to <sap_tools_directory>
git clone https://github.com/IBM/ansible-power-linux-sap.git
cd ansible-power-linux-sap
# configure playbooks/vars/sample-monitoring-sap-parameters.yml
# run the playbook:
ansible-playbook --connection=local -i "localhost," playbooks/sample-monitoring-sap.yml

```

Note: Make sure inside the direcory <sap_tools_directory>  there is only one SAP-HANA-Client as SAR-file and SAPCAR utility.
Not all SAP-HANA-Client downloads include the required Python-HANADB-Driver.
This version comes with the required driver:
IMDB_CLIENT20_020_23-80002082.SAR  (version 2.01)
You may try your existing version, Ansible will tell you if it is successful.

Set your values in the configuration file
playbooks/vars/sample-monitoring-sap-parameters.yml with SAP and monitoring parameters.
Note: Some values need quotation marks as the example file shows.

The configuration file determines the action of the Ansible playbook,
specifically the variables  sap_monitoring_action (add or delete), config_override (true or false), sap_monitoring_nr (a different number for each SAP System).
Details about the variables are lsited in the Quickstart section.

Execute the playbook:

`ansible-playbook --connection=local -i "localhost," playbooks/sample-monitoring-sap.yml `

You may keep track of these <sap_monitoring_nr> and corresponding SAP Systems,
on the command line it is always possible to check how many configurations are running by

`ps aux|grep  -E "prom|hana|sap"`
Adding a monitoring configuration requires all SAP parameters,
deleting a SAP monitoring configuration only requires the <sap_monitoring_nr>, all other parameters will not be checked.

A successful configuration will forward the SAP metrics to your IBM monitoring instance
where you can add Dashboards for Views on HANA DB and SAP Services.
## 5. Create SAP Dashboards in the IBM Cloud Monitoring Instance

A successful configuration will forward the SAP metrics to your IBM monitoring instance

where you can add Dashboards for Views on HANA DB and SAP Services.

Only when SAP metrics are sent to the IBM Cloud monitoring instance

the two SAP Dashboards are visible in monitoring instance/Dashboard/Dashboard Manager:
click on tab “Dashboard Library”:

Click the 3 dots on the very right side of them: “copy to My Dashboards”
Then two SAP Dashboards are visible at Dashboard/Dashboard Manager/My Dashboards/My
Dashboards:

* SAP HANA DB
* SAP SYSTEM

These two SAP Dashboards can be duplicated multiple times,

as the dashboards can be edited to focus on specific SAP Systems only.

Even on the original two Dashboards you may change the scope to make your SAP System visible.

To select the SAP System choose “instance in” on the top of the Dashboard.

The SAP instance is definded by the <sap_monitoring_nr> and the optional <sap_monitoring_solution_name>.

To  verify specific metrics have been collected by the IBM Cloud monitoring instance check monitoring instance navigation panel "Explore":

The SAP metrics are visible in the entity scope "SAP".

If this scope is not set, select the scope via "Grouping scope" + "domain in SAP".

Extend the filters "hanadb" to check HANA-DB metrics,

extend the filter "sap" to check sap service metrics.

The details of these metrics show the instance when "mouseover" one selcted metric  : the sap_monitoring_nr or sap_solution name, instance_nr, instance_hostname and the prometheus exporter

### Duplicate Dashboards

Dashboard/Dashboard Manager/My Dashboards:
each dashboard is listed with 3 dots on the very right of the line: click duplicate dashboard.
You can change any layout of the panels (only as owner of the monitoring instance).

### Public dashboard

A public dashboard is public view on the dashboard without IBM Cloud login with ReadOnly
permissions on the dashboard.
Dashboard/Dashboard Manager/My Dashboards:
each dashboard is listed with 3 dots on the very right of the line: click “Dashboard Settings”
Public Dashboard is listing a hyperlink to access the public dashboard.

### Edit Dashboards

Any changes on a dashboard are better done on a dashboard duplicate.
Any panel can be edited: add/remove data sources, change layouts (timechart/histogram/gauge/table/number/text/toplist).
## 6. HowTo: determine SAP Parameters

Hints to determine SAP parameters on the HANADB-HOST and Application Server

1. List the SAPstart services and OS user of the process
   `root>ps aux|grep sapstartsrv`
   (DI and ASCS on the application server, HANA on the HANA-DB-HOST)
2. List instance and instance_nr (on Apps Server and HANADB-HOST)
   `root>/usr/sap/hostctrl/exe/lssap`
   -->shows: SID, Nr, Instance, SAPLOCALHOST, Version, EXECUTABLE
3. List HTTP/S-Port of SAP InstanceNr

```
su -  <os-user of SAPstartsrv>
sapcontrol -nr  -function GetSystemInstanceList os-userinstance_nr
==> shows: hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
```

4. Determine SQL-Ports of SYSTEMDB and TenantDB

```
root@HANADB>su - hanadb-adm
hdbsql -i instance_nr -d SYSTEMDB -u SYSTEM -p <SQL_UserSYTEMDB_Password>
SELECT * FROM SYS_DATABASES.M_SERVICES
==> shows DATABASE_NAME,HOST,PORT,SERVICE_NAME,PROCESS_ID,DETAIL,ACTIVE_STATUS,
SQL_PORT,COORDINATOR_TYPE,IS_DATABASE_LOCAL
```

5. show Status of HANA DB

```
root@HANADB>su - <HANADB-adm>
hdbsql -i <Instance_nr> -d SYSTEMDB -u SYSTEM -p <SQL_SUERSYSTEMDB_Password>
# or
hdbsql -n localhost:<sap_hana_sql_systemdb_port> -A -e -ssltrustcert -u SYSTEM –p <PASSWORD>

SELECT * FROM SYS.M_DATABASES
==> shows DATABASE_NAME,DESCRIPTION,ACTIVE_STATUS,ACTIVE_STATUS_DETAILS,OS_USER,
OS_GROUP,RESTART_MODE,FALLBACK_SNAPSHOT_CREATE_TIME
```

6. Enabling VRF and service endpoints
   Enabling VRF and service endpoints is documented in IBM Cloud Docu
   [](https://cloud.ibm.com/docs/account?topic=account-vrf-service-endpoint&interface=ui)

## Troubleshooting

Missing data on the SAP monitoring dashboard may be caused by:

* DB tables do not offer metrics (liḱe HANA network receive rate)
* network ports not open (due to firewall settings or VPC ACL settings)
* incorrect port numbers or credentials
* syntax error in configuration files

Execute the following commands to find the causes:

1. To check if HANA-DB is running

   ```
   <sap_hana_ipsap_hana_ips> <su - <hana_db_os_user>
   HDB info
   ps aux|grep -i hana
   ```
2. hdbsql connect to HANA DB

   ```
   <sap_hana_ip> >su - <HANA_DB_OS_USER>
   hdbsql -n localhost:<sap_hana__sql_systemdb_port> -A -e -ssltrustcert
   <sap_hana_sql_systemdb_user> -p <sap_hana_sql_systemdb_password>
   -u
   # or
   hdbsql -i <Instance_nr> -d SYSTEMDB -u SYSTEM -p <sap_hana_sql_systemdb_password>
   ```
3. Show processes of SAP Control instances
   `ps aux|grep sapstartsrv`
4. Show listening ports on HANA host/ Application server
   `ss -tulpen | grep sap`
5. Manual test of hanadb exporter
   `hanadb_exporter -c /etc/hanadb_exporter/config_<sap_monitoring_nr>.json \  -m /etc/hanadb_exporter/metrics.json`
6. Status of the daemons

   systemctl status prometheus@prometheus-<sap_monitoring_nr>
   systemctl status prometheus-hanadb_exporter@config-<sap_monitoring_nr>-SQL
   systemctl status sap_host_exporter@sap_host_exporter-<sap_monitoring_nr>-HANA
   systemctl status sap_host_exporter@sap_host_exporter-<sap_monitoring_nr>-ASCS
   systemctl status  sap_host_exporter@sap_host_exporter-<sap_monitoring_nr>-DI-01
7. Show SAP exporter metrics on the monitoring host

```
# metrics of the prometheus agent
curl http://<${IP_Monitoring_Host}>:5<sap_monitoring_nr>01/metrics

# metrics of the hanadb_exporter
curl http://<${IP_Monitoring_Host}>:5<sap_monitoring_nr>02/metrics

# metrics of the sap_host_exporter for HANA
curl http://<${IP_Monitoring_Host}>:5<sap_monitoring_nr>03/metrics

# metrics of the sap_host_exporter for ASCS
curl http://<${IP_Monitoring_Host}>:5<sap_monitoring_nr>04/metrics

# metrics of the sap_host_exporter for DI-01
curl http://<${IP_Monitoring_Host}>:5<sap_monitoring_nr>05/metrics

# metrics of the sap_host_exporter for DI-02
curl http://<${IP_Monitoring_Host}>:5<sap_monitoring_nr>06/metrics
```

8. List the SAPstart services and OS user of the process

```
root>ps aux | grep sapstartsrv
# show SapStartServices DI and ASCS on the application server, HANA on the HANA-DB host
```

9. List Instance and Instance-Nr (on Apps Server and HANA-DB host)

```
root>/usr/sap/hostctrl/exe/lssap
# shows: ==> SID, Nr,Instance, SAPLOCALHOST,Version,EXECUTABLE
```

10. List HTTP/S-Port of InstanceNr

```
su - <OS-user of SAPstartsrv>
sapcontrol -nr <instance_nr> -function GetSystemInstanceList
# shows ==> hostname, instanceNr, httpPort, httpsPort, startPriority,features, disptatus
```

11. Determine SQL-Ports of SystemDB and TenantDB

```
root@HANADB>su - <HANADB-adm>
hdbsql -i <Instance_nr> -d SYSTEMDB -u SYSTEM -p <PASSWORD>
SELECT * FROM SYS_DATABASES.M_SERVICES
# shows ==>DATABASE_NAME,HOST,PORT,SERVICE_NAME,PROCESS_ID,DETAIL,ACTIVE_STATUS,SQL_PORT,COORDINATOR_TYPE,IS_DATABASE_LOCAL
```

12. Status of all Hana-DBs

```
SELECT * FROM SYS.M_DATABASES
# shows ==>DATABASE_NAME,DESCRIPTION,ACTIVE_STATUS,ACTIVE_STATUS_DETAILS,
OS_USER,OS_GROUP,RESTART_MODE,FALLBACK_SNAPSHOT_CREATE_TIME
```
