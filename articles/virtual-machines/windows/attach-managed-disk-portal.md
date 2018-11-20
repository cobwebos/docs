---
title: 将托管数据磁盘附加到 Windows VM - Azure | Microsoft Docs
description: 如何使用 Azure 门户将托管数据磁盘附加到 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 8d83af114ebb5e5ff78372897d3e08ed592d4012
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093895"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>使用 Azure 门户将托管数据磁盘附加到 Windows VM

本文介绍如何使用 Azure 门户将新的托管数据磁盘附加到 Windows 虚拟机 (VM)。 VM 的大小决定了可以附加的数据磁盘数量。 有关详细信息，请参阅[虚拟机的大小](sizes.md)。


## <a name="add-a-data-disk"></a>添加数据磁盘

1. 从 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“虚拟机”。
2. 从列表中选择虚拟机。
3. 在“虚拟机”页上选择“磁盘”。
4. 在“磁盘”页上选择“添加数据磁盘”。
5. 在新磁盘的下拉列表中，选择“创建磁盘”。
6. 在“创建托管磁盘”页中，键入磁盘名称，并根据需要调整其他设置。 完成操作后，选择“创建”。
7. 在“磁盘”页中，选择“保存”，以保存 VM 的新磁盘配置。
8. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。


## <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

1. 连接到 VM。
1. 在正在运行的 VM 中选择 Windows“开始”菜单，然后在搜索框中输入 **diskmgmt.msc**。 此时会打开“磁盘管理”控制台。
2. “磁盘管理”识别出新的未初始化磁盘，并显示“初始化磁盘”窗口。
3. 请确保选择新磁盘，然后选择“确定”对其进行初始化。
4. 新的磁盘显示为“未分配”。 右键单击磁盘上任意位置，选择“新建简单卷”。 此时会打开“新建简单卷向导”窗口。
5. 完成向导中的每一步，保留所有默认值，完成后，选择“完成”。
6. 关闭“磁盘管理”。
7. 此时出现一个弹出窗口，通知你需要先格式化新磁盘才能使用新磁盘。 选择“格式化磁盘”。
8. 在“格式化新磁盘”窗口中，检查设置，然后选择“启动”。
9. 此时出现一条警告，通知你格式化磁盘会擦除所有数据。 选择“确定”。
10. 完成格式化后，选择“确定”。

## <a name="use-trim-with-standard-storage"></a>将 TRIM 与标准存储配合使用

如果使用标准存储 (HDD)，则应启用 **TRIM** 命令。 **TRIM** 命令会放弃磁盘上未使用的块，以便仅对实际使用的存储计费。 如果创建大型文件，然后将其删除，使用 **TRIM** 便可节省成本。 

若要检查 **TRIM** 设置，请在 Windows VM 上打开命令提示符并输入以下命令：

```
fsutil behavior query DisableDeleteNotify
```

如果该命令返回 0，则表示正确启用了 **TRIM**。 如果该命令返回 1，则运行以下命令以启用 **TRIM**：

```
fsutil behavior set DisableDeleteNotify 0
```

从磁盘中删除数据后，通过使用 **TRIM** 运行碎片整理，可以确保正常刷新 **TRIM** 操作：

```
defrag.exe <volume:> -l
```

也可以通过设置卷格式确保裁剪整个卷。

## <a name="next-steps"></a>后续步骤

- 还可以[使用 PowerShell 附加数据磁盘](attach-disk-ps.md)。
- 如果应用程序需要使用 *D:* 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
