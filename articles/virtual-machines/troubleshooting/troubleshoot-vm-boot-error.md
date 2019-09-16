---
title: Azure 虚拟机启动错误
description: 虚拟机无法启动，因为虚拟机进入了一个修复控制台
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 9995b9049378a0ab4f3450ec577d034598d171e9
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984841"
---
# <a name="vm-boot-error"></a>VM 启动错误

我们已确定虚拟机（VM）输入了 "修复" 控制台。 如果你的 Linux VM 最近应用了内核更改（如内核升级），并且由于在启动过程中出现内核错误，则会出现此问题。 在启动过程中，当启动加载程序尝试查找 Linux 内核并将启动控制移交给它时，VM 将在移交失败时进入一个修复控制台。

如果发现将来无法连接到 VM，可以使用 Azure 门户中的 "启动诊断" 边栏选项卡查看 VM 的屏幕截图。 这可能有助于诊断此问题并确定类似的启动错误是否是原因。

## <a name="recommended-steps"></a>建议的步骤

根据接收到的错误，执行以下缓解步骤：

### <a name="error---unknown-filesystem"></a>错误-未知文件系统

* 如果收到错误 "**未知文件**系统"，则此错误可能是由于启动分区上的文件系统损坏或内核配置不正确引起的。

   * 对于文件系统问题，请按照[Linux 恢复：由于文件系统错误（fsck，inode）](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)，无法通过 SSH 连接到 Linux VM。
   * 对于内核问题，请按照[Linux 恢复：手动修复与内核问题](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)或[Linux 恢复相关的非启动问题：使用 chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)解决与内核问题相关的非启动问题。
   
### <a name="error---file-not-found"></a>错误-找不到文件

* 如果遇到错误**错误15：找不到文件或未找到**初始 RAM 磁盘或**initrd/initramfs 文件**，请按照以下步骤操作。

    * 对于缺少的文件`/boot/grub2/grub.cfg`或`initrd/initramfs`继续执行以下过程：

    1. 确保`/etc/default/grub`存在并且具有正确/所需的设置。 如果不知道哪些是默认设置，可以使用工作的 VM 进行检查。

    2. 接下来，运行以下命令以重新生成其配置：`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 如果缺少的文件是`/boot/grub/menu.lst`，则此错误适用于较旧的操作系统版本（**RHEL**1.x、 **Centos** 1.x 和**Ubuntu 14.04**），因此这些命令可能会有所不同。 你将需要启动旧服务器并进行测试，以确保提供正确的命令。

### <a name="error---no-such-partition"></a>错误-无此分区

* 如果收到错误 "**没有此类分区**"，请参阅[在尝试扩展 OS 驱动器后尝试启动 VM 时，出现 "找不到此类分区" 错误](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)。

### <a name="error---grubcfg-file-not-found"></a>错误-找不到 grub 文件

* 如果遇到错误 **/boot/grub2/grub.cfg 文件**，请执行以下步骤。

    * 对于缺少的文件`/boot/grub2/grub.cfg`或`initrd/initramfs`继续执行以下过程：

    1. 确保`/etc/default/grub`存在并且具有正确/所需的设置。 如果不知道哪些是默认设置，可以使用工作的 VM 进行检查。

    2. 接下来，运行以下命令以重新生成其配置`grub2-mkconfig -o /boot/grub2/grub.cfg`：。

   * 如果缺少的文件是`/boot/grub/menu.lst`，则此错误适用于较旧的操作系统版本（**RHEL**1.x、 **Centos** 1.x 和**Ubuntu 14.04**），因此这些命令可能会延迟。 加速旧服务器并对其进行测试，以确保提供正确的命令。

## <a name="next-steps"></a>后续步骤

* [Azure 虚拟机代理概述](../extensions/agent-windows.md)
* [适用于 Windows 的虚拟机扩展和功能](../extensions/features-windows.md)

