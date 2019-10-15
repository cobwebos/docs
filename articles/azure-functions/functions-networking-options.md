---
title: Azure Functions 网络选项
description: 在 Azure Functions 中可用的所有网络选项的概述
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: ca7985ee302b35f8e7b39c46c229c7b0b263ffce
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170661"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍了可在各种 Azure Functions 托管选项中使用的网络功能。 以下所有网络选项都可以提供某种功能来访问资源，而无需使用可通过 Internet 路由的地址，或限制对函数应用的 Internet 访问。 

托管模型具有不同的可用网络隔离级别。 选择适当的级别有助于满足网络隔离要求。

可按多种方式托管函数应用：

* 有一组计划选项可在多租户基础结构上运行，并提供不同级别的虚拟网络连接和缩放选项：
    * [消耗计划](functions-scale.md#consumption-plan)：可以动态缩放以响应负载，但只能提供极少量的网络隔离选项。
    * [高级计划](functions-scale.md#premium-plan), 还可以动态缩放, 同时提供更全面的网络隔离。
    * Azure[应用服务计划](functions-scale.md#app-service-plan), 以固定规模运行, 并为高级计划提供类似的网络隔离。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法将您的函数部署到您的虚拟网络中, 并提供完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

|                |[消耗计划](functions-scale.md#consumption-plan)|[高级计划 (预览)](functions-scale.md#premium-plan)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[& 专用站点访问的入站 IP 限制](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虚拟网络集成](#virtual-network-integration)|❌No|✅是 (区域)|✅是 (区域和网关)|✅是|
|[虚拟网络触发器 (非 HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅是|✅是|
|[混合连接](#hybrid-connections)|❌No|❌No|✅是|✅是|
|[出站 IP 限制](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅是|


## <a name="inbound-ip-restrictions"></a>入站 IP 限制

可以使用 IP 限制来定义允许/拒绝访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“拒绝所有”。 IP 限制可与所有函数托管选项配合使用。

> [!NOTE]
> 使用网络限制后, 只能在虚拟网络内使用门户编辑器, 或者在将用于访问 Azure 门户的计算机的 IP 列入允许列表时使用。 但是, 你仍然可以从任何计算机访问 "**平台功能**" 选项卡上的任何功能。

有关详细信息，请参阅 [Azure 应用服务静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>专用站点访问

专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 
* 配置**服务终结点**时,[高级](./functions-premium-plan.md)、[消费](functions-scale.md#consumption-plan)和[应用服务计划](functions-scale.md#app-service-plan)中提供了专用站点访问。 
    * 可以基于每个应用在平台功能 > 网络上配置服务终结点, > 配置访问限制 > 添加规则。 现在可以选择虚拟网络作为规则的 "类型"。
    * 有关详细信息, 请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)
        * 请注意, 对于服务终结点, 即使配置了虚拟网络集成, 函数仍具有 internet 的完全出站访问权限。
* 通过使用内部负载均衡器 (ILB) 配置的应用服务环境, 还可以使用专用站点访问。 有关详细信息, 请参阅[创建和使用带有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

## <a name="virtual-network-integration"></a>虚拟网络集成

函数应用可以通过虚拟网络集成来访问虚拟网络内部的资源。 此功能适用于高级计划和应用服务计划。 如果你的应用处于应用服务环境中, 它已在虚拟网络中, 不需要使用虚拟网络集成来访问同一虚拟网络中的资源。

可以使用虚拟网络集成来从应用访问虚拟网络中运行的数据库和 Web 服务。 使用虚拟网络集成就不需要公开 VM 上应用程序的公共终结点。 可以改用不可通过 Internet 路由的专用地址。

虚拟网络集成功能有两种形式

1. 通过区域虚拟网络集成, 可在同一区域中与虚拟网络集成。 此功能的这种形式需要在同一区域的虚拟网络中有一个子网。 此功能仍处于预览阶段, 但 Windows 应用生产工作负荷支持此功能, 但有一些注意事项。
2. 网关所需的虚拟网络集成启用了与远程区域或经典虚拟网络的虚拟网络的集成。 此版本的功能要求在 VNet 中部署虚拟网络网关。 这是基于点到站点 VPN 的功能, 仅在 Windows 应用中受支持。

应用一次只能使用一种形式的 VNet 集成功能。 问题就是应该使用哪种功能。 您可以使用任何一种方法。 不过, 清楚的区别在于:

| 问题  | 解决方案 | 
|----------|----------|
| 希望在同一区域中访问 RFC 1918 地址 (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) | 区域 VNet 集成 |
| 想要在经典 VNet 中或另一个区域中的 VNet 中访问资源 | 网关所需的 VNet 集成 |
| 想要跨 ExpressRoute 访问 RFC 1918 终结点 | 区域 VNet 集成 |
| 希望跨服务终结点实现资源 | 区域 VNet 集成 |

这两项功能都不允许跨 ExpressRoute 访问非 RFC 1918 地址。 为此, 你现在需要使用 ASE。

使用区域 VNet 集成不会将 VNet 连接到本地或配置服务终结点。 这是单独的网络配置。 区域 VNet 集成只是使应用程序能够在这些连接类型上进行调用。

无论使用何种版本, VNet 集成都使函数应用能够访问虚拟网络中的资源, 但不会授予对虚拟网络中的函数应用的专用站点访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成仅适用于从应用到 VNet 的出站调用。 

VNet 集成功能：

* 需要标准、高级或 PremiumV2 应用服务计划
* 支持 TCP 和 UDP
* 适用于应用服务应用和函数应用

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

功能中的虚拟网络集成使用共享基础结构和应用服务 web 应用。 若要阅读有关这两种类型的虚拟网络集成的详细信息, 请参阅:
* [区域 VNET 集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [网关所需的 VNet 集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要详细了解如何使用虚拟网络集成, 请参阅将[function app 与 Azure 虚拟网络集成](functions-create-vnet.md)。

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>将存储帐户限制在虚拟网络中

> [!note] 
> 暂时, 一旦你配置了对该存储帐户的访问限制, 则你的存储帐户最多可能需要12小时才能供函数应用使用。 在这段时间内, 应用程序将完全脱机。

为了提供更高级别的安全性, 你可以将应用程序的存储帐户限制为虚拟网络。 然后, 你必须将你的站点与该虚拟网络集成, 才能访问你的存储帐户。 支持虚拟网络集成的所有计划都支持此配置。

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器 (非 HTTP)

目前, 若要在虚拟网络中使用 HTTP 以外的函数触发器, 必须在应用服务计划或应用服务环境中运行函数应用。

若要提供一个示例, 若要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量, 则需要在与该虚拟网络集成虚拟网络的应用服务计划中部署函数应用, 以配置 Azure Cosmos DB 触发器从该资源。 在预览中, 配置 VNET 集成不会允许高级计划触发该 Azure Cosmos DB 资源。

检查[此列表中的所有非 HTTP 触发器](./functions-triggers-bindings.md#supported-bindings), 以仔细检查支持的内容。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用于访问其他网络中的应用程序资源的一项 Azure 中继功能。 使用混合连接可以从应用访问应用程序终结点。 不能使用混合连接来访问应用程序。 混合连接可用于在[应用服务计划](functions-scale.md#app-service-plan)和[应用服务环境](../app-service/environment/intro.md)中运行的函数。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是能够访问 TCP 侦听端口。 混合连接功能不知道、也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。

有关详细信息，请参阅支持应用服务计划中的 Functions 的[混合连接的应用服务文档](../app-service/app-service-hybrid-connections.md)。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

出站 IP 限制仅适用于部署到应用服务环境的函数。 你可以为部署应用服务环境的虚拟网络配置出站限制。

将高级计划或应用服务计划中的函数应用与虚拟网络集成时, 应用仍可对 internet 进行出站调用。

## <a name="next-steps"></a>后续步骤
若要详细了解网络和 Azure Functions ，请参阅以下链接： 

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [阅读 Azure Functions 网络常见问题解答](./functions-networking-faq.md)
* [详细了解虚拟网络与应用服务/Functions 的集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [启用应用服务环境的更多网络功能和控制](../app-service/environment/intro.md)
* [在不更改防火墙的情况下使用混合连接连接到单个本地资源](../app-service/app-service-hybrid-connections.md)
