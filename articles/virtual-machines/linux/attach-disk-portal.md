---
title: "将数据磁盘附加到 Linux VM | Microsoft Docs"
description: "如何使用 Resource Manager 部署模型在 Azure 门户中将新磁盘或现有数据磁盘附加到 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 185e09366a8d68746415f4cbbac217d4ad558de7
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>如何在 Azure 门户中将数据磁盘附加到 Linux VM
本文介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到 Linux 虚拟机。 也可以[在 Azure 门户中将数据磁盘附加到 Windows VM](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 可以选择使用 Azure 托管磁盘或非托管磁盘。 托管磁盘由 Azure 平台处理，无需任何准备或位置来存储它们。 非托管磁盘需要存储帐户，且存在一些[适用的配额和限制](../../azure-subscription-service-limits.md#storage-limits)。 有关 Azure 托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../../storage/storage-managed-disks-overview.md)。

将磁盘附加到 VM 前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要使用高级存储，需要使用 DS 系列或 GS 序列虚拟机。 可以将高级磁盘和标准磁盘同时用于这些虚拟机。 高级存储只在某些区域可用。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加到虚拟机的磁盘实际上是存储在 Azure 中的 .vhd 文件。 有关详细信息，请参阅[关于虚拟机的磁盘和 VHD](../../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


## <a name="find-the-virtual-machine"></a>查找虚拟机
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单中，单击“虚拟机” 。
3. 从列表中选择虚拟机。
4. 在“虚拟机”边栏选项卡的“概要”中，单击“磁盘”。
   
    ![打开磁盘设置](./media/attach-disk-portal/find-disk-settings.png)

按照附加[托管磁盘](#use-azure-managed-disks)或[非托管磁盘](#use-unmanaged-disks)的说明继续操作。

## <a name="use-azure-managed-disks"></a>使用 Azure 托管磁盘

### <a name="attach-a-new-disk"></a>附加新磁盘

1. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
2. 单击“名称”的下拉列表菜单，然后选择“创建磁盘”：

    ![创建 Azure 托管磁盘](./media/attach-disk-portal/create-new-md.png)

3. 输入托管磁盘的名称。 查看默认设置，根据需要更新，然后单击“创建”。
   
   ![检查磁盘设置](./media/attach-disk-portal/create-new-md-settings.png)

4. 单击“保存”以创建托管磁盘并更新 VM 配置：

   ![保存新的 Azure 托管磁盘](./media/attach-disk-portal/confirm-create-new-md.png)

5. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。 托管磁盘是顶级资源，因此磁盘会显示在资源组的根部：

   ![资源组中的 Azure 托管磁盘](./media/attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>附加现有磁盘
1. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
2. 单击“名称”的下拉列表菜单，查看 Azure 订阅可访问的现有托管磁盘列表。 选择要附加的托管磁盘：

   ![附加现有 Azure 管理磁盘](./media/attach-disk-portal/select-existing-md.png)

3. 单击“保存”以附加现有托管磁盘并更新 VM 配置：
   
   ![保存 Azure 托管磁盘更新](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. 在 Azure 将磁盘附加到虚拟机之后，磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。

## <a name="use-unmanaged-disks"></a>使用非托管磁盘

### <a name="attach-a-new-disk"></a>附加新磁盘

1. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
2. 检查默认设置，根据需要更新，然后单击“确定”。
   
   ![检查磁盘设置](./media/attach-disk-portal/attach-new.png)
3. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。

### <a name="attach-an-existing-disk"></a>附加现有磁盘
1. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
2. 在“附加现有磁盘”下，单击“VHD 文件”。
   
   ![附加现有磁盘](./media/attach-disk-portal/attach-existing.png)
3. 在“存储帐户”下，选择帐户和容纳 .vhd 文件的容器。
   
   ![查找 VHD 位置](./media/attach-disk-portal/find-storage-container.png)
4. 选择 .vhd 文件
5. 在“附加现有磁盘”下，刚才选择的文件将出现在“VHD 文件”中。 单击 **“确定”**。
6. 在 Azure 将磁盘附加到虚拟机之后，磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。


## <a name="next-steps"></a>后续步骤
添加磁盘后，需要对它进行准备以供使用。 有关详细信息，请参阅[如何：在 Linux 中初始化新的数据磁盘](add-disk.md)。

