## Role: powervs_client_enable_services 

### Enabling Services

This module is same for both SLES and RHEL.

This role performs the following tasks:
- Configures **SQUID** proxy.
- Configures **DNS** server.
- Installs **NTP** packages and updates named.conf file with ntp server.
- Installs **NFS** client packages, and mounts nfs exported directories as mentioned in variable file..

This role will also **start and enable** all above mentioned services.

The input variable **client_config** is needed to be provided for this role to be executed. The variable file looks like below
```
client_config: {
squid: { enable: false, squid_server_ip_port: "127.0.0.1:3128", no_proxy_hosts: "161.0.0.0/8" },
ntp: { enable: false, ntp_server_ip: "127.0.0.1" },
nfs: { enable: false, nfs_server_path: "127.0.0.1:/USER;127.0.0.1:/EXAMPLE", nfs_client_path: "/MNT;/HANA" },
dns: { enable: false, dns_server_ip: "127.0.0.1" }
}
```
Each services can be chosen to be enabled or not. Disabling is not supported. This variable file enables users, to enable one or many services on one or multiple SAP instances, as desired.

For **NFS** services, nfs server path, which are already shared, and can be mounted on client should be provided. **nfs_client_path** are directories where NFS shared directory will be locally mounted. 

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
