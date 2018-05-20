---
title: 管理 Azure Stack 的物理内存容量 | Microsoft Docs
description: 监视和管理 Azure Stack 的可用存储空间。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: dc572353c2e27ddfbae2398f1aece56586955e26
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
<!---Loc Comment: Please, check the comment in coversation section---> 
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>管理 Azure Stack 的物理内存容量

*适用于：Azure Stack 集成系统*

若要增加 Azure Stack 的总可用内存容量，可以添加更多内存。 在 Azure Stack 中，物理服务器也称为*缩放单位节点*。 所有属于单一缩放单位的缩放单位节点都必须具有相同的内存量。

> [!note]  
> 在继续之前，请参考硬件制造商提供的文档，了解如果您的制造商支持物理内存升级。 OEM 硬件供应商支持合同可能会要求供应商进行物理服务器机架放置和设备固件更新。

以下流程图显示将内存添加到每个缩放单位节点的一般过程。

![将内存添加到每个缩放单位节点](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>将内存添加到现有节点
以下步骤提供添加内存过程的高级概述。 

> [!Warning]  
请勿在未参考 OEM 提供的文档的情况下按照这些步骤操作。

> [!Warning]  
由于不支持滚动内存升级，因此必须关闭整个缩放单位。

1. 按照[启动和停止 Azure Stack](azure-stack-start-and-stop.md) 一文中所述的步骤，停止 Azure Stack。
2. 使用硬件制造商的文档升级每台物理计算机上的内存。
3. 按照[启动和停止 Azure Stack](azure-stack-start-and-stop.md) 一文中的步骤，启动 Azure Stack。

## <a name="next-steps"></a>后续步骤

 - 若要了解如何管理 Azure 中的存储帐户，以根据业务需求查找、恢复和回收存储容量，请参阅[管理 Azure Stack 中的存储帐户](azure-stack-manage-storage-accounts.md)。
 - 若要了解 Azure Stack 云操作员如何监视和管理其 Azure Stack 部署的存储容量，请参阅[管理 Azure Stack 的存储容量](azure-stack-manage-storage-shares.md)。 
