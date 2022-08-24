## role: powervs_swap_creation

### Configuring SWAP spaces

This module configures swap space on LPAR, and is same for both RHEL and SLES. This role is applicable for **only for SAP Netweaver**

This role performs the following tasks:
- Removes previous swap device configured
- Creates a new swap device on disk provided with swap_disk_wwn variable.

#### Note:
For RHEL, **swap disk of size >= 24GB** is required for community role **[sap-netweaver-preconfigure](https://github.com/linux-system-roles/sap-netweaver-preconfigure)** to succeed.

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
