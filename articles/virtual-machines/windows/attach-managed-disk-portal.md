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
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9c29390756ac2cd925ed7d2989393e63ed0a1239
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>如何在 Azure 门户中将托管数据磁盘附加到 Windows VM

本文介绍了如何通过 Azure 门户将新的托管数据磁盘附加到 Windows 虚拟机。 在开始之前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](sizes.md)。
* 对于新磁盘，你不必首先创建它，因为 Azure 将在附加磁盘时创建该磁盘。

还可以[使用 Powershell 附加数据磁盘](attach-disk-ps.md)。



## <a name="add-a-data-disk"></a>添加数据磁盘
1. 在左侧菜单中单击“虚拟机”。****
2. 从列表中选择虚拟机。
3. 在“虚拟机”边栏选项卡上，单击“磁盘”。****
   4. 在“磁盘”边栏选项卡上，单击“+ 添加数据磁盘”。
5. 在新磁盘的下拉列表中，选择“创建空磁盘”。****
6. 在“创建托管磁盘”边栏选项卡中，键入磁盘名称，并根据需要调整其他设置。**** 完成后，单击“创建”。****
7. 在“磁盘”边栏选项卡中，单击“保存”，保存 VM 的新磁盘配置。****
6. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。****


## <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

1. 连接到 VM。
1. 单击 VM 中的“开始”菜单，键入“diskmgmt.msc”，然后点击“Enter”。**** 这会启动“磁盘管理”管理单元。
2. “磁盘管理”识别出空的未初始化磁盘，弹出“初始化磁盘”窗口。
3. 请确保已选择新磁盘，单击“确定”对其进行初始化。****
4. 现在新磁盘显示为“未分配”。**** 右键单击磁盘上任意位置，选择“新建简单卷”。**** 此时会启动“新建简单卷向导”。****
5. 完成向导中的每一步，保留所有默认值，完成后，选择“完成”。****
6. 关闭“磁盘管理”。
7. 随即弹出提示：需要先格式化新磁盘才能使用新磁盘。 单击“格式化磁盘”。****
8. 在“格式化新磁盘”对话框中，检查设置，然后单击“启动”。****
9. 随即显示格式化磁盘会清除所有数据的警告，请单击“确定”。****
10. 格式化完成后，单击“确定”。****

## <a name="next-steps"></a>后续步骤
如果应用程序需要使用 D: 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

