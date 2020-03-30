---
title: 使用 ISE 连接到 Azure 虚拟网络
description: 创建集成服务环境 （ISE），该环境可以从 Azure 逻辑应用访问 Azure 虚拟网络 （VNET）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fedc1f6ce8fbaeaf0d2cae3a1b04169192868e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270688"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络

对于逻辑应用和集成帐户需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的情况，请创建[*集成服务环境*（ISE）。](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ISE 是一个隔离的环境，它使用专用存储和其他资源，这些资源与"全局"多租户逻辑应用服务分开。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。 ISE 还为您提供自己的静态 IP 地址。 这些 IP 地址与公共多租户服务中的逻辑应用共享的静态 IP 地址分开。

创建 ISE 时，Azure*会将*该 ISE 注入 Azure 虚拟网络，然后将逻辑应用服务部署到虚拟网络。 创建逻辑应用或集成帐户时，选择 ISE 作为其位置。 然后，逻辑应用或集成帐户可以直接访问虚拟网络中的虚拟机 (VM)、服务器、系统和服务等资源。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 对于逻辑应用和集成帐户在 ISE 中协同工作，两者必须使用*与其位置相同的 ISE。*

ISE 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅[Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 要了解有关 ISEs 的更多信息，请参阅[从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文演示如何使用 Azure 门户完成这些任务：

* 启用 ISE 的访问。
* 创建 ISE。
* 为您的 ISE 添加额外的容量。

您还可以使用逻辑应用 REST API 创建 ISE，包括设置客户管理的密钥：

* [使用逻辑应用 REST API 创建集成服务环境 （ISE）](../logic-apps/create-integration-service-environment-rest-api.md)
* [设置客户管理的密钥以加密 ISEs 静态数据](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

  > [!IMPORTANT]
  > 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 要了解 ISEs 的定价和计费的工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

* [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 如果没有虚拟网络，请了解如何[创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。

  * 虚拟网络需要四个*空*子网来创建和部署 ISE 中的资源。 每个子网都支持 ISE 中使用的不同逻辑应用组件。 您可以提前创建这些子网，也可以等到创建 ISE，以便可以同时创建子网。 了解有关[子网要求](#create-subnet)的更多。

  * 子网名称需要以字母字符或下划线开头，`<`不能使用这些字符： `>`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `%` `&` `\\` `?` `/`、 、 、 、 、 、 、 、 
  
  * 如果要通过 Azure 资源管理器模板部署 ISE，请确保将一个空子网委派给 Microsoft.Logic/集成服务环境。 通过 Azure 门户进行部署时，不需要执行此委派。

  * 确保虚拟网络[支持对 ISE 的访问](#enable-access)，以便 ISE 能够正常工作并保持可访问性。

  * 如果使用[ExpressRoute](../expressroute/expressroute-introduction.md)，它提供连接提供商提供的与 Microsoft 云服务的专用连接，则必须[创建具有以下路由的路由表](../virtual-network/manage-route-table.md)，并将该表链接到 ISE 使用的每个子网：

    **名称**： <*路由名称*><br>
    **地址前缀**： 0.0.0.0/0<br>
    **下一跳**： 互联网

* 如果要为 Azure 虚拟网络使用自定义 DNS 服务器，请[按照以下步骤设置这些服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，然后再将 ISE 部署到虚拟网络。 有关管理 DNS 服务器设置的详细信息，请参阅[创建、更改或删除虚拟网络](../virtual-network/manage-virtual-network.md#change-dns-servers)。

  > [!NOTE]
  > 如果更改 DNS 服务器或 DNS 服务器设置，必须重新启动 ISE，以便 ISE 可以选取这些更改。 有关详细信息，请参阅重新启动[ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>为 ISE 启用访问权限

将 ISE 与 Azure 虚拟网络一起使用时，常见的设置问题是存在一个或多个被阻止的端口。 用于在 ISE 和目标系统之间创建连接的连接器也可能有自己的端口要求。 例如，如果使用 FTP 连接器与 FTP 系统通信，则需要在 FTP 系统上使用的端口可用，例如用于发送命令的端口 21。

为了确保 ISE 是可访问的，并且 ISE 中的逻辑应用可以跨虚拟网络中的每个子网进行通信，[请为每个子网打开此表中描述的端口](#network-ports-for-ise)。 如果任何必需的端口不可用，则 ISE 将无法正常工作。

* 如果有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将[Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络，并通过该防火墙或网络虚拟设备路由出站流量。 然后，您可以[设置单个、出站、公共、静态和可预测的 IP 地址，](connect-virtual-network-vnet-set-up-single-ip-address.md)虚拟网络中的所有 ISE 实例都可以使用该地址与目标系统通信。 这样，您就不必为每个 ISE 在这些目标系统设置额外的防火墙打开。

   > [!NOTE]
   > 当方案需要限制需要访问的 IP 地址数时，可以将此方法用于单个 ISE。 考虑防火墙或虚拟网络设备的额外成本是否适合您的方案。 了解有关[Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)的信息。

* 如果创建新的 Azure 虚拟网络和子网没有任何约束，则无需在虚拟网络中设置[网络安全组 （NSG）](../virtual-network/security-overview.md#network-security-groups)即可控制跨子网的流量。

* 在现有虚拟网络上，您可以通过[筛选跨子网的网络流量](../virtual-network/tutorial-filter-network-traffic.md)*来选择*设置 NSG。 如果要走此路线，或者如果您已经在使用 NSG，请确保[打开虚拟网络上的此表中的端口](#network-ports-for-ise)，其中具有 NSG 或想要设置 NSG。

  > [!NOTE]
  > 如果使用[NSG 安全规则](../virtual-network/security-overview.md#security-rules)，则需要*同时*使用 TCP 和 UDP 协议。 NSG 安全规则描述必须为需要访问这些端口的 IP 地址打开的端口。 确保这些终结点之间存在的任何防火墙、路由器或其他项目也会使这些端口对这些 IP 地址具有可访问性。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE 使用的网络端口

此表介绍 ISE 使用的 Azure 虚拟网络中的端口以及这些端口的使用位置。 为了帮助降低创建安全规则时的复杂性，表中[的服务标记](../virtual-network/service-tags-overview.md)表示特定 Azure 服务的 IP 地址前缀组。

> [!IMPORTANT]
> 源端口是短暂的，因此请确保`*`为所有规则设置它们。 如果注意到，内部 ISE 和外部 ISE 是指[在 ISE 创建时选择的终结点](connect-virtual-network-vnet-isolated-environment.md#create-environment)。 有关详细信息，请参阅[终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 

| 目的 | 方向 | 目标端口 | 源服务标记 | 目标服务标记 | 说明 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| 虚拟网络中的子网间通信 | 入站和出站 | * | 具有 ISE 子网的虚拟网络的地址空间 | 具有 ISE 子网的虚拟网络的地址空间 | 需要流量才能在虚拟网络中的子网*之间*流动。 <p><p>**重要提示**：要使流量在每个子网中的*组件*之间流动，请确保打开每个子网中的所有端口。 |
| 与逻辑应用的通信 | 入站 | 443 | 内部 ISE： <br>VirtualNetwork <p><p>外部 ISE： <br>Internet <br>（请参阅**备注**列） | VirtualNetwork | 您可以指定逻辑应用中调用任何请求触发器或 Webhook 的计算机或服务的源 IP 地址，而不是使用**Internet**服务标记。 <p><p>**重要提示**：关闭或阻止此端口可防止对具有请求触发器的逻辑应用进行 HTTP 调用。 |
| 逻辑应用运行历史记录 | 入站 | 443 | 内部 ISE： <br>VirtualNetwork <p><p>外部 ISE： <br>Internet <br>（请参阅**备注**列） | VirtualNetwork | 您可以指定计算机或服务的源 IP 地址，而不是使用**Internet**服务标记，从中查看逻辑应用的运行历史记录。 <p><p>**重要提示**：虽然关闭或阻止此端口不会阻止您查看运行历史记录，但无法查看该运行历史记录中每个步骤的输入和输出。 |
| 逻辑应用设计器 - 动态属性 | 入站 | 454 | 逻辑应用管理 | VirtualNetwork | 请求来自逻辑应用访问该区域[入站](../logic-apps/logic-apps-limits-and-config.md#inbound)IP 地址。 |
| 连接器部署 | 入站 | 454 | AzureConnectors | VirtualNetwork | 部署和更新连接器所需的。 关闭或阻止此端口会导致 ISE 部署失败，并阻止连接器更新或修复。 |
| 网络运行状况检查 | 入站 | 454 | LogicApps | VirtualNetwork | 请求来自该区域[入站](../logic-apps/logic-apps-limits-and-config.md#inbound)和[出站](../logic-apps/logic-apps-limits-and-config.md#outbound)IP 地址的逻辑应用访问终结点。 |
| 应用服务管理依赖项 | 入站 | 454、455 | AppServiceManagement | VirtualNetwork | |
| 来自 Azure 流量管理器的通信 | 入站 | 内部 ISE： 454 <p><p>外部 ISE： 443 | AzureTrafficManager | VirtualNetwork | |
| API 管理 - 管理终结点 | 入站 | 3443 | APIManagement | VirtualNetwork | |
| 连接器策略部署 | 入站 | 3443 | APIManagement | VirtualNetwork | 部署和更新连接器所需的。 关闭或阻止此端口会导致 ISE 部署失败，并阻止连接器更新或修复。 |
| 来自逻辑应用的通信 | 出站 | 80、443 | VirtualNetwork | 因目的地而异 | 逻辑应用需要与其通信的外部服务的终结点。 |
| Azure Active Directory | 出站 | 80、443 | VirtualNetwork | AzureActiveDirectory | |
| 连接管理 | 出站 | 443 | VirtualNetwork  | 应用服务 | |
| 发布诊断日志和指标 | 出站 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure 存储依赖项 | 出站 | 80, 443, 445 | VirtualNetwork | 存储 | |
| Azure SQL 依赖项 | 出站 | 1433 | VirtualNetwork | SQL | |
| Azure 资源运行状况 | 出站 | 1886 | VirtualNetwork | AzureMonitor | 将运行状况发布到资源运行状况所需的 |
| “记录到事件中心”策略和监视代理中的依赖项 | 出站 | 5672 | VirtualNetwork | EventHub | |
| 访问角色实例之间的 Azure Redis 缓存实例 | 入站 <br>出站 | 6379 - 6383 | VirtualNetwork | VirtualNetwork | 此外，要使 ISE 与 Redis 的 Azure 缓存配合使用，必须打开[雷迪斯常见问题 解答的 Azure 缓存中描述的这些出站和入站端口](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>创建 ISE

1. 在[Azure 门户](https://portal.azure.com)中，在主 Azure`integration service environments`搜索框中，输入为筛选器，然后选择**集成服务环境**。

   ![查找并选择"集成服务环境"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在 **"集成服务环境**"窗格中，选择 **"添加**"。

   ![查找并选择"集成服务环境"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 为您的环境提供这些详细信息，然后选择 **"审阅 + 创建**"，例如：

   ![提供环境详细信息](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | properties | 必选 | “值” | 描述 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure 订阅名称*> | 用于环境的 Azure 订阅 |
   | **资源组** | 是 | <*Azure 资源组名称*> | 要在其中创建环境的新或现有 Azure 资源组 |
   | **集成服务环境名称** | 是 | <*环境名称*> | ISE 名称，只能包含字母、数字、连字符 （）、`-`下划线 （`_`和句点`.`（ ）。 |
   | **位置** | 是 | <*Azure-数据中心区域*> | 要在其中部署环境的 Azure 数据中心区域 |
   | **Sku** | 是 | **高级**或**开发人员（无 SLA）** | 要创建和使用 ISE SKU。 有关这些 SKU 之间的差异，请参阅[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要提示**：此选项仅在 ISE 创建时可用，以后无法更改。 |
   | **额外容量** | 高级： <br>是 <p><p>开发人员版： <br>不适用 | 高级： <br>0 到 10 <p><p>开发人员版： <br>不适用 | 用于此 ISE 资源的其他处理单元数。 要在创建后添加容量，请参阅[添加 ISE 容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 |
   | **访问终结点** | 是 | **内部**或**外部** | 用于 ISE 的访问终结点的类型。 这些终结点确定 ISE 中逻辑应用上的请求或 Webhook 触发器是否可以从虚拟网络外部接收呼叫。 <p><p>您的选择还会影响在逻辑应用中查看和访问输入和输出的方式运行历史记录。 有关详细信息，请参阅[ISE 终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要提示**：此选项仅在 ISE 创建时可用，以后无法更改。 |
   | **虚拟网络** | 是 | <*Azure 虚拟网络名称*> | 要注入环境以便该环境中的逻辑应用可以访问虚拟网络的 Azure 虚拟网络。 如果没有网络，[请先创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 <p><p>**重要提示**：*您只能在*创建 ISE 时执行此注入。 |
   | **子网** | 是 | <*子网资源列表*> | ISE 需要四个*空*子网来创建和部署环境中的资源。 要创建每个子网，请[按照此表下方的步骤操作](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **创建子网**

   要在环境中创建和部署资源，ISE 需要四个未委派给任何服务的*空*子网。 每个子网都支持 ISE 中使用的不同逻辑应用组件。 创建环境后 *，无法*更改这些子网地址。 每个子网都需要满足这些要求：

   * 名称以字母字符或下划线开头（无数字），`<`并且不使用这些字符： `>`、 `%`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `&` `\\` `?` `/`

   * 使用[无类域间路由 （CIDR） 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和 B 类地址空间。

   * 在地址空间中`/27`至少使用 a，因为每个子网*至少需要*32 个地址。 例如：

     * `10.0.0.0/28`只有 16 个地址，并且太小，因为 2<sup>（32-28）</sup>是 2<sup>4</sup>或 16。

     * `10.0.0.0/27` 有 32 个地址，因为 2<sup>(32-27)</sup> 是 2<sup>5</sup> 或 32。

     * `10.0.0.0/24` 有 256 个地址，因为 2<sup>(32-24)</sup> 是 2<sup>8</sup> 或 256。 但是，更多的地址不提供任何其他好处。

     要了解有关计算地址的更多详细信息，请参阅[IPv4 CIDR 块](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果使用[ExpressRoute](../expressroute/expressroute-introduction.md)，则必须[创建具有以下路由的路由表](../virtual-network/manage-route-table.md)，并将该表链接到 ISE 使用的每个子网：

     **名称**： <*路由名称*><br>
     **地址前缀**： 0.0.0.0/0<br>
     **下一跳**： 互联网

   1. 在 **"子网**"列表中，选择 **"管理子网配置**"。

      ![管理子网配置](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. 在 **"子网"** 窗格中，选择**子网**。

      ![添加四个空子网](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. 在“添加子网”窗格中，提供此信息****。

      * **名称**： 子网的名称
      * **地址范围 （CIDR 块）：** 您的子网范围在虚拟网络和 CIDR 格式

      ![添加子网详细信息](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 完成后，请选择“确定”****。

   1. 重复这些步骤创建额外三个子网。

      > [!NOTE]
      > 如果尝试创建的子网无效，Azure 门户将显示一条消息，但不会阻止进度。

   有关创建子网的详细信息，请参阅[添加虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)。

1. Azure 成功验证 ISE 信息后，选择 **"创建**"，例如：

   ![成功验证后，选择"创建"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 开始部署环境，通常需要两个小时才能完成。 有时，部署可能需要长达四个小时。 要检查部署状态，请在 Azure 工具栏上选择通知图标，该图标将打开通知窗格。

   ![检查部署状态](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   如果部署成功完成，Azure 会显示以下通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   否则，请按照 Azure 门户说明进行故障排除部署。

   > [!NOTE]
   > 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 此延迟意味着您可能需要等待，然后再在另一个 ISE 中重用这些子网。
   >
   > 如果删除虚拟网络，Azure 通常会在释放子网之前最多需要两个小时，但此操作可能需要更长时间。 
   > 删除虚拟网络时，请确保没有资源仍然连接。 
   > 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 要查看环境，如果 Azure 在部署完成后未自动转到环境，请选择 **"转到资源**"。

1. 要检查 ISE 的网络运行状况，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 要开始在 ISE 中创建逻辑应用和其他项目，请参阅[向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

   > [!IMPORTANT]
   > 创建 ISE 后可用的托管 ISE 连接器不会自动显示在逻辑应用设计器上的连接器选取器中。 在使用这些 ISE 连接器之前，必须手动将这些[连接器添加到 ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)中，以便它们显示在逻辑应用设计器中。

## <a name="next-steps"></a>后续步骤

* [向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解[Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
