---
title: SD-WAN 连接体系结构
titleSuffix: Azure Virtual WAN
description: 了解如何使用 Azure 虚拟 WAN 互连专用 SD 广域网
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/08/2020
ms.author: cherylmc
ms.openlocfilehash: 34b2282421b9c95ad99ad040cb11847a30d3b52c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199997"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>SD-具有 Azure 虚拟 WAN 的广域网连接体系结构

Azure 虚拟 WAN 是一种网络服务，它通过单一操作接口将许多云连接和安全服务结合在一起。 这些服务包括分支（通过站点到站点 VPN）、远程用户（点到站点 VPN）、专用（ExpressRoute）连接、适用于 Vnet 的内部可传递连接、VPN 和 ExpressRoute 互连、路由、Azure 防火墙，以及用于专用连接的加密。

尽管 Azure 虚拟 WAN 本身是一个软件定义的 WAN （SD-WAN），但它也设计用于实现与基于本地的 SD 的技术和服务的无缝互连。 许多此类服务都是由我们的[虚拟广域网](virtual-wan-locations-partners.md)生态系统和 Azure 网络托管服务合作伙伴[（msp）](../networking/networking-partners-msp.md)提供的。 如果企业将其专用广域网转换为 SD 广域网，则在将其专用 SD 广域网与 Azure 虚拟 WAN 建立互连时，会有一些选择。 企业可以从以下选项中进行选择：

* 直接互连模型
* 间接互连模型
* 使用其喜爱的托管服务提供程序的托管混合 WAN 模型[MSP](../networking/networking-partners-msp.md)

在所有这些情况下，虚拟 WAN 与 SD 广域网的互连类似于连接端，但业务流程和操作方面可能会有所不同。

## <a name="direct-interconnect-model"></a><a name="direct"></a>直接互连模型

![直接互连模型](./media/sd-wan-connectivity-architecture/direct.png)

在此体系结构模型中，SD WAN 分支客户本地设备（CPE）通过 IPsec 连接直接连接到虚拟 WAN hub。 Branch CPE 还可以通过专用 SD-WAN 连接到其他分支，或利用虚拟 WAN 进行分支到分支连接。 需要在 Azure 中访问其工作负荷的分支可以通过在虚拟 WAN 集线器中终止的 IPsec 隧道直接安全地访问 Azure。

SD-WAN CPE 合作伙伴可以启用自动化，以便从其各自的 CPE 设备自动执行通常单调乏味且容易出错的 IPsec 连接。 自动化允许 SD WAN 控制器通过虚拟 WAN API 与 Azure 进行通信，以配置虚拟 WAN 站点，以及将必要的 IPsec 隧道配置推送到分支 CPEs。 请参阅[自动指导原则](virtual-wan-configure-automation-providers.md)，了解各种 SD WAN 伙伴的虚拟 WAN 互连自动化的说明。

SD WAN CPE 继续是实现和强制执行流量优化以及路径选择的位置。 

在此模型中，可能不支持基于实时流量特征的某些供应商专用流量优化，因为与虚拟 WAN 的连接是通过 IPsec 进行的，并且 IPsec VPN 在虚拟 WAN VPN 网关上终止。 例如，分支 CPE 上的动态路径选择是可行的，因为分支设备将各种网络数据包信息与另一个 SD WAN 节点交换在一起，从而确定在分支上动态地使用各种优先级的流量的最佳链接。 此功能在需要最后一英里优化（分支到最近的 Microsoft POP）的区域中可能非常有用。

利用虚拟广域网，用户可以获得 Azure 路径选择，这是从分支 CPE 到虚拟 WAN VPN 网关的多个 ISP 链接中的基于策略的路径选择。 虚拟 WAN 允许从同一 SD WAN branch CPE 中设置多个链接（路径）;每个链接表示从 SD 的唯一公共 IP 到 Azure 虚拟 WAN VPN 网关的两个不同实例的双隧道连接。 SD-WAN 供应商可以根据其策略引擎在 CPE 链接上设置的流量策略，实现 Azure 的最佳路径。

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>间接互连模型

![间接互连模型](./media/sd-wan-connectivity-architecture/indirect.png)

在此体系结构模型中，SD WAN branch CPEs 间接连接到虚拟 WAN hub。 如图所示，SD WAN 虚拟 CPE 部署在企业 VNet 中。 此虚拟 CPE 进而使用 IPsec 连接到虚拟 WAN 集线器。 虚拟 CPE 充当 Azure 中的 SD 广域网网关。 需要在 Azure 中访问其工作负荷的分支可以通过 "v-CPE" 网关访问它们。

由于与 Azure 的连接是通过 v-CPE 网关（NVA）进行的，因此进出 Azure 工作负荷 Vnet 的所有流量都将通过 NVA。 在此模型中，用户负责管理和操作 SD WAN NVA，包括高可用性、可伸缩性和路由。
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>托管混合 WAN 模型

![托管混合 WAN 模型](./media/sd-wan-connectivity-architecture/hybrid.png)

在此体系结构模型中，企业可以利用托管服务提供商（MSP）合作伙伴提供的托管 SD WAN 服务。 此模型类似于上面所述的直接或间接模型。 但是，在此模型中，sd wan 设计、业务流程和操作由 SD WAN 提供程序提供。

[Azure 网络 MSP 合作伙伴](../networking/networking-partners-msp.md)可以使用[azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/)在企业客户的 AZURE 订阅中实现 SD 广域网和虚拟 WAN 服务，并代表客户操作端到端混合 WAN。 这些 Msp 还可以将 Azure ExpressRoute 实现到虚拟 WAN，并将其作为端到端托管服务进行操作。

## <a name="additional-information"></a>其他信息

* [包含常见问题](virtual-wan-faq.md)
* [解决远程连接](work-remotely-support.md)
