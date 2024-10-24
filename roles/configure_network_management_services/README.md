## Role: powervs_client_enable_services

### Enabling Services

This module is same for both SLES and RHEL.

This role performs the following tasks:
- Configures **SQUID** proxy client.
- Configures **DNS**.
- Installs **NTP** packages and updates named.conf file with ntp server.
- Installs **NFS** client packages, and mounts nfs exported directories as mentioned in variable file.
- Set up **network proxy** on client. Modifies **/etc/bashrc** and **/etc/dnf/dnf.conf** file**
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