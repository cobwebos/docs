---
title: 跨多个 ISP 链接进行 Azure 路径选择
titleSuffix: Azure Virtual WAN
description: 了解 Azure 路径选择和虚拟 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267748"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>跨多个 ISP 链接进行 Azure 路径选择

Azure 虚拟 WAN 为用户提供了在 VPN 站点中包含链接信息的功能，使 VPN/SD-WAN 设备解决方案能够对特定于分支的策略进行编程，引导流量跨不同链接流入 Azure。 这称为 **Azure 路径选择**。

## <a name="architecture"></a>体系结构

为了理解 Azure 路径选择的工作原理，让我们以虚拟 WAN VPN 站点和站点到站点连接为例。

VPN 站点表示包含公共 IP、设备型号和名称等信息的本地 SD-WAN/VPN 设备。实际的本地 VPN 站点可能有多个 ISP 链接，这些链接也可以包含在虚拟 WAN VPN 站点信息中。 这使你可以在 Azure 中查看链接信息。

进入虚拟 WAN 的 VPN 的站点到站点 IPsec 连接在虚拟中心内的 VPN 网关实例上终止。 站点到站点连接表示 VPN 站点与 Azure VPN 网关之间的连接。 它由一个或多个链接连接组成。 每个链接连接都由两个隧道组成，每个隧道都在 Azure 虚拟 WAN VPN 网关的唯一实例上终止。 站点到站点连接最多可以设置四个链接连接，这使站点到站点连接中最多可以有八个隧道。 Azure 支持多达 2000 个隧道，这些隧道在单个虚拟 WAN VPN 网关内终止。

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="多链接关系图":::

此图显示了在连接到 Azure 虚拟 WAN 的站点中的多链接。 在此图中：

* 本地分支（VPN/SD-WAN 设备）有两个 ISP 链接。 每个 ISP 链接对应于一个链接连接。

* 假定本地客户管理器 VPN/SD-WAN 设备支持 IKEv1 或 IKEv2 IPsec。

* 每个 Azure 站点到站点虚拟 WAN 连接均由其自身内部的链接连接组成。 一个连接最多支持四个链接连接。 Azure 会针对虚拟 WAN 连接收取连接单元费用。 链接连接不会产生任何费用。

* 而每个链接连接由两个 IPsec 隧道组成，这些隧道可以在虚拟 WAN VPN 网关的两个不同实例上终止。 网关设置为主动 - 主动网关，以提高复原能力。 每个链接连接需要有一个唯一的 IP 地址和 BGP 对等 IP。 在图中，隧道 0 可以在实例 0 上终止，隧道 1 可以在实例 1 上终止。

* 提供路径选择的分支设备可以在分支管理解决方案中启用适当的策略，以引导流量跨不同链接流入 Azure。 例如，ISP 1 链接可用于更高优先级的流量，ISP 2 链接可以用作备份。

* 需要注意的是，虚拟中心 VPN 在所有终止隧道中使用 ECMP（等价多路径路由）。

## <a name="next-steps"></a>后续步骤

请参阅 [Azure 常见问题解答](virtual-wan-faq.md)。