---
title: 按云简单配置 VMware 解决方案 - Azure
description: 了解如何使用 CloudSimple 部署将节点添加到 VMWare
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024800"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>按云简单调配 Azure VMware 解决方案

在 Azure 门户中预配节点。 然后，您可以为云简单私有云环境设置即付容量。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>将节点添加到云简单私有云

1. 选择**所有服务**。
2. 搜索**云简单节点**。

   ![搜索云简单节点](media/create-cloudsimple-node-search.png)

3. 选择**云简单节点**。
4. 单击"**添加**"以创建节点。

    ![添加云简单节点](media/create-cloudsimple-node-add.png)

5. 选择要预配云简单节点的订阅。
6. 选择节点的资源组。 要添加新资源组，请单击"**新建**"。
7. 输入前缀以标识节点。
8. 选择节点资源的位置。
9. 选择托管节点资源的专用位置。
10. 选择[节点类型](cloudsimple-node.md)。
11. 选择要预配的节点数。
12. 选择 **"审阅 " 创建**。
13. 查看设置。 要修改任何设置，请单击"**上一个**"。
14. 选择 **“创建”**。

## <a name="next-steps"></a>后续步骤

* [创建私有云](create-private-cloud.md)
