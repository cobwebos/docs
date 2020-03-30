---
title: Azure VMware 解决方案（按云简单 ） - 创建云简单服务
description: 介绍如何在 Azure 门户中创建云简单服务
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024817"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>按云简单服务创建 Azure VMware 解决方案

要开始使用 Azure VMware 解决方案（按云简单，请通过在 Azure 门户中创建云简单服务）Azure VMware 解决方案。

## <a name="before-you-begin"></a>开始之前

为网关子网分配 /28 CIDR 块。 每个 CloudSimple 服务都需要网关子网，并且对于创建网关子网的区域是唯一的。 网关子网用于边缘网络服务，需要 /28 CIDR 块。 网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure[门户](https://portal.azure.com)。

## <a name="create-the-service"></a>创建服务

1. 选择**所有服务**。
2. 搜索**云简单服务**。
    ![搜索云简单服务](media/create-cloudsimple-service-search.png)
3. 选择**云简单服务**。
4. 单击"**添加**"以创建新服务。
    ![添加云简单服务](media/create-cloudsimple-service-add.png)
5. 选择要创建云简单服务的订阅。
6. 选择服务的资源组。 要添加新资源组，请单击"**新建**"。
7. 输入名称以标识服务。
8. 输入服务网关的 CIDR。 指定不与任何本地子网、Azure 子网或计划的 CloudSimple 子网重叠的 /28 子网。 创建服务后，无法更改 CIDR。

    ![创建云简单服务](media/create-cloudsimple-service.png)
9. 单击“确定”。

该服务将创建并添加到服务列表中。

## <a name="next-steps"></a>后续步骤

* 了解如何[预配节点](create-nodes.md)
* 了解如何[创建私有云](create-private-cloud.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
