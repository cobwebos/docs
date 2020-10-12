---
title: Linux VM 启动到 Grub 救援
description: 虚拟机因进入救援控制台而无法启动
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 5a2fd7fcfdae8559bfb39bffff7c73c7082a86aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543276"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM 启动到 Grub 救援

我们确定你的虚拟机 (VM) 已进入救援控制台。 如果你的 Linux VM 最近应用了内核更改（例如内核升级），在启动过程中因内核错误而无法正常启动，则会发生此问题。 在启动过程中，当启动加载程序尝试查找 Linux 内核并将启动控制移交给它时，如果移交失败，则 VM 会进入救援控制台。

如果将来发现无法连接到 VM，则可使用 Azure 门户中的启动诊断边栏选项卡查看 VM 的屏幕截图。 这可能有助于诊断此问题并确定类似的启动错误是否是原因。

## <a name="recommended-steps"></a>建议的步骤

根据所收到的错误，按下面的缓解步骤操作：

### <a name="error---unknown-filesystem"></a>错误 - 未知的文件系统

* 如果出现“未知的文件系统”错误，则请注意，此错误可能是由于启动分区中的文件系统损坏，或者是由于内核配置不正确。 

   * 对于文件系统问题，请按 [Linux Recovery:Cannot SSH to Linux VM due to file system errors (fsck, inodes)](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes)（Linux 恢复：由于文件系统错误（fsck、inode）而无法通过 SSH 连接到 Linux VM）一文中的步骤操作。
   * 对于内核问题，请按照[如何从与内核相关的启动问题中恢复 Azure Linux 虚拟机](https://support.microsoft.com/help/4091524/how-recover-azure-linux-vm-from-kernel-related-boot-related-issues)或 [Linux 恢复：Fixing non-boot issues related to Kernel problems using chroot](http://linuxonazure.azurewebsites.net/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)（Linux 恢复：使用 chroot 修复与内核问题相关的非启动问题）一文中的步骤操作。
   
### <a name="error---file-not-found"></a>错误 - 找不到文件

* 如果出现“错误 15:  找不到文件或初始 RAM 磁盘”或“找不到 initrd/initramfs 文件”错误，请执行以下步骤。 

    * 如果 `/boot/grub2/grub.cfg` 或 `initrd/initramfs` 文件缺失，请完成以下过程：

    1. 确保 `/etc/default/grub` 存在且具有正确的/所需的设置。 如果不知道哪些是默认设置，可以通过工作 VM 进行检查。

    2. 接下来，运行以下命令以重新生成其配置：`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 如果缺失的文件为 `/boot/grub/menu.lst`，则该错误是针对旧版 OS（**RHEL 6.x**、**Centos 6.x** 和 **Ubuntu 14.04**）的，因此命令可能会有所不同。 需启动旧服务器并进行测试，确保提供了正确的命令。

### <a name="error---no-such-partition"></a>错误 - 无此类分区

* 如果出现“无此类分区”错误，  请参阅 [Case Scenario : "no such partition" error while trying to start the VM after attempting to extend the OS drive](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive)（案例情况：在尝试扩展 OS 驱动器后尝试启动 VM 时出现“无此类分区”错误）。

### <a name="error---grubcfg-file-not-found"></a>错误 - 找不到 grub.cfg 文件

* 如果出现“找不到 /boot/grub2/grub.cfg 文件”错误，请按以下步骤操作。 

    * 如果 `/boot/grub2/grub.cfg` 或 `initrd/initramfs` 文件缺失，请完成以下过程：

    1. 确保 `/etc/default/grub` 存在且具有正确的/所需的设置。 如果不知道哪些是默认设置，可以通过工作 VM 进行检查。

    2. 接下来，运行以下命令以重新生成其配置：`grub2-mkconfig -o /boot/grub2/grub.cfg`。

   * 如果缺失的文件为 `/boot/grub/menu.lst`，则该错误是针对旧版 OS（**RHEL 6.x**、**Centos 6.x** 和 **Ubuntu 14.04**）的，因此命令可能会有所不同。 请启动旧服务器并对其进行测试，确保提供了正确的命令。

## <a name="next-steps"></a>后续步骤

* [Azure 虚拟机代理概述](../extensions/agent-windows.md)
* [适用于 Windows 的虚拟机扩展和功能](../extensions/features-windows.md)
