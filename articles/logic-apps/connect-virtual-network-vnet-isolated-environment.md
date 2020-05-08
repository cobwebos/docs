---
title: 使用 ISE 连接到 Azure 虚拟网络
description: 创建可从 Azure 逻辑应用访问 Azure 虚拟网络（Vnet）的集成服务环境（ISE）
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 8fab8c51655c860bc63715a5313c18ac72d4b0cd
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871617"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络

对于逻辑应用和集成帐户需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的方案，请创建[*集成服务环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是一个独立的环境，它使用专用存储和其他与 "全局" 多租户逻辑应用服务分离的资源。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。 ISE 还提供自己的静态 IP 地址。 这些 IP 地址独立于公共多租户服务中的逻辑应用共享的静态 IP 地址。

当你创建 ISE 时，Azure 会将此 ISE*注入*到 Azure 虚拟网络，然后将逻辑应用服务部署到你的虚拟网络中。 创建逻辑应用或集成帐户时，请选择你的 ISE 作为其位置。 然后，逻辑应用或集成帐户可以直接访问虚拟网络中的虚拟机 (VM)、服务器、系统和服务等资源。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 要使逻辑应用和集成帐户在 ISE 中协同工作，两者都必须使用*相同的 ise*作为其位置。

ISE 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅[Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 若要了解有关 ISEs 的详细信息，请参阅[从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文说明如何使用 Azure 门户完成这些任务：

* 为 ISE 启用访问权限。
* 创建 ISE。
* 向 ISE 添加额外的容量。

还可以通过使用[示例 Azure 资源管理器快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)或使用逻辑应用 REST API （包括设置客户管理的密钥）创建 ISE：

* [使用逻辑应用创建集成服务环境（ISE） REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [设置客户管理的密钥，以便为 ISEs 加密静态数据](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

  > [!IMPORTANT]
  > 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 若要了解 ISEs 的定价和计费工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

* [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 如果没有虚拟网络，请了解如何[创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。

  * 虚拟网络需要四个*空*子网，以便在 ISE 中创建和部署资源。 每个子网支持在 ISE 中使用的不同逻辑应用组件。 你可以提前创建这些子网，也可以等待，直到创建了你可以在其中创建子网的 ISE。 了解有关[子网要求](#create-subnet)的详细信息。

  * 子网名称必须以字母字符或下划线开头，不能使用以下`<`字符：、 `>`、 `%`、 `&`、 `\\`、 `?`、。 `/` 
  
  * 如果要通过 Azure 资源管理器模板部署 ISE，请首先确保将一个空子网委托给 integrationServiceEnvironment/。 通过 Azure 门户部署时，无需执行此委派。

  * 确保你的虚拟网络[为 ise 启用访问权限](#enable-access)，以便 ise 能够正常运行并保持可访问性。

  * 如果你使用[ExpressRoute](../expressroute/expressroute-introduction.md)（提供与连接提供商所提供的 Microsoft 云服务的专用连接），则必须创建一个具有以下路由的[路由表](../virtual-network/manage-route-table.md)，并将该表链接到你的 ISE 使用的每个子网：

    **名称**： <*路由名称*><br>
    **地址前缀**： 0.0.0.0/0<br>
    **下一个跃点**： Internet

* 如果要对 Azure 虚拟网络使用自定义 DNS 服务器，请在将 ISE 部署到虚拟网络之前，按照[以下步骤设置这些服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 有关管理 DNS 服务器设置的详细信息，请参阅[创建、更改或删除虚拟网络](../virtual-network/manage-virtual-network.md#change-dns-servers)。

  > [!NOTE]
  > 如果更改 DNS 服务器或 DNS 服务器设置，则必须重启 ISE，以便 ISE 可以选取这些更改。 有关详细信息，请参阅[重新启动 ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>为 ISE 启用访问权限

将 ISE 与 Azure 虚拟网络一起使用时，常见的安装问题是有一个或多个被阻止的端口。 用于在 ISE 与目标系统之间建立连接的连接器还可能有自己的端口要求。 例如，如果使用 FTP 连接器与 FTP 系统进行通信，则需要提供在 FTP 系统上使用的端口，例如，用于发送命令的端口21。

若要确保 ISE 可以访问，并且此 ISE 中的逻辑应用可以在虚拟网络中的每个子网之间进行通信，请[打开此表中描述的每个子网的端口](#network-ports-for-ise)。 如果任何所需的端口不可用，ISE 将无法正常运行。

* 如果有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将[Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络中，并通过该防火墙或网络虚拟设备路由出站流量。 然后，可以[设置单一、出站、公共、静态和可预测的 IP 地址](connect-virtual-network-vnet-set-up-single-ip-address.md)，虚拟网络中的所有 ISE 实例都可以使用该地址与目标系统进行通信。 这样，就无需在每个 ISE 的目标系统上设置其他防火墙。

   > [!NOTE]
   > 如果你的方案需要限制需要访问的 IP 地址的数量，则可以将此方法用于单个 ISE。 考虑防火墙或虚拟网络设备的额外成本是否适合你的方案。 了解有关[Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)的详细信息。

* 如果创建了新的 Azure 虚拟网络和子网而没有任何限制，则无需在虚拟网络中设置[网络安全组（nsg）](../virtual-network/security-overview.md#network-security-groups)来控制子网之间的流量。

* 对于现有虚拟网络，可以*选择性地*设置[网络安全组（nsg）](../virtual-network/security-overview.md#network-security-groups) ，以便[在子网之间筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)。 如果要实现此路线，或者如果已在使用 Nsg，请确保为这些 Nsg[打开此表中所述的端口](#network-ports-for-ise)。

  设置[NSG 安全规则](../virtual-network/security-overview.md#security-rules)时，需要*同时*使用**TCP**和**UDP**协议，或者也可以选择**任何**协议，这样就不必为每个协议创建单独的规则。 NSG 安全规则描述必须为需要访问这些端口的 IP 地址打开的端口。 确保这些终结点之间存在的任何防火墙、路由器或其他项也会保留这些 IP 地址可访问的那些端口。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE 使用的网络端口

下表介绍了 ISE 需要访问的端口和这些端口的用途。 为了在设置安全规则时帮助降低复杂性，此表使用[服务标记](../virtual-network/service-tags-overview.md)来表示特定 Azure 服务的 IP 地址前缀组。 如果提到，*内部 ise*和*外部 ise*指的是在[创建 ISE 期间选择的访问终结点](connect-virtual-network-vnet-isolated-environment.md#create-environment)。 有关详细信息，请参阅[终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。

> [!IMPORTANT]
> 对于所有规则，请确保将源端口设置为`*` ，因为源端口是暂时的。

#### <a name="inbound-security-rules"></a>入站安全规则

| 目标 | 源服务标记或 IP 地址 | 源端口 | 目标服务标记或 IP 地址 | 目标端口 | 说明 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 虚拟网络内的 Intersubnet 通信 | 包含 ISE 子网的虚拟网络的地址空间 | * | 包含 ISE 子网的虚拟网络的地址空间 | * | 在虚拟网络中的子网*之间*流动的流量是必需的。 <p><p>**重要提示**：对于要在每个子网中的*组件*之间流动的流量，请确保打开每个子网中的所有端口。 |
| 两者： <p>与逻辑应用的通信 <p><p>逻辑应用的运行历史记录| 内部 ISE： <br>**VirtualNetwork** <p><p>外部 ISE： **Internet**或请参阅**说明** | * | **VirtualNetwork** | 443 | 你可以指定以下项的源 IP 地址，而不是使用**Internet**服务标记： <p><p>-在逻辑应用中调用任何请求触发器或 webhook 的计算机或服务 <p>-要从中访问逻辑应用运行历史记录的计算机或服务 <p><p>**重要提示**：关闭或阻止此端口会阻止调用具有请求触发器或 webhook 的逻辑应用。 还会阻止你访问运行历史记录中每个步骤的输入和输出。 但是，不会阻止你访问逻辑应用运行历史记录。|
| 逻辑应用设计器-动态属性 | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | 请求来自该区域的逻辑应用访问终结点的[入站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#inbound)。 |
| 连接器部署 | **AzureConnectors** | * | **VirtualNetwork** | 454 | 部署和更新连接器时需要。 关闭或阻止此端口会导致 ISE 部署失败，并阻止连接器更新和修补程序。 |
| 网络运行状况检查 | **LogicApps** | * | **VirtualNetwork** | 454 | 请求来自逻辑应用访问终结点的[入站 ip 地址](../logic-apps/logic-apps-limits-and-config.md#inbound)和该区域的[出站 ip 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 |
| 应用服务管理依赖项 | **AppServiceManagement** | * | **VirtualNetwork** | 454、455 ||
| 来自 Azure 流量管理器的通信 | **AzureTrafficManager** | * | **VirtualNetwork** | 内部 ISE：454 <p><p>外部 ISE：443 ||
| 两者： <p>连接器策略部署 <p>API 管理 - 管理终结点 | **APIManagement** | * | **VirtualNetwork** | 3443 | 对于连接器策略部署，需要进行端口访问以部署和更新连接器。 关闭或阻止此端口会导致 ISE 部署失败，并阻止连接器更新和修补程序。 |
| 访问角色实例之间的 Azure Redis 缓存实例 | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383，还请参阅**说明**| 要使 ISE 使用适用于 Redis 的 Azure 缓存，必须打开这些[出站和入站端口，这些端口由 Azure cache 用于 REDIS 常见问题解答](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
|||||||

#### <a name="outbound-security-rules"></a>入站安全规则

| 目标 | 源服务标记或 IP 地址 | 源端口 | 目标服务标记或 IP 地址 | 目标端口 | 说明 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 虚拟网络内的 Intersubnet 通信 | 包含 ISE 子网的虚拟网络的地址空间 | * | 包含 ISE 子网的虚拟网络的地址空间 | * | 在虚拟网络中的子网*之间*流动的流量是必需的。 <p><p>**重要提示**：对于要在每个子网中的*组件*之间流动的流量，请确保打开每个子网中的所有端口。 |
| 来自逻辑应用的通信 | **VirtualNetwork** | * | 取决于目标 | 80、443 | 目标取决于逻辑应用需要与其通信的外部服务的终结点。 |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80、443 ||
| Azure 存储依赖项 | **VirtualNetwork** | * | **存储** | 80、443、445 ||
| 连接管理 | **VirtualNetwork** | * | **AppService** | 443 ||
| & 度量值发布诊断日志 | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL 依赖关系 | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure 资源运行状况 | **VirtualNetwork** | * | **AzureMonitor** | 1886 | 将运行状况状态发布到资源运行状况是必需的。 |
| “记录到事件中心”策略和监视代理中的依赖项 | **VirtualNetwork** | * | **EventHub** | 5672 ||
| 访问角色实例之间的 Azure Redis 缓存实例 | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383，还请参阅**说明**| 要使 ISE 使用适用于 Redis 的 Azure 缓存，必须打开这些[出站和入站端口，这些端口由 Azure cache 用于 REDIS 常见问题解答](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
|||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>创建 ISE

1. 在 " [Azure 门户](https://portal.azure.com)中的" Azure 搜索 "框中， `integration service environments`输入作为筛选器，并选择" **Integration Service 环境**"。

   ![查找并选择 "Integration Service 环境"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在 " **Integration Service 环境**" 窗格上，选择 "**添加**"。

   ![查找并选择 "Integration Service 环境"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 为环境提供这些详细信息，然后选择 "**查看 + 创建**"，例如：

   ![提供环境详细信息](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 属性 | 必须 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure-订阅-名称*> | 用于环境的 Azure 订阅 |
   | **资源组** | 是 | <*Azure-资源组-名称*> | 要在其中创建环境的新的或现有的 Azure 资源组 |
   | **Integration service 环境名称** | 是 | <*环境-名称*> | ISE 名称，只能包含字母、数字、连字符（`-`）、下划线（`_`）和句点（`.`）。 |
   | **位置** | 是 | <*Azure-数据中心-地区*> | 要在其中部署环境的 Azure 数据中心区域 |
   | **SKU** | 是 | **高级**或**开发人员（无 SLA）** | 要创建和使用的 ISE SKU。 有关这些 Sku 之间的差异，请参阅[ISE sku](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要说明**：此选项仅在创建 ISE 时可用，不能在以后更改。 |
   | **额外容量** | 高级： <br>是 <p><p>开发人员版： <br>不适用 | 高级： <br>0到10 <p><p>开发人员版： <br>不适用 | 要用于此 ISE 资源的其他处理单元的数量。 若要在创建后添加容量，请参阅[添加 ISE 容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 |
   | **访问终结点** | 是 | **内部**或**外部** | 要用于 ISE 的访问终结点的类型。 这些终结点确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。 <p><p>你的选择还会影响你可以查看和访问逻辑应用运行历史记录中的输入和输出的方式。 有关详细信息，请参阅[ISE 终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要提示**：只能在 ISE 创建过程中选择访问终结点，以后无法更改此选项。 |
   | **虚拟网络** | 是 | <*Azure-虚拟网络名称*> | 要注入环境以便该环境中的逻辑应用可以访问虚拟网络的 Azure 虚拟网络。 如果没有网络，请[先创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 <p><p>**重要提示**：只有在创建 ISE 时*才*可以执行此注入。 |
   | **子网** | 是 | <*子网-资源列表*> | ISE 需要四个*空*子网，以便在您的环境中创建和部署资源。 要创建每个子网，请[按照此表下方的步骤操作](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **创建子网**

   若要在环境中创建和部署资源，ISE 需要四个不委托给任何服务的*空*子网。 每个子网支持在 ISE 中使用的不同逻辑应用组件。 创建环境后，*无法*更改这些子网地址。 每个子网都需要满足以下要求：

   * 的名称以字母字符或下划线（无数字）开头，并且不使用以下`<`字符：、 `>`、 `%`、 `&`、 `\\`、 `?`、。 `/`

   * 使用无[类别域间路由（CIDR）格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和 B 类地址空间。

   * 在地址`/27`空间中使用，因为每个子网都需要32地址。 例如，具有`10.0.0.0/27` 32 地址，因为 2<sup>（32-27）</sup>为 2<sup>5</sup>或32。 更多的地址不提供额外的好处。  若要了解有关计算地址的详细信息，请参阅[IPV4 CIDR 块](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果使用[ExpressRoute](../expressroute/expressroute-introduction.md)，则必须创建一个包含以下路由的[路由表](../virtual-network/manage-route-table.md)，并将该表链接到你的 ISE 使用的每个子网：

     **名称**： <*路由名称*><br>
     **地址前缀**： 0.0.0.0/0<br>
     **下一个跃点**： Internet

   1. 在 "**子网**" 列表下，选择 "**管理子网配置**"。

      ![管理子网配置](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. 在 "**子网**" 窗格中，选择 "**子网**"。

      ![添加四个空子网](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. 在“添加子网”窗格中，提供此信息****。

      * **名称**：子网的名称
      * **地址范围（CIDR 块）**：你的虚拟网络中的子网范围和 cidr 格式

      ![添加子网详细信息](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 完成后，请选择“确定”  。

   1. 重复这些步骤创建额外三个子网。

      > [!NOTE]
      > 如果尝试创建的子网无效，Azure 门户将显示一条消息，但不会阻止你的进度。

   有关创建子网的详细信息，请参阅[添加虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)。

1. 在 Azure 成功验证 ISE 信息后，选择 "**创建**"，例如：

   ![验证成功后，选择 "创建"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 会开始部署环境，这通常需要两个小时才能完成。 有时，部署可能最多需要四个小时。 若要检查部署状态，请在 Azure 工具栏上选择 "通知" 图标，打开 "通知" 窗格。

   ![检查部署状态](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   如果部署成功完成，Azure 会显示以下通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   否则，请按照 Azure 门户说明进行故障排除部署。

   > [!NOTE]
   > 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 这种延迟意味着可能需要等待，才能在另一 ISE 中重用这些子网。
   >
   > 如果删除虚拟网络，则在释放子网之前，Azure 通常需要两个小时，但此操作可能需要更长时间。 
   > 删除虚拟网络时，请确保没有资源仍处于连接状态。 
   > 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 若要查看环境，请**选择 "在部署**完成后，Azure 不会自动切换到你的环境"。

1. 若要查看 ISE 的网络运行状况，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 若要开始在 ISE 中创建逻辑应用和其他项目，请参阅[将资源添加到 integration service 环境](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

   > [!IMPORTANT]
   > 创建 ISE 之后可用的托管 ISE 连接器不会自动显示在逻辑应用设计器上的连接器选取器中。 在可以使用这些 ISE 连接器之前，必须手动[将这些连接器添加到 ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) ，使其显示在逻辑应用设计器中。

## <a name="next-steps"></a>后续步骤

* [向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解[Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
