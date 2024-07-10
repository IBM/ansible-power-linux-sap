# Create a HANA-DB-User with ReadOnly permissions

To avoid using admin privileges on the SAP HANA database, create a new user with ReadOnly permissions
on systemdb and tenant database.
Run the next commands as <SAP HANA Database System Administrator> user  SYSTEMDB to create a new user
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
