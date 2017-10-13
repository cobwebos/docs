---
title: "从 Linux VM 中分离数据磁盘 - Azure | Microsoft Docs"
description: "了解如何使用 CLI 2.0 或 Azure 门户从 Azure 虚拟机中分离数据磁盘。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: 3f29547e1da6028b1e4b91d9e29fd3bcdfe08d50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>如何从 Linux 虚拟机中分离数据磁盘

不再需要附加到虚拟机的数据磁盘时，可以轻松地分离它。 这会从虚拟机中删除磁盘，但不会从存储中删除它。 

> [!WARNING]
> 如果分离磁盘，它将不会自动删除。 如果订阅了高级存储，则将继续承担该磁盘的存储费用。 有关详细信息，请参阅[使用高级存储时的定价和计费方式](../../storage/common/storage-premium-storage.md#pricing-and-billing)。 
> 
> 

如果希望再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个虚拟机。  

## <a name="detach-a-data-disk-using-cli-20"></a>使用 CLI 2.0 分离数据磁盘

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

磁盘保留在存储中，但不再附加到虚拟机。


## <a name="detach-a-data-disk-using-the-portal"></a>使用门户分离数据磁盘
1. 在门户中心中，选择“虚拟机”。
2. 选择具有要分离的数据磁盘的虚拟机，并单击“停止”以解除分配 VM。
3. 在虚拟机边栏选项卡中，选择“磁盘”。
4. 在“磁盘”边栏选项卡的顶部，选择“编辑”。
5. 在“磁盘”边栏选项卡中，转到要分离的数据磁盘最右侧，并单击![分离按钮图像](./media/detach-disk/detach.png)分离按钮。
5. 删除磁盘后，单击边栏选项卡顶部的“保存”。
6. 在虚拟机边栏选项卡中，单击“概述”，并单击边栏选项卡顶部的“开始”按钮重启 VM。

磁盘保留在存储中，但不再附加到虚拟机。








## <a name="next-steps"></a>后续步骤
要重新使用数据磁盘，只需[将其附加到其他 VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 即可。

