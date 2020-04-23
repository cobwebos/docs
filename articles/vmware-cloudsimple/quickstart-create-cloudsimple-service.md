---
title: 快速入门：创建 VMware 云简单服务
titleSuffix: Azure VMware Solution by CloudSimple
description: 了解如何创建云简单服务、购买节点和预留节点
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868019"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>快速入门 - 通过云简单服务创建 Azure VMware 解决方案

要开始，请在 Azure 门户中按云简单创建 Azure VMware 解决方案。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 解决方案（按云简单 ） - 服务概述

云简单服务允许您使用 Azure VMware 解决方案（按云简单。  创建服务允许您预配节点、保留节点和创建私有云。  在云简单服务可用的每个 Azure 区域中添加云简单服务。  该服务通过云简单定义 Azure VMware 解决方案的边缘网络。  此边缘网络用于包括 VPN、ExpressRoute 和 Internet 连接到私有云的服务。

要添加 CloudSimple 服务，必须创建网关子网。 创建边缘网络时使用网关子网，并且需要 /28 CIDR 块。 网关子网地址空间必须是唯一的。 它不能与任何本地网络地址空间或 Azure 虚拟网络地址空间重叠。

## <a name="before-you-begin"></a>开始之前

为网关子网分配 /28 CIDR 块。  每个 CloudSimple 服务都需要网关子网，并且对于创建网关子网的区域是唯一的。 网关子网用于 Azure VMware 解决方案（由云简单边缘网络服务，需要 /28 CIDR 块）。 网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。  与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。

查看[网络先决条件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”  。
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

## <a name="provision-nodes"></a>预配节点

要为云简单私有云环境设置即付容量，首先在 Azure 门户中预配节点。

1. 选择“所有服务”  。
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
12. 选择“查看 + 创建”  。
13. 查看设置。 要修改任何设置，请单击"**上一个**"。
14. 选择“创建”  。

## <a name="next-steps"></a>后续步骤

* [创建私有云并配置环境](quickstart-create-private-cloud.md)
* 了解有关[云简单服务](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service)的更多
