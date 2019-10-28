---
title: Azure VMware 解决方案（按 CloudSimple）-创建 CloudSimple 私有云
description: 介绍如何创建 CloudSimple 私有云，以将 VMware 工作负荷扩展到云，从而提高操作灵活性和连续性
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893919"
---
# <a name="create-a-cloudsimple-private-cloud"></a>创建 CloudSimple 私有云

私有云是支持 ESXi 主机、vCenter、vSAN 和 NSX 的独立 VMware 堆栈。 私有云通过 CloudSimple 门户进行管理。 它们在自己的管理域中具有自己的 vCenter 服务器。 堆栈在专用节点和隔离的裸机硬件节点上运行。

创建私有云有助于满足网络基础结构的各种常见需求：

* **增长**。 如果已达到现有基础结构的硬件刷新点，则可以使用私有云进行扩展，无需新的硬件投资。

* **快速扩展**。 如果出现任何临时或计划外的容量需求，可以使用私有云创建额外的容量，而无需延迟。

* **增强的保护**。 使用三个或更多节点的私有云，可以获得自动冗余和高可用性保护。

* 长期**基础结构需求**。 如果你的数据中心处于容量上，或者你希望重新构建以降低成本，则私有云使你可以停用数据中心并迁移到基于云的解决方案，同时保持与企业运营的兼容。

在创建私有云时，你将获得单个 vSphere 群集和在该群集中创建的所有管理 Vm。

## <a name="before-you-begin"></a>开始之前

必须先设置节点，然后才能创建私有云。 有关预配节点的详细信息，请参阅[通过 CloudSimple 预配 Azure VMware 解决方案的节点](create-nodes.md)。

为私有云分配 vSphere/vSAN 子网的 CIDR 范围。 私有云是作为独立的 VMware 堆栈环境（包含由 vCenter 服务器托管的 ESXi 主机、vCenter、vSAN 和 NSX）创建的。 管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。 在部署过程中，网络 CIDR 范围分为不同的子网。 VSphere/vSAN 子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。 有关 vSphere/vSAN 子网的详细信息，请参阅 Vlan 和子网概述。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀：/24
* 最大 vSphere/vSAN 子网 CIDR 范围前缀：/21


## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>创建新的私有云

1. 选择“所有服务”。
2. 搜索 " **CloudSimple Services**"。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 从 "**概述**" 中，单击 "**创建私有云**" 以打开 CloudSimple 门户的新浏览器选项卡。 如果系统提示，请用 Azure 登录凭据登录。

    ![从 Azure 创建私有云](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 门户中，提供私有云的名称。
6. 选择私有云的 "**位置**"。
7. 选择 "**节点类型**"，与在 Azure 上预配的类型一致。
8. 指定**节点计数**。  至少需要三个节点才能创建私有云。

    ![创建私有云-基本信息](media/create-private-cloud-basic-info.png)

9. 单击 "**下一步：高级选项**"。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保 CIDR 范围不与任何本地或其他 Azure 子网（虚拟网络）或网关子网重叠。

    **CIDR 范围选项：** /24、/23、/22 或/21。 A/24 CIDR 范围支持最多9个节点，/23 个 CIDR 范围最多支持41个节点，并且/22 和/21 CIDR 范围最多支持64个节点（私有云中的最大节点数）。

    > [!IMPORTANT]
    > VSphere/vSAN CIDR 范围中的 IP 地址保留供私有云基础结构使用。  请勿在任何虚拟机上使用此范围内的 IP 地址。

11. 单击 "**下一步"：查看和创建**。
12. 查看设置。 如果需要更改任何设置，请单击 "**上一步**"。
13. 单击“创建”。

私有云预配过程开始。 预配私有云可能需要长达两个小时。

有关扩展现有私有云的说明，请参阅[扩展私有云](expand-private-cloud.md)。
