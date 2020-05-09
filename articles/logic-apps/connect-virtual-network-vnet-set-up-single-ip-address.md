---
title: 为 ISEs 设置公共出站 IP 地址
description: 了解如何在 Azure 逻辑应用中为 integration service 环境（ISEs）设置单个公共出站 IP 地址
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 2132dc464ee404339d9de03c0c797426aea04ce2
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927133"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>为 Azure 逻辑应用中的一个或多个 integration services 环境设置单个 IP 地址

使用 Azure 逻辑应用时，可以为托管逻辑应用（需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中的资源）设置[ *integration service 环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 。 如果有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将[Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络中，并通过该防火墙或网络虚拟设备路由出站流量。 然后，你可以让虚拟网络中的所有 ISE 实例使用单个公共、静态和可预测的 IP 地址与所需的目标系统进行通信。 这样一来，就不必为每个 ISE 在目标系统上设置其他防火墙。

本主题说明如何通过 Azure 防火墙路由出站流量，但你可以将类似的概念应用于网络虚拟设备，例如 Azure Marketplace 中的第三方防火墙。 尽管本主题重点介绍多个 ISE 实例的设置，但当方案需要限制需要访问的 IP 地址的数目时，还可以将此方法用于单个 ISE。 考虑防火墙或虚拟网络设备的额外成本是否适合你的方案。 了解有关[Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)的详细信息。

## <a name="prerequisites"></a>必备条件

* 与 ISE 在同一虚拟网络中运行的 Azure 防火墙。 如果没有防火墙，请先将名`AzureFirewallSubnet`为的[子网添加](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)到虚拟网络。 然后，你可以在虚拟网络中[创建和部署防火墙](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

* 一个 Azure[路由表](../virtual-network/manage-route-table.md)。 如果没有，请先创建一个[路由表](../virtual-network/manage-route-table.md#create-a-route-table)。 有关路由的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="set-up-route-table"></a>设置路由表

1. 在[Azure 门户](https://portal.azure.com)中，选择路由表，例如：

   ![选择路由表，其中包含用于定向出站流量的规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 若要[添加新的路由](../virtual-network/manage-route-table.md#create-a-route)，请在 "路由表" 菜单上选择 "**路由** > **添加**"。

   ![为定向出站流量添加路由](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 在 "**添加路由**" 窗格上，使用规则[设置新的路由](../virtual-network/manage-route-table.md#create-a-route)，该规则指定目标系统的所有出站流量都遵循此行为：

   * 使用[**虚拟设备**](../virtual-network/virtual-networks-udr-overview.md#user-defined)作为下一跃点类型。

   * 转到防火墙实例的专用 IP 地址作为下一个跃点地址。

     若要查找此 IP 地址，请在防火墙菜单上，选择 "**概述**"，在 "**专用 IP 地址**" 下找到地址，例如：

     ![查找防火墙专用 IP 地址](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   下面的示例演示了此类规则的外观：

   ![设置用于定向出站流量的规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **路由名称** | <*唯一路由名称*> | 路由表中的路由的唯一名称 |
   | **地址前缀** | <*目标地址*> | 要在其中发送出站流量的目标系统的地址前缀。 请确保对此地址使用无类[域间路由（CIDR）表示法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 在此示例中，此地址前缀用于 SFTP 服务器，这在 "[设置网络规则](#set-up-network-rule)" 一节中进行了介绍。 |
   | **下一跃点类型** | **虚拟设备** | 出站流量使用的[跃点类型](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **下一跃点地址** | <*防火墙-专用 IP 地址*> | 防火墙的专用 IP 地址 |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>设置网络规则

1. 在 Azure 门户中，找到并选择你的防火墙。 在 "防火墙" 菜单上的 "**设置**" 下，选择 "**规则**"。 在 "规则" 窗格中，选择 "**网络规则集合** > **添加网络规则集合**"。

   ![向防火墙添加网络规则集合](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 在集合中，添加允许流量流向目标系统的规则。

   例如，假设你有一个在 ISE 中运行并且需要与 SFTP 服务器通信的逻辑应用。 创建名为`LogicApp_ISE_SFTP_Outbound`的网络规则集合，其中包含名为`ISE_SFTP_Outbound`的网络规则。 此规则允许来自任何子网的 IP 地址的流量，其中 ISE 使用防火墙的专用 IP 地址在虚拟网络中运行到目标 SFTP 服务器。

   ![设置防火墙的网络规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **网络规则集合属性**

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | <*网络规则-集合名称*> | 网络规则集合的名称 |
   | **优先级** | <*优先级别*> | 用于运行规则集合的优先级顺序。 有关详细信息，请参阅[什么是 Azure 防火墙概念](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)？ |
   | **操作** | **允许** | 要为此规则执行的操作类型 |
   |||

   **网络规则属性**

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | <*网络规则-名称*> | 网络规则的名称 |
   | **协议** | <*连接-协议*> | 要使用的连接协议。 例如，如果使用 NSG 规则，请同时选择**tcp**和**UDP**，而不是仅选择**tcp**。 |
   | **源地址** | <*ISE-子网地址*> | 运行 ISE 的子网 IP 地址，以及来自逻辑应用的流量的来源 |
   | **目标地址** | <*目标 IP 地址*> | 要在其中发送出站流量的目标系统的 IP 地址。 在此示例中，此 IP 地址用于 SFTP 服务器。 |
   | **目标端口** | <*目标-端口*> | 目标系统用于入站通信的任何端口 |
   |||

   有关网络规则的详细信息，请参阅以下文章：

   * [配置网络规则](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure 防火墙规则处理逻辑](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure 防火墙常见问题解答](../firewall/firewall-faq.md)
   * [Azure PowerShell： AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI： az 网络防火墙网络规则](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>后续步骤

* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
