## Check Hardware Info

### `lscpu`

lscpu gathers CPU architecture information from sysfs and /proc/cpuinfo. The command output can be optimized for parsing or for easy readability by humans. The information includes, for example, the number of CPUs, threads, cores, sockets, and Non-Uniform Memory Access (NUMA) nodes. There is also information about the CPU caches and cache sharing, family, model, bogoMIPS, byte order, and stepping.

```
$ lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 23
Stepping:              10
CPU MHz:               1998.000
BogoMIPS:              5302.48
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              2048K
NUMA node0 CPU(s):     0-3
```

### `lsblk`

lsblk lists information about all available or the specified block devices. The lsblk command
reads the sysfs filesystem and udev db to gather information. The command prints all block devices (except RAM disks) in a tree-like format by default. Use lsblk --help to get a list of all available columns.

```
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 465.8G  0 disk
├─sda1   8:1    0    70G  0 part
├─sda2   8:2    0     1K  0 part
├─sda5   8:5    0  97.7G  0 part /media/4668484A68483B47
├─sda6   8:6    0  97.7G  0 part /
├─sda7   8:7    0   1.9G  0 part [SWAP]
└─sda8   8:8    0 198.5G  0 part /media/13f35f59-f023-4d98-b06f-9dfaebefd6c1
sr0     11:0    1  1024M  0 rom
```

### `df`

Reports various partitions, their mount points and the used and available space on each.

```
$ df -H
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda6       104G   26G   73G  26% /
none            4.1k     0  4.1k   0% /sys/fs/cgroup
udev            4.2G  4.1k  4.2G   1% /dev
tmpfs           837M  1.6M  835M   1% /run
none            5.3M     0  5.3M   0% /run/lock
none            4.2G   13M  4.2G   1% /run/shm
none            105M   21k  105M   1% /run/user
/dev/sda8       210G  149G   51G  75% /media/13f35f59-f023-4d98-b06f-9dfaebefd6c1
/dev/sda5       105G   31G   75G  30% /media/4668484A68483B47
```

### `fdisk`
Fdisk is a utility to modify partitions on hard drives, and can be used to list out the partition information as well.
```
$ sudo fdisk -l

Disk /dev/sda: 500.1 GB, 500107862016 bytes
255 heads, 63 sectors/track, 60801 cylinders, total 976773168 sectors
Units = sectors of 1 \* 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x30093008

Device Boot Start End Blocks Id System
/dev/sda1 \* 63 146801969 73400953+ 7 HPFS/NTFS/exFAT
/dev/sda2 146802031 976771071 414984520+ f W95 Ext'd (LBA)
/dev/sda5 146802033 351614654 102406311 7 HPFS/NTFS/exFAT
/dev/sda6 351614718 556427339 102406311 83 Linux
/dev/sda7 556429312 560427007 1998848 82 Linux swap / Solaris
/dev/sda8 560429056 976771071 208171008 83 Linux
```

### `mount`
The mount is used to mount/unmount and view mounted file systems.
```
$ mount | column -t
/dev/sda6    on  /                                            type  ext4             (rw,errors=remount-ro)
proc         on  /proc                                        type  proc             (rw,noexec,nosuid,nodev)
sysfs        on  /sys                                         type  sysfs            (rw,noexec,nosuid,nodev)
none         on  /sys/fs/cgroup                               type  tmpfs            (rw)
none         on  /sys/fs/fuse/connections                     type  fusectl          (rw)
none         on  /sys/kernel/debug                            type  debugfs          (rw)
none         on  /sys/kernel/security                         type  securityfs       (rw)
udev         on  /dev                                         type  devtmpfs         (rw,mode=0755)
devpts       on  /dev/pts                                     type  devpts           (rw,noexec,nosuid,gid=5,mode=0620)
tmpfs        on  /run                                         type  tmpfs            (rw,noexec,nosuid,size=10%,mode=0755)
none         on  /run/lock                                    type  tmpfs            (rw,noexec,nosuid,nodev,size=5242880)
none         on  /run/shm                                     type  tmpfs            (rw,nosuid,nodev)
none         on  /run/user                                    type  tmpfs            (rw,noexec,nosuid,nodev,size=104857600,mode=0755)
none         on  /sys/fs/pstore                               type  pstore           (rw)
/dev/sda8    on  /media/13f35f59-f023-4d98-b06f-9dfaebefd6c1  type  ext4             (rw,nosuid,nodev,errors=remount-ro)
/dev/sda5    on  /media/4668484A68483B47                      type  fuseblk          (rw,nosuid,nodev,allow_other,blksize=4096)
binfmt_misc  on  /proc/sys/fs/binfmt_misc                     type  binfmt_misc      (rw,noexec,nosuid,nodev)
systemd      on  /sys/fs/cgroup/systemd                       type  cgroup           (rw,noexec,nosuid,nodev,none,name=systemd)
gvfsd-fuse   on  /run/user/1000/gvfs                          type  fuse.gvfsd-fuse
```

### `/proc`
Many of the virtual files in the /proc directory contain information about hardware and configurations. Here are some of them

#### CPU/Memory information
```
# cpu information
$ cat /proc/cpuinfo

# memory information
$ cat /proc/meminfo
```

#### Linux/kernel information
```
$ cat /proc/version
Linux version 3.11.0-12-generic (buildd@allspice) (gcc version 4.8.1 (Ubuntu/Linaro 4.8.1-10ubuntu7) ) #19-Ubuntu SMP Wed Oct 9 16:20:46 UTC 2013
```
#### SCSI/Sata devices
```
$ cat /proc/scsi/scsi
Attached devices:
Host: scsi3 Channel: 00 Id: 00 Lun: 00
  Vendor: ATA      Model: ST3500418AS      Rev: CC38
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi4 Channel: 00 Id: 00 Lun: 00
  Vendor: SONY     Model: DVD RW DRU-190A  Rev: 1.63
  Type:   CD-ROM                           ANSI  SCSI revision: 05
```
#### Partitions
```
$ cat /proc/partitions
major minor  #blocks  name

   8        0  488386584 sda
   8        1   73400953 sda1
   8        2          1 sda2
   8        5  102406311 sda5
   8        6  102406311 sda6
   8        7    1998848 sda7
   8        8  208171008 sda8
  11        0    1048575 sr0
```