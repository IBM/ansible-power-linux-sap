## Role: powervs_fs_creation

### Creating Filesystems for SAP installations

This module is same for both SLES and RHEL.

This role performs the following tasks:
- **Creates filesystems** with user defined **stripe size** using ansible **builtin** LVM logical volumes modules.
- **Mounts** the filesystems on provided **mount points**
- **Adds an entry to /etc/fstab** for **automount** on reboot.
- **Optional** :Converts the input data structure from **terraform to a general data structure** (Terraform output support)

A separate **task** called **terraform-wrapper.yml** is used to handle the variable values passed **from terraform output** to execute this role, via **terraform**.

The input variable **disks_configuration** for this role supports 2 data structures. Only then terraform-wrapper.yml will convert the terraform data structure in **example A** to normal data structure in **example B** below.

**Example A**.**Terraform** Data structure for **disks_configuration** variable value example:
```
disks_configuration:
{
counts: [2,2,1],
names: [data,log,shared],
paths: [/hana/data,/hana/log,/hana/shared],
wwns: [600507681082018bc8000000000057e4,600507681082018bc8000000000057e8,600507681082018bc8000000000057e5,600507681082018bc8000000000057e6,600507681082018bc8000000000057e7]}
}
```

**Example B**. Data structure for **disks_configuration** variable value example:
```
disks_configuration: [
{
name: data,
path: /hana/data,
wwns: 600507681082018bc8000000000057e4,600507681082018bc8000000000057e8
},
{
name: log,
path: /hana/log,
wwns: 600507681082018bc8000000000057d9,600507681082018bc8000000000057ed7
},
{
name: shared,
path: /hana/shared,
wwns: 600507681082018bc8000000000057f1
}
.
.
.
]
```

## Dependencies

None.

## License

This collection is licensed under the [Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
