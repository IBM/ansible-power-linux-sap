## role: powervs_prepare_sles_sap

### Preparing Operating System for SAP installations on SLES

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **TSO** is enabled for SAP network interfaces
- **SAPTUNE SOLUTION** for **HANA or NETWEAVER or NETWEAVER+HANA** is applied based on parameter passed. **Only Saptune v3 is supported.** 
- Activates **SUSE subscription** or **[Full Linux Subscription](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux)**
- Set up **network proxy** on client. Modifies **/etc/bash.bashrc** file**

All settings applied remain persistent across reboot.

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
