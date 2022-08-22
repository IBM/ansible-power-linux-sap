## Role: powervs_install_services 

### Enabling Services

This module is same for both SLES and RHEL.

This role performs the following tasks:
- Installs **SQUID** package, and configures squid.conf file as described in this link https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux
- Installs **DNS** packages and configure DNS server based on the **dns_servers** input provided
- Installs **NTP** packages and updates named.conf file as per the requirement.
- Installs **NFS** server packages, creates NFS directories as provided in input and also exports them as mountable directories.

This role will also **start and enable** all above mentioned services.

Additionally it will also install **awscli** package. awscli should be configured manually later.

The input variable **server_config** is needed to be provided for this role to be executed. The variable file looks like below
```
server_config: {
squid: { enable: false },
ntp: { enable: false },
nfs: { enable: true, nfs_directory: "/NFS; /hana/software" },
dns: { enable: false, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" },
awscli: { enable: false }
}
```
Each services can be choosen to be enabled or not. Disabling is not supported. This variable file enables users, to enable one or many services on one or multiple servers, as desired.

For NFS services, additional variable nfs_directory need to be provided. Directories name provided will be created, if not already present and are exported as a mountable directory.

For DNS services, additional variable dns_server is required. These are user-defined DNS servers IPs. Please note, **;** as a seperator, in example.


## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
