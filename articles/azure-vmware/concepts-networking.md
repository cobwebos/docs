---
title: 概念-网络互连
description: 了解 Azure VMware 解决方案（AVS）中的网络和互连的主要方面和使用案例
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 82e5497c30bf02313e5ff8ad24167af569a153c2
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740910"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware 解决方案（AVS）预览版联网和互连的概念

Azure VMware 解决方案（AVS）私有云和本地环境或 Azure 中的虚拟网络之间的网络互连可让你访问和使用你的私有云。 本文介绍了几个建立互连基础的关键网络和互连概念。

对互连的一种很有用的观点是考虑两种类型的 AVS 私有云实现。 仅限 Azure 的基本互连和实现的实现实现了完整的本地到私有云的互连。

AVS 私有云的用例包括：
- 云中的新 VMware VM 工作负荷
- VM 工作负荷突发到云
- VM 工作负荷迁移到云
- 灾难恢复
- Azure 服务的消耗

 可以通过本地到私有云连接来启用 AVS 服务的所有用例。 基本互连模型最适用于无需从本地环境访问的 AVS 评估或实现。

以下各节介绍了两种类型的 AVS 私有云互连。  最基本的互连是 "Azure 虚拟网络连接"，它使你能够管理和使用只有 Azure 中的单个虚拟网络的私有云。 "本地连接" 中所述的互连将虚拟网络连接扩展到本地环境和 AVS 私有云之间的互连。

## <a name="azure-virtual-network-interconnectivity"></a>Azure 虚拟网络互连

下图显示了在私有云部署时建立的基本网络互连。 其中显示了 Azure 中的虚拟网络与私有云之间基于 ExpressRoute 的逻辑网络。 互连实现了三个主要用例：
- 对 vCenter 服务器和 NSX-T 管理器所在的管理网络的入站访问。
    - 可从 Azure 订阅中的 Vm 进行访问，而不能从本地系统进行访问。
- 从 Vm 到 Azure 服务的出站访问。
- 运行私有云的工作负荷的入站访问和消耗。

![基本虚拟网络到私有云连接](./media/concepts/adjacency-overview-drawing-single.png)

当你从订阅中的虚拟网络连接到私有云的 ExpressRoute 线路时，将建立此虚拟网络到私有云方案的 ExpressRoute 线路。 对等互连使用在 Azure 门户中请求的授权密钥和线路 ID。 通过对等互连建立的 ExpressRoute 连接是私有云和虚拟网络之间的一种专用的一对一连接。 你可以管理私有云，使用私有云中的工作负荷，并通过该 ExpressRoute 连接访问 Azure 服务。

部署 AVS 私有云时，需要使用单个/22 专用网络地址空间。 此地址空间不应与订阅中其他虚拟网络中使用的地址空间重叠。 此地址空间内会自动设置管理、设置和 vMotion 网络。 路由基于 BGP，并且默认为每个私有云部署自动预配并启用。

部署私有云时，将为你提供 vCenter 和 NSX-T 管理器的 IP 地址。 若要访问这些管理接口，请在订阅中的虚拟网络中创建其他资源。 教程中提供了创建这些资源和建立 ExpressRoute 专用对等互连的过程。

设计私有云逻辑网络并通过 NSX 实现它。 私有云附带预预配的 NSX-T。 第1层网关 & 为你预配第1层网关。 你可以创建一个段并将其附加到现有第1层网关，或者附加到可定义的新第1层网关。 NSX-T 逻辑网络组件提供工作负载之间的中东连接，还提供与 internet 和 Azure 服务的北南部连接。 

## <a name="on-premises-interconnectivity"></a>本地互连

你还可以将本地环境连接到 AVS 私有云。 这种类型的互连是上一节中所述的基本互连的扩展。

![虚拟网络和本地完整私有云连接](./media/concepts/adjacency-overview-drawing-double.png)

若要为私有云建立完全互连，请使用 Azure 门户在私有云 ExpressRoute 线路和本地 ExpressRoute 线路之间启用 ExpressRoute Global Reach。 此配置将基本连接扩展为包含从本地环境访问私有云的权限。

若要从本地环境连接到 Azure 中的私有云，需要本地到 Azure 虚拟网络 ExpressRoute 线路。 此 ExpressRoute 线路位于你的订阅中，并且不是私有云部署的一部分。 本地 ExpressRoute 线路超出了本文档的范围。 如果需要到私有云的本地连接，可以使用现有 ExpressRoute 线路或在 Azure 门户中购买一个。

与 Global Reach 链接后，这两条 ExpressRoute 线路会在本地环境和私有云之间路由网络流量。 前面的关系图中显示了本地到私有云互连。 关系图中表示的互连支持以下用例：

- 热/冷跨 vCenter vMotion
- 本地到 AVS 私有云管理访问权限

若要启用完全连接，可在 Azure 门户中请求 Global Reach 的授权密钥和专用对等互连 ID。 使用密钥和 ID 在订阅中的 ExpressRoute 线路与新的私有云的 ExpressRoute 线路之间建立 Global Reach。 [创建私有云的教程](tutorial-create-private-cloud.md)提供了请求和使用密钥和 ID 的步骤。

解决方案的路由要求需要计划私有云网络地址空间，以便避免与其他虚拟网络和本地网络重叠。 AVS 私有云至少需要子网的`/22` CIDR 网络地址块，如下所示。 此网络补充你的本地网络。 若要连接到本地环境和虚拟网络，它必须是不重叠的网络地址块。

CIDR `/22`网络地址块示例：`10.10.0.0/22`

子网：

| 网络使用情况             | 子网 | 示例        |
| ------------------------- | ------ | -------------- |
| 私有云管理            | `/24`    | `10.10.0.0/24`   |
| vMotion 网络       | `/24`    | `10.10.1.0/24`   |
| VM 工作负荷 | `/24`   | `10.10.2.0/24`   |
| ExpressRoute 对等互连 | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>后续步骤 

下一步是了解[私有云存储的概念](concepts-storage.md)。

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

