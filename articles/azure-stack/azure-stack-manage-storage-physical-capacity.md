---
title: "管理 Azure 堆栈的物理内存容量 |Microsoft 文档"
description: "监视和管理 Azure 堆栈可用存储空间。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/13/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: b922790d51c7028c37bb5863d43e99e19790488c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>管理 Azure 堆栈的物理内存容量

*适用于：Azure Stack 集成系统*

若要增加 Azure 堆栈的总可用内存容量，可以添加更多的内存。 Azure 堆栈中物理服务器也称为*缩放单元节点*。 所有成员的单个缩放单位的缩放单元节点必须具有相同的内存量。

> [!note]  
> 在继续之前，请参阅硬件制造商文档以查看是否经过内存升级您的制造商支持的物理内存升级。 OEM 硬件供应商支持协定可能需要供应商执行的物理服务器机架布局和设备的固件更新。

以下流程图显示将内存添加到每个缩放单元节点的一般过程。

![将内存添加到每个缩放单元节点](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>将内存添加到现有节点
以下步骤提供添加内存过程的高级概述。 

> [!Warning]  
不用按照这些步骤而不会引用您 OEM 提供的文档。

> [!Warning]  
因为滚动内存升级不受支持，必须关闭整个缩放单位。

1. 停止使用中所述的步骤的 Azure 堆栈[启动和停止 Azure 堆栈](azure-stack-start-and-stop.md)文章。
2. 升级使用硬件制造商提供的文档的每台物理计算机上的内存。
3. 开始使用中的步骤的 Azure 堆栈[启动和停止 Azure 堆栈](azure-stack-start-and-stop.md)文章。

## <a name="next-steps"></a>后续步骤

 - 若要了解如何管理 Azure 堆栈以查找，恢复，并回收根据业务需要的存储容量中的存储帐户，请参阅[管理 Azure 堆栈中的存储帐户](azure-stack-manage-storage-accounts.md)。
 - 若要了解 Azure 堆栈云操作员监视和管理其 Azure 堆栈部署的存储容量，请参阅[管理 Azure 堆栈的存储容量](azure-stack-manage-storage-shares.md)。 