## Role: powervs_configure_os_for_sap

### Preparing Operating System for SAP installations for RHEL

This role performs the following tasks:
- Enables **multipathd** daemon
- Enables **NFS** Service
- Enables **rpcbind** daemon
- Sets **MTU** value to **9000** for SAP network interfaces
- **TSO** is enabled for SAP network interfaces
- Setting **transparent_hugepage** to never.

### SAP System Preparation on RHEL and SLES

To prepare RHEL and SLES for SAP applications, this role performs tuning and optimization to meet SAP’s performance and compliance requirements.

On **RHEL**, this role installs and applies **[RHEL system roles](https://access.redhat.com/articles/3050101)**, which streamline system configurations for SAP workloads:

- **`sap_general_preconfigure`**: Provides baseline OS settings for all SAP applications.
- **`sap_hana_preconfigure`**: Adds SAP HANA-specific tuning, optimizing memory and I/O.
- **`sap_netweaver_preconfigure`**: Configures settings tailored to SAP NetWeaver’s application server needs.

To use these roles, install following packages:

```bash
sudo dnf -y install rhel-system-roles rhel-system-roles-sap
```

On **SLES**, this role performs saptune package installation, use the saptune tool to apply specific SAP profiles, such as HANA, which configures the system based on SAP Notes like below:
```bash
sudo zypper install saptune
sudo saptune solution apply HANA
```

All settings applied remain persistent across reboot.

**Note:**
Ansible playbook may report **Failure/Warning**, if scripts analyze reboot is required for settings applied by it. User should reboot their LPAR, in that case.

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).