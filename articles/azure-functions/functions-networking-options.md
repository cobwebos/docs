---
title: Azure Functions 网络连接选项
description: 可在 Azure Functions 中的所有网络选项的概述
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 10d7daa6da45c56e20c622fcbca9ee288e737dab
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358161"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络连接选项

本文档介绍了一套可用的网络功能跨 Azure Functions 托管选项。 所有以下网络选项提供访问的资源，而无需使用 internet 路由的地址，或限制对 Function App 的 internet 访问某些功能。 托管的所有模型都具有不同级别的网络隔离，并选择正确的订阅将使您能够满足你的网络隔离要求。

函数应用可以托管多个不同的方式。

* 有一组的多租户基础结构，具有各种级别的 VNET 连接和扩展选项运行的计划选项。
    1. 消耗计划，它根据负载动态缩放，并建议最少的网络隔离选项。
    1. 高级计划，还可以扩展动态，同时还提供更全面的网络隔离。
    1. 应用服务计划，它在固定小数位数，进行操作并提供类似的网络隔离级别提高到高级版计划。
* 函数还可以运行在应用服务环境 (ASE) 部署到 VNet 的函数并提供完全的网络控制和隔离。

## <a name="networking-feature-matrix"></a>网络功能矩阵

|                |[使用计划](functions-scale.md#consumption-plan)|⚠ [高级计划](functions-scale.md##premium-plan-public-preview)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**入站的 IP 限制**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**VNET 集成**](#vnet-integration)|❌No|⚠ 是的|✅Yes|✅Yes|
|[**预览版 VNET 集成 （Express Route 和服务终结点）**](#preview-vnet-integration)|❌No|⚠ 是的|⚠ 是的|✅Yes|
|[**混合连接**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**专用站点访问**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ 预览功能，不适用于生产环境中使用的功能

## <a name="inbound-ip-restrictions"></a>入站的 IP 限制

通过 IP 限制，可定义允许访问应用的 IP 地址的允许/拒绝列表（按优先级排序）。 此允许列表可能包含 IPv4 和 IPv6 地址。 如果存在一个或多个条目，则在列表末尾会存在一个隐式的“拒绝所有”。 IP 限制功能适用于托管选项的所有函数。

> [!NOTE]
> 若要能够使用 Azure 门户的编辑器，在门户必须能够直接访问正在运行的函数应用，并用于访问门户，设备必须拥有其 IP 加入允许列表。 使用现有的网络限制，你仍可以访问中的任何功能**平台功能**选项卡。

[此处详细了解](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>VNET 集成

VNET 集成，函数应用可以访问内部 VNET 的资源。 VNET 集成功能的高级计划和应用服务计划中可用。 如果应用是应用服务环境中，然后它已在 VNet 中，并且不需要使用 VNet 集成功能来访问同一 VNet 中的资源。

VNet 集成功能允许在函数应用访问的虚拟网络中的资源，但不授予[专用站点访问](#private-site-access)到函数应用从虚拟网络。

VNet 集成通常用于实现应用对 VNet 中的数据库和运行 Web 服务的访问。 使用 VNet 集成时，不需要公开 VM 中应用程序的公共终结点，可以改用无法通过 Internet 路由的专用地址。

VNET 集成的已公开发布版本依赖于将函数应用连接到虚拟网络的 VPN 网关。 它现已推出应用服务计划中托管的函数。 若要了解如何配置此功能，请参阅[相同的功能的应用服务文档](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)。

### <a name="preview-vnet-integration"></a>预览版 VNET 集成

VNet 集成功能目前提供处于预览阶段的新版本。 该版本不依赖于点到站点 VPN，且同样支持对 ExpressRoute 和服务终结点上的资源进行访问。 在高级版计划中，并且应用服务计划缩放到高级 V2 中，此功能才可用。

新版本的 VNet 集成，当前处于预览状态，提供以下优势：

* 不需网关即可使用新的 VNet 集成功能
* 可以通过 ExpressRoute 连接来访问资源，除了与连接 ExpressRoute 的 VNet 集成以外，不需要任何其他配置。
* 可以使用服务终结点保护的资源运行函数。 若要实现这一点，请在用于 VNet 集成的子网上启用服务终结点。
* 不能配置触发器，以使用服务终结点保护的资源使用新的 VNet 集成功能。 
* 函数应用和 VNet 必须位于同一区域中。
* 新功能要求在资源管理器 VNet 中有一个未使用的子网。
* 处于预览状态时，VNet 集成的新版本上不支持生产工作负荷。
* 新的 VNet 集成功能尚不支持使用路由表和全球对等互联。
* 一个地址用于每个潜在的 function app 实例。 由于子网大小不能更改分配后，使用可以轻松地支持你的最大缩放大小的子网。 例如，若要支持可以扩展到 80 实例的高级计划，我们建议`/25`提供 126 主机地址的子网。

若要了解有关使用预览 VNET 集成的详细信息，请参阅[将函数应用与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用来访问其他网络中的应用程序资源的 Azure 中继的一项功能。 使用混合连接可以从应用访问应用程序终结点。 它不能用于访问你的应用程序。 混合连接可供运行的函数[应用服务计划](functions-scale.md#app-service-plan)和一个[应用服务环境](../app-service/environment/intro.md)。

在函数中使用，与单个 TCP 主机和端口组合相关联的每个混合连接。 这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是能够访问 TCP 侦听端口。 混合连接功能不知道、也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。

若要了解详细信息，请参阅[混合连接的应用服务文档](../app-service/app-service-hybrid-connections.md)，它支持函数和 Web 应用。

## <a name="private-site-access"></a>专用站点访问

专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 专用站点访问仅适用于部署了内部负载均衡器 (ILB) 的 ASE。 有关使用 ILB ASE 的详细信息，请参阅[创建和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。

有许多方法来访问 VNET 中其他托管选项，但 ASE 是允许通过 VNET 的函数的触发器的唯一方法。
