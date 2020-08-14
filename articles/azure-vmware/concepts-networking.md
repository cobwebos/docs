---
title: 概念-网络互连
description: '了解 Azure VMware 解决方案中的网络和互连的主要方面和使用案例 (AVS) '
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214107"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware 解决方案 (AVS) 预览版网络和互连概念

Azure VMware 解决方案 (AVS) 私有云和本地环境或 Azure 中的虚拟网络之间的网络互连可让你访问和使用你的私有云。 在本文中，我们将介绍几个关键概念，这些概念建立了网络和互连的基础。

对互连的一种很有用的观点是考虑以下两种类型的 AVS 私有云实现：

1. [**仅限 azure 的基本互连**](#azure-virtual-network-interconnectivity) 使你可以在 Azure 中管理和使用只有单个虚拟网络的私有云。 此实现最适用于无需从本地环境访问的 AVS 评估或实现。

1. [**完整本地到私有云互连**](#on-premises-interconnectivity) 扩展了基于 Azure 的基本实现，以在本地与 AVS 私有云之间包括互连。
 
可在以下各节中找到有关要求的详细信息和两种类型的 AVS 私有云互连实现。

## <a name="avs-private-cloud-use-cases"></a>AVS 私有云用例

AVS 私有云的用例包括：
- 云中的新 VMware VM 工作负荷
- VM 工作负荷突发到云 (仅限本地到 AVS) 
- VM 工作负荷迁移到云 (仅限本地到 AVS) 
- 灾难恢复 (AVS 到 AVS 或本地到 AVS) 
- Azure 服务的消耗

 可以通过本地到私有云连接来启用 AVS 服务的所有用例。 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>虚拟网络和 ExpressRoute 线路需求
 
当你从你的订阅中的虚拟网络创建连接时，ExpressRoute 线路通过对等互连建立，使用授权密钥和你在 Azure 门户中请求的对等互连 ID。 对等互连是私有云和虚拟网络之间的一种专用的一对一连接。

> [!NOTE] 
> ExpressRoute 线路不是私有云部署的一部分。 本地 ExpressRoute 线路超出了本文档的范围。 如果需要到私有云的本地连接，可以使用现有 ExpressRoute 线路或在 Azure 门户中购买一个。

部署私有云时，你将收到 vCenter 和 NSX-T Manager 的 IP 地址。 若要访问这些管理接口，需要在订阅中的虚拟网络中创建其他资源。 在教程中，可以找到有关创建这些资源和建立 ExpressRoute 专用对等互连的步骤。

私有云逻辑网络随附预配的 NSX-T。 第0层网关和第1层网关是预先预配的。 你可以创建一个段并将其附加到现有第1层的网关，或将其附加到你定义的新第1层网关。 NSX-T 逻辑网络组件提供工作负载之间的中东连接，还提供与 internet 和 Azure 服务的北南部连接。 

## <a name="routing-and-subnet-requirements"></a>路由和子网要求

路由基于 (BGP) 边界网关协议，默认情况下，每个私有云部署自动预配并启用该路由。 对于 AVS 私有云，你需要计划私有云网络地址空间，其中至少有/22 个前缀长度为子网的 CIDR 网络地址块，如下表所示。 地址块不应与订阅和本地网络中的其他虚拟网络中使用的地址块重叠。 在此地址块中，将自动设置管理、设置和 vMotion 网络。

示例 `/22` CIDR 网络地址块：`10.10.0.0/22`

子网：

| 网络用途             | 子网 | 示例        |
| ------------------------- | ------ | -------------- |
| 私有云管理  | `/24`  | `10.10.0.0/24` |
| vMotion 网络           | `/24`  | `10.10.1.0/24` |
| VM 工作负荷              | `/24`  | `10.10.2.0/24` |
| ExpressRoute 对等互连      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Azure 虚拟网络互连

在虚拟网络到私有云实现中，你可以管理你的 AVS 私有云，使用私有云中的工作负荷，并通过 ExpressRoute 连接访问 Azure 服务。 

下图显示了在私有云部署时建立的基本网络互连。 其中显示了 Azure 中的虚拟网络与私有云之间基于 ExpressRoute 的逻辑网络。 互连实现了三个主要用例：
* 可从 Azure 订阅中的 Vm 而不是从本地系统访问的 vCenter 服务器和 NSX-T 管理器的入站访问。 
* 从 Vm 到 Azure 服务的出站访问。 
* 运行私有云的工作负荷的入站访问和消耗。

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="基本虚拟网络到私有云连接" border="false":::

## <a name="on-premises-interconnectivity"></a>本地互连

在虚拟网络和本地到完全私有云实现中，可以从本地环境访问 AVS 私有云。 此实现是上一节中所述的基本实现的扩展。 与基本实现一样，ExpressRoute 线路是必需的，但通过此实现，它用于从本地环境连接到 Azure 中的私有云。 

下图显示了本地到私有云的互连，它支持以下用例：
* 热/冷跨 vCenter vMotion
* 本地到 AVS 私有云管理访问权限

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="虚拟网络和本地完整私有云连接" border="false":::

对于私有云，请 Global Reach 启用 ExpressRoute，然后在 Azure 门户中请求 Global Reach 的授权密钥和专用对等互连 ID。 授权密钥和对等互连 ID 用于在你的订阅中的 ExpressRoute 线路与新的私有云的 ExpressRoute 线路之间建立 Global Reach。 链接后，这两条 ExpressRoute 线路将本地环境之间的网络流量路由到私有云。  有关请求和使用授权密钥和对等互连 ID 的过程，请参阅 [创建与私有云的 ExpressRoute Global Reach 对等互连教程](tutorial-expressroute-global-reach-private-cloud.md) 。


## <a name="next-steps"></a>后续步骤 

下一步是了解 [私有云存储的概念](concepts-storage.md)。

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
