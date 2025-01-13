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
`su - <OS-user of SAPstartsrv>`

List the instance_numbers and status of all SapStart services

`sapcontrol -nr sapstart -function GetSystemInstanceList`

List the configured webmethods (using the instance numbers extracted with lssap or sapcontrol)

`sapcontrol -nr <instance-nr> -function ParameterValue service/protectedwebmethods`

If this shows the result: <br>
`SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList - GetEnvironment –ABAPGetSystemWPTable` <br>
then no other configuration is required.
Any other result requires to add one line to the configuration file.

Show the configuration files <br>
`sapcontrol -nr <instance-nr> -function ListConfigFiles`

3 config files of the instance are listed,
edit the file that does not list 'DEFAULT.PFL' or 'secinfo' <br>
(it is listed as well with `ps aux | grep -I sapstart `) <br>

Before changing the configuration of the specific serive it is best to stop the service: <br>

`sapcontrol -nr  instance_nr sapstart -function StopSystem` <br>

Add these lines as hithero owner of the configuration file: <br>

(The config text must be put in one single line or change the line if an entry already exists)
“'service/protectedwebmethods'” after the SETENV parameters (the line service is one line only): <br>

```
# IBM SAP monitoring
service/protectedwebmethods = SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList -GetEnvironment -ABAPGetSystemWPTable
```

Save the config file without changing the file name. <br>
Restart the corresponding services: <br>
`sapcontrol -nr <instance-nr> sapstart -function RestartService` <br>

This may take some time, to check the status: <br>
`sapcontrol -nr sapstart -function GetSystemInstanceList` <br>

The status will show GREEN/ GRAY /YELLOW. <br>
If the service does not return to GREEN, the chapter troubleshooting will list some options.
Restarting the Service may fail in some cases. <br>
Then start the service: <br>

```
sapcontrol -nr service -function StartService instance_name
# The value of instance_name is listed in the sapstart configuration file.
```

List again the configured webmethods (using the instance numbers extracted with lssap) <br>
`sapcontrol -nr <instance-nr> -function ParameterValue service/protectedwebmethods`

This is the desired result: <br>
`SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList - GetEnvironment -ABAPGetSystemWPTable`
