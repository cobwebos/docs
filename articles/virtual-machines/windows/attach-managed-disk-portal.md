---
title: "将托管数据磁盘附加到 Windows VM - Azure | Microsoft Docs"
description: "如何使用 Resource Manager 部署模型在 Azure 门户中将新的托管数据磁盘附加到 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 603d1c423ff2039915bdd3d5ed4a79b78d491edc
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 门户中将托管数据磁盘附加到 Windows VM

本文介绍如何在 Azure 门户中将新的托管数据磁盘附加到 Windows 虚拟机。 在开始之前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](sizes.md)。
* 对于新磁盘，不必首先创建它，因为 Azure 会在附加磁盘时创建该磁盘。

还可以[使用 Powershell 附加数据磁盘](attach-disk-ps.md)。



## <a name="add-a-data-disk"></a>添加数据磁盘
1. 在左侧菜单中单击“虚拟机”。
2. 从列表中选择虚拟机。
3. 在“虚拟机”页上，单击“磁盘”。
4. 在“磁盘”页上，单击“+ 添加数据磁盘”。
5. 在新磁盘的下拉列表中，选择“创建磁盘”。
6. 在“创建托管磁盘”页中，键入磁盘名称，并根据需要调整其他设置。 完成后，单击“创建”。
7. 在“磁盘”页中，单击“保存”，保存 VM 的新磁盘配置。
6. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。


## <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

1. 连接到 VM。
1. 单击 VM 中的“开始”菜单，键入“diskmgmt.msc”，并点击“Enter”。 此时将打开“磁盘管理”管理单元。
2. “磁盘管理”识别出新的未初始化磁盘，将弹出“初始化磁盘”窗口。
3. 请确保已选择新磁盘，单击“确定”对其进行初始化。
4. 新的磁盘显示为“未分配”。 右键单击磁盘上任意位置，选择“新建简单卷”。 此时将打开“新建简单卷”向导。
5. 完成向导中的每一步，保留所有默认值，完成后，选择“完成”。
6. 关闭“磁盘管理”。
7. 随即弹出需要先格式化新磁盘才能使用新磁盘的提示。 单击“格式化磁盘”。
8. 在“格式化新磁盘”对话框中，检查设置，并单击“启动”。
9. 随即显示格式化磁盘会清除所有数据的警告，请单击“确定”。
10. 格式化完成后，单击“确定”。

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

也可以通过设置卷格式确保裁剪整个卷。

## <a name="next-steps"></a>后续步骤
如果应用程序需要使用 D: 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
