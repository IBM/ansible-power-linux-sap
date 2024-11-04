# Sections

1. [Introduction](README.md#1-Introduction)
2. [Role Description](README.md#2-Role-description)
3. [Configuration Variables](README.md#3-Edit-parameters-in-the-monitoring-configuration-file)
4. [Installation Guide](README.md#4-Installation-Guide)

# 1. Introduction

The `configure_network_management_services` Ansible role is designed to install and configure network services on virtual server instances, taking into account the instance's role as either a client or server for the network services.  The role is compatible with both SLES and RHEL operating systems.

# 2. Role Description

This role provides a set of tasks to configure a virtual server instance as either a server or client for network management services. The configuration is controlled by input variables server_config and client_config.

## Server Configuration

To configure a virtual server instance as a server for network management services, the `server_config` input variable must be set. The role performs the following tasks:

- Installs and configures SQUID package and squid.conf file
- Installs and configures DNS packages and sets up DNS server based on the dns_servers input
- Installs and configures NTP packages and updates named.conf file
- Installs and configures NFS server packages, creates NFS directories, and exports them as mountable directories
- Starts and enables all above-mentioned services

## Client Configuration

To configure a virtual server instance as a client for network management services, the `client_config` input variable must be set. The role performs the following tasks:

- Configures SQUID proxy client
- Configures DNS
- Installs and configures NTP packages and updates named.conf file with NTP server
- Installs and configures NFS client packages, and mounts NFS exported directories
- Sets up network proxy on client, modifying /etc/bashrc and /etc/dnf/dnf.conf files
- Starts and enables all above-mentioned services

# 3. Edit parameters in the monitoring configuration file
To set up the network management server, edit the configuration file - `playbooks/vars/sample-variables-configure-network-services-host.yml` and to set up the network management services client, edit the the configuration file - `playbooks/vars/sample-variables-configure-network-services-client.yml`.

| Name  | Type  |Example  | Description |
|-------|-------|---------|-------------|
| client_config  | Object  |client_config: <br>&nbsp;&nbsp;squid: <br>&nbsp;&nbsp;&nbsp;&nbsp;enable: true <br> &nbsp;&nbsp;&nbsp;&nbsp;squid_server_ip_port: "10.51.0.121:3128" <br> &nbsp;&nbsp;&nbsp;&nbsp;no_proxy_hosts: "161.0.0.0/8" <br> &nbsp;&nbsp;ntp: <br> &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br> &nbsp;&nbsp;&nbsp;&nbsp;ntp_server_ip: "10.51.0.121" <br> &nbsp;&nbsp;nfs: <br> &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br> &nbsp;&nbsp;&nbsp;&nbsp;nfs_server_path: "10.51.0.121:/nfs;10.51.0.121:/software" <br> &nbsp;&nbsp;&nbsp;&nbsp;nfs_client_path: "/nfs;/software" <br> &nbsp;&nbsp;&nbsp;&nbsp;opts: "sec=sys,nfsvers=4.1,nofail" <br> &nbsp;&nbsp;&nbsp;&nbsp;fstype: nfs4 <br> &nbsp;&nbsp;dns: <br> &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br> &nbsp;&nbsp;&nbsp;&nbsp;dns_server_ip: "10.51.0.121" <br> | Configures the host to reach network management services <br> `squid.enable` (bool) - The squid proxy client is set up if this var is set to true <br> `squid.squid_server_ip_port` - The the IP and port for the Squid server <br> `squid.no_proxy_hosts` - Comma-separated list of hosts that bypass the proxy <br> `ntp.enable` (bool) - Enables or disables the NTP service <br> `ntp.ntp_server_ip` - Specifies the IP of the NTP server <br> `nfs.enable` (bool) - Enables or disables the NFS service <br> `nfs.nfs_server_path` - Specifies the path to the NFS server <br> `nfs.nfs_client_path` - Specifies the path on the client for NFS <br> `nfs.opts` - Options for NFS mount command <br> `nfs.fstype` - Filesystem type for NFS <br> `dns.enable` (bool) - Enables or disables the DNS service <br> `dns.dns_server_ip` - Specifies the IP of the DNS server|
| server_config  | Object  |server_config: <br>  &nbsp;&nbsp;squid: <br>    &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br>  &nbsp;&nbsp;ntp: <br>    &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br>  &nbsp;&nbsp;nfs: <br>    &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br>    &nbsp;&nbsp;&nbsp;&nbsp;directories:  <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- nfs <br> &nbsp;&nbsp;dns: <br>    &nbsp;&nbsp;&nbsp;&nbsp;enable: true <br>    &nbsp;&nbsp;&nbsp;&nbsp;dns_servers: "161.26.0.7; 161.26.0.8; 9.9.9.9;" <br> |Optionally install network services Squid Proxy, NTP, NFS and DNS.<br>`squid.enable` (bool) - if true, installs SQUID package, and configures squid.conf file <br>`ntp.enable` (bool) - if true, Installs and configures NTP packages and updates named.conf file<br>`nfs.enable` (bool) - if true, installs NFS packages, creates NFS directories <br>`nfs.directories` (bool) - list of NFS directories that must be created if they do not already exist. They will be made available as mountable directories. <br>`dns.enable` (bool)- if true, installs and configures DNS packages and sets up DNS server <br>`dns.dns_servers` - user-defined DNS server IPs|

# 4. Installation Guide

## 4.1 Prerequisites 
To enable services on the network management server, in addition to setting the boolean flags, additional information must be planned and provided. For DNS, specify the target DNS server IPs. For NFS service setup, provide the directories that need to be created when editing the configuration file.
The network management services client setup the network management server information:
- copy the IP and port of Squid Proxy server
- copy the IPs of NTP, NFS and DNS servers

## 4.2 Edit the network services server/client configuration file
- To set up the network management server, edit the configuration file - `playbooks/vars/sample-variables-configure-network-services-host.yml`
- To set up the network management services client, edit the the configuration file - `playbooks/vars/sample-variables-configure-network-services-client.yml`

## 4.3. Execute the Ansible playbook
To set up the network management server, execute the ansible playbook:
`ansible-playbook --connection=local -i "localhost," playbooks/sample-configure-network-services-host.yml`

To set up the network management client, execute the ansible playbook:
`ansible-playbook --connection=local -i "localhost," playbooks/sample-configure-network-services-client.yml`

# Notes
- By default every service is `disabled`.
- Each service can be chosen to be enabled or not by setting `enable: true`.
- Disabling is not supported.
- For **NFS** services, `nfs_server_path`, which are already shared, and can be mounted on client should be provided. `nfs_client_path` are directories where NFS shared directory will be locally mounted.

# Dependencies

None.

# License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).