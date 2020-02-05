---
title: 删除 VMware 解决方案的节点（AVS）-Azure
description: 了解如何从 VMWare 中删除具有 AVS 部署的节点
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024732"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>从 Azure VMware 解决方案中删除使用 AVS 的节点

创建 AVS 节点后，将对其进行计量。 必须删除节点以停止对节点进行计数。 删除 Azure 门户中未使用的节点。

## <a name="before-you-begin"></a>开始之前

仅在以下条件下才能删除节点：

* 将删除使用节点创建的 AVS 私有云。 若要删除 AVS 私有云，请参阅[删除 Azure VMware 解决方案（通过 AVS 私有云](delete-private-cloud.md)）。
* 通过压缩 AVS 私有云，已从 AVS 私有云中删除该节点。 若要压缩 AVS 私有云，请参阅[通过 Avs 私有云缩小 Azure VMware 解决方案](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="delete-avs-node"></a>删除 AVS 节点

1. 选择“所有服务”。

2. 搜索**AVS 节点**。

   ![搜索 AVS 节点](media/create-cloudsimple-node-search.png)

3. 选择 " **AVS 节点**"。

4. 选择不属于要删除的 AVS 私有云的节点。 " **AVS 私有云名称**" 列显示节点所属的 AVS 私有云名称。 如果某个节点未由 AVS 私有云使用，则该值将为空。 

    ![选择 AVS 节点](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能删除不属于 AVS 私有云的节点。

## <a name="next-steps"></a>后续步骤

* 了解[AVS 私有云](cloudsimple-private-cloud.md)
