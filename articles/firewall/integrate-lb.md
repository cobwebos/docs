---
title: 将 Azure 防火墙与 Azure 标准负载均衡器相集成
description: 了解如何将 Azure 防火墙与 Azure 标准负载均衡器相集成
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193745"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>将 Azure 防火墙与 Azure 标准负载均衡器相集成

可将 Azure 防火墙集成到使用 Azure 标准负载均衡器（公共或内部）的虚拟网络中。 

首选的设计是将内部负载均衡器集成与 Azure 防火墙中，因为这是要简单得多的设计。 如果已经有了一个部署，并且你想要将它保存在位置，可以使用公共负载均衡器。 但需要注意，非对称路由问题可能会破坏公共负载均衡器方案的功能。

有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>公共负载均衡器

使用公共负载均衡器时，部署的负载均衡器使用公共前端 IP 地址。

### <a name="asymmetric-routing"></a>非对称路由

非对称路由是指数据包采用一条路径发往目标，并采用另一条路径返回到源。 如果子网的默认路由转到防火墙的专用 IP 地址，并且使用的是公共负载均衡器，则会出现非对称路由问题。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，并且无法识别此类已建立的会话，因此会丢弃返回的数据包。

### <a name="fix-the-routing-issue"></a>解决路由问题

将 Azure 防火墙部署到子网时，一个步骤是为子网创建默认路由，用于通过 AzureFirewallSubnet 中的防火墙专用 IP 地址定向数据包。 有关详细信息，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md#create-a-default-route)。

在负载均衡器方案中引入防火墙时，Internet 流量需要通过防火墙的公共 IP 地址传入。 在此处，防火墙将应用其防火墙规则，并将数据包的网络地址转换 (NAT) 成负载均衡器的公共 IP 地址。 这就是问题所在。 数据包抵达防火墙的公共 IP 地址，但通过专用 IP 地址返回到防火墙（使用默认路由）。
若要避免此问题，请为防火墙的公共 IP 地址创建附加的主机路由。 发往防火墙公共 IP 地址的数据包将通过 Internet 路由。 这可以避免默认路由转到防火墙的专用 IP 地址。

![非对称路由](media/integrate-lb/Firewall-LB-asymmetric.png)

例如，以下路由适用于公共 IP 地址为 13.86.122.41、专用 IP 地址为 10.3.1.4 的防火墙。

![路由表](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>内部负载均衡器

使用内部负载均衡器时，部署的负载均衡器使用专用前端 IP 地址。

此方案不会出现非对称路由问题。 传入的数据包抵达防火墙的公共 IP 地址、转换为负载均衡器的专用 IP 地址，然后使用相同的返回路径返回到防火墙的专用 IP 地址。

因此，可以像部署公共负载均衡器方案一样部署此方案，但无需防火墙公共 IP 地址主机路由。

## <a name="additional-security"></a>其他安全性

若要进一步增强负载均衡方案的安全性，可以使用网络安全组 (NSG)。

例如，可以在负载均衡虚拟机所在的后端子网中创建 NSG。 允许源自防火墙 IP 地址/端口的传入流量。

有关 NSG 的详细信息，请参阅[安全组](../virtual-network/security-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。