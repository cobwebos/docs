---
title: Azure VMware 解决方案（按云简单 - 创建云简单私有云）
description: 描述如何创建云简单私有云，以具有操作灵活性和连续性将 VMware 工作负载扩展到云
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024783"
---
# <a name="create-a-cloudsimple-private-cloud"></a>创建云简单私有云

私有云是一个孤立的 VMware 堆栈，支持 ESXi 主机、vCenter、vSAN 和 NSX。 私有云通过云简单门户进行管理。 它们有自己的 vCenter 服务器。 堆栈在专用节点和隔离裸机硬件节点上运行。

创建私有云可帮助您满足网络基础设施的各种常见需求：

* **增长**。 如果您已达到现有基础架构的硬件刷新点，则私有云允许您扩展，无需新的硬件投资。

* **快速扩展**。 如果出现任何临时或计划外容量需求，私有云允许您立即创建额外的容量。

* **加强保护**。 使用三个或更多节点的私有云，您可以获得自动冗余和高可用性保护。

* **长期需要基础设施**。 如果您的数据中心处于容量，或者您想要进行重组以降低成本，则私有云允许您停用数据中心并迁移到基于云的解决方案，同时保持与企业运营兼容。

创建私有云时，您将获得单个 vSphere 群集以及该群集中创建的所有管理 VM。

## <a name="before-you-begin"></a>开始之前

必须先预配节点，然后才能创建私有云。 有关预配节点的详细信息，请参阅[按云简单为 的 Azure VMware 解决方案预配节点](create-nodes.md)。

为私有云为 vSphere/vSAN 子网分配 CIDR 范围。 私有云创建为独立的 VMware 堆栈环境（具有 ESXi 主机、vCenter、vSAN 和 NSX），由 vCenter 服务器管理。 管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。 网络 CIDR 范围在部署期间分为不同的子网。 vSphere/vSAN 子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。 有关 vSphere/vSAN 子网的详细信息，请参阅 VLAN 和子网概述。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀： /24
* 最大 vSphere/vSAN 子网 CIDR 范围前缀： /21


## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[云简单门户](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>创建新私有云

1. 选择**所有服务**。
2. 搜索**云简单服务**。
3. 选择要在其中创建私有云的云简单服务。
4. 从**概述**中，单击 **"创建私有云**"可为云简单门户打开新的浏览器选项卡。 如果出现提示，请使用 Azure 登录凭据登录。

    ![从 Azure 创建私有云](media/create-private-cloud-from-azure.png)

5. 在云简单门户中，为私有云提供名称。
6. 选择私有云**的位置**。
7. 选择**节点类型**，与在 Azure 上预配的内容一致。
8. 指定**节点计数**。  创建私有云至少需要三个节点。

    ![创建私有云 - 基本信息](media/create-private-cloud-basic-info.png)

9. 单击 **"下一步：高级选项**"。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 确保 CIDR 范围不会与任何本地或其他 Azure 子网（虚拟网络）或网关子网重叠。

    **CIDR 范围选项**：/24、/23、/22 或 /21。 /24 CIDR 范围最多支持 9 个节点，/23 CIDR 范围最多支持 41 个节点，/22 和 /21 CIDR 范围支持多达 64 个节点（私有云中最大节点数）。

    > [!IMPORTANT]
    > vSphere/vSAN CIDR 范围内的 IP 地址保留供私有云基础设施使用。  不要在任何虚拟机上使用此范围内的 IP 地址。

11. 单击 **"下一步："查看并创建**。
12. 查看设置。 如果需要更改任何设置，请单击"**上一个**"。
13. 单击 **“创建”**。

私有云预配过程开始。 预配私有云最多可能需要两个小时。

有关扩展现有私有云的说明，请参阅[扩展私有云](expand-private-cloud.md)。
