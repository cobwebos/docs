---
title: Azure 虚拟 WAN 概述 | Microsoft Docs
description: 了解如何通过虚拟 WAN 自动建立大规模的分支到分支连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54d177d7542d7501a91a955be20af776b16657a2
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182359"
---
# <a name="what-is-azure-virtual-wan"></a>什么是 Azure 虚拟 WAN？

Azure Virtual WAN 是一种网络服务，它通过 Azure 提供经优化、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 可以手动完成此操作，也可以通过虚拟 WAN 合作伙伴使用首选的合作伙伴设备完成此操作。 有关详细信息，请参阅[首选合作伙伴](https://go.microsoft.com/fwlink/p/?linkid=2019615)一文。 使用首选的合作伙伴设备可以降低操作难度、简化连接和进行配置管理。 Azure WAN 的内置仪表板提供即时故障排除见解，可帮助你节省时间，以及方便查看大规模的连接。

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan.png)

本文提供 Azure 和非 Azure 工作负荷网络连接的快速视图。 虚拟 WAN 提供以下优势：

* **中心和分支中的集成式连接解决方案：** 在本地站点与 Azure 中心之间自动建立站点到站点配置和连接。
* **自动化的分支设置和配置：** 将虚拟网络和工作负荷无缝连接到 Azure 中心。
* **直观的故障排除：** 可以查看 Azure 中的端到端流，并使用此信息来执行所需的操作。

## <a name="s2s"></a>站点到站点连接

若要使用虚拟 WAN 创建站点到站点连接，可以咨询[虚拟 WAN 合作伙伴](https://go.microsoft.com/fwlink/p/?linkid=2019615)，或手动创建连接。

### <a name="s2spartner"></a>与虚拟 WAN 合作伙伴合作

与虚拟 WAN 合作伙伴合作时，此过程包括：

1. 分支设备 (VPN/SDWAN) 控制器经过身份验证后，使用 [Azure 服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)将站点导向型信息导出到 Azure。
2. 分支设备 (VPN/SDWAN) 控制器获取 Azure 连接配置并更新本地设备。 这样可以自动完成配置下载，以及本地 VPN 设备的编辑和更新。
3. 设备获得正确的 Azure 配置后，将与 Azure WAN 建立站点到站点连接（两个活动隧道）。 Azure 同时支持 IKEv1 和 IKEv2。 BGP 是可选的。


如果不想使用首选合作伙伴，可以手动配置连接，请参阅[使用虚拟 WAN 创建站点到站点连接](virtual-wan-site-to-site-portal.md)。

## <a name="p2s"></a>点到站点连接（预览版）

点到站点 (P2S) 连接用于创建从单个客户端计算机到虚拟中心的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 对于要从远程位置（例如从家里或会议室）连接的远程工作者，此解决方案很有用。 如果只有一些客户端需要连接，则还可以使用 P2S VPN 这一解决方案来代替 S2S VPN。

若要手动创建连接，请参阅[使用虚拟 WAN 创建点到站点连接](https://go.microsoft.com/fwlink/p/?linkid=2020051&clcid)。

## <a name="er"></a>ExpressRoute 连接（预览版）

若要手动创建连接，请参阅[使用虚拟 WAN 创建 ExpressRoute 连接](https://go.microsoft.com/fwlink/p/?linkid=2020148&clcid)。


## <a name="resources"></a>虚拟 WAN 资源

若要配置端到端虚拟 WAN，请创建以下资源：

* **virtualWAN：** virtualWAN 资源表示 Azure 网络的一个虚拟叠加层，是多个资源的集合。 它包含要部署到虚拟 WAN 中的所有虚拟中心的链接。 虚拟 WAN 资源相互隔离，不能包含公用中心。 跨虚拟 WAN 的虚拟中心不会相互通信。 “允许分支到分支流量”属性允许 VPN 站点之间的流量以及启用 ExpressRoute 的站点的 VPN。 请注意，Azure 虚拟 WAN 中的 ExpressRoute 当前处于预览状态。

* **站点：** 称作 vpnsite 的站点资源表示本地 VPN 设备及其设置。 可以通过与虚拟 WAN 合作伙伴合作，使用一个内置的解决方案自动将此信息导出到 Azure。

* **中心：** 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含不同的服务终结点，可从本地网络 (vpnsite) 建立连接。 中心是区域中网络的核心。 每个 Azure 区域只能有一个中心。 使用 Azure 门户创建中心时，会创建虚拟中心 VNet 和虚拟中心 vpngateway。

  中心网关与用于 ExpressRoute 和 VPN 网关的虚拟网络网关不同。 例如，使用虚拟 WAN 时，不要直接从本地站点来与 VNet 建立站点到站点连接， 而应该与中心建立站点到站点连接。 流量始终通过中心网关。 这意味着，VNet 不需要自身的虚拟网络网关。 虚拟 WAN 可让 VNet 通过虚拟中心和虚拟中心网关轻松利用缩放功能。 

* **中心虚拟网络连接：** 中心虚拟网络连接资源用于将中心无缝连接到虚拟网络。 目前，只能连接到同一中心区域内的虚拟网络。

* **中心路由表：** 可以创建一个虚拟中心路由，并将该路由应用于虚拟中心路由表。 可以将多个路由应用于虚拟中心路由表。

## <a name="faq"></a>常见问题解答

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>后续步骤

* 查看[虚拟 WAN 合作伙伴和位置](https://aka.ms/virtualwan)