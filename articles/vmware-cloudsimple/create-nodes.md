---
title: 为 VMware 解决方案预配节点（AVS）-Azure
description: 了解如何通过 AVS 部署将节点添加到 VMWare
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024800"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>预配 Azure VMware 解决方案（AVS）的节点

在 Azure 门户中设置节点。 然后，你可以为你的 AVS 私有云环境设置即用即付容量。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="add-a-node-to-your-avs-private-cloud"></a>将节点添加到 AVS 私有云

1. 选择“所有服务”。
2. 搜索**AVS 节点**。

   ![搜索 AVS 节点](media/create-cloudsimple-node-search.png)

3. 选择 " **AVS 节点**"。
4. 单击 "**添加**" 创建节点。

    ![添加 AVS 节点](media/create-cloudsimple-node-add.png)

5. 选择要在其中预配 AVS 节点的订阅。
6. 选择节点的资源组。 若要添加新的资源组，请单击 "**新建**"。
7. 输入前缀来标识节点。
8. 选择节点资源的位置。
9. 选择用于托管节点资源的专用位置。
10. 选择[节点类型](cloudsimple-node.md)。
11. 选择要预配的节点数。
12. 选择“查看 + 创建”。
13. 查看设置。 若要修改任何设置，请单击 "**上一步**"。
14. 选择“创建”。

## <a name="next-steps"></a>后续步骤

* [创建 AVS 私有云](create-private-cloud.md)
