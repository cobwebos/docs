---
title: Azure Functions 的网络选项
description: 可在 Azure Functions 中的所有网络选项的概述
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437678"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 的网络选项

本文介绍在 Azure Functions 的宿主选项可用的网络功能。 所有以下网络选项提供访问的资源，而无需使用 internet 路由的地址，或限制对 function app 的 internet 访问某些功能。 

托管模型具有不同的可用的网络隔离级别。 选择正确的订阅将帮助你满足网络隔离要求。

你可以托管两种方法中的函数应用：

* 还有一组具有不同级别的虚拟网络连接和缩放选项的多租户基础结构运行的计划选项：
    * [消耗计划](functions-scale.md#consumption-plan)，其根据负载动态缩放，并提供了最少的网络隔离选项。
    * [高级版计划](functions-scale.md#premium-plan-public-preview)，这还可以扩展动态，同时还提供更全面的网络隔离。
    * Azure[应用服务计划](functions-scale.md#app-service-plan)，它在固定的比例进行操作，并提供了类似的网络隔离级别提高到高级版计划。
* 您可以在运行函数[应用服务环境](../app-service/environment/intro.md)。 此方法将函数部署到你的虚拟网络，并提供完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

|                |[消耗计划](functions-scale.md#consumption-plan)|⚠ [高级计划](functions-scale.md#premium-plan-public-preview)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[入站的 IP 限制](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[虚拟网络集成](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[虚拟网络集成 （Azure ExpressRoute 和服务终结点） 的预览](#preview-version-of-virtual-network-integration)|❌No|⚠是的|⚠是的|✅Yes|
|[混合连接](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[专用站点访问](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ 此预览功能不适用于生产环境中使用。

## <a name="inbound-ip-restrictions"></a>入站的 IP 限制

IP 限制可用于定义按优先级顺序排列的 IP 地址的允许/拒绝访问你的应用列表。 列表可包含 IPv4 和 IPv6 地址。 如果有一个或多个条目，隐式"全部拒绝"位于列表的末尾。 IP 限制适用于所有托管函数的选项。

> [!NOTE]
> 若要使用 Azure 门户的编辑器，在门户必须能够直接访问正在运行的函数应用。 此外，你要用于访问门户的设备必须具有其 IP 加入允许列表。 使用现有的网络限制，你仍可以访问的任何功能上**平台功能**选项卡。

若要了解详细信息，请参阅[Azure App Service 静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="virtual-network-integration"></a>虚拟网络集成

虚拟网络集成，函数应用可以访问虚拟网络中的资源。 此功能是可用的高级计划和应用服务计划中。 如果应用是应用服务环境中，它已在虚拟网络，并且不需要使用虚拟网络集成访问同一虚拟网络中的资源。

虚拟网络集成允许在函数应用访问的虚拟网络中的资源，但不允许[专用站点访问](#private-site-access)到函数应用从虚拟网络。

可以使用虚拟网络集成以启用从应用到数据库和虚拟网络中运行的 web 服务的访问。 使用虚拟网络集成，不需要公开 VM 上的应用程序的公共终结点。 可以改为使用专用的非 internet 可路由地址。

虚拟网络集成的已公开发布版本依赖于将函数应用连接到虚拟网络的 VPN 网关。 它现已推出应用服务计划中托管的函数。 若要了解如何配置此功能，请参阅[将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)。

### <a name="preview-version-of-virtual-network-integration"></a>虚拟网络集成预览版本

新版本的虚拟网络集成功能处于预览状态。 它不依赖于点到站点 VPN。 它支持通过 ExpressRoute 访问资源或服务终结点。 高级版计划中并且在应用服务计划缩放到高级 V2，它才可用。

下面是此版本的一些特征：

* 您不需要网关以使用它。
* 可以通过 ExpressRoute 连接来访问资源，除了与连接 ExpressRoute 的虚拟网络集成以外，不需要任何其他配置。
* 可以使用服务终结点保护运行函数的资源。 若要执行此操作，启用服务终结点上用于虚拟网络集成的子网。
* 不能配置触发器，以使用服务终结点保护的资源。 
* 函数应用和虚拟网络必须位于同一区域中。
* 新功能都需要通过 Azure Resource Manager 部署的虚拟网络中的未使用子网。
* 该功能处于预览状态时，不支持生产工作负荷。
* 路由表和全局对等互连尚不可用的功能。
* 一个地址用于函数应用的每个潜在实例。 由于分配后，不能更改子网大小，请使用可以轻松地支持你的最大缩放大小的子网。 例如，若要支持可以扩展到 80 实例的高级计划，我们建议`/25`提供 126 主机地址的子网。

若要了解有关使用虚拟网络集成预览版本的详细信息，请参阅[将函数应用与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用于访问其他网络中的应用程序资源的 Azure 中继的一项功能。 使用混合连接可以从应用访问应用程序终结点。 不能用于访问你的应用程序。 混合连接可供运行的函数[应用服务计划](functions-scale.md#app-service-plan)和一个[应用服务环境](../app-service/environment/intro.md)。

在 Azure Functions 中使用，与单个 TCP 主机和端口组合相关联的每个混合连接。 这意味着混合连接终结点可以位于任何操作系统和任何应用程序，只要您正在访问 TCP 侦听端口。 混合连接功能不知道，也不关心应用程序协议是什么，或者你的访问。 它只是提供网络访问。

若要了解详细信息，请参阅[混合连接的应用服务文档](../app-service/app-service-hybrid-connections.md)，它支持在应用服务计划中的函数。

## <a name="private-site-access"></a>专用站点访问

专用站点访问指的是使您的应用程序只能从专用网络如从 Azure 虚拟网络中访问。 专用站点访问位于仅与应用服务环境配置具有内部负载均衡器 (ILB)。 有关详细信息，请参阅[创建和使用具有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

有许多方法来访问其他托管选项中的虚拟网络资源。 但是，应用服务环境是允许通过虚拟网络的函数的触发器的唯一方法。

## <a name="next-steps"></a>后续步骤
若要了解有关网络和 Azure 函数的详细信息： 

* [遵循有关如何开始使用虚拟网络集成教程](./functions-create-vnet.md)
* [读取网络常见问题函数](./functions-networking-faq.md)
* [了解有关虚拟网络集成使用应用服务/功能的详细信息](../app-service/web-sites-integrate-with-vnet.md)
* [了解有关 Azure 中的虚拟网络的详细信息](../virtual-network/virtual-networks-overview.md)
* [启用网络功能和使用应用服务环境的控制](../app-service/environment/intro.md)
* [使用混合连接连接到独立的本地资源，而无需防火墙更改](../app-service/app-service-hybrid-connections.md)
