---
title: 使用 CloudSimple 快速入门的 Azure VMware 解决方案-创建服务
description: 了解如何创建 CloudSimple 服务、预配节点和预留节点
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13b07b3b50bdb03373275ca9594baa6357e9f66f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812290"
---
# <a name="quickstart---create-service"></a>快速入门-创建服务

若要开始, 请在 Azure 门户中通过 CloudSimple 创建 Azure VMware 解决方案。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 解决方案 (按 CloudSimple)-服务概述

CloudSimple 服务允许 CloudSimple 使用 Azure VMware 解决方案。  通过创建服务, 可以预配节点、保留节点并创建私有云。  在 CloudSimple 服务可用的每个 Azure 区域中添加 CloudSimple 服务。  该服务通过 CloudSimple 定义 Azure VMware 解决方案的边缘网络。  此边缘网络用于包括 VPN、ExpressRoute 和 Internet 连接到私有云的服务。

若要添加 CloudSimple 服务, 必须创建一个网关子网。 网关子网是在创建边缘网络时使用的, 需要一个/28 个 CIDR 块。 网关子网地址空间必须是唯一的。 它不能与任何本地网络地址空间或 Azure 虚拟网络地址空间重叠。

## <a name="before-you-begin"></a>开始之前

为网关子网分配 a/28 CIDR 块。  网关子网是每个 CloudSimple 服务所必需的, 并且对其创建的区域是唯一的。 网关子网用于边缘网络服务, 需要一个/28 个 CIDR 块。 网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。  与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。 

## <a name="sign-in-to-azure"></a>登录  Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”。
2. 搜索**CloudSimple 服务**。

    ![搜索 CloudSimple 服务](media/create-cloudsimple-service-search.png)

3. 选择**CloudSimple Services**。
4. 单击 "**添加**" 创建新服务。

    ![添加 CloudSimple 服务](media/create-cloudsimple-service-add.png)

5. 选择要在其中创建 CloudSimple 服务的订阅。
6. 选择服务的资源组。 若要添加新的资源组, 请单击 "**新建**"。
7. 输入名称以标识该服务。
8. 输入服务网关的 CIDR。 指定不与任何本地子网、Azure 子网或计划 CloudSimple 子网重叠的 a/28 子网。 创建服务后无法更改 CIDR。

    ![创建 CloudSimple 服务](media/create-cloudsimple-service.png)

9. 单击 **“确定”** 。

创建服务并将其添加到服务列表中。

## <a name="provision-nodes"></a>预配节点

若要为 CloudSimple 私有云环境设置即用即付容量, 请首先在 Azure 门户中设置节点。

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

* [创建私有云并配置环境](quickstart-create-private-cloud.md)
* 了解有关[CloudSimple 服务](https://docs.azure.cloudsimple.com/cloudsimple-service)的详细信息
