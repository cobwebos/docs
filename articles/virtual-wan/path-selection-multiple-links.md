---
title: 跨多个 ISP 链接选择 Azure 路径
titleSuffix: Azure Virtual WAN
description: 了解 Azure 路径选择和虚拟 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: cherylmc
ms.openlocfilehash: a83a050b65f5673a86dd07ec13842f6009d2c8dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753342"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>跨多个 ISP 链接选择 Azure 路径

Azure 虚拟 WAN 为用户提供在 VPN 站点中包含链接信息的功能，从而实现了这样的情况： VPN/SD WAN 设备解决方案可以对特定于分支的策略进行编程，以跨多个链接将流量传输到 Azure。 这称为**Azure 路径选择**。

## <a name="architecture"></a>体系结构

为了了解 Azure 路径选择的工作方式，让我们使用虚拟 WAN VPN 站点和站点到站点连接的示例。

VPN 站点表示本地 SD-WAN/VPN 设备，其中包含公共 IP、设备型号和名称等信息。实际的本地 VPN 站点可能有多个 ISP 链接，这些链接也可以包含在虚拟 WAN VPN 站点信息中。 这允许你查看 Azure 中的链接信息。

进入虚拟 WAN VPN 的站点到站点 IPsec 连接将在虚拟中心内的 VPN 网关实例上终止。 站点到站点连接表示 VPN 站点与 Azure VPN 网关之间的连接。 它包含一个或多个链接连接。 每个链接连接都包含两个隧道，每个隧道在 Azure 虚拟 WAN VPN 网关的唯一实例上终止。 在站点到站点连接中，最多可以设置四个链接连接，这样就可以在一个站点到站点连接中拥有多达8个隧道。 Azure 最多支持2000隧道在单个虚拟 WAN VPN 网关中内终止。

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="多链接关系图":::

此图显示了在连接到 Azure 虚拟 WAN 的站点上的多链路。 在此图中：

* 本地分支有两个 ISP 链接（VPN/SD-WAN 设备）。 每个 ISP 链接都对应一个链接连接。

* 假设本地客户管理器 VPN/SD-WAN 设备支持 IKEv1 或 IKEv2 IPsec。

* 每个 Azure 站点到站点虚拟 WAN 连接都包含在其自身内的链接连接。 连接最多支持四个链接连接。 Azure 会按虚拟 WAN 连接的连接单位费用收费。 链接连接不收取任何费用。

* 相反，每个链接连接都包含两个 IPsec 隧道，可以在两个不同的虚拟 WAN VPN 网关实例上终止。 网关设置为主动-主动网关以实现复原。 每个链接连接都需要具有唯一的 IP 地址和 BGP 对等互连 IP。 在关系图中，隧道0可以在实例0处终止，隧道1可以在实例1上终止。

* 提供路径选择的分支设备可以在分支管理解决方案中启用适当的策略，以跨多个链接连接到 Azure。 例如，"ISP 1" 链接可用于更高优先级的流量，"ISP 2" 链接可用作备份。

* 务必要注意的是，虚拟中心 VPN 跨所有终止隧道使用 ECMP （同等开销多路径路由）。

## <a name="next-steps"></a>后续步骤

请参阅[AZURE 常见问题解答](virtual-wan-faq.md)。