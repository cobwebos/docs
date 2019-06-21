---
title: VMware CloudSimple-Azure 解决方案的设置节点
description: 了解如何将节点添加到与 CloudSimple 部署在 VMWare
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165256"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>VMware CloudSimple-Azure 解决方案的设置节点

在 Azure 门户中的设置节点。 然后可以将设置付款现转容量 CloudSimple 私有云环境。

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>将预配的节点添加到 CloudSimple 私有云

1. 选择“所有服务”  。
2. 搜索**CloudSimple 节点**。

   ![搜索 CloudSimple 节点](media/create-cloudsimple-node-search.png)

3. 选择**CloudSimple 节点**。
4. 单击**添加**来创建节点。

    ![添加 CloudSimple 节点](media/create-cloudsimple-node-add.png)

5. 选择你想要预配 CloudSimple 节点的订阅。
6. 选择节点的资源组。 若要添加新的资源组，请单击**创建新**。
7. 输入标识的节点的前缀。
8. 选择节点资源的位置。
9. 选择要托管节点资源的专用的位置。
10. 选择的节点类型。 你可以选择[CS28 或 CS36 选项](cloudsimple-node.md)。 后一种包含的最大的计算和内存容量。
11. 选择预配的节点数。
12. 选择“查看 + 创建”  。
13. 查看设置。 若要修改任何设置，请单击**上一步**。
14. 选择“创建”  。

## <a name="next-steps"></a>后续步骤

* [创建私有云](https://docs.azure.cloudsimple.com/create-private-cloud/)
