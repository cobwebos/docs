---
title: 按云简单删除 VMware 解决方案的节点 - Azure
description: 了解如何使用 CloudSimple 部署从 VMWare 中删除节点
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024732"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>按云简单从 Azure VMware 解决方案中删除节点

创建云简单节点后，将按流量计费。  必须删除节点以停止节点的计量。  删除未从 Azure 门户使用的节点。

## <a name="before-you-begin"></a>开始之前

只有在以下情况下才能删除节点：

* 删除使用节点创建的私有云。  要删除私有云，请参阅[通过云简单私有云删除 Azure VMware 解决方案](delete-private-cloud.md)。
* 通过缩小私有云，该节点已从私有云中删除。  要收缩私有云，请参阅[按云简单私有云收缩 Azure VMware 解决方案](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="delete-cloudsimple-node"></a>删除云简单节点

1. 选择**所有服务**。

2. 搜索**云简单节点**。

   ![搜索云简单节点](media/create-cloudsimple-node-search.png)

3. 选择**云简单节点**。

4. 选择要删除不属于私有云的节点。  **私有云名称**列显示节点所属的私有云名称。  如果私有云不使用节点，则该值将为空。 

    ![选择云简单节点](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能删除不属于私有云的节点。

## <a name="next-steps"></a>后续步骤

* 了解[私有云](cloudsimple-private-cloud.md)
