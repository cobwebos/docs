---
title: "将磁盘附加到 VM | Microsoft Docs"
description: "将数据磁盘附加到使用经典部署模型创建的 Windows 虚拟机并进行初始化。"
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 6e2b33492344acedf1c79d4f67a6d69ad163c14c


---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>将数据磁盘附加到使用经典部署模型创建的 Windows 虚拟机
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 如果想要使用新的门户，请参阅[如何在 Azure 门户中将数据磁盘附加到 Windows VM](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如果需要其他数据磁盘，可将空磁盘或现有的数据磁盘附加到 VM。 在这两种情况下，磁盘是驻留在 Azure 存储帐户中的 .vhd 文件。 如果是新磁盘，在附加磁盘之后，也需要将它初始化，使其可供 Windows VM 使用。

有关磁盘的更多详细信息，请参阅[关于虚拟机的磁盘和 VHD](virtual-machines-windows-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>初始化磁盘
1. 连接到虚拟机。 有关说明，请参阅[如何登录到运行 Windows Server 的虚拟机][logon]。
2. 在登录虚拟机后，打开“服务器管理器”。 在左窗格中，选择“文件和存储服务”。
   
    ![打开服务器管理器](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. 展开菜单并选择“磁盘”。
4. “磁盘”部分会列出磁盘。 在大多数情况下，会有磁盘 0、磁盘 1 和磁盘 2。 磁盘 0 是操作系统磁盘，磁盘 1 是临时磁盘，磁盘 2 是刚附加到 VM 的数据磁盘。 新的数据磁盘会将分区列为“未知”。 右键单击磁盘，然后选择“初始化”。
5. 在初始化磁盘时，系统会通知用户所有的数据将被擦除。 单击“是”以确认警告并初始化磁盘。 完成后，即会将分区列为“GPT”。 再次右键单击磁盘，然后选择“新建卷”。
6. 使用默认值完成向导操作。 完成向导后，“卷”部分将列出新卷。 现在，磁盘处于联机状态并已准备好存储数据。
   
   ![已成功初始化卷](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> VM 的大小决定可以在其上附加的磁盘数量。 有关详细信息，请参阅[虚拟机大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
> 
> 

## <a name="additional-resources"></a>其他资源
[如何从 Windows 虚拟机分离磁盘](virtual-machines-windows-classic-detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[关于虚拟机的磁盘和 VHD](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[logon]: virtual-machines-windows-classic-connect-logon.md



<!--HONumber=Dec16_HO1-->


