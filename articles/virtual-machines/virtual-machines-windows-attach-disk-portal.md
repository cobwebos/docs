---
title: "将数据磁盘附加到 Windows VM | Microsoft Docs"
description: "如何使用资源管理器部署模型在 Azure 门户中将新磁盘或现有数据磁盘附加到 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: e7ec8715087abaf5c96a6327d4bcde0ad3fd547a


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 门户中将数据磁盘附加到 Windows VM。
本文向你介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到 Windows 虚拟机。 也可以[在 Azure 门户中将磁盘数据附加到 Linux VM](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 在开始之前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 对于新磁盘，你不必首先创建它，因为 Azure 将在附加磁盘时创建该磁盘。
* 对于现有磁盘，Azure 存储帐户中必须要有可用的 .vhd 文件。 你可以使用已经存在的 .vhd（如果该磁盘没有附加到另一虚拟机），或者将你自己的 .vhd 文件上载到存储帐户。

还可以[使用 Powershell 附加数据磁盘](virtual-machines-windows-ps-manage.md#add-a-data-disk-to-a-virtual-machine)。

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="a-idinitializeinwsahow-to-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>如何：在 Windows Server 中初始化新的数据磁盘
1. 连接到虚拟机。 有关说明，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
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

## <a name="next-steps"></a>后续步骤
如果应用程序需要使用 D: 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。




<!--HONumber=Nov16_HO3-->


