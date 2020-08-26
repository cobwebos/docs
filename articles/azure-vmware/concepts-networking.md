---
title: 概念-网络互连
description: 了解 Azure VMware 解决方案中的网络和互连的主要方面和用例。
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750571"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Azure VMware 解决方案预览版网络和互连概念

Azure VMware 解决方案 (AVS) 为用户和应用程序提供从本地和基于 Azure 的环境或资源访问的 VMware 私有云环境。 Azure ExpressRoute 和 VPN 连接等服务提供连接。 这些服务需要特定的网络地址范围和防火墙端口来启用这些服务。  

部署私有云时，将创建用于管理、预配和 vMotion 的专用网络。 它们用于访问 vCenter 和 NSX-T 管理器以及虚拟机 vMotion 或部署。 可以通过 Azure 中的 VNet 或本地环境访问所有专用网络。 ExpressRoute Global Reach 用于将私有云连接到本地环境，此连接要求在订阅中提供一个使用 ExpressRoute 线路的 VNet。

此外，在部署私有云时，将设置和提供对 internet 和 Azure 服务的访问权限，以便生产网络中的 Vm 可以使用它们。  默认情况下，对于新的私有云，internet 访问是禁用的，在任何时候都可以启用或禁用。

对互连的一种很有用的观点是考虑以下两种类型的 AVS 私有云实现：

1. [**仅限 azure 的基本互连**](#azure-virtual-network-interconnectivity) 使你可以在 Azure 中管理和使用只有单个虚拟网络的私有云。 此实现最适用于无需从本地环境访问的 AVS 评估或实现。

1. [**完整本地到私有云互连**](#on-premises-interconnectivity) 扩展了基于 Azure 的基本实现，以在本地与 AVS 私有云之间包括互连。
 
在本文中，我们将介绍几个建立网络和互连的关键概念，包括要求和限制。 我们还将介绍两种类型的 AVS 私有云互连实现的详细信息。 本文提供将网络配置为正确使用 AVS 所需了解的信息。

## <a name="avs-private-cloud-use-cases"></a>AVS 私有云用例

AVS 私有云的用例包括：
- 云中的新 VMware VM 工作负荷
- VM 工作负荷突发到云 (仅限本地到 AVS) 
- VM 工作负荷迁移到云 (仅限本地到 AVS) 
- 灾难恢复 (AVS 到 AVS 或本地到 AVS) 
- Azure 服务的消耗

> [!TIP]
> 可以通过本地到私有云连接来启用 AVS 服务的所有用例。

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

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="虚拟网络和本地完整私有云连接" border="false":::

对于私有云，请 Global Reach 启用 ExpressRoute，然后在 Azure 门户中请求 Global Reach 的授权密钥和专用对等互连 ID。 授权密钥和对等互连 ID 用于在你的订阅中的 ExpressRoute 线路与新的私有云的 ExpressRoute 线路之间建立 Global Reach。 链接后，这两条 ExpressRoute 线路将本地环境之间的网络流量路由到私有云。  有关请求和使用授权密钥和对等互连 ID 的过程，请参阅 [创建与私有云的 ExpressRoute Global Reach 对等互连教程](tutorial-expressroute-global-reach-private-cloud.md) 。

## <a name="next-steps"></a>后续步骤 

- 了解有关 [网络连接注意事项和要求](tutorial-network-checklist.md)的详细信息。 
- 了解 [私有云存储概念](concepts-storage.md)。


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

