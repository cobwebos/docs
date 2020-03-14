---
title: 使用 Azure 专用终结点将专用连接到 Web 应用
description: 使用 Azure 专用终结点将专用连接到 Web 应用
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136666"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>使用 Azure Web 应用的专用终结点（预览）

你可以使用 Azure Web 应用的专用终结点，以允许位于专用网络中的客户端通过专用链接安全访问应用。 专用终结点使用 Azure VNet 地址空间中的 IP 地址。 专用网络上的客户端与 Web 应用之间的网络流量通过 Vnet 和 Microsoft 主干网络上的专用链接进行遍历，从而消除了公共 Internet 的泄露。

使用 Web 应用的专用终结点可以：

- 通过配置服务终结点来保护 Web 应用，消除公开公开
- 使用 VPN 或 ExpressRoute 专用对等互连从连接到 Vnet 的本地网络安全连接到 Web 应用。

如果你只需要在 Vnet 与 Web 应用之间建立安全连接，则服务终结点是最简单的解决方案。 如果还需要通过 Azure 网关从本地访问 web 应用，请使用突破对等互连 Vnet 或全局对等互连 Vnet，专用终结点是解决方案。  

有关[服务终结点][serviceendpoint]的详细信息

## <a name="conceptual-overview"></a>概念概述

专用终结点是虚拟网络（Vnet）的子网中的 Azure Web 应用的特殊网络接口（nic）。
为 Web 应用创建专用终结点时，它会在专用网络上的客户端和 Web 应用之间提供安全连接。 专用终结点是从 Vnet 的 IP 地址范围分配的 IP 地址。
专用终结点和 Web 应用之间的连接使用安全的[专用链接][privatelink]。 专用终结点仅用于到 Web 应用的传入流。 传出流将不使用此专用终结点，但你可以通过[Vnet 集成功能][vnetintegrationfeature]将传出流注入到不同子网中的网络。

插入专用终结点的子网中可以有其他资源，无需专用的空子网。
你可以在与 Web 应用不同的区域内部署专用终结点。 

> [!Note]
>Vnet 集成功能不能使用与专用终结点相同的子网，这是 Vnet 集成功能的限制

从安全角度来看：

- 当你启用到 Web 应用的服务终结点时，将禁用所有公共访问权限
- 可以在其他区域的其他 Vnet 和子网中启用多个专用终结点，包括其他区域中的 Vnet
- 专用终结点 NIC 的 IP 地址必须是动态的，但在删除专用终结点之前，该地址将保持不变
- 专用终结点的 NIC 不能有关联的 NSG
- 承载专用终结点的子网可以有一个关联的 NSG，但必须禁用专用终结点的网络策略强制，请参阅[此文][disablesecuritype]。 因此，不能按任何 NSG 对专用终结点的访问权限进行筛选
- 当你启用到 Web 应用的专用终结点时，不会对 Web 应用的[访问限制][accessrestrictions]配置进行评估。
- 可以通过删除渗透中的所有 NSG 规则（其中目标为 Internet 或 Azure 服务）来减少数据的风险。 但在子网中添加 Web 应用服务终结点可让你访问在同一 stamp 中托管的任何 Web 应用，并将其公开到 Internet。

Web 应用的专用终结点可用于层 PremiumV2，并与外部 ASE 隔离。

在 Web 应用的 Web http 日志中，你将找到客户端源 IP。 我们实现了 TCP 代理协议，并将客户端 IP 属性转发到 Web 应用。 有关详细信息，请参阅[此文章][tcpproxy]。

![全局概述][1]


## <a name="dns"></a>DNS

由于此功能处于预览阶段，我们不会在预览过程中更改 DNS 条目。 你需要自行管理专用 DNS 服务器或 Azure DNS 专用区域中的 DNS 条目。 如果需要使用自定义 DNS 名称，则必须在 Web 应用中添加自定义名称。 在预览期间，必须使用公共 DNS 解析方式验证自定义名称是否与任何自定义名称相同。 [自定义 DNS 验证技术参考][dnsvalidation]

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价][pricing]。

## <a name="limitations"></a>限制

我们会定期改善私有链接功能和私有终结点，查看[本文][pllimitations]，了解有关限制的最新信息。

## <a name="next-steps"></a>后续步骤

若要通过门户部署 Web 应用的专用终结点，请参阅[如何将私下连接到 Web 应用][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
