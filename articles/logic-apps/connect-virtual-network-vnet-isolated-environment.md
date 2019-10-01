---
title: 通过集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络
description: 创建集成服务环境 (ISE)，以便逻辑应用和集成帐户可以访问 Azure 虚拟网络，同时保持专用并与公共或“全局”Azure 相隔离
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 15e1f1c4c8757ca55ec27659a4ca11b1729aebc2
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701951"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络

对于逻辑应用和集成帐户需要访问 [ Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的情况，请创建[集成服务环境(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是专用和隔离的环境, 使用专用的存储和其他与公共或 "全局" 逻辑应用服务不同的资源。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。

当你创建 ISE 时, Azure 会将此 ISE*注入*到 Azure 虚拟网络, 然后将逻辑应用服务部署到你的虚拟网络中。 创建逻辑应用或集成帐户时, 请选择你的 ISE 作为其位置。 然后，逻辑应用或集成帐户可以直接访问虚拟网络中的虚拟机 (VM)、服务器、系统和服务等资源。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 要使逻辑应用和集成帐户在 ISE 中协同工作，两者都必须使用*相同的 ise*作为其位置。

ISE 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息, 请参阅[Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)。 若要了解有关 ISEs 的详细信息, 请参阅[从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文介绍如何完成以下任务：

* 请确保虚拟网络上的任何必要端口都已打开，以便流量可以通过 ISE 跨该虚拟网络的子网。

* 创建 ISE。

* 向 ISE 添加额外的容量。

> [!IMPORTANT]
> 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 若要了解 ISEs 的定价和计费工作原理, 请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 如果没有虚拟网络，请了解如何[创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 

  * 虚拟网络需要四个*空*子网，以便在 ISE 中创建和部署资源。 你可以提前创建这些子网, 也可以等待, 直到创建了你可以在其中创建子网的 ISE。 了解有关[子网要求](#create-subnet)的详细信息。

  * 子网名称必须以字母字符或下划线开头，不能使用`<`以下字符：、 `>`、 `%`、 `&`、 `\\` `?`、、 `/`。 

  * 请确保虚拟网络[提供这些端口](#ports), 使 ISE 正常工作并保持可访问。

  * 如果使用提供与 Microsoft 云服务的专用连接的[ExpressRoute](../expressroute/expressroute-introduction.md)，则必须创建一个包含以下路由的[路由表](../virtual-network/manage-route-table.md)，并将该表链接到你的 ISE 使用的每个子网：

    **名称**: <*路由名称*><br>
    **地址前缀**:0.0.0.0/0<br>
    **下一跃点**：Internet

* 如果要对 Azure 虚拟网络使用自定义 DNS 服务器, 请在将 ISE 部署到虚拟网络之前, 按照[以下步骤设置这些服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 否则，每次更改 DNS 服务器时，还必须重新启动 ISE。

  > [!IMPORTANT]
  > 如果在创建 ISE 之后更改 DNS 服务器设置，请确保重启 ISE。 有关管理 DNS 服务器设置的详细信息，请参阅[创建、更改或删除虚拟网络](../virtual-network/manage-virtual-network.md#change-dns-servers)。

<a name="ports"></a>

## <a name="check-network-ports"></a>检查网络端口

将 ISE 与 Azure 虚拟网络一起使用时，常见的安装问题是有一个或多个被阻止的端口。 用于在 ISE 与目标系统之间创建连接的连接器还可能有自己的端口要求。 例如，如果使用 FTP 连接器与 FTP 系统进行通信，请确保在 FTP 系统上使用的端口可用，例如，使用端口21发送命令。 若要确保 ISE 保持可访问且可以正常工作，请打开下表指定的端口。 否则, 如果任何所需的端口不可用, ISE 将停止工作。

> [!IMPORTANT]
> 源端口是暂时的，因此请确保将其设置为所有规则 `*`。
> 对于子网中的内部通信，ISE 要求打开这些子网中的所有端口。

* 如果创建的新虚拟网络和子网没有任何限制，则无需在虚拟网络中设置[网络安全组（nsg）](../virtual-network/security-overview.md#network-security-groups)来控制子网之间的流量。

* 在现有虚拟网络上，你可以*选择*通过[筛选子网中的网络流量](../virtual-network/tutorial-filter-network-traffic.md)来设置 nsg。 如果选择此路由，请在要设置 Nsg 的虚拟网络上，确保打开下表指定的端口。 如果使用[NSG 安全规则](../virtual-network/security-overview.md#security-rules)，则需要 TCP 和 UDP 协议。

* 如果你之前在虚拟网络中现有 Nsg 或防火墙，请确保打开下表指定的端口。 如果使用[NSG 安全规则](../virtual-network/security-overview.md#security-rules)，则需要 TCP 和 UDP 协议。

下表描述了 ISE 使用的虚拟网络中的端口以及这些端口的使用位置。 [资源管理器服务标记](../virtual-network/security-overview.md#service-tags)代表一组 IP 地址前缀, 有助于在创建安全规则时最大程度地降低复杂性。

| 用途 | Direction | 目标端口 | 源服务标记 | 目标服务标记 | 说明 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| 从 Azure 逻辑应用通信 | 出站 | 80、443 | VirtualNetwork | Internet | 端口依赖于逻辑应用服务通信时所用的外部服务 |
| Azure Active Directory | 出站 | 80、443 | VirtualNetwork | AzureActiveDirectory | |
| Azure 存储依赖项 | 出站 | 80、443 | VirtualNetwork | 存储 | |
| Intersubnet 通信 | 入站和出站 | 80、443 | VirtualNetwork | VirtualNetwork | 子网之间的通信 |
| 与 Azure 逻辑应用通信 | 入站 | 443 | 内部访问终结点： <br>VirtualNetwork <p><p>外部访问终结点： <br>Internet <p><p>**注意**：这些终结点引用[在创建 ISE 时选择](#create-environment)的终结点设置。 有关详细信息，请参阅[终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 | VirtualNetwork | 调用逻辑应用中存在的任何请求触发器或 webhook 的计算机或服务的 IP 地址。 关闭或阻止此端口会阻止对具有请求触发器的逻辑应用的 HTTP 调用。 |
| 逻辑应用运行历史记录 | 入站 | 443 | 内部访问终结点： <br>VirtualNetwork <p><p>外部访问终结点： <br>Internet <p><p>**注意**：这些终结点引用[在创建 ISE 时选择](#create-environment)的终结点设置。 有关详细信息，请参阅[终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 | VirtualNetwork | 从中查看逻辑应用的运行历史记录的计算机的 IP 地址。 尽管关闭或阻止此端口不会阻止你查看运行历史记录, 但无法查看该运行历史记录中每个步骤的输入和输出。 |
| 连接管理 | 出站 | 443 | VirtualNetwork  | Internet | |
| 发布诊断日志和指标 | 出站 | 443 | VirtualNetwork  | AzureMonitor | |
| 来自 Azure 流量管理器的通信 | 入站 | 443 | AzureTrafficManager | VirtualNetwork | |
| 逻辑应用设计器 - 动态属性 | 入站 | 454 | Internet | VirtualNetwork | 请求来自于逻辑应用[访问终结点在该区域中的入站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#inbound)。 |
| 应用服务管理依赖项 | 入站 | 454、455 | AppServiceManagement | VirtualNetwork | |
| 连接器部署 | 入站 | 454 | AzureConnectors | VirtualNetwork | 需要用于部署和更新连接器。 关闭或阻止此端口会导致 ISE 部署失败, 并阻止连接器更新或修复。 |
| 连接器策略部署 | 入站 | 3443 | Internet | VirtualNetwork | 需要用于部署和更新连接器。 关闭或阻止此端口会导致 ISE 部署失败, 并阻止连接器更新或修复。 |
| Azure SQL 依赖关系 | 出站 | 1433 | VirtualNetwork | SQL | |
| Azure 资源运行状况 | 出站 | 1886 | VirtualNetwork | AzureMonitor | 用于将运行状况状态发布到资源运行状况 |
| API 管理 - 管理终结点 | 入站 | 3443 | APIManagement | VirtualNetwork | |
| “记录到事件中心”策略和监视代理中的依赖项 | 出站 | 5672 | VirtualNetwork | EventHub | |
| 访问角色实例之间的 Azure Redis 缓存实例 | 入站 <br>出站 | 6379-6383 | VirtualNetwork | VirtualNetwork | 此外, 要使 ISE 使用 Azure Cache for Redis, 必须打开[Azure cache For REDIS 常见问题中所述的这些出站和入站端口](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
| Azure 负载均衡器 | 入站 | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>创建 ISE

若要创建集成服务环境 (ISE)，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)上的 Azure 主菜单上，选择“创建资源”。
在搜索框中，输入“集成服务环境”作为筛选器。

   ![新建资源](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在集成服务环境创建 "窗格上, 选择"**创建**"。

   ![选择“创建”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 为环境提供以下详细信息，然后选择“查看 + 创建”，例如：

   ![提供环境详细信息](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 属性 | 必填 | Value | 描述 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure-subscription-name*> | 用于环境的 Azure 订阅 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 要在其中创建环境的 Azure 资源组 |
   | **Integration service 环境名称** | 是 | <*environment-name*> | ISE 名称，只能包含字母、数字、连字符（`-`）、下划线（`_`）和句点（`.`）。 |
   | **Location** | 是 | <*Azure-datacenter-region*> | 要在其中部署环境的 Azure 数据中心区域 |
   | **SKU** | 是 | **高级**或**开发人员 (无 SLA)** | 要创建和使用的 ISE SKU。 有关这些 Sku 之间的差异, 请参阅[ISE sku](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要说明**：此选项仅在创建 ISE 时可用，不能在以后更改。 |
   | **额外容量** | 价格 <br>是 <p><p>开发 <br>不适用 | 价格 <br>0到10 <p><p>开发 <br>不适用 | 要用于此 ISE 资源的其他处理单元的数量。 若要在创建后添加容量, 请参阅[添加 ISE 容量](#add-capacity)。 |
   | **访问终结点** | 是 | **内部**或**外部** | 用于 ISE 的访问终结点的类型，用于确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。 终结点类型还会影响对逻辑应用运行历史记录中的输入和输出的访问。 有关详细信息，请参阅[终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要说明**：此选项仅在创建 ISE 时可用，不能在以后更改。 |
   | **虚拟网络** | 是 | <Azure-virtual-network-name> | 要注入环境以便该环境中的逻辑应用可以访问虚拟网络的 Azure 虚拟网络。 如果没有网络, 请[先创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 <p>**重要说明**：创建 ISE 时可以仅执行此注入。 |
   | **子网** | 是 | <*subnet-resource-list*> | ISE 需要四个*空*子网, 以便在您的环境中创建和部署资源。 要创建每个子网，请[按照此表下方的步骤操作](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **创建子网**

   若要在环境中创建和部署资源, ISE 需要四个不委托给任何服务的*空*子网。 创建环境后,*无法*更改这些子网地址。
   
   > [!IMPORTANT]
   > 
   > 子网名称必须以字母字符或下划线（无数字）开头，不能使用`<`以下字符：、 `>`、 `%`、 `&`、 `\\` `?`、、 `/`。
   
   而且，每个子网都必须满足以下要求：

   * 使用无[类别域间路由 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和 B 类地址空间。

   * 至少`/27`在地址空间中使用，因为每个子网必须*至少*具有 32*地址。* 例如：

     * `10.0.0.0/27`具有32个地址, 因为 2<sup>(32-27)</sup>为 2<sup>5</sup>或32。

     * `10.0.0.0/24`具有256个地址, 因为 2<sup>(32-24)</sup>是 2<sup>8</sup>或256。

     * `10.0.0.0/28`只有16个地址并且太小, 因为 2<sup>(32-28)</sup>为 2<sup>4</sup>或16。

     若要了解有关计算地址的详细信息, 请参阅[IPV4 CIDR 块](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果使用[ExpressRoute](../expressroute/expressroute-introduction.md)，则必须创建一个包含以下路由的[路由表](../virtual-network/manage-route-table.md)，并将该表链接到你的 ISE 使用的每个子网：

     **名称**: <*路由名称*><br>
     **地址前缀**:0.0.0.0/0<br>
     **下一跃点**：Internet

   1. 在“子网”列表下，选择“管理子网配置”。

      ![管理子网配置](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. 在“子网”窗格中，选择“子网”。

      ![添加子网](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. 在“添加子网”窗格中，提供此信息。

      * **名称**：子网的名称
      * **地址范围(CIDR 块)** ：虚拟网络采用 CIDR 格式的子网范围

      ![添加子网详细信息](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完成后，选择“确定”。

   1. 重复这些步骤创建额外三个子网。

      > [!NOTE]
      > 如果尝试创建的子网无效, Azure 门户将显示一条消息, 但不会阻止你的进度。

   有关创建子网的详细信息, 请参阅[添加虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)。

1. Azure 成功验证 ISE 信息后，请选择“创建”，例如：

   ![成功验证后，选择“创建”](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 随即开始部署该环境，但此过程最长可能需要两个小时才能完成。 若要检查部署状态，请在 Azure 工具栏上选择通知图标打开通知窗格。

   ![检查部署状态](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   如果部署成功完成，Azure 会显示以下通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   否则, 请按照 Azure 门户说明进行故障排除部署。

   > [!NOTE]
   > 如果部署失败或删除了 ISE, Azure 可能需要长达一小时的时间才能释放子网。 这种延迟意味着可能需要等待, 才能在另一 ISE 中重用这些子网。
   >
   > 如果删除虚拟网络, 则在释放子网之前, Azure 通常需要两个小时, 但此操作可能需要更长时间。 
   > 删除虚拟网络时, 请确保没有资源仍处于连接状态。 
   > 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 如果部署完成后 Azure 未自动转到你的环境，可以选择“转到资源”来查看该环境。

1. 若要查看 ISE 的网络运行状况，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 若要开始在 ISE 中创建逻辑应用和其他项目，请参阅[将项目添加到 integration service 环境](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>添加 ISE 容量

高级 ISE 基本单元具有固定容量, 因此, 如果需要更多的吞吐量, 则可以在创建期间或之后添加更多缩放单位。 你可以根据性能指标或其他一些处理单元自动进行自动缩放。 如果选择 "基于度量值自动缩放", 则可以从各种条件中进行选择, 并指定满足条件的阈值条件。 开发人员 SKU 不包括添加缩放单元的功能。

1. 在 Azure 门户中, 找到 ISE。

1. 若要查看 ISE 的使用情况和性能指标, 请在 ISE 的主菜单上, 选择 "**概述**"。

   ![查看 ISE 的使用情况](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. 若要设置自动缩放, 请在 "**设置**" 下选择 "**横向扩展**"。在 "**配置**" 选项卡上, 选择 "**启用自动缩放**"。

   ![启用自动缩放](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. 对于**自动缩放设置 "名称**", 请提供设置的名称。

1. 在 "**默认**" 部分中, 选择 "**基于指标缩放**" 或 "**缩放到特定实例计数**"。

   * 如果选择 "基于实例", 请输入0到10之间的处理单元数。

   * 如果选择 "基于指标", 请执行以下步骤:

     1. 在 "**规则**" 部分中, 选择 "**添加规则**"。

     1. 在 "**缩放规则**" 窗格上, 设置在触发规则时要执行的条件和操作。

     1. 完成后, 选择 "**添加**"。

1. 完成自动缩放设置后, 请保存所做的更改。

## <a name="next-steps"></a>后续步骤

* [向 integration service 环境添加项目](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [检查 integration service 环境的网络运行状况](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
