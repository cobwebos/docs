---
title: Azure Functions 网络选项
description: Azure Functions 中提供的所有网络选项的概述。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f06c50c35e25f2f64948c5f18672e00382d4ef42
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893071"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍适用于 Azure Functions 的托管选项的网络功能。 通过以下所有网络选项，您可以在不使用可通过 internet 路由的地址的情况下访问资源，或限制对函数应用程序的 internet 访问。

托管模型提供不同级别的网络隔离。 选择正确的将有助于满足网络隔离要求。

可以通过以下几种方式承载函数应用：

* 在多租户基础结构上运行的一组计划选项有各种级别的虚拟网络连接和缩放选项：
    * [消耗计划](functions-scale.md#consumption-plan)，可在响应负载时动态缩放，并提供最少的网络隔离选项。
    * [高级计划](functions-scale.md#premium-plan)，还可以动态缩放，同时提供更全面的网络隔离。
    * Azure[应用服务计划](functions-scale.md#app-service-plan)，以固定规模运行，并提供类似的网络隔离作为高级计划。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法将您的函数部署到您的虚拟网络中，并提供完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

|                |[消耗计划](functions-scale.md#consumption-plan)|[高级计划](functions-scale.md#premium-plan)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[& 专用站点访问的入站 IP 限制](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虚拟网络集成](#virtual-network-integration)|❌否|✅是（区域）|✅是（区域和网关）|✅是|
|[虚拟网络触发器（非 HTTP）](#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|
|[混合连接](#hybrid-connections)（仅限 Windows）|❌否|✅是|✅是|✅是|
|[出站 IP 限制](#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|

## <a name="inbound-ip-restrictions"></a>入站 IP 限制

可以使用 IP 限制来定义允许或拒绝其访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址。 如果有一个或多个条目，则在该列表的末尾存在一个隐式 "全部拒绝"。 IP 限制适用于所有函数宿主选项。

> [!NOTE]
> 有了网络限制后，只需从虚拟网络内使用门户编辑器，或者在将用于访问 "安全收件人" 列表中的 Azure 门户的计算机的 IP 地址放入后，就可以使用该编辑器。 但是，你仍然可以从任何计算机访问 "**平台功能**" 选项卡上的任何功能。

若要了解详细信息，请参阅[Azure App Service 静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>专用站点访问

专用站点访问是指使应用只能通过专用网络（例如 Azure 虚拟网络）进行访问。

* 配置服务终结点时，[高级](./functions-premium-plan.md)、[消费](functions-scale.md#consumption-plan)和[应用服务](functions-scale.md#app-service-plan)计划中提供了专用站点访问。
    * 可以基于每个应用在**平台功能** > **网络**上配置服务终结点， > **配置访问限制** > **添加规则**。 现在可以选择虚拟网络作为规则类型。
    * 有关详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
    * 请注意，对于服务终结点，即使配置了虚拟网络集成，函数仍具有 internet 的完全出站访问权限。
* 还可在使用内部负载均衡器（ILB）配置的应用服务环境中获取专用站点访问。 有关详细信息，请参阅[创建和使用带有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

## <a name="virtual-network-integration"></a>虚拟网络集成

通过虚拟网络集成，函数应用可以访问虚拟网络中的资源。 Azure Functions 支持两种类型的虚拟网络集成：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions 中的虚拟网络集成使用共享基础结构和应用服务 web 应用。 若要了解有关这两种类型的虚拟网络集成的详细信息，请参阅：

* [区域虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [网关所需的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要了解如何设置虚拟网络集成，请参阅将[function app 与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>区域虚拟网络集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>连接到服务终结点保护的资源

若要提供更高级别的安全性，可以使用服务终结点将多个 Azure 服务限制为一个虚拟网络。 然后，必须将 function app 与该虚拟网络集成，才能访问该资源。 支持虚拟网络集成的所有计划都支持此配置。

[详细了解虚拟网络服务终结点。](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>将存储帐户限制在虚拟网络中

创建 function app 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 目前无法对此帐户使用任何虚拟网络限制。 如果在用于函数应用的存储帐户上配置虚拟网络服务终结点，则会中断你的应用程序。

[了解有关存储帐户要求的详细信息。](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>使用 Key Vault 引用 

Key Vault 引用允许在 Azure Functions 应用程序中使用 Azure Key Vault 的机密，无需进行任何代码更改。 Azure Key Vault 是一种提供集中式机密管理的服务，可以完全控制访问策略和审核历史记录。

如果你的 Key Vault 通过服务终结点进行保护，则当前[Key Vault 引用](../app-service/app-service-key-vault-references.md)将不起作用。 若要使用虚拟网络集成连接到 Key Vault，需要在应用程序代码中调用密钥保管库。

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器（非 HTTP）

目前，可以通过以下两种方式之一从虚拟网络内使用非 HTTP 触发器函数： 
+ 在高级计划中运行函数应用，并启用虚拟网络触发器支持。
+ 在应用服务计划或应用服务环境中运行函数应用。

### <a name="premium-plan-with-virtual-network-triggers"></a>虚拟网络触发器的高级计划

在高级计划中运行时，可以将非 HTTP 触发器函数连接到虚拟网络中运行的服务。 为此，必须为 function app 启用虚拟网络触发器支持。 "**虚拟网络触发器支持**" 设置位于 "**函数应用设置**" 下的 " [Azure 门户](https://portal.azure.com)中。

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

你还可以使用以下 Azure CLI 命令启用虚拟网络触发器：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

版本2.x 和更高版本的函数运行时支持虚拟网络触发器。 支持以下非 HTTP 触发器类型。

| 分机 | 最低版本 |
|-----------|---------| 
|[Microsoft Azure Web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或更高版本 |
|[EventHubs （& e）](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或更高版本|
|["Node.js"。](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或更高版本|
|[CosmosDB （& e）](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或更高版本|
|[DurableTask （& e）](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或更高版本|

> [!IMPORTANT]
> 启用虚拟网络触发器支持时，只有上面的触发器类型会随应用程序动态缩放。 你仍可以使用上面未列出的触发器，但不会超出其预准备好的实例计数。 有关触发器的完整列表，请参阅[触发器和绑定](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>应用服务计划和应用服务环境虚拟网络触发器

函数应用在应用服务计划或应用服务环境中运行时，可以使用非 HTTP 触发器函数。 为了使函数正确触发，你必须连接到一个虚拟网络，该虚拟网络具有访问触发器连接中定义的资源的权限。 

例如，假设要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量。 在这种情况下，必须在提供虚拟网络与该虚拟网络集成的应用服务计划中部署函数应用。 这样，就可以通过该 Azure Cosmos DB 资源触发函数。 

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是 Azure 中继的一项功能，可用于访问其他网络中的应用程序资源。 使用混合连接可以从应用访问应用程序终结点。 不能使用它来访问应用程序。 混合连接可用于在除消耗计划之外的所有 Windows 上运行的函数。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着混合连接的终结点可以位于任何操作系统和任何应用程序上，只要你要访问 TCP 侦听端口。 混合连接功能不知道或不关心应用程序协议或要访问的内容。 它只提供网络访问。

若要了解详细信息，请参阅[应用服务文档中的混合连接](../app-service/app-service-hybrid-connections.md)。 Azure Functions 支持这些相同的配置步骤。

>[!IMPORTANT]
> 只有 Windows 计划支持混合连接。 不支持 Linux

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

"高级" 计划、应用服务计划或应用服务环境提供出站 IP 限制。 你可以为部署应用服务环境的虚拟网络配置出站限制。

将高级计划或应用服务计划中的函数应用与虚拟网络集成时，默认情况下，应用仍可对 internet 进行出站调用。 通过将应用程序设置添加 `WEBSITE_VNET_ROUTE_ALL=1`，可以强制将所有出站流量发送到虚拟网络，在该网络中，网络安全组规则可用于限制流量。

## <a name="troubleshooting"></a>故障排除 

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解网络和 Azure Functions：

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [阅读函数网络常见问题](./functions-networking-faq.md)
* [详细了解与应用服务/功能的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [启用应用服务环境的更多网络功能和控制](../app-service/environment/intro.md)
* [使用混合连接连接到单个本地资源，而无需更改防火墙](../app-service/app-service-hybrid-connections.md)
