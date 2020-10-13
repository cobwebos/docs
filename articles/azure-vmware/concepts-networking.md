---
title: 概念-网络互连
description: 了解 Azure VMware 解决方案中的网络和互连的主要方面和用例。
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: f8e9ed143d53afe2f7a24c832c69390c6ffcb36b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575752"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware 解决方案网络和互连的概念

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

对互连的一种很有用的观点是考虑两种类型的 Azure VMware 解决方案私有云实现：

1. [**仅限 azure 的基本互连**](#azure-virtual-network-interconnectivity) 使你可以在 Azure 中管理和使用只有单个虚拟网络的私有云。 此实现最适用于不需要从本地环境访问的 Azure VMware 解决方案评估或实现。

1. [**完整本地到私有云互连**](#on-premises-interconnectivity) 扩展了仅限 Azure 的基本实现，包括本地和 Azure VMware 解决方案私有云之间的互连。
 
在本文中，我们将介绍几个建立网络和互连的关键概念，包括要求和限制。 我们还将介绍两种类型的 Azure VMware 解决方案私有云互连实现的详细信息。 本文提供配置网络以正确使用 Azure VMware 解决方案所需的信息。

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware 解决方案私有云用例

Azure VMware 解决方案私有云的用例包括：
- 云中的新 VMware VM 工作负荷
- VM 工作负荷突发到云 (仅限本地到 Azure VMware 解决方案) 
- VM 工作负荷迁移到云 (仅限本地到 Azure 的 VMware 解决方案) 
- 灾难恢复 (azure vmware 解决方案到 Azure VMware 解决方案或本地到 Azure VMware 解决方案) 
- Azure 服务的消耗

> [!TIP]
> Azure VMware 解决方案服务的所有用例都是通过本地到私有云连接而启用的。

## <a name="azure-virtual-network-interconnectivity"></a>Azure 虚拟网络互连

在虚拟网络到私有云实现中，你可以管理 Azure VMware 解决方案私有云，使用私有云中的工作负荷，并通过 ExpressRoute 连接访问 Azure 服务。 

下图显示了在私有云部署时建立的基本网络互连。 其中显示了 Azure 中的虚拟网络与私有云之间基于 ExpressRoute 的逻辑网络。 互连实现了三个主要用例：
* 可从 Azure 订阅中的 Vm 而不是从本地系统访问的 vCenter 服务器和 NSX-T 管理器的入站访问。 
* 从 Vm 到 Azure 服务的出站访问。 
* 运行私有云的工作负荷的入站访问和消耗。

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="基本虚拟网络到私有云连接" border="false":::

## <a name="on-premises-interconnectivity"></a>本地互连

在虚拟网络和本地到完全私有云实现中，可以从本地环境访问 Azure VMware 解决方案私有云。 此实现是上一节中所述的基本实现的扩展。 与基本实现一样，ExpressRoute 线路是必需的，但通过此实现，它用于从本地环境连接到 Azure 中的私有云。 

下图显示了本地到私有云的互连，它支持以下用例：
* 热/冷跨 vCenter vMotion
* 本地到 Azure VMware 解决方案私有云管理访问权限

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="基本虚拟网络到私有云连接" border="false":::

对于私有云，请 Global Reach 启用 ExpressRoute，然后在 Azure 门户中请求 Global Reach 的授权密钥和专用对等互连 ID。 授权密钥和对等互连 ID 用于在你的订阅中的 ExpressRoute 线路与新的私有云的 ExpressRoute 线路之间建立 Global Reach。 链接后，这两条 ExpressRoute 线路将本地环境之间的网络流量路由到私有云。  有关请求和使用授权密钥和对等互连 ID 的过程，请参阅 [创建与私有云的 ExpressRoute Global Reach 对等互连教程](tutorial-expressroute-global-reach-private-cloud.md) 。



## <a name="next-steps"></a>后续步骤 
了解 [私有云存储概念](concepts-storage.md)。


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

