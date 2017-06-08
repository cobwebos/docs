---
title: "将非托管数据磁盘附加到 Windows VM - Azure | Microsoft Docs"
description: "如何使用 Resource Manager 部署模型在 Azure 门户中将新的或现有的非托管数据磁盘附加到 Windows VM。"
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
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d02f92a8809efd6f58312af8cb40739299ea28f6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 门户中将非托管数据磁盘附加到 Windows VM

本文介绍如何通过 Azure 门户将新的和现有的非托管磁盘附加到 Windows 虚拟机。 还可以[使用 PowerShell 附加数据磁盘](./attach-disk-ps.md)。 在开始之前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](sizes.md)。
* 若要使用高级存储，需要使用 DS 系列或 GS 序列虚拟机。 可以从高级存储帐户和标准存储帐户通过这些虚拟机使用磁盘。 高级存储只在某些区域可用。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 对于新磁盘，你不必首先创建它，因为 Azure 将在附加磁盘时创建该磁盘。


还可以[使用 Powershell 附加数据磁盘](attach-disk-ps.md)。


## <a name="find-the-virtual-machine"></a>查找虚拟机
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中单击“虚拟机”。
3. 从列表中选择虚拟机。
4. 在“虚拟机”边栏选项卡中，单击“磁盘”。
   
按照附加[新磁盘](#option-1-attach-a-new-disk)或[现有磁盘](#option-2-attach-an-existing-disk)的说明继续操作。

## <a name="option-1-attach-and-initialize-a-new-disk"></a>选项 1：附加并初始化新的磁盘
1. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
2. 在“附加托管磁盘”边栏选项卡中，在“名称”中键入磁盘名称，然后在“源类型”中选择“新建(空磁盘)”。
3. 在“存储容器”下方，单击“浏览”按钮，浏览到要存储新 VHD 的存储帐户和容器，然后单击“选择”。 
  
   ![检查磁盘设置](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. 完成数据磁盘设置后，单击“确定”。
4. 返回“磁盘”边栏选项卡，单击“保存”，将磁盘添加到 VM 配置。


### <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

1. 连接到虚拟机。 有关说明，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
1. 单击 VM 中的“开始”菜单，键入“diskmgmt.msc”，然后点击“Enter”。 这会启动“磁盘管理”管理单元。
2. “磁盘管理”识别出空的未初始化磁盘，弹出“初始化磁盘”窗口。
3. 请确保已选择新磁盘，单击“确定”对其进行初始化。
4. 现在新磁盘显示为“未分配”。 右键单击磁盘上任意位置，选择“新建简单卷”。 此时会启动新建简单卷向导。
5. 完成向导中的每一步，保留所有默认值，完成后，选择“完成”。
6. 关闭“磁盘管理”。
7. 随即弹出需要先格式化新磁盘才能使用新磁盘的提示。 单击“格式化磁盘”。
8. 在“格式化新磁盘”对话框中，检查设置，然后单击“启动”。
9. 随即显示格式化磁盘会清除所有数据的警告，请单击“确定”。
10. 格式化完成后，单击“确定”。


## <a name="option-2-attach-an-existing-disk"></a>选项 2：附加现有磁盘
1. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
2. 在“附加非托管磁盘”边栏选项卡的“源类型”中，选择“现有 blob”。

    ![检查磁盘设置](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. 单击“浏览”，导航到现有 VHD 所在的存储帐户和容器。 单击 VHD，然后单击“选择”。
4. 在“附加非托管磁盘”边栏选项卡中单击“确定”。
5. 在“磁盘”边栏选项卡中，单击“保存”，将磁盘添加到 VM 的配置。
   


## <a name="use-trim-with-standard-storage"></a>将 TRIM 与标准存储配合使用

如果使用标准存储 (HDD)，则应启用 TRIM。 TRIM 会放弃磁盘上未使用的块，以便仅对实际使用的存储进行收费。 如果创建了较大的文件，然后将其删除，这样可以节省成本。 

可以运行此命令来检查 TRIM 设置。 打开 Windows VM 上的命令提示符，键入：

```
fsutil behavior query DisableDeleteNotify
```

如果该命令返回 0，则正确启用了 TRIM。 如果该命令返回 1，则运行以下命令以启用 TRIM：
```
fsutil behavior set DisableDeleteNotify 0
```
                
从磁盘中删除数据后，可以通过使用 TRIM 运行碎片整理确保 TRIM 操作刷新正常：

```
defrag.exe <volume:> -l
```

## <a name="next-steps"></a>后续步骤
如果应用程序需要使用 D: 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。


