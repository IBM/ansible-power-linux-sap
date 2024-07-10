
# Troubleshooting

Missing data on the SAP monitoring dashboard may be caused by:
* DB tables do not offer metrics (liḱe HANA network receive rate)
* network ports not open (due to firewall settings or VPC ACL settings)
* incorrect port numbers or credentials
* syntax error in configuration files

Execute the following commands to find the causes:

1. To list existing monitoring configurations execute:
`ps aux|grep  -E "prom|hana|sap"`



2. To check if HANA-DB is running
   ```
    <sap_hana_ipsap_hana_ips> <su - <hana_db_os_user>
   HDB info
   ps aux|grep -i hana
   ```

3. hdbsql connect to HANA DB
   ```
   <sap_hana_ip> >su - <HANA_DB_OS_USER>
   hdbsql -n localhost:<sap_hana__sql_systemdb_port> -A -e -ssltrustcert -u <sap_hana_sql_systemdb_user> \
   -p <sap_hana_sql_systemdb_password>
   # or
   hdbsql -i <Instance_nr> -d SYSTEMDB -u SYSTEM -p <sap_hana_sql_systemdb_password>
   ```
4. Show processes of SAP Control instances
   `ps aux|grep sapstartsrv`

5. Show listening ports on HANA host/ Application server
   `ss -tulpen | grep sap`
6. Manual test of hanadb exporter
   `hanadb_exporter -c /etc/hanadb_exporter/config_<sap_monitoring_nr>.json \  -m /etc/hanadb_exporter/metrics.json`
7. Show the status of the daemons
   ```
   systemctl status prometheus@prometheus-<sap_monitoring_nr>
   systemctl status prometheus-hanadb_exporter@config-<sap_monitoring_nr>-SQL
   systemctl status sap_host_exporter@sap_host_exporter-<sap_monitoring_nr>-HANA
   systemctl status sap_host_exporter@sap_host_exporter-<sap_monitoring_nr>-ASCS
   systemctl status  sap_host_exporter@sap_host_exporter-<sap_monitoring_nr>-DI-01
   ```

8. Show SAP exporter metrics on the monitoring host
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

9. List the SAPstart services and OS user of the process
   ```
   root>ps aux | grep sapstartsrv
   # show SapStartServices DI and ASCS on the application server, HANA on the HANA-DB host
   ```

10. List Instance and Instance-Nr (on Apps Server and HANA-DB host)
   ```
   root>/usr/sap/hostctrl/exe/lssap
   # shows: ==> SID, Nr,Instance, SAPLOCALHOST,Version,EXECUTABLE
   ```

11. List HTTP/S-Port of InstanceNr
   ```
   su - <OS-user of SAPstartsrv>
   sapcontrol -nr <instance_nr> -function GetSystemInstanceList
   # shows ==> hostname, instanceNr, httpPort, httpsPort, startPriority,features, disptatus
   ```

12. Determine SQL-Ports of SystemDB and TenantDB
   ```
   root@HANADB>su - <HANADB-adm>
   hdbsql -i <Instance_nr> -d SYSTEMDB -u SYSTEM -p <PASSWORD>
   SELECT * FROM SYS_DATABASES.M_SERVICES
   # shows ==>DATABASE_NAME,HOST,PORT,SERVICE_NAME,PROCESS_ID,DETAIL,ACTIVE_STATUS,SQL_PORT,COORDINATOR_TYPE,IS_DATABASE_LOCAL
   ```

13. Status of all Hana-DBs
   ```
   SELECT * FROM SYS.M_DATABASES
   # shows ==>DATABASE_NAME,DESCRIPTION,ACTIVE_STATUS,ACTIVE_STATUS_DETAILS,
   OS_USER,OS_GROUP,RESTART_MODE,FALLBACK_SNAPSHOT_CREATE_TIME
   ```
