---
title: 将数据磁盘附加到 Linux VM | Microsoft Docs
description: 使用门户将新的或现有数据磁盘附加到 Linux VM。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 4acfe53d68db3192c1f6c3c9e5f91b55bd5df7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>使用门户将数据磁盘附加到 Linux VM 
本文介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到 Linux 虚拟机。 也可以[在 Azure 门户中将数据磁盘附加到 Windows VM](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

将磁盘附加到 VM 前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要使用高级存储，需要使用 DS 系列或 GS 序列虚拟机。 可以将高级磁盘和标准磁盘同时用于这些虚拟机。 高级存储只在某些区域可用。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加到虚拟机的磁盘实际上是存储在 Azure 中的 .vhd 文件。 有关详细信息，请参阅[关于虚拟机的磁盘和 VHD](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


## <a name="find-the-virtual-machine"></a>查找虚拟机
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击左侧菜单中的“虚拟机” 。
3. 从列表中选择虚拟机。
4. 在“虚拟机”页的“概要”中，单击“磁盘”。
   
    ![打开磁盘设置](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>附加新磁盘

1. 在“磁盘”窗格上，单击“+ 添加数据磁盘”。
2. 单击“名称”的下拉列表菜单，并选择“创建磁盘”：

    ![创建 Azure 托管磁盘](./media/attach-disk-portal/create-new-md.png)

3. 输入托管磁盘的名称。 查看默认设置，根据需要更新，并单击“创建”。
   
   ![检查磁盘设置](./media/attach-disk-portal/create-new-md-settings.png)

4. 单击“保存”以创建托管磁盘并更新 VM 配置：

   ![保存新的 Azure 托管磁盘](./media/attach-disk-portal/confirm-create-new-md.png)

5. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。 托管磁盘是顶级资源，因此磁盘会显示在资源组的根部：

   ![资源组中的 Azure 托管磁盘](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>附加现有磁盘
1. 在“磁盘”窗格上，单击“+ 添加数据磁盘”。
2. 单击“名称”的下拉列表菜单，查看 Azure 订阅可访问的现有托管磁盘列表。 选择要附加的托管磁盘：

   ![附加现有 Azure 管理磁盘](./media/attach-disk-portal/select-existing-md.png)

3. 单击“保存”以附加现有托管磁盘并更新 VM 配置：
   
   ![保存 Azure 托管磁盘更新](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. 在 Azure 将磁盘附加到虚拟机之后，磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。



## <a name="next-steps"></a>后续步骤
还可使用 Azure CLI [附加数据磁盘](add-disk.md)。
