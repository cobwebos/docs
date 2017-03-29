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
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 9a5eceff35420c1560ff1185a1e4fb1286d74250
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>如何从 Linux 虚拟机中分离数据磁盘

当你不再需要附加到虚拟机的数据磁盘时，你可以轻松地分离它。 这将从虚拟机中删除磁盘，但不会从存储中删除它。 

> [!WARNING]
> 如果你分离磁盘，它将不会自动删除。 如果你订阅了高级存储，则将继续承担该磁盘的存储费用。 有关详细信息，请参阅[使用高级存储时的定价和计费方式](../storage/storage-premium-storage.md#pricing-and-billing)。 
> 
> 

若果你希望再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个虚拟机。  

## <a name="detach-a-data-disk-using-cli-20"></a>使用 CLI 2.0 分离数据磁盘

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

磁盘保留在存储中，但不再附加到虚拟机。


## <a name="detach-a-data-disk-using-the-portal"></a>使用门户分离数据磁盘
1. 在门户中心中，选择“虚拟机”。
2. 选择具有要分离的数据磁盘的虚拟机，然后单击“停止”以解除分配 VM。
3. 在虚拟机边栏选项卡中，选择“磁盘”。
4. 在“磁盘”边栏选项卡的顶部，选择“编辑”。
5. 在“磁盘”边栏选项卡中，转到要分离的数据磁盘最右侧，然后单击![分离按钮图像](./media/virtual-machines-common-detach-disk/detach.png)分离按钮。
5. 删除磁盘后，单击边栏选项卡顶部的“保存”。
6. 在虚拟机边栏选项卡中，单击“概述”，然后单击边栏选项卡顶部的“开始”按钮重启 VM。

磁盘保留在存储中，但不再附加到虚拟机。








## <a name="next-steps"></a>后续步骤
若要重新使用数据磁盘，只需[将其附加到其他 VM](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 即可。


