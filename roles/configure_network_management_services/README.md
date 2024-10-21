## Role: configure_network_management_services

### Configuring Networks Services Host (configure_network_services_host.yml)

This module is same for both SLES and RHEL.

This role performs the following tasks:
- Installs **SQUID** package, and configures squid.conf file as described in this link https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux
- Installs **DNS** packages and configure DNS server based on the **dns_servers** input provided
- Installs **NTP** packages and updates named.conf file as per the requirement.
- Installs **NFS** server packages, creates NFS directories as provided in input and also exports them as mountable directories.

This role will also **start and enable** all above mentioned services.


The input variable **server_config** is needed to be provided for this role to be executed. The variable file looks like below
```
server_config: {
squid: { enable: false },
ntp: { enable: false },
nfs: { enable: true, nfs_directory: "/NFS; /hana/software" },
dns: { enable: false, dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" }
}
```
Each services can be chosen to be enabled or not. Disabling is not supported. This variable file enables users, to enable one or many services on one or multiple servers, as desired.

For NFS services, additional variable **nfs_directory** need to be provided. Directories name provided will be created, if not already present and are exported as a mountable directory.

For DNS services, additional variable **dns_servers** is required. These are user-defined DNS servers IPs. Please note, **;** as a **separator**, in example.

### Configuring Networks Services Client (configure_network_services_client.yml)

This module is same for both SLES and RHEL.

This role performs the following tasks:
- Configures **SQUID** proxy client.
- Configures **DNS**.
- Installs **NTP** packages and updates named.conf file with ntp server.
- Installs **NFS** client packages, and mounts nfs exported directories as mentioned in variable file.
- All above services will be **started and enabled**

To execute this role the input variable **client_config** is required. The variable is defined below.

```
client_config:
  squid:
    enable: false
    squid_server_ip_port: "10.51.0.121:3128"
    no_proxy_hosts: "161.0.0.0/8"
  ntp:
    enable: false
    ntp_server_ip: "10.51.0.121"
  nfs:
    enable: false
    nfs_server_path: "10.51.0.121:/nfs;10.51.0.121:/software"
    nfs_client_path: "/nfs;/software"
    opts: sec=sys,nfsvers=4.1,nofail
    fstype: nfs4
  dns:
    enable: false
    dns_server_ip: "10.51.0.121"
```

## Notes
- By default every service is `disabled`.
- Each service can be chosen to be enabled or not by setting `enable: true`.
- Disabling is not supported.
- For **NFS** services, `nfs_server_path`, which are already shared, and can be mounted on client should be provided. `nfs_client_path` are directories where NFS shared directory will be locally mounted.

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
