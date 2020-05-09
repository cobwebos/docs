---
title: 使用 GPT 分区调整 OS 磁盘的大小 |Microsoft Docs
description: 本文介绍如何使用 GPT 分区调整 OS 磁盘的大小。
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: f863233f0a34271841cc8e973f9aa3ca9416ceeb
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858985"
---
# <a name="resize-an-os-disk-with-a-gpt-partition"></a>调整 OS 磁盘的大小，并使用 GPT 分区

> [!NOTE]
> 此方案仅适用于包含 GPT 分区的 OS 磁盘。

本文介绍如何使用 Linux 中的 GPT 分区增加 OS 磁盘的大小。

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>确定 OS 磁盘是否有 MBR 或 GPT 分区

使用**parted**命令来确定磁盘分区是使用主启动记录（MBR）分区还是 GUID 分区表（GPT）分区创建的。

### <a name="mbr-partition"></a>MBR 分区

在下面的输出中，**分区表**显示值**msdos.sys**，其中标识**MBR**分区。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT 分区

在下面的输出中，**分区表**显示值**gpt**，用于标识 gpt 分区。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

如果你的虚拟机（VM）在操作系统磁盘上有 GPT 分区，则增加操作系统磁盘的大小。

## <a name="increase-the-size-of-the-os-disk"></a>增加操作系统磁盘的大小

以下说明适用于 Linux 认可的分发版。

> [!NOTE]
> 在继续下一步之前，请为 VM 创建备份副本，或者创建 OS 磁盘的快照。

### <a name="ubuntu-16x-and-18x"></a>Ubuntu 16. x 和 18. x

若要增加 Ubuntu 16. x 和 18. x 中 OS 磁盘的大小，请执行以下操作：

1. 停止 VM。
1. 从门户增加 OSDisk 的大小。
1. 重新启动 VM，并以**根**用户身份登录到 vm。
1. OSDisk 现在会显示增加的文件系统大小。

如以下示例中所示，操作系统磁盘已从门户调整到 100 GB，因为**/** 现在装入的 **/dev/sda1**文件系统显示 97 gb。

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse-12-sp4suse-sles-12-for-sap-suse-sles-15-and-suse-sles-15-for-sap"></a>SUSE 12 SP4、SUSE SLES 12 for SAP、SUSE SLES 15 和 SUSE SLES 15 for SAP

若要增加 SUSE 12 SP4 中的 OS 磁盘的大小，SUSE SLES 15 和 SUSE SLES 15 for SAP：

1. 停止 VM。
1. 从门户增加 OSDisk 的大小。
1. 重启 VM。

重新启动 VM 后，请执行以下步骤：

   1. 使用以下命令以**根用户**身份访问 VM：
   
      `#sudo su`

   1. 使用以下命令安装**gptfdisk**包，这是增加 OS 磁盘大小所必需的：

      `#zypper install gptfdisk -y`

   1. 若要查看磁盘上可用的最大扇区，请运行以下命令：

      `#sgdisk -e /dev/sda`

   1. 调整分区大小，但不使用以下命令将其删除。 **Parted**命令有一个名为**resizepart**的选项，用于重设分区大小，而不会将其删除。 Resizepart 后面的数字4指示调整第四个（第四个）分区的大小。

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. 运行`#lsblk`命令以检查分区是否已增加。

      以下输出显示已将 **/dev/sda4**分区调整为 98.5 GB。

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. 使用以下命令标识 OSDisk 上的文件系统类型：

      `blkid`

      示例输出：

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. 根据文件系统类型，使用相应的命令调整文件系统的大小。

      对于**xfs**，请使用以下命令：

      ` #xfs_growfs /`

      示例输出：

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      对于**ext4**，请使用以下命令：

      ```#resize2fs /dev/sda4```

   1. 使用以下命令验证**df-Th**增加的文件系统大小：

      `#df -Th`

      示例输出：

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

如前面的示例所示，我们可以看到 OSDisk 的文件系统大小已增加。

### <a name="rhel-7x-with-lvm"></a>RHEL 7、windows with LVM

1. 停止 VM。
1. 从门户增加 OSDisk 的大小。
1. 启动 VM。

重新启动 VM 后，请执行以下步骤：

   1. 使用以下命令以**根用户**身份访问 VM：
   
      `#sudo su`

   1. 安装**gptfdisk**包，它需要增加操作系统磁盘的大小。

      `#yum install gdisk -y`

   1. 若要查看磁盘上的最大可用扇区，请运行以下命令：

      `#sgdisk -e /dev/sda`

   1. 调整分区大小，但不使用以下命令将其删除。 **Parted**命令有一个名为**resizepart**的选项，用于重设分区大小，而不会将其删除。 Resizepart 后面的数字4指示调整第四个（第四个）分区的大小。

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. 运行以下命令以验证分区是否已增加：

      `#lsblk`

      以下输出显示已将 **/dev/sda4**分区调整为 99 GB。

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. 使用以下命令调整**物理卷的大小（PV）**：

      `#pvresize /dev/sda4`

      以下输出显示 PV 已调整大小为 99.02 GB。

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. 在下面的示例中`/dev/mapper/rootvg-rootlv` ，使用以下命令将大小从2gb 调整为12GB （增加10gb），这也会调整文件系统的大小：

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      示例输出：

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
      Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
      Logical volume rootvg/rootlv successfully resized.
      meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=0 spinodes=0
      data     =                       bsize=4096   blocks=524288, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 524288 to 3145728
      ```
         
   1. 验证是否`/dev/mapper/rootvg-rootlv`增加了文件系统大小或未使用以下命令：

      `#df -Th /`

      示例输出：

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

      > [!NOTE]
      > 若要使用相同的过程调整任何其他逻辑卷的大小，请在步骤7中更改**lv**名称

## <a name="next-steps"></a>后续步骤

- [调整磁盘大小](expand-disks.md)