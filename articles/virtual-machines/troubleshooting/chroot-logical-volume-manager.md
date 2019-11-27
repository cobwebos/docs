---
title: 使用 chroot 恢复 Linux Vm，其中使用 LVM （逻辑卷管理器）-Azure Vm
description: 通过 LVMs 恢复 Linux Vm。
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 9c3f054a1bae745e4ee7ce9e3bddca3c9bf31083
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535998"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>当无权访问 Azure 串行控制台且磁盘布局使用 LVM （逻辑卷管理器）时，Linux VM 的故障排除

此故障排除指南适用于以下情况： Linux VM 未启动，ssh 无法进行，并且基础文件系统布局是使用 LVM （逻辑卷管理器）配置的。

## <a name="take-snapshot-of-the-failing-vm"></a>获取发生故障的 VM 的快照

拍摄受影响 VM 的快照。 

快照随后会附加到 "**修复**VM"。 按照[此处](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal)的说明操作，了解如何拍摄**快照**。

## <a name="create-a-rescue-vm"></a>创建修复 VM
通常，建议使用相同或类似操作系统版本的 "修复 VM"。 使用受影响 VM 的同一**区域**和**资源组**

## <a name="connect-to-the-rescue-vm"></a>连接到修复 VM
使用 ssh 连接到 "**修复**VM"。 提升权限，并使用超级用户

`sudo su -`

## <a name="attach-the-disk"></a>附加磁盘
将磁盘附加到从之前拍摄的快照进行的 "**修复**VM"。

Azure 门户-> 选择 "**修复**VM->**磁盘**" 

![创建磁盘](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

填充字段。 为新磁盘分配名称，选择与快照、受影响的 VM 和修复 VM 相同的资源组。

**源类型**为**Snapshot** 。
**源快照**是以前创建的**快照**的名称。

![创建磁盘2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

为附加的磁盘创建装入点。

`mkdir /rescue`

运行**fdisk-l**命令以验证是否已附加快照磁盘，并列出所有可用的设备和分区

`fdisk -l`

大多数情况下，附加的快照磁盘将被视为 **/dev/sdc**显示两个分区 **/dev/sdc1**和 **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

**\*** 指示启动分区，这两个分区都将装载。

运行命令**lsblk** ，查看受影响 VM 的 LVMs

`lsblk`

![运行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


验证是否显示受影响 VM 中的 LVMs。
否则，请使用以下命令启用它们并重新运行**lsblk**。
在继续操作之前，请确保已连接的磁盘中的 LVMs 可见。

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

找到用于装载包含/（根）分区的逻辑卷的路径。 它包含配置文件，如/etc/default/grub

在此示例中，采用前面的**lsblk**命令的输出**rootvg-rootlv**是要装载的正确**根**LV，可在下一个命令中使用。

下一个命令的输出将显示要为**根**LV 装载的路径

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

继续在目录/rescue 上装载此设备

`mount /dev/rootvg/rootlv /rescue`

装载在/rescue/boot 上设置了**启动标志**的分区

`
mount /dev/sdc1 /rescue/boot
`

使用**lsblk**命令验证附加磁盘的文件系统是否已正确装载

![运行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

或**df-Th**命令

![加](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>获取 chroot 访问

获取**chroot**访问权限，这将允许你执行各种修补程序，每个 Linux 分发版都存在细微的差异。

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

如果出现错误，如：

**chroot：无法运行命令 "/bin/bash"：没有此类文件或目录**

尝试装载**usr**逻辑卷

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> 在**chroot**环境中执行命令时，请注意，将针对附加的 OS 磁盘（而不是本地**修复**VM）运行这些命令。 

命令可用于安装、删除和更新软件。 为了修复错误，对 Vm 进行故障排除。


执行 lsblk 命令，/rescue 现在为/，/rescue/boot 为/boot ![Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>执行修复

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>示例 1-将 VM 配置为从其他内核启动

常见的方案是强制 VM 从以前的内核启动，因为当前安装的内核可能已损坏或升级未正确完成。


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*演练*

**Grep**命令列出了由**grub**识别的内核。
![内核](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv 列表**显示在下一次启动 ![内核默认情况下将加载哪个内核](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2**用于更改到另一个内核 ![grub2 集](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv**列表显示 ![新内核下一次启动时将加载哪个内核](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2 grub2-mkconfig**使用 ![grub2 grub2-mkconfig 所需的版本重新生成](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>示例 2-升级包

失败的内核升级可能会导致 VM 无法引导。
装载所有逻辑卷以允许删除或重新安装包

运行**lvs**命令来验证哪些**lvs**可用于装载，每个已迁移的 VM 或来自另一个云提供商的 VM 将因配置而异。

退出**chroot**环境，装载所需的**LV**

![高级](./media/chroot-logical-volume-manager/advanced.png)

现在，通过运行

`chroot /rescue`

所有 LVs 应作为已装入分区显示

![高级](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

查询安装的**内核**

![高级](./media/chroot-logical-volume-manager/rpm-kernel.png)

如果需要，请将**内核**
![高级](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>示例 3-启用串行控制台
如果无法访问 Azure 串行控制台，请验证 Linux VM 的 GRUB 配置参数并更正它们。 可[在此文档中](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)找到详细信息


## <a name="exit-chroot-and-swap-the-os-disk"></a>退出 chroot 并交换 OS 磁盘

修复此问题后，请继续卸载并将其与修复 VM 分离，使其能够与受影响的 VM OS 磁盘交换。

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

从修复 VM 分离磁盘，并执行磁盘交换。

从门户**磁盘**中选择 VM，并选择 "**分离**
![分离磁盘"](./media/chroot-logical-volume-manager/detach-disk.png) 

保存更改 ![保存分离](./media/chroot-logical-volume-manager/save-detach.png) 

磁盘现在将变为可用，允许其与受影响 VM 的原始 OS 磁盘交换。

在 Azure 门户中导航到发生故障的 VM，并选择 "**磁盘**" -> **交换 OS 磁盘**
![交换磁盘](./media/chroot-logical-volume-manager/swap-disk.png) 

完成 "**选择磁盘**" 这一步骤刚刚分离的快照磁盘中的字段。 还需要受影响 VM 的 VM 名称，然后选择 **"确定"**

![新 os 磁盘](./media/chroot-logical-volume-manager/new-osdisk.png) 

如果 VM 正在运行，则磁盘交换会将其关闭，并在磁盘交换操作完成后重新启动 VM。


## <a name="next-steps"></a>后续步骤
详细了解[Azure 串行控制台]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
