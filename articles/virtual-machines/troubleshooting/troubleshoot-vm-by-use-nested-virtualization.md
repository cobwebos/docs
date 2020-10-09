---
title: 使用 Azure 中的嵌套虚拟化对故障的 Azure VM 进行故障排除 |Microsoft Docs
description: 如何使用 Azure 中的嵌套虚拟化排查问题 Azure VM
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76119612"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>使用 Azure 中的嵌套虚拟化排查故障 Azure VM

本文介绍如何在 Microsoft Azure 中创建嵌套虚拟化环境，以便可以在 Hyper-v 主机上装载错误 VM 的磁盘， (修复 VM) 进行故障排除。

## <a name="prerequisites"></a>先决条件

若要装载故障 VM，救援 VM 与故障 VM 必须使用相同类型的存储帐户（标准或高级）。

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>步骤 1：创建救援 VM 并安装 Hyper-V 角色

1.  创建一台新的救援 VM：

    -  操作系统：Windows Server 2016 Datacenter

    -  大小：任何支持嵌套虚拟化且至少具有双核的 V3 系列。 有关详细信息，请参阅[引入新的 Dv3 和 Ev3 VM 大小](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)。

    -  与故障 VM 相同的位置、存储帐户和资源组。

    -  选择与故障 VM 相同的存储类型（标准或高级）。

2.  创建救援 VM 后，通过远程桌面连接到救援 VM。

3.  在服务器管理器中，选择“管理” > “添加角色和功能”   。

4.  在“安装类型”  部分，选择“基于角色或基于功能的安装”  。

5.  在“选择目标服务器”  部分中，请确保已选择“救援 VM”。

6.  选择“HYPER-V 角色” > “添加功能”   。

7.  选择“功能”  部分上的“下一步”  。

8.  如果虚拟交换机可用，请选择它。 否则请选择“下一步”  。

9.  在“迁移”  部分上，选择“下一步”  。

10. 在“默认存储”  部分，选择“下一步”  。

11. 如果需要，请选中此框以自动重启服务器。

12. 选择“安装”  。

13. 允许服务器安装 Hyper-V 角色。 这需要几分钟的时间，服务器将自动重新启动。

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>步骤2：在修复 VM 的 Hyper-v 服务器上创建故障 VM

1.  为发生问题的 VM 的 OS 磁盘[创建快照磁盘](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk)，然后将该快照磁盘附加到救援 VM。

2.  通过远程桌面连接到救援 VM。

3.  打开磁盘管理 (diskmgmt.msc)。 请确保将故障 VM 的磁盘设置为“离线”  。

4.  打开 Hyper-V 管理器：在“服务器管理器”  中，选择“Hyper-V 角色”  。 右键单击服务器，然后选择“Hyper-V 管理器”  。

5.  在 HYPER-V 管理器中，右键单击救援 VM，然后选择“新建” > “虚拟机” > “下一步”    。

6.  键入 VM 的名称，然后选择“下一步”  。

7.  选择“第 1 代”  。

8.  设置启动内存为 1024 MB 或以上。

9. 如果适用，请选择已创建的 Hyper-V 网络交换机。 否则请转到下一页。

10. 选择“稍后附加虚拟硬盘”  。

    ![有关“稍后附加虚拟硬盘”选项的图像](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. 创建 VM 后，选择“完成”  。

12. 右键单击创建的 VM，然后选择“设置”  。

13. 依次选择“IDE Controller 0”  、“硬盘”  ，然后单击“添加”  。

    ![有关添加新硬盘的图像](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. 在“物理硬盘”  中，选择已附加到 Azure VM 的故障 VM 的磁盘。 如果没有看到任何列出的磁盘，请使用磁盘管理检查是否将磁盘设置为离线。

    ![有关安装磁盘的图像](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. 依次选择“应用”、“确定”   。

16. 双击 VM，然后启动它。

17. 现在你可以将此 VM 作为本地 VM 进行操作。 你可以按照所需的任何故障排除步骤操作。

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>步骤 3：替换故障 VM 所使用的 OS 磁盘

1.  重新在线获得 VM 后，关闭 Hyper-V 管理器中的 VM。

2.  [卸载并分离已修复的 OS 磁盘](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
)。
3.  [将 VM 使用的 OS 磁盘替换为修复的 OS 磁盘](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
)。

## <a name="next-steps"></a>后续步骤

如果在连接到 VM 时遇到问题，请参阅[对 Azure VM 的 RDP 连接进行故障排除](troubleshoot-rdp-connection.md)。 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[对 Windows VM 上的应用程序连接问题进行故障排除](troubleshoot-app-connection.md)。
