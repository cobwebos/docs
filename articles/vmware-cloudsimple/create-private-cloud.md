---
title: 通过 CloudSimple 私有云创建 Azure VMware 解决方案
description: 介绍如何创建 CloudSimple 私有云, 以将 VMware 工作负荷扩展到云, 从而提高操作灵活性和连续性
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812252"
---
# <a name="create-a-cloudsimple-private-cloud"></a>创建 CloudSimple 私有云

创建私有云有助于满足网络基础结构的各种常见需求:

* **增长**。 如果已达到现有基础结构的硬件刷新点, 则可以使用私有云进行扩展, 无需新的硬件投资。

* **快速扩展**。 如果出现任何临时或计划外的容量需求, 可以使用私有云创建额外的容量, 而无需延迟。

* **增强的保护**。 使用三个或更多节点的私有云, 可以获得自动冗余和高可用性保护。

* 长期**基础结构需求**。 如果你的数据中心处于容量上, 或者你希望重新构建以降低成本, 则私有云使你可以停用数据中心并迁移到基于云的解决方案, 同时保持与企业运营的兼容。

在创建私有云时, 你将获得单个 vSphere 群集和在该群集中创建的所有管理 Vm。

## <a name="before-you-begin"></a>开始之前

必须先设置节点, 然后才能创建私有云。  有关预配节点的详细信息, 请参阅[通过 CloudSimple 为 VMware 解决方案预配节点-Azure 一](create-nodes.md)文。

为私有云分配 vSphere/vSAN 子网的 CIDR 范围。 私有云是作为由 vCenter 服务器管理的隔离 VMware 堆栈 (ESXi 主机、vCenter、vSAN 和 NSX) 环境创建的。 管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。 在部署过程中, 网络 CIDR 范围分为不同的子网。  VSphere/vSAN 子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。  与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。  有关 vSphere/vSAN 子网的详细信息, 请参阅[vlan 和子网概述](cloudsimple-vlans-subnets.md)。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀:/24 
* 最大 vSphere/vSAN 子网 CIDR 范围前缀:/21

## <a name="sign-in-to-azure"></a>登录  Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>创建新的私有云

1. 在 "**资源**" 页上, 单击 "**新建私有云**"。

    ![创建私有云-如何启动](media/create-pc-button.png)

2. 选择要托管私有云资源的位置。

3. 选择为私有云预配的 CS28 或 CS36 节点类型 you'ev。 后一种方法包括最大计算和内存容量。

4. 选择私有云的节点数。 最多可以选择 you'ev[预配](create-nodes.md)的可用节点数。

    ![创建私有云-基本设置](media/create-private-cloud-basic-info.png)

5. 单击“下一步:**高级选项**。

6. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保 CIDR 范围不与任何本地或其他 Azure 子网 (虚拟网络) 或网关子网重叠。  不要使用 Azure 虚拟网络上定义的任何 CIDR 范围。
    
    **CIDR 范围选项:** /24、/23、/22 或/21。 A/24 CIDR 范围支持最多9个节点,/23 个 CIDR 范围最多支持41个节点, 并且/22 和/21 CIDR 范围最多支持64个节点 (私有云中的最大节点数)。

    > [!CAUTION]
    > VSphere/vSAN CIDR 范围中的 IP 地址保留供私有云基础结构使用。  请勿在任何虚拟机上使用此范围内的 IP 地址。

7. 单击“下一步:**查看和创建**。

8. 查看设置。 如果需要更改任何设置, 请单击 "**上一步**"。

9. 单击“创建”。

单击 "创建" 后, 将启动私有云设置。  可以从 CloudSimple 门户上的 "[任务](https://docs.azure.cloudsimple.com/activity/#tasks)" 页中监视进度。  预配可能需要30分钟到2小时。  预配完成后, 你将收到一封电子邮件。

## <a name="next-steps"></a>后续步骤

* [展开私有云](expand-private-cloud.md)