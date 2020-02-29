---
title: 将 Azure 防火墙与 Azure 标准负载均衡器相集成
description: 可将 Azure 防火墙集成到使用 Azure 标准负载均衡器（公共或内部）的虚拟网络中。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196685"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>将 Azure 防火墙与 Azure 标准负载均衡器相集成

可将 Azure 防火墙集成到使用 Azure 标准负载均衡器（公共或内部）的虚拟网络中。 

首选的设计是将内部负载均衡器与 Azure 防火墙集成，因为这是一个更简单的设计。 如果已部署了一个公共负载均衡器，并且想要将其保留，则可以使用该负载均衡器。 但需要注意，非对称路由问题可能会破坏公共负载均衡器方案的功能。

有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>公共负载均衡器

使用公共负载均衡器时，部署的负载均衡器使用公共前端 IP 地址。

### <a name="asymmetric-routing"></a>非对称路由

非对称路由是指数据包采用一条路径发往目标，并采用另一条路径返回到源。 如果子网的默认路由转到防火墙的专用 IP 地址，并且使用的是公共负载均衡器，则会出现非对称路由问题。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，因此它会删除返回的数据包，因为防火墙无法识别此类已建立的会话。

### <a name="fix-the-routing-issue"></a>解决路由问题

将 Azure 防火墙部署到子网时，一个步骤是为子网创建默认路由，用于通过 AzureFirewallSubnet 中的防火墙专用 IP 地址定向数据包。 有关详细信息，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md#create-a-default-route)。

在负载均衡器方案中引入防火墙时，Internet 流量需要通过防火墙的公共 IP 地址传入。 在此处，防火墙将应用其防火墙规则，并将数据包的网络地址转换 (NAT) 成负载均衡器的公共 IP 地址。 这就是问题所在。 数据包抵达防火墙的公共 IP 地址，但通过专用 IP 地址返回到防火墙（使用默认路由）。
若要避免此问题，请为防火墙的公共 IP 地址创建附加的主机路由。 发往防火墙公共 IP 地址的数据包将通过 Internet 路由。 这可以避免默认路由转到防火墙的专用 IP 地址。

![非对称路由](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>路由表示例

例如，以下路由用于公共 IP 地址20.185.97.136 和专用 IP 地址10.0.1.4 的防火墙。

> [!div class="mx-imgBorder"]
> ![路由表](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>NAT 规则示例

在下面的示例中，NAT 规则会将20.185.97.136 的 RDP 流量转换为20.42.98.220 上的负载均衡器：

> [!div class="mx-imgBorder"]
> ![NAT 规则](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>运行状况探测

请记住，如果使用 TCP 运行状况探测到端口80或 HTTP/HTTPS 探测，则需要在负载均衡器池中的主机上运行 web 服务。

## <a name="internal-load-balancer"></a>内部负载均衡器

使用内部负载均衡器时，部署的负载均衡器使用专用前端 IP 地址。

此方案不会出现非对称路由问题。 传入的数据包抵达防火墙的公共 IP 地址、转换为负载均衡器的专用 IP 地址，然后使用相同的返回路径返回到防火墙的专用 IP 地址。

因此，可以像部署公共负载均衡器方案一样部署此方案，但无需防火墙公共 IP 地址主机路由。

## <a name="additional-security"></a>其他安全性

若要进一步增强负载均衡方案的安全性，可以使用网络安全组 (NSG)。

例如，可以在负载均衡虚拟机所在的后端子网中创建 NSG。 允许源自防火墙 IP 地址/端口的传入流量。

![网络安全组](media/integrate-lb/nsg-01.png)

有关 NSG 的详细信息，请参阅[安全组](../virtual-network/security-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。