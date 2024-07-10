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
