## role: powervs_configure_os_for_sap

### Preparing Operating System for SAP installations for RHEL

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **TSO** is enabled for SAP network interfaces
- Setting **transparent_hugepage** to never.

This role is followed by execution of following community roles
- **[sap-preconfigure](https://github.com/linux-system-roles/sap-preconfigure)**
- **[sap-hana-preconfigure](https://github.com/linux-system-roles/sap-hana-preconfigure)**
- **[sap-netweaver-preconfigure](https://github.com/linux-system-roles/sap-netweaver-preconfigure)**

All settings applied remain persistent across reboot.

**Note:**
Ansible playbook may report **Failure/Warning**, if scripts analyze reboot is required for settings applied by it. User should reboot their LPAR, in that case.

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).