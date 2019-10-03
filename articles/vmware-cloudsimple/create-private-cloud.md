---
title: Azure VMware 解决方案 (按 CloudSimple)-创建 CloudSimple 私有云
description: 介绍如何创建 CloudSimple 私有云, 以将 VMware 工作负荷扩展到云, 从而提高操作灵活性和连续性
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aacdb57c312946a9ec2b17a8d41aa9150efc277d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640973"
---
# <a name="create-a-cloudsimple-private-cloud"></a>创建 CloudSimple 私有云

私有云是支持 ESXi 主机、vCenter、vSAN 和 NSX 的独立 VMware 堆栈。 私有云通过 CloudSimple 门户进行管理。 它们在自己的管理域中具有自己的 vCenter 服务器。 堆栈在专用节点和隔离的裸机硬件节点上运行。

创建私有云有助于满足网络基础结构的各种常见需求:

* **增长**。 如果已达到现有基础结构的硬件刷新点, 则可以使用私有云进行扩展, 无需新的硬件投资。

* **快速扩展**。 如果出现任何临时或计划外的容量需求, 可以使用私有云创建额外的容量, 而无需延迟。

* **增强的保护**。 使用三个或更多节点的私有云, 可以获得自动冗余和高可用性保护。

* 长期**基础结构需求**。 如果你的数据中心处于容量上, 或者你希望重新构建以降低成本, 则私有云使你可以停用数据中心并迁移到基于云的解决方案, 同时保持与企业运营的兼容。

在创建私有云时, 你将获得单个 vSphere 群集和在该群集中创建的所有管理 Vm。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>创建新的私有云

1. 选择“所有服务”。
2. 搜索 " **CloudSimple Services**"。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 从 "**概述**" 中, 单击 "**创建私有云**" 以打开 CloudSimple 门户的新浏览器选项卡。 如果系统提示, 请用 Azure 登录凭据登录。

    ![从 Azure 创建私有云](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 门户中, 提供私有云的名称。
6. 选择私有云的 "**位置**"。
7. 选择 "**节点类型**", 与 Azure 上购买的内容一致。  你可以选择[CS28 或 CS36 选项](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku)。 后一种方法包括最大计算和内存容量。
8. 指定**节点计数**。  至少需要三个节点才能创建私有云。

    ![创建私有云-基本信息](media/create-private-cloud-basic-info.png)

9. 单击“下一步:**高级选项**。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保 CIDR 范围不与任何本地或其他 Azure 子网 (虚拟网络) 或网关子网重叠。

    **CIDR 范围选项:** /24、/23、/22 或/21。 A/24 CIDR 范围支持最多9个节点,/23 个 CIDR 范围最多支持41个节点, 并且/22 和/21 CIDR 范围最多支持64个节点 (私有云中的最大节点数)。

    > [!IMPORTANT]
    > VSphere/vSAN CIDR 范围中的 IP 地址保留供私有云基础结构使用。  请勿在任何虚拟机上使用此范围内的 IP 地址。

11. 单击“下一步:**查看和创建**。
12. 查看设置。 如果需要更改任何设置, 请单击 "**上一步**"。
13. 单击“创建”。

私有云预配过程开始。 预配私有云可能需要长达两个小时。

有关扩展现有私有云的说明, 请参阅[扩展私有云](expand-private-cloud.md)。
