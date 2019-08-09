---
title: 通过 CloudSimple 删除 VMware 解决方案的节点-Azure
description: 了解如何通过 CloudSimple 部署删除 VMWare 中的节点
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9dbe1751ac77f370991af75ea161247c21700ff2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886983"
---
# <a name="delete-nodes-from-vmware-solution-by-cloudsimple---azure"></a>通过 CloudSimple 从 VMware 解决方案中删除节点-Azure

创建 CloudSimple 节点后, 将对其进行计量。  必须删除节点以停止对节点进行计数。  删除 Azure 门户中未使用的节点。 

## <a name="before-you-begin"></a>开始之前

仅在以下条件下才能删除节点:

* 将删除使用节点创建的私有云。  若要删除私有云, 请参阅[通过 CloudSimple 私有云删除 Azure VMware 解决方案](delete-private-cloud.md)。
* 已通过收缩私有云从私有云中删除了节点。  若要缩小私有云, 请参阅[通过 CloudSimple 私有云缩小 Azure VMware 解决方案](shrink-private-cloud.md)。


## <a name="sign-in-to-azure"></a>登录  Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="delete-cloudsimple-node"></a>删除 CloudSimple 节点

1. 选择“所有服务”。

2. 搜索**CloudSimple 节点**。

   ![搜索 CloudSimple 节点](media/create-cloudsimple-node-search.png)

3. 选择**CloudSimple 节点**。

4. 选择不属于要删除的私有云的节点。  "**私有云名称**" 列显示节点所属的私有云名称。  如果私有云未使用节点, 则该值将为空。 

    ![选择 CloudSimple 节点](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能删除不属于私有云的节点。

## <a name="next-steps"></a>后续步骤

* 了解[私有云](cloudsimple-private-cloud.md)
