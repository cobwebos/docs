---
title: Azure Functions 网络选项
description: 在 Azure Functions 中可用的所有网络选项的概述。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 5a88570805a9fc23904bfe82f91813b3460d0b10
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045940"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍了可在各种 Azure Functions 托管选项中使用的网络功能。 通过以下所有网络选项，你可以在不使用可通过 Internet 路由的地址的情况下访问资源，或限制对函数应用的 Internet 访问。

托管模型具有不同的可用网络隔离级别。 选择正确模型有助于满足网络隔离要求。

可按多种方式托管函数应用：

* 可以从在多租户基础结构上运行的计划选项中进行选择，其中包含不同级别的虚拟网络连接和缩放选项：
    * [消耗计划](functions-scale.md#consumption-plan)会动态缩放以响应负载，并提供最少的网络隔离选项。
    * [高级计划](functions-scale.md#premium-plan)也可以动态缩放，并提供更全面的网络隔离。
    * Azure [应用服务计划](functions-scale.md#app-service-plan)按固定规模运行，提供类似于高级计划的网络隔离。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法可以将函数部署到虚拟网络中，并且可以进行完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-ip-restrictions"></a>入站 IP 限制

可以使用 IP 限制来定义被允许或拒绝访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“拒绝所有”。 IP 限制可与所有函数托管选项配合使用。

> [!NOTE]
> 如果进行了网络限制，则只能从虚拟网络内部使用门户编辑器，或者在已将用于访问 Azure 门户的计算机的 IP 地址加入安全收件人列表之后使用该编辑器。 但是，仍可以从任何计算机访问“平台功能”选项卡中的任何功能。****

有关详细信息，请参阅 [Azure 应用服务静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>专用站点访问

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

## <a name="virtual-network-integration"></a>虚拟网络集成

函数应用可以通过虚拟网络集成来访问虚拟网络内部的资源。
Azure Functions 支持两种类型的虚拟网络集成：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions 中的虚拟网络集成使用与应用服务 Web 应用共享的基础结构。 若要详细了解这两种类型的虚拟网络集成，请参阅：

* [区域虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [需要网关的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要了解如何设置虚拟网络集成，请参阅[将函数应用与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>区域虚拟网络集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>连接到服务终结点保护的资源

为了提供更高级别的安全性，可以使用服务终结点将许多 Azure 服务限制在一个虚拟网络中。 然后，必须将函数应用与该虚拟网络集成，才能访问资源。 支持虚拟网络集成的所有计划都支持此配置。

若要了解详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>将存储帐户限制到虚拟网络中

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 目前不能对此帐户使用任何虚拟网络限制。 如果在用于函数应用的存储帐户上配置虚拟网络服务终结点，则该配置会中断应用。

有关详细信息，请参阅[存储帐户要求](./functions-create-function-app-portal.md#storage-account-requirements)。

## <a name="use-key-vault-references"></a>使用 Key Vault 引用

可以在不需进行任何代码更改的情况下，通过 Azure Key Vault 引用在 Azure Functions 应用程序中使用 Azure Key Vault 中的机密。 Azure Key Vault 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。

目前，如果密钥保管库使用服务终结点进行保护，则 [Key Vault 引用](../app-service/app-service-key-vault-references.md)不起作用。 若要使用虚拟网络集成连接到密钥保管库，需要在应用程序代码中调用 Key Vault。

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器（非 HTTP）

目前，可以通过以下两种方式之一从虚拟网络内使用非 HTTP 触发器函数：

+ 在高级计划中运行函数应用，并启用虚拟网络触发器支持。
+ 在应用服务计划或应用服务环境中运行函数应用。

### <a name="premium-plan-with-virtual-network-triggers"></a>具有虚拟网络触发器的高级计划

运行高级计划时，可以将非 HTTP 触发器函数连接到在虚拟网络中运行的服务。 为此，必须为函数应用启用虚拟网络触发器支持。 虚拟网络触发器支持设置处于 [Azure 门户](https://portal.azure.com)中的“配置” > “函数运行时设置”下。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

还可以使用以下 Azure CLI 命令启用虚拟网络触发器：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

版本 2.x 和更高版本的 Functions 运行时支持虚拟网络触发器。 支持以下非 HTTP 触发器类型。

| 分机 | 最低版本 |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或更高版本 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或更高版本|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或更高版本|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或更高版本|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或更高版本|

> [!IMPORTANT]
> 启用虚拟网络触发器支持时，只有上表中显示的触发器类型才能随应用程序进行动态缩放。 仍可以使用不在该表中的触发器，但它们不会缩放到超出其预热实例计数。 有关触发器的完整列表，请参阅[触发器和绑定](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>具有虚拟网络触发器的应用服务计划和应用服务环境

函数应用在应用服务计划或应用服务环境中运行时，可以使用非 HTTP 触发器函数。 为了正确触发函数，必须连接到可访问在触发器连接中定义的资源的虚拟网络。

例如，假设要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量。 在这种情况下，必须在应用服务计划中部署函数应用，该计划提供与该虚拟网络的虚拟网络集成。 集成使函数可以由该 Azure Cosmos DB 资源触发。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../azure-relay/relay-hybrid-connections-protocol.md)是可用于访问其他网络中的应用程序资源的一项 Azure 中继功能。 使用混合连接可以从应用访问应用程序终结点。 不能使用混合连接来访问应用程序。 在除消耗计划之外的所有计划中，混合连接可用于 Windows 上运行的函数。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是你能够访问 TCP 侦听端口。 混合连接功能不知道也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。

有关详细信息，请参阅[应用服务文档中的“混合连接”](../app-service/app-service-hybrid-connections.md)。 Azure Functions 支持这些相同配置步骤。

>[!IMPORTANT]
> 只有 Windows 计划才支持混合连接。 不支持 Linux。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

高级计划、应用服务计划或应用服务环境中提供出站 IP 限制。 可以为部署应用服务环境的虚拟网络配置出站限制。

将高级计划或应用服务计划中的函数应用与虚拟网络集成时，默认情况下，应用仍可对 Internet 进行出站呼叫。 通过添加应用程序设置 `WEBSITE_VNET_ROUTE_ALL=1`，可强制将所有出站流量发送到虚拟网络中，在其中可以使用网络安全组规则限制流量。

## <a name="automation"></a>自动化
以下 Api 允许你以编程方式管理区域虚拟网络集成：

+ **Azure CLI**：使用 [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) 命令添加、列出或删除区域虚拟网络集成。  
+ **ARM 模板**：可以通过使用 Azure 资源管理器模板来启用区域虚拟网络集成。 有关完整示例，请参阅[此函数快速入门模板](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/)。

## <a name="troubleshooting"></a>疑难解答

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解网络和 Azure Functions：

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [阅读 Functions 网络常见问题解答](./functions-networking-faq.md)
* [详细了解虚拟网络与应用服务/Functions 的集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [在应用服务环境中允许更多的网络功能和控制](../app-service/environment/intro.md)
* [在不更改防火墙的情况下使用混合连接连接到单个本地资源](../app-service/app-service-hybrid-connections.md)
