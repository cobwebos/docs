---
title: Azure Functions 网络选项
description: 在 Azure Functions 中可用的所有网络选项的概述。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419540"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍了可在各种 Azure Functions 托管选项中使用的网络功能。 以下所有网络选项都使您能够访问资源，而无需使用可路由地址或限制对功能应用的 Internet 访问。

托管模型具有不同的可用网络隔离级别。 选择正确的网络隔离要求可帮助您满足网络隔离要求。

可按多种方式托管函数应用：

* 您可以从在多租户基础结构上运行的计划选项中进行选择，这些选项具有不同级别的虚拟网络连接和缩放选项：
    * [消耗计划](functions-scale.md#consumption-plan)可动态扩展以响应负载，并提供最少的网络隔离选项。
    * [高级计划](functions-scale.md#premium-plan)还动态扩展，并提供更全面的网络隔离。
    * Azure[应用服务计划](functions-scale.md#app-service-plan)以固定规模运行，并提供类似于高级计划的网络隔离。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法可以将函数部署到虚拟网络中，并且可以进行完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

|                |[消费计划](functions-scale.md#consumption-plan)|[保费计划](functions-scale.md#premium-plan)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[入站 IP 限制和专用站点访问](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虚拟网络集成](#virtual-network-integration)|❌否|✅是（区域）|✅是（区域和网关）|✅是|
|[虚拟网络触发器（非 HTTP）](#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|
|[混合连接](#hybrid-connections)（仅限 Windows）|❌否|✅是|✅是|✅是|
|[出站 IP 限制](#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|

## <a name="inbound-ip-restrictions"></a>入站 IP 限制

可以使用 IP 限制来定义被允许或拒绝访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“拒绝所有”。 IP 限制可与所有函数托管选项配合使用。

> [!NOTE]
> 如果进行了网络限制，则只能从虚拟网络内部使用门户编辑器，或者在已将用于访问 Azure 门户的计算机的 IP 地址加入安全收件人列表之后使用该编辑器。 但是，仍可以从任何计算机访问“平台功能”选项卡中的任何功能。****

有关详细信息，请参阅 [Azure 应用服务静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>专用站点访问

专用站点访问是指仅从专用网络（如 Azure 虚拟网络）访问应用。

* 配置服务终结点时，在[高级](./functions-premium-plan.md)、[消费](functions-scale.md#consumption-plan)[和应用服务](functions-scale.md#app-service-plan)计划中提供专用站点访问。
    * 服务终结点可以根据**平台功能** > **"网络** > **配置访问限制** > **添加规则**"按应用配置。 现在可以选择虚拟网络作为规则类型。
    * 有关详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
    * 请记住，如果使用服务终结点，那么即使已配置虚拟网络集成，函数也仍对 Internet 拥有完全的出站访问权限。
* 已配置了内部负载均衡器 (ILB) 的应用服务环境内也提供专用站点访问。 有关详细信息，请参阅[在应用服务环境中创建并使用内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

要了解如何设置私有站点访问，请参阅[建立 Azure 函数专用网站访问](functions-create-private-site-access.md)。

## <a name="virtual-network-integration"></a>虚拟网络集成

函数应用可以通过虚拟网络集成来访问虚拟网络内部的资源。
Azure 函数支持两种类型的虚拟网络集成：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions 中的虚拟网络集成使用与应用服务 Web 应用共享的基础结构。 若要详细了解这两种类型的虚拟网络集成，请参阅：

* [区域虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [网关所需的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

要了解如何设置虚拟网络集成，请参阅[将函数应用与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>区域虚拟网络集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>连接到服务终结点安全资源

为了提供更高级别的安全性，可以使用服务终结点将许多 Azure 服务限制在一个虚拟网络中。 然后，必须将函数应用与该虚拟网络集成，才能访问资源。 支持虚拟网络集成的所有计划都支持此配置。

要了解更多信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>将存储帐户限制为虚拟网络

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 目前不能对此帐户使用任何虚拟网络限制。 如果在用于功能应用的存储帐户上配置虚拟网络服务终结点，则该配置将中断应用。

有关详细信息，请参阅[存储帐户要求](./functions-create-function-app-portal.md#storage-account-requirements)。

## <a name="use-key-vault-references"></a>使用 Key Vault 引用

可以使用 Azure 密钥保管库引用在 Azure 函数应用程序中使用 Azure 密钥保管库中的秘密，而无需进行任何代码更改。 Azure Key Vault 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。

目前，如果密钥保管库使用服务终结点保护，[密钥保管库引用](../app-service/app-service-key-vault-references.md)将不起作用。 要使用虚拟网络集成连接到密钥保管库，需要在应用程序代码中调用密钥保管库。

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器（非 HTTP）

目前，您可以通过两种方式之一在虚拟网络内使用非 HTTP 触发器函数：

+ 在高级计划中运行函数应用，并启用虚拟网络触发器支持。
+ 在应用服务计划或应用服务环境中运行函数应用。

### <a name="premium-plan-with-virtual-network-triggers"></a>具有虚拟网络触发器的高级计划

运行高级计划时，可以将非 HTTP 触发器功能连接到在虚拟网络内运行的服务。 为此，必须为函数应用启用虚拟网络触发器支持。 **虚拟网络触发器支持**设置位于[Azure 门户](https://portal.azure.com)中，位于**功能应用设置**下。

![虚拟网络切换](media/functions-networking-options/virtual-network-trigger-toggle.png)

还可以使用以下 Azure CLI 命令启用虚拟网络触发器：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

函数运行时的版本 2.x 及以上都支持虚拟网络触发器。 支持以下非 HTTP 触发器类型。

| 分机 | 最低版本 |
|-----------|---------| 
|[微软.Azure.Web作业.扩展.存储](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或以上 |
|[微软.Azure.Web作业.扩展.事件中心](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或以上|
|[微软.Azure.Web作业.扩展.服务总线](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或以上|
|[微软.Azure.Web作业.扩展.宇宙DB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或以上|
|[微软.Azure.Web作业.扩展.持久任务](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或以上|

> [!IMPORTANT]
> 启用虚拟网络触发器支持时，只有上一表中显示的触发器类型会随应用程序动态扩展。 您仍可以使用不在表中的触发器，但它们不会超出其预热的实例计数。 有关触发器的完整列表，请参阅[触发器和绑定](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>具有虚拟网络触发器的应用服务计划和应用服务环境

当函数应用在应用服务计划或应用服务环境中运行时，可以使用非 HTTP 触发器功能。 为了正确触发函数，必须连接到可访问在触发器连接中定义的资源的虚拟网络。

例如，假设要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量。 在这种情况下，必须在应用服务计划中部署函数应用，该计划提供与该虚拟网络的虚拟网络集成。 集成使该 Azure Cosmos 数据库资源可以触发函数。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用于访问其他网络中的应用程序资源的一项 Azure 中继功能。 使用混合连接可以从应用访问应用程序终结点。 不能使用混合连接来访问应用程序。 混合连接可用于在 Windows 上运行的函数，但使用计划外的所有函数都可用。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是你能够访问 TCP 侦听端口。 混合连接功能不知道也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。

有关详细信息，请参阅[应用服务文档中的“混合连接”](../app-service/app-service-hybrid-connections.md)。 这些相同的配置步骤支持 Azure Functions。

>[!IMPORTANT]
> 混合连接仅在 Windows 计划中受支持。 不支持 Linux。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

出站 IP 限制在高级计划、应用服务计划或应用服务环境中可用。 可以为部署了应用服务环境的虚拟网络配置出站限制。

当您将功能应用集成到高级计划或应用服务计划中与虚拟网络时，默认情况下，该应用仍可以向 Internet 进行出站呼叫。 通过添加应用程序设置`WEBSITE_VNET_ROUTE_ALL=1`，可以强制将所有出站流量发送到虚拟网络，其中网络安全组规则可用于限制流量。

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解网络和 Azure Functions ，请参阅以下链接：

* [按照有关开始虚拟网络集成的教程进行操作](./functions-create-vnet.md)
* [阅读 Azure Functions 网络常见问题解答](./functions-networking-faq.md)
* [详细了解虚拟网络与应用服务/Functions 的集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [在应用服务环境中允许更多的网络功能和控制](../app-service/environment/intro.md)
* [在不更改防火墙的情况下使用混合连接连接到单个本地资源](../app-service/app-service-hybrid-connections.md)
