# PIHOLE

This project shows how to install and configure Pihole on your environment to block ads using Kubernetes.

I assume that you already have your Kubernetes cluster up and running!

## Step by Step

1. Namespace
2. Persistent Volume
3. Persistent Volume Claim

**lsblk**

Used to list device and view status.

```
[root@chlabkubn02 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm
sdb               8:16   0   10G  0 disk
sr0              11:0    1  4.3G  0 rom
```

**pvcreate**
```    
[root@chlabkubn02 ~]# pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.
```

**pvs**

Used to list phisycal Volume.

```
[root@chlabkubn02 ~]# pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  centos lvm2 a--  <19.00g     0
  /dev/sdb          lvm2 ---   10.00g 10.00g
```

**vgcreate**

Used to initialize an entire disk or a disk partition for use LVM.

```
[root@chlabkubn02 ~]# vgcreate chlabvg01 /dev/sdb
  Volume group "chlabvg01" successfully created
  
```
**vgs** 

Used to list a logical volume group

```
[root@chlabkubn02 ~]# vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  centos      1   2   0 wz--n- <19.00g      0
  chlabvg01   1   0   0 wz--n- <10.00g <10.00g
```

**lvcreate** 

Used to create a logical volume lvcreate.

```
[root@chlabkubn02 ~]# lvcreate -L 5000 -n chlablv01 chlabvg01
  Logical volume "chlablv01" created.
```

**lvs**

Used to list Logical Volume created.

```
[root@chlabkubn02 ~]# lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root      centos    -wi-ao---- <17.00g
  swap      centos    -wi-a-----   2.00g
  chlablv01 chlabvg01 -wi-a-----   4.88g
```

**vgdisplay**

 Used to get details about VG.

```
[root@chlabkubn02 ~]# vgdisplay -v chlabvg01
  --- Volume group ---
  VG Name               chlabvg01
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  2
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <10.00 GiB
  PE Size               4.00 MiB
  Total PE              2559
  Alloc PE / Size       1250 / 4.88 GiB
  Free  PE / Size       1309 / 5.11 GiB
  VG UUID               HPzBOj-N5vb-cAsn-JnL8-TBhD-cKYv-lBhVKc

  --- Logical volume ---
  LV Path                /dev/chlabvg01/chlablv01
  LV Name                chlablv01
  VG Name                chlabvg01
  LV UUID                9QFKTY-Ks4F-dS7F-cAJ4-gzpa-PSus-F2gwqG
  LV Write Access        read/write
  LV Creation host, time chlabkubn02.chlab.local, 2019-06-26 10:33:47 +0200
  LV Status              available
  # open                 0
  LV Size                4.88 GiB
  Current LE             1250
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:2

  --- Physical volumes ---
  PV Name               /dev/sdb
  PV UUID               08iD6Q-zepH-L8Ce-FI1N-bgjN-aPpS-Ur2mwR
  PV Status             allocatable
  Total PE / Free PE    2559 / 1309
