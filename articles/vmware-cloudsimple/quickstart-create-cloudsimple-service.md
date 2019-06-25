---
title: Azure 的 VMware 解决方案通过 CloudSimple 快速入门-创建服务
description: 了解如何创建 CloudSimple 服务预配节点，并保留节点数
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164218"
---
# <a name="quickstart---create-service"></a>快速入门-创建服务

若要开始，请在 Azure 门户中创建 Azure VMware 解决方案的 CloudSimple。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 解决方案 CloudSimple-服务概述

CloudSimple 服务，可使用 Azure CloudSimple VMware 解决方案。  创建服务允许你设置节点保留节点，并创建私有云。  在提供 CloudSimple 服务的每个 Azure 区域中添加 CloudSimple 服务。  该服务定义 Azure VMware 解决方案通过 CloudSimple 边界网络。  此边界网络用于服务，包括私有云与 VPN、 ExpressRoute 和 Internet 连接。

若要添加 CloudSimple 服务，必须创建网关子网。 网关子网创建边界网络时使用，并且需要/28 CIDR 块。 网关子网地址空间必须是唯一的。 它不能与任何本地网络地址空间或 Azure 虚拟网络地址空间重叠。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>启用 Microsoft.VMwareCloudSimple 资源提供程序

请按照以下步骤来启用 CloudSimple 服务的资源提供程序。

1. 选择“所有服务”  。
2. 搜索并选择**订阅**。

    ![选择订阅](media/cloudsimple-service-select-subscriptions.png)

3. 选择你想要启用 CloudSimple 服务的订阅
4. 单击**资源提供程序**的订阅
5. 使用**Microsoft.VMwareCloudSimple**以筛选资源提供程序
6. 选择**Microsoft.VMwareCloudSimple**资源提供程序，并单击**注册**

    ![注册资源提供程序](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”  。
2. 搜索**CloudSimple 服务**。

    ![搜索 CloudSimple 服务](media/create-cloudsimple-service-search.png)

3. 选择**CloudSimple 服务**。
4. 单击**添加**若要创建新的服务。

    ![添加 CloudSimple 服务](media/create-cloudsimple-service-add.png)

5. 选择你想要创建 CloudSimple 服务的订阅。
6. 选择该服务的资源组。 若要添加新的资源组，请单击**创建新**。
7. 输入名称以标识该服务。
8. 输入服务网关 CIDR。 指定/28 与你的本地子网、 Azure 子网或计划内的 CloudSimple 子任何的网不重叠的子网。 创建服务后，您无法更改 CIDR。

    ![创建 CloudSimple 服务](media/create-cloudsimple-service.png)

9. 单击“确定”。 

创建服务并将其添加到服务的列表。

## <a name="provision-nodes"></a>预配节点

若要将设置付款现转容量 CloudSimple 私有云环境中，在 Azure 门户中的第一个设置节点。

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

* [创建私有云并配置环境](quickstart-create-private-cloud.md)
* 详细了解[CloudSimple 服务](https://docs.azure.cloudsimple.com/cloudsimple-service)
