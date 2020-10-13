---
title: 虚拟 WAN 和 SD 广域网连接体系结构
titleSuffix: Azure Virtual WAN
description: 了解如何将专用 SD-WAN 与 Azure 虚拟 WAN 互连
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: e3f6f947b86b1cb34fde66c62199336403037827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828063"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>带有 Azure 虚拟 WAN 的 SD-WAN 连接体系结构

Azure 虚拟 WAN 是一个网络服务，其中整合了许多云连接与安全服务，并提供单一操作界面。 这些服务包括分支（通过站点到站点 VPN 连接）、远程用户（点到站点 VPN）、专用连接 (ExpressRoute)、VNet 的云内部可中转连接、VPN 和 ExpressRoute 互连、路由、Azure 防火墙，以及专用连接加密。

尽管 Azure 虚拟 WAN 本身是软件定义的 WAN (SD-WAN)，但它也用于实现与基于场地的 SD-WAN 技术和服务无缝互连。 许多此类服务由我们的[虚拟 WAN](virtual-wan-locations-partners.md) 生态系统和 Azure 网络托管服务合作伙伴 [(MSP)](../networking/networking-partners-msp.md) 提供。 将其专用 WAN 转换为 SD-WAN 的企业在将其专用 SD-WAN 与 Azure 虚拟 WAN 互连时可以使用多个选项。 企业可从以下选项中进行选择：

* 直接互连模型
* 直接互连模型与 NVA-VWAN
* 间接互连模型
* 使用企业偏好的托管服务提供商 ([MSP](../networking/networking-partners-msp.md)) 的托管混合 WAN 模型

在所有这些情况下，虚拟 WAN 与 SD-WAN 的互连与连接端类似，但业务流程和操作端可能存在差异。

## <a name="direct-interconnect-model"></a><a name="direct"></a>直接互连模型

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="直接互连模型":::

在此体系结构模型中，SD-WAN 分支客户场地设备 (CPE) 通过 IPsec 连接来与虚拟 WAN 中心直接连接。 分支 CPE 还可以通过专用 SD-WAN 连接到其他分支，或利用虚拟 WAN 建立分支到分支的连接。 需要访问其在 Azure 中的工作负荷的分支可以通过在虚拟 WAN 中心内终止的 IPsec 隧道，直接且安全地访问 Azure。

SD-WAN CPE 合作伙伴可以启用自动化，以通过其相关 CPE 设备，将通常比较繁琐且容易出错的 IPsec 连接自动化。 自动化使 SD-WAN 控制器能够通过虚拟 WAN API 来与 Azure 通信，以配置虚拟 WAN 站点，以及将必要的 IPsec 隧道配置推送到分支 CPE。 有关不同 SD-WAN 合作伙伴提供的虚拟 WAN 互连自动化的说明，请参阅[自动化指导原则](virtual-wan-configure-automation-providers.md)。

SD-WAN CPE 仍然是实现和强制实施流量优化和路径选择的地方。 

在此模型中，基于实时流量特征的某些供应商专用流量优化可能不受支持，因为与虚拟 WAN 的连接是通过 IPsec 建立的，并且 IPsec VPN 在虚拟 WAN VPN 网关上终止。 例如，分支 CPE 上的动态路径选择是可行的，因为分支设备与另一个 SD-WAN 节点交换各种网络数据包信息，从而可以在分支上动态识别要用于各种优先级流量的最佳链接。 在需要进行最后一英里优化的区域（分支到最靠近的 Microsoft POP），此功能可能非常有用。

使用虚拟 WAN，用户可以获得 Azure 路径选择，即，通过从分支 CPE 到虚拟 WAN VPN 网关的多个 ISP 链接进行基于策略的路径选择。 虚拟 WAN 允许设置多个来自同一 SD-WAN 分支 CPE 的链接（路径）；每个链接代表从 SD-WAN CPE 的某个唯一公共 IP 到 Azure 虚拟 WAN VPN 网关的两个不同实例的双隧道连接。 SD-WAN 供应商可以根据其在 CPE 链接上的策略引擎设置的流量策略，实施连接 Azure 的最佳路径。 在 Azure 端上，将同等对待传入的所有连接。

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>直接互连模型与 NVA-VWAN

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="直接互连模型":::

此体系结构模型支持将第三方 [网络虚拟设备 (NVA 直接部署到虚拟中心) ](https://docs.microsoft.com/azure/virtual-wan/about-nva-hub)。 这允许希望将其分支 CPE 连接到虚拟中心中的同一品牌 NVA 的客户，以便在连接到 Azure 工作负载时，可以利用专用的端到端 SD 功能。 

几个虚拟 WAN 合作伙伴已努力提供在部署过程中自动配置 NVA 的体验。 将 NVA 预配到 virtual hub 后，NVA 所需的任何其他配置必须通过 NVA 合作伙伴门户或管理应用程序完成。 无法直接访问 NVA。 可直接部署到 Azure 虚拟 WAN 中心的 Nva 专门设计为在虚拟中心中使用。 对于在 VWAN hub 中支持 NVA 的合作伙伴及其部署指南，请参阅 [虚拟 WAN 合作伙伴](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) 一文。

SD-WAN CPE 仍然是实现和强制实施流量优化和路径选择的地方。
在此模型中，支持基于实时流量特征的供应商专有流量优化，因为与虚拟 WAN 的连接是通过集线器中的 SD 广域网 NVA 来进行的。

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>间接互连模型

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="直接互连模型":::

在此体系结构模型中，SD-WAN 分支 CPE 间接连接到虚拟 WAN 中心。 如图所示，企业 VNet 中部署了一个 SD-WAN 虚拟 CPE。 此虚拟 CPE 使用 IPsec 连接到虚拟 WAN 中心。 该虚拟 CPE 充当用于接入 Azure 的 SD-WAN 网关。 需要访问其在 Azure 中的工作负荷的分支可以通过 v-CPE 网关进行访问。

由于与 Azure 的连接是通过 v-CPE 网关 (NVA) 建立的，因此从 Azure 工作负荷 VNet 传入和传出到其他 SD-WAN 分支的所有流量都通过 NVA 传送。 在此模型中，用户负责管理和操作 SD-WAN NVA（包括高可用性、可伸缩性和路由）。
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>托管混合 WAN 模型

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="直接互连模型":::

在此体系结构模型中，企业可以利用托管服务提供商 (MSP) 合作伙伴提供的托管 SD-WAN 服务。 此模型类似于上面所述的直接模型或间接模型。 但是，在此模型中，SD-WAN 设计、业务流程和操作由 SD-WAN 提供商提供。

[Azure 网络 MSP 合作伙伴](../networking/networking-partners-msp.md)可以使用 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) 在企业客户的 Azure 订阅中实施 SD-WAN 和虚拟 WAN 服务，并代表客户操作端到端的混合 WAN。 这些 MSP 还可以在虚拟 WAN 中实施 Azure ExpressRoute，并将其用作端到端的托管服务。

## <a name="additional-information"></a>其他信息

* [包括常见问题解答](virtual-wan-faq.md)
* [解决远程连接性问题](work-remotely-support.md)
