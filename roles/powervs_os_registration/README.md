## Role: powervs_os_registration

### Preparing Operating System for SAP installations for RHEL or SLES

This role performs the following tasks:
- Activates **[Full Linux Subscription](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-set-full-Linux)** or **RHEL subscription** or **SUSE subscription**

All settings applied remain persistent across reboot.

**Note:**
Ansible playbook may report **Failure/Warning**, if scripts analyse reboot is required for settings applied by it. User should reboot their LPAR, in that case.

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).