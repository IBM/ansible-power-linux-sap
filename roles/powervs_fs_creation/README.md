## Role: powervs_fs_creation

### Creating Filesystems for SAP installations

This module is same for both SLES and RHEL.

This role performs the following tasks:
- **Creates filesystems** with user defined **stripe size** using ansible **built-in** LVM logical volumes modules.
- **Mounts** the filesystems on provided **mount points**
- **Adds an entry to /etc/fstab** for **automount** on reboot.
- **Optional** :Converts the input data structure of disks_configuration variable from **dictionary to a general data structure**.

A separate **task** called **disks-dict2list.yml** is used to handle disks_configuration variable values passed **as dictionary** to execute this role.

The input variable **disks_configuration** for this role supports 2 data structures. Only then disks_configuration will convert the disks_configuration variable data structure in **example A** to general data structure in **example B** below.

**Example A**. Data structure for **disks_configuration** variable as **dictionary** value example:
```
disks_configuration:
{
counts: [2,2,1],
names: [data,log,shared],
paths: [/hana/data,/hana/log,/hana/shared],
wwns: [600507681082018bc8000000000057e4,600507681082018bc8000000000057e8,600507681082018bc8000000000057e5,600507681082018bc8000000000057e6,600507681082018bc8000000000057e7]}
}
```

**Example B**. Data structure for **disks_configuration** variable as **list** value example:
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
