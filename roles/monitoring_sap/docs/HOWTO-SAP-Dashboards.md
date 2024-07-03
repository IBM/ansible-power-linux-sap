## 5. Create SAP Dashboards in the IBM Cloud Monitoring Instance

A successful configuration will forward the SAP metrics to your IBM monitoring instance
where you can add Dashboards for Views on HANA DB and SAP Services.
Only when SAP metrics are sent to the IBM Cloud monitoring instance
the two SAP Dashboards are visible in monitoring instance/Dashboard/Dashboard Manager:
click on tab “`Dashboard Library`”:

Click the 3 dots on the very right side of them: “copy to My Dashboards”
Then two SAP Dashboards are visible at ** Dashboard/Dashboard Manager/My Dashboards/My
Dashboards:**

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
