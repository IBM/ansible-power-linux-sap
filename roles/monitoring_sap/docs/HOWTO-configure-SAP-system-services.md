# Configure the SAP SYSTEM to allow HTTP-queries without user/passwords

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
“'service/protectedwebmethods'” after the SETENV parameters (the line service is one line only):

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
