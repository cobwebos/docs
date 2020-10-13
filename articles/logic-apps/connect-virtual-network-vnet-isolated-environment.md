---
title: 使用 ISE 连接到 Azure 虚拟网络
description: 创建可以从 Azure 逻辑应用访问 Azure 虚拟网络 (VNET) 的集成服务环境 (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 49248575cb10f3df746b9ba484244e4702fb5d72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369002"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络

对于逻辑应用和集成帐户需要访问 [ Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的情况，请创建[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是一个专用环境，它使用专用存储和其他与“全局”多租户逻辑应用服务分离的资源。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。 ISE 还为你提供你自己的静态 IP 地址。 这些 IP 地址与公共、多租户服务中的逻辑应用共享的静态 IP 地址分隔开。

创建 ISE 时，Azure 将 ISE 注入到 Azure 虚拟环境，然后将逻辑应用服务部署到虚拟网络中。 创建逻辑应用或集成帐户时，请选择你的 ISE 作为其位置。 然后，逻辑应用或集成帐户可以直接访问虚拟网络中的虚拟机 (VM)、服务器、系统和服务等资源。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 要使逻辑应用和集成帐户在 ISE 中协同工作，两者都必须使用相同的 ISE 作为其位置。

ISE 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 若要详细了解 ISE，请参阅[从独立 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文介绍如何使用 Azure 门户完成以下任务：

* 为 ISE 启用访问权限。
* 创建 ISE。
* 向 ISE 添加额外的容量。

你还可以使用[示例 Azure 资源管理器快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)或者使用逻辑应用 REST API（包括设置客户管理密钥）创建 ISE：

* [使用逻辑应用 REST API 创建集成服务环境 (ISE)](../logic-apps/create-integration-service-environment-rest-api.md)
* [设置客户管理密钥，以便为 ISE 加密静态数据](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

  > [!IMPORTANT]
  > 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用与基于消费的定价计划不同的定价计划。 要了解 ISE 的定价和计费原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

* [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 虚拟网络需要四个 *空* 子网，这些子网是在 ISE 中创建和部署资源所必需的，由这些内部和隐藏组件使用：

  * 逻辑应用计算
  * 内部应用服务环境 (连接器) 
  * 内部 API 管理 (连接器) 
  * 用于缓存和性能的内部 Redis
  
  可以提前创建子网，也可以等待，直到创建了 ISE，以便可以同时创建子网。 但是，在创建子网之前，请查看 [子网要求](#create-subnet)。

  > [!IMPORTANT]
  >
  > 不要为虚拟网络或子网使用以下 IP 地址空间，因为它们无法通过 Azure 逻辑应用解析：<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * 确保你的虚拟网络[为 ISE 启用访问权限](#enable-access)，以便 ISE 能够正常运行并保持可访问性。

  * 如果使用或需要与[强制隧道](../firewall/forced-tunneling.md)一起使用[ExpressRoute](../expressroute/expressroute-introduction.md) ，则必须使用以下特定路由[创建路由表](../virtual-network/manage-route-table.md)，并将路由表链接到你的 ISE 使用的每个子网：

    **名称**：<*route-name*><br>
    **地址前缀**：0.0.0.0/0<br>
    **下一跃点**：Internet
    
    需要此特定的路由表，以便逻辑应用组件可以与其他依赖 Azure 服务（如 Azure 存储和 Azure SQL DB）进行通信。 有关此路由的详细信息，请参阅 [0.0.0.0/0 地址前缀](../virtual-network/virtual-networks-udr-overview.md#default-route)。 如果不对 ExpressRoute 使用强制隧道，则不需要此特定的路由表。
    
    ExpressRoute 使你可以将本地网络扩展到 Microsoft 云，并通过连接提供商加速的专用连接连接到 Microsoft 云服务。 具体而言，ExpressRoute 是通过专用网络而不是通过公共 internet 路由流量的虚拟专用网络。 逻辑应用可以连接到同一虚拟网络中的本地资源（当它们通过 ExpressRoute 或虚拟专用网络连接时）。
   
  * 如果 [ (NVA) 使用网络虚拟设备 ](../virtual-network/virtual-networks-udr-overview.md#user-defined)，请确保不启用 TLS/ssl 终止或更改出站 TLS/ssl 流量。 此外，请确保不要对来自你的 ISE 子网的流量启用检测。 有关详细信息，请参阅 [虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。

  * 如果要为 Azure 虚拟网络使用自定义 DNS 服务器，请[按照以下步骤设置这些服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，然后将 ISE 部署到虚拟网络。 有关管理 DNS 服务器设置的详细信息，请参阅[创建、更改或删除虚拟网络](../virtual-network/manage-virtual-network.md#change-dns-servers)。

    > [!NOTE]
    > 如果更改 DNS 服务器或 DNS 服务器设置，则必须重启 ISE，以便 ISE 可以选取这些更改。 有关详细信息，请参阅[重启 ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>为 ISE 启用访问权限

将 ISE 用于 Azure 虚拟网络时，常见的设置问题是一个或多个端口被阻止。 用于在 ISE 和目标系统之间创建连接的连接器也可能有其自身的端口要求。 例如，如果使用 FTP 连接器与 FTP 系统通信，则在 FTP 系统上使用的端口（例如用于发送命令的端口 21）必须可用。

为了确保 ISE 可供访问且该 ISE 中的逻辑应用可以跨虚拟网络中的每个子网进行通信，请[打开此表中描述的每个子网的端口](#network-ports-for-ise)。 如果任何所需的端口都不可用，则 ISE 将无法正常运行。

* 如果有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将 [Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络中，并通过该防火墙或网络虚拟设备路由出站流量。 然后，可以[设置单个、出站、公共、静态、可预测的 IP 地址](connect-virtual-network-vnet-set-up-single-ip-address.md)，虚拟网络中的所有 ISE 实例都可以使用该 IP 地址与目标系统进行通信。 这样就无需在每个 ISE 的那些目标系统上设置其他防火墙开口。

   > [!NOTE]
   > 如果你的场景要求限制需要访问的 IP 地址数，可以将这种方法用于单个 ISE。 考虑防火墙或虚拟网络设备的额外费用是否适合你的场景。 详细了解 [Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)。

* 如果在没有任何约束的情况下创建了新的 Azure 虚拟网络和子网，则无需在虚拟网络中设置[网络安全组 (NSG)](../virtual-network/security-overview.md#network-security-groups) 来控制跨子网的流量。

* 对于现有的虚拟网络，可以有选择性地设置[网络安全组 (NSG)](../virtual-network/security-overview.md#network-security-groups)，以[跨子网筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)。 如果你想这样做或已在使用 NSG，请确保为这些 NSG [打开表中所述的端口](#network-ports-for-ise)。

  设置 [NSG 安全规则](../virtual-network/security-overview.md#security-rules)时，需要同时使用 TCP 和 UDP 协议，也可以改为选择任意一个协议，从而不必为每个协议创建单独的规则  。 NSG 安全规则描述了必须为需要访问这些端口的 IP 地址打开的端口。 确保这些终结点之间存在的任何防火墙、路由器或其他项也使这些 IP 地址可以访问这些端口。

* 如果通过防火墙设置强制隧道以重定向 Internet 绑定的流量，请查看 [其他强制隧道要求](#forced-tunneling)。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE 使用的网络端口

下表介绍了 ISE 需要访问的端口以及这些端口的用途。 为了帮助减少设置安全规则时的复杂性，该表使用[服务标记](../virtual-network/service-tags-overview.md)来表示特定 Azure 服务的 IP 地址前缀组。 请注意，内部 ISE 和外部 ISE 表示[在 ISE 创建期间选择的访问终结点](connect-virtual-network-vnet-isolated-environment.md#create-environment) 。 有关详细信息，请参阅[终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。

> [!IMPORTANT]
> 对于所有规则，请确保将源端口设置为 `*`，因为源端口是临时端口。

#### <a name="inbound-security-rules"></a>入站安全规则

| 目的 | 源服务标记或 IP 地址 | 源端口 | 目标服务标记或 IP 地址 | 目标端口 | 说明 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 在虚拟网络中进行子网间通信 | 具有 ISE 子网的虚拟网络的地址空间 | * | 具有 ISE 子网的虚拟网络的地址空间 | * | 虚拟网络中子网之间流量流动所必需。 <p><p>**重要说明**：为了使流量在每个子网的组件之间流动，请确保打开每个子网中的所有端口。 |
| 两者： <p>与逻辑应用进行通信 <p><p>逻辑应用的运行历史记录| 内部 ISE： <br>**VirtualNetwork** <p><p>外部 ISE：Internet 或参阅“说明”  | * | **VirtualNetwork** | 443 | 可以指定这些项的源 IP 地址，而不使用 Internet 服务标记： <p><p>- 在逻辑应用中调用任何请求触发器或 Webhook 的计算机或服务 <p>- 要从中访问逻辑应用运行历史记录的计算机或服务 <p><p>**重要说明**：关闭或阻止此端口可防止调用具有请求触发器或 Webhook 的逻辑应用。 还可以阻止你访问运行历史记录中每个步骤的输入和输出。 但是，不会阻止你访问逻辑应用运行历史记录。|
| 逻辑应用设计器 - 动态属性 | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | 请求来自该区域的逻辑应用访问终结点的[入站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#inbound)。 |
| 连接器部署 | **AzureConnectors** | * | **VirtualNetwork** | 454 | 部署和更新连接器需要。 关闭或阻止此端口会导致 ISE 部署失败，并阻止连接器更新和修复。 |
| 网络运行状况检查 | **LogicApps** | * | **VirtualNetwork** | 454 | 请求来自该区域的逻辑应用访问终结点的[入站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#inbound)和[出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 |
| 应用服务管理依赖项 | **AppServiceManagement** | * | **VirtualNetwork** | 454、455 ||
| 来自 Azure 流量管理器的通信 | **AzureTrafficManager** | * | **VirtualNetwork** | 内部 ISE：454 <p><p>外部 ISE：443 ||
| 两者： <p>连接器策略部署 <p>API 管理 - 管理终结点 | **APIManagement** | * | **VirtualNetwork** | 3443 | 对于连接器策略部署，需要端口访问权限才能部署和更新连接器。 关闭或阻止此端口会导致 ISE 部署失败，并阻止连接器更新和修复。 |
| 访问角色实例之间的 Azure Redis 缓存实例 | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383，另请参阅“说明”| 为了使 ISE 与 Azure Cache for Redis 一起使用，必须打开 [Azure Cache for Redis 常见问题解答中所述的出站和入站端口](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
|||||||

#### <a name="outbound-security-rules"></a>入站安全规则

| 目的 | 源服务标记或 IP 地址 | 源端口 | 目标服务标记或 IP 地址 | 目标端口 | 说明 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 在虚拟网络中进行子网间通信 | 具有 ISE 子网的虚拟网络的地址空间 | * | 具有 ISE 子网的虚拟网络的地址空间 | * | 虚拟网络中子网之间流量流动所必需。 <p><p>**重要说明**：为了使流量在每个子网的组件之间流动，请确保打开每个子网中的所有端口。 |
| 来自逻辑应用的通信 | **VirtualNetwork** | * | 取决于目标 | 80、443 | 目标因逻辑应用需要与之通信的外部服务的终结点而异。 |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80、443 ||
| Azure 存储依赖项 | **VirtualNetwork** | * | **存储** | 80、443、445 ||
| 连接管理 | **VirtualNetwork** | * | **AppService** | 443 ||
| 发布诊断日志和指标 | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL 依赖项 | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure 资源运行状况 | **VirtualNetwork** | * | **AzureMonitor** | 1886 | 将运行状况发布到资源运行状况时需要。 |
| “记录到事件中心”策略和监视代理中的依赖项 | **VirtualNetwork** | * | **EventHub** | 5672 ||
| 访问角色实例之间的 Azure Redis 缓存实例 | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383，另请参阅“说明”| 为了使 ISE 与 Azure Cache for Redis 一起使用，必须打开 [Azure Cache for Redis 常见问题解答中所述的出站和入站端口](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
| DNS 名称解析 | **VirtualNetwork** | * | 虚拟网络上的任何自定义域名系统 (DNS) 服务器的 IP 地址 | 53 | 仅当你在虚拟网络中使用自定义 DNS 服务器时才需要 |
|||||||

此外，还需要为 [应用服务环境 (ASE) ](../app-service/environment/intro.md)添加出站规则：

* 如果使用 Azure 防火墙，需要使用应用服务环境 (ASE) [完全限定的域名 (FQDN) 标记](../firewall/fqdn-tags.md#current-fqdn-tags)设置防火墙，这允许出站访问 ASE 平台通信。

* 如果使用除 Azure 防火墙以外的防火墙设备，则需要使用应用服务环境所需的[防火墙集成依赖项](../app-service/environment/firewall-integration.md#dependencies)中列出的*所有*规则设置防火墙。

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>强制隧道要求

如果通过防火墙设置或使用 [强制隧道](../firewall/forced-tunneling.md) ，则需要为 ISE 允许其他外部依赖项。 强制隧道可让你将 Internet 绑定流量重定向到指定的下一跃点，如虚拟专用网 (VPN) 或虚拟设备，而不是连接到 Internet，以便可以检查和审核出站网络流量。

通常，所有 ISE 出站依赖项流量均通过使用 ISE 预配 (VIP) 的虚拟 IP 地址。 但是，如果你在 ISE 中更改了流量路由，则需要通过将其下一跃点设置为来允许防火墙上的以下出站依赖项 `Internet` 。 如果使用 Azure 防火墙，请按照 [说明使用应用服务环境设置防火墙](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)。

如果不允许访问这些依赖项，ISE 部署将失败，部署的 ISE 将停止工作：

* [应用服务环境管理地址](../app-service/environment/management-addresses.md)

* [Azure API 管理地址](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Azure 流量管理器管理地址](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [ISE 区域的逻辑应用入站和出站地址](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* [ISE 区域中的连接器的 Azure IP 地址，位于此下载文件中](https://www.microsoft.com/download/details.aspx?id=56519)

* 需要为 Azure SQL、存储、服务总线和事件中心启用服务终结点，因为不能通过防火墙将流量发送到这些服务。

<a name="create-environment"></a>

## <a name="create-your-ise"></a>创建 ISE

1. 在 [Azure 门户](https://portal.azure.com)的 Azure 主搜索框中，输入 `integration service environments` 作为筛选器，然后选择“集成服务环境”。

   ![查找并选择“集成服务环境”](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在“集成服务环境”窗格中，选择“添加” 。

   ![选择 "添加" 以创建 integration service 环境](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 为环境提供以下详细信息，然后选择“查看 + 创建”，例如：

   ![提供环境详细信息](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | properties | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure-subscription-name*> | 用于环境的 Azure 订阅 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 要在其中创建环境的新的或现有的 Azure 资源组 |
   | **集成服务环境名称** | 是 | <*environment-name*> | ISE 名称，只能包含字母、数字、连字符 (`-`)、下划线 (`_`) 和句点 (`.`)。 |
   | **位置** | 是 | <*Azure-datacenter-region*> | 要在其中部署环境的 Azure 数据中心区域 |
   | **SKU** | 是 | “高级”或“开发人员（无 SLA）”  | 要创建和使用的 ISE SKU。 有关这些 SKU 之间的差异，请参阅 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要说明**：此选项仅在创建 ISE 时可用，以后不能更改。 |
   | **额外容量** | 高级： <br>是 <p><p>开发人员： <br>不适用 | 高级： <br>0 到 10 <p><p>开发人员： <br>不适用 | 用于此 ISE 资源的额外处理单元的数量。 若要在创建后添加容量，请参阅[添加 ISE 容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 |
   | 访问终结点 | 是 | “内部”或“外部”  | 用于 ISE 的访问终结点的类型。 这些终结点确定 ISE 中逻辑应用上的请求或 Webhook 触发器是否可以接收来自虚拟网络外部的调用。 <p><p>你的选择还会影响在逻辑应用运行历史记录中查看和访问输入和输出的方式。 有关详细信息，请参阅 [ISE 终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要说明**：只能在创建 ISE 的过程中选择访问终结点，以后不能更改此选项。 |
   | **虚拟网络** | 是 | <Azure-virtual-network-name> | 要注入环境以便该环境中的逻辑应用可以访问虚拟网络的 Azure 虚拟网络。 如果没有网络，请先[创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 <p><p>**重要说明**：创建 ISE 时可以仅执行此注入。 |
   | **子网** | 是 | <*subnet-resource-list*> | ISE 需要四个 *空白* 子网，这些子网是在 ISE 中创建和部署资源所必需的，由内部逻辑应用组件（如连接器和缓存）用于性能。 <p>**重要提示**：请确保 [先查看子网要求，然后再继续执行这些步骤，以创建子网](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **创建子网**

   ISE 需要四个 *空* 子网，这些子网是在 ISE 中创建和部署资源所必需的，由内部逻辑应用组件（如连接器和缓存）用于性能。 创建环境后无法更改这些子网地址。 如果通过 Azure 门户创建并部署了 ISE，请确保不要将这些子网委托给任何 Azure 服务。 但是，如果通过 REST API、Azure PowerShell 或 Azure 资源管理器模板创建并部署了 ISE，则需要将一个空子网 [委托](../virtual-network/manage-subnet-delegation.md) 给 `Microsoft.integrationServiceEnvironment` 。 有关详细信息，请参阅 [添加子网委派](../virtual-network/manage-subnet-delegation.md)。

   每个子网都需要满足以下要求：

   * 使用以字母字符或下划线开头的名称， (不) 数字，并且不使用以下字符： `<` 、 `>` 、、、、 `%` `&` `\\` `?` 、 `/` 。

   * 使用[无类别域际路由 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和 B 类地址空间。
   
     > [!IMPORTANT]
     >
     > 不要为虚拟网络或子网使用以下 IP 地址空间，因为它们无法通过 Azure 逻辑应用解析：<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * 使用地址空间中的 `/27`，因为每个子网都需要 32 个地址。 例如，`10.0.0.0/27` 有 32 个地址，因为 2<sup>(32-27)</sup> 是 2<sup>5</sup> 或 32。 增加地址数量不会带来更多好处。 若要详细了解如何计算地址，请参阅 [IPv4 CIDR 块](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果使用 [ExpressRoute](../expressroute/expressroute-introduction.md)，则必须[创建具有以下路由的路由表](../virtual-network/manage-route-table.md)，并将该表与 ISE 使用的每个子网链接：

     **名称**：<*route-name*><br>
     **地址前缀**：0.0.0.0/0<br>
     **下一跃点**：Internet

   1. 在“子网”列表下，选择“管理子网配置” 。

      ![管理子网配置](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. 在“子网”窗格中，选择“子网” 。

      ![添加四个空子网](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. 在“添加子网”窗格中，提供此信息。

      * **Name**：子网的名称
      * **地址范围(CIDR 块)** ：虚拟网络采用 CIDR 格式的子网范围

      ![添加子网详细信息](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 完成后，请选择“确定”。

   1. 重复这些步骤创建额外三个子网。

      > [!NOTE]
      > 如果尝试创建的子网无效，则 Azure 门户会显示一条消息，但不会阻止你的进度。

   有关创建子网的详细信息，请参阅[添加虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)。

1. Azure 成功验证 ISE 信息后，请选择“创建”，例如：

   ![成功验证后，选择“创建”](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 开始部署环境，此过程通常需要两个小时才能完成。 有时，部署过程可能长达四个小时。 若要检查部署状态，请在 Azure 工具栏上选择通知图标，这将打开通知窗格。

   ![检查部署状态](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   如果部署成功完成，Azure 会显示以下通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   否则，请按照有关部署故障排除的 Azure 门户说明执行操作。

   > [!NOTE]
   > 如果部署失败或删除了 ISE，则在释放子网之前，Azure 可能最多需要一小时或更长的时间。 因此，你可能需要等待，然后才能在另一 ISE 中重复使用这些子网。
   >
   > 如果删除虚拟网络，Azure 通常需要长达两小时才能释放子网，但是此操作可能需要更长的时间。 
   > 删除虚拟网络时，请确保没有资源仍处于连接状态。 
   > 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 如果部署完成后 Azure 未自动转到你的环境，可以选择“转到资源”来查看该环境。

1. 要检查 ISE 的网络运行状况，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 要开始在 ISE 中创建逻辑应用和其他项目，请参阅[向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

   > [!IMPORTANT]
   > 创建 ISE 后可用的托管 ISE 连接器不会自动出现在逻辑应用设计器的连接器选择器中。 必须手动[将 ISE 连接器添加到 ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)，使其出现在逻辑应用设计器中，才能使用这些连接器。

   > [!IMPORTANT]
   > 托管 ISE 连接器目前不支持 [标记](../azure-resource-manager/management/tag-support.md)。 如果设置了强制执行标记的策略，请尝试添加 ISE 连接器  
   > 可能会失败并出现类似于以下示例的错误： 
   > 
   > ```json
   > {
   >    "error": { 
   >       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
   >       "message": "The tags are not supported in the managed API 'azureblob'."
   >    }
   > }
   > ```
   > 若要添加 ISE 连接器，必须禁用或删除策略。
   > 

## <a name="next-steps"></a>后续步骤

* [向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
