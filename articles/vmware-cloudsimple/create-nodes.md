---
title: 通过 CloudSimple 为 VMware 解决方案预配节点-Azure
description: 了解如何通过 CloudSimple 部署将节点添加到 VMWare
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 898b07d05abf3bfad644fb590d90c7a90c5a1c0d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883225"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>通过 CloudSimple 为 VMware 解决方案预配节点-Azure

在 Azure 门户中设置节点。 然后, 你可以为 CloudSimple 私有云环境设置即用即付容量。

## <a name="sign-in-to-azure"></a>登录  Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。


## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>将节点添加到 CloudSimple 私有云

1. 选择“所有服务”。
2. 搜索**CloudSimple 节点**。

   ![搜索 CloudSimple 节点](media/create-cloudsimple-node-search.png)

3. 选择**CloudSimple 节点**。
4. 单击 "**添加**" 创建节点。

    ![添加 CloudSimple 节点](media/create-cloudsimple-node-add.png)

5. 选择要在其中预配 CloudSimple 节点的订阅。
6. 选择节点的资源组。 若要添加新的资源组, 请单击 "**新建**"。
7. 输入前缀来标识节点。
8. 选择节点资源的位置。
9. 选择用于托管节点资源的专用位置。
10. 选择节点类型。 你可以选择[CS28 或 CS36 选项](cloudsimple-node.md)。 后一种方法包括最大计算和内存容量。
11. 选择要预配的节点数。
12. 选择“查看 + 创建”。
13. 查看设置。 若要修改任何设置, 请单击 "**上一步**"。
14. 选择“创建”。

## <a name="next-steps"></a>后续步骤

* [创建私有云](create-private-cloud.md)
