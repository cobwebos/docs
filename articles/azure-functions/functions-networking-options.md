---
title: Azure Functions 网络选项
description: Azure Functions 中提供的所有网络选项的概述
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 9fe7147325b2e14a7ae6bb4b31aa941fb4059b11
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690832"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍适用于 Azure Functions 的托管选项的网络功能。 以下所有网络选项都提供了在不使用 internet 可路由地址的情况下访问资源的功能，或限制 internet 访问函数应用。 

托管模型提供不同级别的网络隔离。 选择正确的将有助于满足网络隔离要求。

可以通过以下几种方式承载函数应用：

* 有一组计划选项可在多租户基础结构上运行，具有各种级别的虚拟网络连接和缩放选项：
    * [消耗计划](functions-scale.md#consumption-plan)，可在响应负载时动态缩放，并提供最少的网络隔离选项。
    * [高级计划](functions-scale.md#premium-plan)，还可以动态缩放，同时提供更全面的网络隔离。
    * Azure[应用服务计划](functions-scale.md#app-service-plan)，以固定规模运行，并为高级计划提供类似的网络隔离。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法将您的函数部署到您的虚拟网络中，并提供完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

|                |[消耗计划](functions-scale.md#consumption-plan)|[高级计划（预览）](functions-scale.md#premium-plan)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[& 专用站点访问的入站 IP 限制](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[虚拟网络集成](#virtual-network-integration)|❌No|✅Yes （区域）|✅Yes （区域和网关）|✅Yes|
|[虚拟网络触发器（非 HTTP）](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[混合连接](#hybrid-connections)|❌No|✅Yes|✅Yes|✅Yes|
|[出站 IP 限制](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>入站 IP 限制

你可以使用 IP 限制来定义允许/拒绝访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址。 如果有一个或多个条目，则在列表的末尾存在一个隐式 "全部拒绝"。 IP 限制适用于所有函数宿主选项。

> [!NOTE]
> 使用网络限制后，只能在虚拟网络内使用门户编辑器，或者在将用于访问 Azure 门户的计算机的 IP 列入白名单时使用。 但是，你仍然可以从任何计算机访问 "**平台功能**" 选项卡上的任何功能。

若要了解详细信息，请参阅[Azure App Service 静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>专用站点访问

专用站点访问是指使应用只能从专用网络（如 Azure 虚拟网络中的）访问。 
* 配置**服务终结点**时，"[高级](./functions-premium-plan.md)"、"使用情况"、"功能"、"使用情况-计划" 和 "[应用服务计划](functions-scale.md#app-service-plan)" 中提供了专用站点访问。 
    * 可以基于每个应用在平台功能 > 网络上配置服务终结点，> 配置访问限制 > 添加规则。 现在可以选择虚拟网络作为规则的 "类型"。
    * 有关详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)
        * 请注意，对于服务终结点，即使配置了虚拟网络集成，函数仍具有 internet 的完全出站访问权限。
* 通过使用内部负载均衡器（ILB）配置的应用服务环境，还可以使用专用站点访问。 有关详细信息，请参阅[创建和使用带有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

## <a name="virtual-network-integration"></a>虚拟网络集成

通过虚拟网络集成，函数应用可以访问虚拟网络中的资源。 此功能适用于高级计划和应用服务计划。 如果你的应用处于应用服务环境中，它已在虚拟网络中，不需要使用虚拟网络集成来访问同一虚拟网络中的资源。

你可以使用虚拟网络集成来启用从应用到你的虚拟网络中运行的数据库和 web 服务的访问。 通过虚拟网络集成，无需向 VM 上的应用程序公开公共终结点。 可以改用专用的非 internet 可路由地址。

虚拟网络集成功能有两种形式

1. 通过区域虚拟网络集成，可在同一区域中与虚拟网络集成。 此功能的这种形式需要在同一区域的虚拟网络中有一个子网。 此功能仍处于预览阶段，但 Windows 应用生产工作负荷支持此功能，但有一些注意事项。
2. 网关所需的虚拟网络集成启用了与远程区域或经典虚拟网络的虚拟网络的集成。 此版本的功能要求在 VNet 中部署虚拟网络网关。 这是基于点到站点 VPN 的功能，仅在 Windows 应用中受支持。

应用一次只能使用一种形式的 VNet 集成功能。 问题就是应该使用哪种功能。 您可以使用任何一种方法。 不过，清楚的区别在于：

| 问题  | 解决方案 | 
|----------|----------|
| 希望在同一区域中访问 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16） | 区域 VNet 集成 |
| 想要在经典 VNet 中或另一个区域中的 VNet 中访问资源 | 网关所需的 VNet 集成 |
| 想要跨 ExpressRoute 访问 RFC 1918 终结点 | 区域 VNet 集成 |
| 希望跨服务终结点实现资源 | 区域 VNet 集成 |

这两项功能都不允许跨 ExpressRoute 访问非 RFC 1918 地址。 为此，你现在需要使用 ASE。

使用区域 VNet 集成不会将 VNet 连接到本地或配置服务终结点。 这是单独的网络配置。 区域 VNet 集成只是使应用程序能够在这些连接类型上进行调用。

无论使用何种版本，VNet 集成都使函数应用能够访问虚拟网络中的资源，但不会授予对虚拟网络中的函数应用的专用站点访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成仅适用于从应用到 VNet 的出站调用。 

VNet 集成功能：

* 需要标准、高级或 PremiumV2 应用服务计划
* 支持 TCP 和 UDP
* 适用于应用服务应用和函数应用

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

功能中的虚拟网络集成使用共享基础结构和应用服务 web 应用。 若要阅读有关这两种类型的虚拟网络集成的详细信息，请参阅：
* [区域 VNET 集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [网关所需的 VNet 集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要详细了解如何使用虚拟网络集成，请参阅将[function app 与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="connecting-to-service-endpoint-secured-resources"></a>连接到服务终结点保护的资源

> [!note] 
> 暂时地，在对下游资源配置访问限制后，新的服务终结点就可能需要长达12小时才能供函数应用使用。 在此期间，资源将对你的应用程序完全不可用。

为了提供更高级别的安全性，可以使用服务终结点将多个 Azure 服务限制为一个虚拟网络。 然后，必须将 function app 与该虚拟网络集成，才能访问该资源。 支持虚拟网络集成的所有计划都支持此配置。

[在此处阅读有关虚拟网络服务终结点的详细信息。](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>将存储帐户限制在虚拟网络中
创建 function app 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 目前不能对此帐户使用任何虚拟网络限制。 如果在用于 function app 的存储帐户上配置虚拟网络服务终结点，则会中断你的应用程序。

[在此处了解有关存储帐户要求的详细信息。](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器（非 HTTP）

目前，若要在虚拟网络中使用 HTTP 以外的函数触发器，必须在应用服务计划或应用服务环境中运行函数应用。

若要提供一个示例，若要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量，则需要在与该虚拟网络集成虚拟网络的应用服务计划中部署函数应用，以配置 Azure Cosmos DB 触发器从该资源。 在预览中，配置 VNET 集成不会允许高级计划触发该 Azure Cosmos DB 资源。

检查[此列表中的所有非 HTTP 触发器](./functions-triggers-bindings.md#supported-bindings)，以仔细检查支持的内容。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是 Azure 中继的一项功能，可用于访问其他网络中的应用程序资源。 使用混合连接可以从应用访问应用程序终结点。 不能使用它来访问应用程序。 混合连接可用于除了消耗计划中运行的函数。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着混合连接的终结点可以位于任何操作系统和任何应用程序上，前提是你要访问 TCP 侦听端口。 混合连接功能不知道或不关心应用程序协议的定义，或者正在访问的内容。 它只提供网络访问。

若要了解详细信息，请参阅[混合连接的应用服务文档](../app-service/app-service-hybrid-connections.md)，该文档通过相同的配置步骤支持函数。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

出站 IP 限制仅适用于部署到应用服务环境的函数。 你可以为部署应用服务环境的虚拟网络配置出站限制。

将高级计划或应用服务计划中的函数应用与虚拟网络集成时，应用仍可对 internet 进行出站调用。

## <a name="next-steps"></a>后续步骤
若要详细了解网络和 Azure Functions： 

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [阅读函数网络常见问题](./functions-networking-faq.md)
* [详细了解与应用服务/功能的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [启用应用服务环境的更多网络功能和控制](../app-service/environment/intro.md)
* [使用混合连接连接到单个本地资源，而无需更改防火墙](../app-service/app-service-hybrid-connections.md)
