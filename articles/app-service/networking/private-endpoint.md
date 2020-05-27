---
title: 使用 Azure 专用终结点以私密方式连接到 Web 应用
description: 使用 Azure 专用终结点以私密方式连接到 Web 应用
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a95c021153a458a4e3f804e64724b73ea1f1937
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198821"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>为 Azure Web 应用使用专用终结点（预览版）

> [!Note]
> 在“美国东部”和“美国西部 2”区域中，针对所有高级 V2 Windows 和 Linux Web 应用以及弹性高级函数提供了此预览版。 

可以为 Azure Web 应用使用专用终结点，以允许位于专用网络中的客户端通过专用链接安全地访问应用。 专用终结点使用你的 Azure VNet 地址空间中的 IP 地址。 专用网络上客户端与 Web 应用之间的网络流量将通过 VNet 以及 Microsoft 主干网络上的专用链接，因此不会从公共 Internet 公开。

对 Web 应用使用专用终结点的目的如下：

- 通过配置专用终结点来保护 Web 应用，避免公开。
- 从那些使用 VPN 或 ExpressRoute 专用对等互连连接到 VNet 的本地网络安全地连接到 Web 应用。

如果你只是在 VNet 与 Web 应用之间需要安全连接，则服务终结点是最简单的解决方案。 如果你还需要从本地通过 Azure 网关、区域对等互连 VNet 或全局对等互连 VNet 来访问 Web 应用，请使用专用终结点作为解决方案。  

有关详细信息，请参阅[服务终结点][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概述

专用终结点是一个特殊的网络接口 (NIC)，适用于虚拟网络 (VNet) 的子网中的 Azure Web 应用。
为 Web 应用创建专用终结点时，它会在专用网络上的客户端与 Web 应用之间提供安全连接。 专用终结点是从 VNet 的 IP 地址范围分配的 IP 地址。
专用终结点与 Web 应用之间的连接使用安全的[专用链接][privatelink]。 专用终结点仅用于到 Web 应用的传入流。 传出流将不使用此专用终结点，但你可以通过 [VNet 集成功能][vnetintegrationfeature]将传出流注入到你的网络中的另一个子网。

在其中插入专用终结点的子网中可以有其他资源，不需要是专用的空子网。
你还可以在与 Web 应用不同的区域中部署专用终结点。 

> [!Note]
>VNet 集成功能不能使用与专用终结点相同的子网，这是 VNet 集成功能的局限性。

从安全角度来看：

- 当你启用到 Web 应用的专用终结点时，将禁用所有公共访问权限。
- 可以在其他 VNet 和子网（包括其他区域中的 VNet）中启用多个专用终结点。
- 专用终结点 NIC 的 IP 地址必须是动态的，但在你删除专用终结点之前，该地址会保持不变。
- 专用终结点的 NIC 不能有关联的 NSG。
- 承载专用终结点的子网可以有一个关联的 NSG，但你必须禁用专用终结点的网络策略实施：请参阅[禁用专用终结点的网络策略][disablesecuritype]。 因此，不能按任何 NSG 来筛选对专用终结点的访问权限。
- 当你启用到 Web 应用的专用终结点时，系统不会评估对 Web 应用的[访问限制][accessrestrictions]配置。
- 可以通过删除目标为标记“Internet”或“Azure 服务”的所有 NSG 规则来降低 VNet 中的数据泄露风险。 但是，通过在子网中添加 Web 应用专用终结点，你将能够访问托管在同一部署缩放单元中并向 Internet 公开的任何 Web 应用。

在 Web 应用的 Web HTTP 日志中，可找到客户端源 IP。 这是使用 TCP 代理协议实现的，它将客户端 IP 属性转发到 Web 应用。 有关详细信息，请参阅[使用 TCP 代理 v2 获取连接信息][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 应用专用终结点全局概述](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

由于此功能为预览版，我们不会在预览版期间更改 DNS 条目。 你需要自行在专用 DNS 服务器或 Azure DNS 专用区域中管理 DNS 条目。
如果你需要使用自定义 DNS 名称，则必须在 Web 应用中添加自定义名称。 在预览版期间，与任何自定义名称一样，必须使用公共 DNS 解析来验证该自定义名称。 有关详细信息，请参阅[自定义 DNS 验证][dnsvalidation]。

如果需要使用 Kudu 控制台或 Kudu REST API（例如，使用 Azure DevOps 自托管代理进行部署），则需要在 Azure DNS 专用区域或自定义 DNS 服务器中创建两个记录。 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价][pricing]。

## <a name="limitations"></a>限制

将弹性高级计划中的 Azure 函数与专用终结点配合使用时，若要在 Azure Web 门户中运行或执行函数，你必须具有直接网络访问权限，否则会收到 HTTP 403 错误。 换句话说，你的浏览器必须能够访问专用终结点，以便从 Azure Web 门户执行该函数。 

在预览版期间，只会在专用终结点后面公开生产槽，其他槽只能通过公共终结点访问。

我们会定期改进专用链接功能和专用终结点。若要了解有关限制的最新信息，请查看[此文][pllimitations]。

## <a name="next-steps"></a>后续步骤

若要通过门户为 Web 应用部署专用终结点，请参阅[如何以私密方式连接到 Web 应用][howtoguide]




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
