---
title: 使用 Azure 专用终结点私下连接到 Web 应用
description: 使用 Azure 专用终结点私下连接到 Web 应用
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756276"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>为 Azure Web 应用使用专用终结点（预览）

> [!Note]
> 预览版在美国东部和美国西部 2 区提供，适用于所有高级 V2 Windows 和 Linux Web 应用和弹性高级功能。 

您可以将 Azure Web 应用的专用终结点用于允许位于专用网络中的客户端通过专用链接安全地访问应用。 专用终结点使用 Azure VNet 地址空间中的 IP 地址。 专用网络上的客户端和 Web 应用之间的网络流量会通过 VNet 和 Microsoft 骨干网络上的专用链接遍历，从而消除公共 Internet 的暴露。

为 Web 应用使用专用终结点使您能够：

- 通过配置专用终结点来保护 Web 应用，从而消除公共公开。
- 使用 VPN 或 ExpressRoute 专用对等互连从连接到 VNet 的本地网络安全地连接到 Web 应用。

如果只需要 VNet 和 Web 应用之间的安全连接，则服务终结点是最简单的解决方案。 如果需要通过 Azure 网关、区域对等 VNet 或全局对等 VNet 从本地访问 Web 应用，则私有终结点就是解决方案。  

有关详细信息，请参阅[服务终结点][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概述

专用终结点是虚拟网络 （VNet） 中子网中的 Azure Web 应用的特殊网络接口 （NIC）。
为 Web 应用创建专用终结点时，它可在专用网络上的客户端和 Web 应用之间提供安全连接。 从 VNet 的 IP 地址范围内为专用终结点分配了 IP 地址。
专用终结点和 Web 应用之间的连接使用安全的[专用链接][privatelink]。 专用终结点仅用于传入 Web 应用的流。 传出流不会使用此专用终结点，但您可以通过[VNet 集成功能][vnetintegrationfeature]将传出流注入到其他子网中的网络。

插入专用终结点的子网可以具有其他资源，您不需要专用空子网。
您还可以将专用终结点部署到与 Web 应用不同的区域中。 

> [!Note]
>VNet 集成功能不能使用与专用终结点相同的子网，这是 VNet 集成功能的限制。

从安全角度来看：

- 当您将专用终结点启用到 Web 应用时，将禁用所有公共访问。
- 您可以在其他 VNet 和子网中启用多个专用终结点，包括其他地区的 VNet。
- 专用终结点 NIC 的 IP 地址必须是动态的，但在删除专用终结点之前将保持不变。
- 专用终结点的 NIC 不能关联 NSG。
- 承载专用终结点的子网可以关联 NSG，但必须禁用专用终结点的网络策略实施：请参阅[禁用专用终结点的网络策略][disablesecuritype]。 因此，您不能通过任何 NSG 筛选对私有终结点的访问。
- 当您将专用终结点启用到 Web 应用时，不会评估 Web 应用[的访问限制][accessrestrictions]配置。
- 您可以通过删除目标为 Internet 或 Azure 服务的所有 NSG 规则来降低来自 VNet 的数据外泄风险。 但是，在子网中添加 Web 应用专用终结点将允许您访问在同一部署戳中托管并暴露到 Internet 的任何 Web 应用。

在 Web 应用的 Web HTTP 日志中，您将找到客户端源 IP。 这是使用 TCP 代理协议实现的，将客户端 IP 属性转发到 Web 应用。 有关详细信息，请参阅使用[TCP 代理 v2 获取连接信息][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 应用专用终结点全局概述](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

由于此功能处于预览状态，因此在预览期间我们不会更改 DNS 条目。 您需要自行管理专用 DNS 服务器或 Azure DNS 专用区域中的 DNS 条目。
如果需要使用自定义 DNS 名称，则必须在 Web 应用中添加自定义名称。 在预览期间，必须像使用公共 DNS 解析一样验证自定义名称。 有关详细信息，请参阅[自定义 DNS 验证][dnsvalidation]。

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价][pricing]。

## <a name="limitations"></a>限制

我们正在定期改进专用链接功能和专用终结点，请查看[本文][pllimitations]，了解有关限制的最新信息。

## <a name="next-steps"></a>后续步骤

要通过门户部署 Web 应用的专用终结点，请参阅[如何私下连接到 Web 应用][howtoguide]




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
