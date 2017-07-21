---
title: "关于用于 Microsoft Azure Linux VM 的磁盘和 VHD | Microsoft Docs"
description: "了解 Azure 中 Linux 虚拟机磁盘和 VHD 的基础知识。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 8305abeb8d943cdcc78383d678387c12cedfd1c3
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017


---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>关于用于 Azure Linux VM 的磁盘和 VHD
就像其他任何计算机一样，Azure 中的虚拟机将磁盘用作存储操作系统、应用程序和数据的位置。 所有 Azure 虚拟机都至少有两个磁盘，即 Linux 操作系统磁盘和临时磁盘。 操作系统磁盘基于映像创建，操作系统磁盘和该映像实际上都存储在 Azure 存储帐户中的虚拟硬盘 (VHD) 内。 虚拟机还可以有一个或多个数据磁盘，而这些磁盘也存储为 VHD。 

本文介绍磁盘的不同用法，以及可以创建和使用的不同类型的磁盘。 本文也适用于 [Windows 虚拟机](storage-about-disks-and-vhds-windows.md)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM 使用的磁盘

下面介绍 VM 会如何使用磁盘。

## <a name="operating-system-disk"></a>操作系统磁盘
每个虚拟机都附加了一个操作系统磁盘。 默认情况下，它注册为 SATA 驱动器并标为 /dev/sda。 此磁盘的最大容量为 2048 GB。 

## <a name="temporary-disk"></a>临时磁盘
每个 VM 包含一个临时磁盘。 临时磁盘为应用程序和进程提供短期存储存储空间，仅用于存储页面或交换文件等数据。 在[维护事件](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)期间或[重新部署 VM](../virtual-machines/linux/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 时，临时磁盘上的数据可能会丢失。 在 VM 标准重启期间，临时驱动器上的数据应会保留。

在 Linux 虚拟机上，此磁盘通常为 **/dev/sdb**，并且由 Azure Linux 代理格式化和装入到 **/mnt**。 临时磁盘的大小因虚拟机的大小而异。 有关详细信息，请参阅 [Linux 虚拟机的大小](../virtual-machines/linux/sizes.md)。

有关 Azure 如何使用临时磁盘的详细信息，请参阅 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)（了解 Microsoft Azure 虚拟机上的临时驱动器）

## <a name="data-disk"></a>数据磁盘
数据磁盘是附加到虚拟机的 VHD，用于存储应用程序数据或其他需要保留的数据。 数据磁盘注册为 SCSI 驱动器并且带有所选择的字母标记。 每个数据磁盘的最大容量为 4095 GB。 虚拟机的大小决定了可附加的磁盘数目，以及可用来托管磁盘的存储类型。

> [!NOTE]
> 有关虚拟机容量的详细信息，请参阅 [Linux 虚拟机的大小](../virtual-machines/linux/sizes.md)。
> 

当你基于映像创建虚拟机时，Azure 将会创建操作系统磁盘。 如果你使用包含数据磁盘的映像，则 Azure 还会在创建虚拟机时创建数据磁盘。 否则，你需要在创建虚拟机后添加数据磁盘。

随时可以将数据磁盘添加到虚拟机，只需将该磁盘**附加**到虚拟机即可。 你可以使用已上传或复制到存储帐户的 VHD，也可以让 Azure 为你创建 VHD。 附加数据磁盘会将 VHD 文件与 VM 关联，方法是在 VHD 上放置“租约”，因此在仍附加 VHD 时无法从存储中删除它。

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>故障排除
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>后续步骤
* [附加磁盘](../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)可为 VM 添加额外的存储。
* [配置软件 RAID](../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 实现冗余。
* [捕获 Linux 虚拟机](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)，以便快速部署更多 VM。


