---
title: Azure Functions 网络选项
description: Azure Functions 中提供的所有网络选项的概述。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: a3df48115dde27478446614c0446d64709adbc6f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226797"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 网络选项

本文介绍了可在各种 Azure Functions 托管选项中使用的网络功能。 通过以下所有网络选项，您可以在不使用可通过 internet 路由的地址的情况下访问资源，或限制对函数应用程序的 internet 访问。

托管模型具有不同的可用网络隔离级别。 选择适当的级别有助于满足网络隔离要求。

可按多种方式托管函数应用：

* 在多租户基础结构上运行的一组计划选项有各种级别的虚拟网络连接和缩放选项：
    * [消耗计划](functions-scale.md#consumption-plan)：可以动态缩放以响应负载，但只能提供极少量的网络隔离选项。
    * [高级计划](functions-scale.md#premium-plan)，还可以动态缩放，同时提供更全面的网络隔离。
    * Azure[应用服务计划](functions-scale.md#app-service-plan)，以固定规模运行，并提供类似的网络隔离作为高级计划。
* 可以在[应用服务环境](../app-service/environment/intro.md)中运行函数。 此方法将您的函数部署到您的虚拟网络中，并提供完全的网络控制和隔离。

## <a name="matrix-of-networking-features"></a>网络功能矩阵

|                |[消耗计划](functions-scale.md#consumption-plan)|[高级计划](functions-scale.md#premium-plan)|[应用服务计划](functions-scale.md#app-service-plan)|[应用服务环境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[入站 IP 限制和专用站点访问](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虚拟网络集成](#virtual-network-integration)|❌否|✅是（区域）|✅是（区域和网关）|✅是|
|[虚拟网络触发器（非 HTTP）](#virtual-network-triggers-non-http)|❌否| ❌否|✅是|✅是|
|[混合连接](#hybrid-connections)|❌否|✅是|✅是|✅是|
|[出站 IP 限制](#outbound-ip-restrictions)|❌否| ❌否|❌否|✅是|

## <a name="inbound-ip-restrictions"></a>入站 IP 限制

可以使用 IP 限制来定义允许或拒绝其访问应用的 IP 地址的优先级排序列表。 此列表可以包含 IPv4 和 IPv6 地址。 如果有一个或多个条目，则在该列表的末尾存在一个隐式 "全部拒绝"。 IP 限制可与所有函数托管选项配合使用。

> [!NOTE]
> 有了网络限制后，只需从虚拟网络内使用门户编辑器，或者在将用于访问 "安全收件人" 列表中的 Azure 门户的计算机的 IP 地址放入后，就可以使用该编辑器。 但是，仍可以从任何计算机访问“平台功能”选项卡中的任何功能。

有关详细信息，请参阅 [Azure 应用服务静态访问限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>专用站点访问

专用站点访问是指使应用只能通过专用网络（例如 Azure 虚拟网络）进行访问。

* 配置服务终结点时，[高级](./functions-premium-plan.md)、[消费](functions-scale.md#consumption-plan)和[应用服务](functions-scale.md#app-service-plan)计划中提供了专用站点访问。
    * 可以基于每个应用在**平台功能** > **网络**上配置服务终结点， > **配置访问限制** > **添加规则**。 现在可以选择虚拟网络作为规则类型。
    * 有关详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
    * 请注意，对于服务终结点，即使配置了虚拟网络集成，函数仍具有 internet 的完全出站访问权限。
* 还可在使用内部负载均衡器（ILB）配置的应用服务环境中获取专用站点访问。 有关详细信息，请参阅[创建和使用带有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

## <a name="virtual-network-integration"></a>虚拟网络集成

函数应用可以通过虚拟网络集成来访问虚拟网络内部的资源。 此功能适用于高级计划和应用服务计划。 如果你的应用处于应用服务环境中，它已在虚拟网络中，不需要虚拟网络集成即可访问同一虚拟网络中的资源。

可以使用虚拟网络集成来从应用访问虚拟网络中运行的数据库和 Web 服务。 使用虚拟网络集成就不需要公开 VM 上应用程序的公共终结点。 可以改用专用的非 internet 可路由地址。

虚拟网络集成有两种形式：

+ **区域虚拟网络集成（预览版）** ：可在同一区域中与虚拟网络集成。 这种类型的集成需要在同一区域的虚拟网络中有一个子网。 此功能仍处于预览状态，但在 Windows 上运行的函数应用支持此功能，但在下面的问题/解决方案表后面有说明。
+ **网关所需的虚拟网络集成**：允许与远程区域或经典虚拟网络的虚拟网络集成。 这种类型的集成要求在 VNet 中部署虚拟网络网关。 这是一个基于点到站点 VPN 的功能，仅支持在 Windows 上运行的函数应用。

应用一次只能使用一种类型的虚拟网络集成功能。 尽管这两种方案对许多情况都很有用，但下表指明了应使用每个方案的位置：

| 问题  | 解决方案 |
|----------|----------|
| 想要访问同一区域中的某个 RFC 1918 地址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16） | 区域虚拟网络集成 |
| 想要访问经典虚拟网络或另一个区域中的虚拟网络中的资源 | 网关所需的虚拟网络集成 |
| 想要跨 Azure ExpressRoute 访问 RFC 1918 终结点 | 区域虚拟网络集成 |
| 想要跨服务终结点访问资源 | 区域虚拟网络集成 |

这两种功能都不允许跨 ExpressRoute 访问非 RFC 1918 地址。 为此，您当前必须使用应用服务环境。

使用区域虚拟网络集成不会将虚拟网络连接到本地终结点或配置服务终结点。 这是一个单独的网络配置。 区域虚拟网络集成只是使应用程序能够在这些连接类型上进行调用。

无论使用何种版本，虚拟网络集成都使函数应用能够访问虚拟网络中的资源，但不会授予对虚拟网络中的函数应用的专用站点访问权限。 "专用站点访问" 意味着使应用只能通过专用网络（如 Azure 虚拟网络）进行访问。 虚拟网络集成仅适用于从应用到虚拟网络的出站调用。

虚拟网络集成功能：

* 需要标准、高级或 PremiumV2 应用服务计划
* 支持 TCP 和 UDP
* 适用于应用服务应用和函数应用

虚拟网络集成不支持某些功能，其中包括：

* 装载驱动器
* Active Directory 集成
* NetBIOS

Azure Functions 中的虚拟网络集成使用共享基础结构和应用服务 web 应用。 若要了解有关这两种类型的虚拟网络集成的详细信息，请参阅：

* [区域虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [网关所需的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要详细了解如何使用虚拟网络集成，请参阅将[function app 与 Azure 虚拟网络集成](functions-create-vnet.md)。

## <a name="connecting-to-service-endpoint-secured-resources"></a>连接到服务终结点保护的资源

> [!NOTE]
> 目前，在配置下游资源的访问限制后，最多可能需要12小时的时间，新服务终结点才可用于函数应用。 在此期间，资源将对应用完全不可用。

若要提供更高级别的安全性，可以使用服务终结点将多个 Azure 服务限制为一个虚拟网络。 然后，必须将函数应用与该虚拟网络集成，才能访问资源。 支持虚拟网络集成的所有计划都支持此配置。

[详细了解虚拟网络服务终结点。](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>将存储帐户限制到虚拟网络

创建 function app 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 目前无法对此帐户使用任何虚拟网络限制。 如果在用于函数应用的存储帐户上配置虚拟网络服务终结点，则会中断你的应用程序。

[了解有关存储帐户要求的详细信息。](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>使用 Key Vault 引用 

Key Vault 引用允许在 Azure Functions 应用程序中使用 Azure Key Vault 的机密，无需进行任何代码更改。 Azure Key Vault 是一种提供集中式机密管理的服务，可以完全控制访问策略和审核历史记录。

如果你的 Key Vault 通过服务终结点进行保护，则当前[Key Vault 引用](../app-service/app-service-key-vault-references.md)将不起作用。 若要使用虚拟网络集成连接到 Key Vault，需要在应用程序代码中调用密钥保管库。

## <a name="virtual-network-triggers-non-http"></a>虚拟网络触发器（非 HTTP）

目前，若要在虚拟网络中使用除 HTTP 之外的函数触发器，必须在应用服务计划或应用服务环境中运行函数应用。

例如，假设要将 Azure Cosmos DB 配置为仅接受来自虚拟网络的流量。 需要在应用服务计划中部署函数应用，该计划提供与该虚拟网络的虚拟网络集成，以便从该资源配置 Azure Cosmos DB 触发器。 在预览期间，配置虚拟网络集成不允许高级计划触发该 Azure Cosmos DB 资源。

请查看[此列表以了解所有非 HTTP 触发器](./functions-triggers-bindings.md#supported-bindings)，以仔细检查受支持的内容。

## <a name="hybrid-connections"></a>混合连接

[混合连接](../service-bus-relay/relay-hybrid-connections-protocol.md)是可用于访问其他网络中的应用程序资源的一项 Azure 中继功能。 使用混合连接可以从应用访问应用程序终结点。 不能使用混合连接来访问应用程序。 在除消耗计划以外的所有计划中运行的函数都可以使用混合连接。

在 Azure Functions 中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着混合连接的终结点可以位于任何操作系统和任何应用程序上，只要你要访问 TCP 侦听端口。 混合连接功能不知道或不关心应用程序协议或要访问的内容。 它只提供网络访问。

若要了解详细信息，请参阅[应用服务文档中的混合连接](../app-service/app-service-hybrid-connections.md)。 Azure Functions 支持这些相同的配置步骤。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

出站 IP 限制仅适用于部署到应用服务环境的函数。 你可以为部署应用服务环境的虚拟网络配置出站限制。

将高级计划或应用服务计划中的函数应用与虚拟网络集成时，应用仍可对 internet 进行出站调用。

## <a name="next-steps"></a>后续步骤

若要详细了解网络和 Azure Functions ，请参阅以下链接：

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [阅读 Azure Functions 网络常见问题解答](./functions-networking-faq.md)
* [详细了解虚拟网络与应用服务/Functions 的集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [启用应用服务环境的更多网络功能和控制](../app-service/environment/intro.md)
* [在不更改防火墙的情况下使用混合连接连接到单个本地资源](../app-service/app-service-hybrid-connections.md)
