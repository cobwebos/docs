---
title: 设置多个 ISEs 的访问权限
description: 对于多个 integration services 环境（ISEs），可以设置单个公共出站 IP 地址，以便从 Azure 逻辑应用访问外部系统
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f3b422a55b7e2abbc8b1538183fd57fb234900d4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792699"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>在 Azure 逻辑应用中设置多个 integration service 环境的访问权限

使用 Azure 逻辑应用时，可以为托管逻辑应用（需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中的资源）设置[ *integration service 环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 。 如果有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将[Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络中，并通过该防火墙或网络虚拟设备路由出站流量。 然后，你可以让虚拟网络中的所有 ISE 实例使用单个、可预测和公共 IP 地址与目标系统进行通信。 这样，就不必在每个 ISE 的目标系统上设置其他防火墙。 本主题说明如何通过 Azure 防火墙路由出站流量，但你可以将类似的概念应用到虚拟网络虚拟设备，例如 Azure Marketplace 中的第三方防火墙。

## <a name="prerequisites"></a>必备组件

* 与 ISE 在同一虚拟网络中运行的 Azure 防火墙。 如果没有防火墙，请先将名为 `AzureFirewallSubnet` 的[子网添加](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)到虚拟网络。 然后，你可以在虚拟网络中[创建和部署防火墙](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

* 一个 Azure[路由表](../virtual-network/manage-route-table.md)。 如果没有，请先创建一个[路由表](../virtual-network/manage-route-table.md#create-a-route-table)。 有关路由的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="set-up-route-table"></a>设置路由表

1. 在[Azure 门户](https://portal.azure.com)中，选择路由表，例如：

   ![选择路由表，其中包含用于定向出站流量的规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 若要[添加新的路由](../virtual-network/manage-route-table.md#create-a-route)，请在 "路由表" 菜单上，选择 "**路由** > "**添加**"。

   ![为定向出站流量添加路由](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 在 "**添加路由**" 窗格上，使用规则[设置新的路由](../virtual-network/manage-route-table.md#create-a-route)，该规则指定目标系统的所有传出流量都遵循此行为：

   * 使用[**虚拟设备**](../virtual-network/virtual-networks-udr-overview.md#user-defined)作为下一跃点类型。

   * 转到防火墙实例的专用 IP 地址作为下一个跃点地址。

     若要查找此 IP 地址，请在防火墙菜单上，选择 "**概述**"，在 "**专用 IP 地址**" 下找到地址，例如：

     ![查找防火墙专用 IP 地址](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   下面的示例演示了此类规则的外观：

   ![设置用于定向出站流量的规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | properties | Value | 描述 |
   |----------|-------|-------------|
   | **路由名称** | <*唯一路由名称*> | 路由表中的路由的唯一名称 |
   | **地址前缀** | <*目标地址*> | 要向其发送流量的目标系统地址。 请确保对此地址使用无类[域间路由（CIDR）表示法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 |
   | **下一跃点类型** | **虚拟设备** | 出站流量使用的[跃点类型](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **下一个跃点地址** | <*防火墙-专用 IP 地址*> | 防火墙的专用 IP 地址 |
   |||

## <a name="set-up-network-rule"></a>设置网络规则

1. 在 Azure 门户中，找到并选择你的防火墙。 在 "防火墙" 菜单上的 "**设置**" 下，选择 "**规则**"。 在 "规则" 窗格中，选择 "**网络规则集合**" > "**添加网络规则集合**"。

   ![向防火墙添加网络规则集合](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 在集合中，添加允许流量流向目标系统的规则。

   例如，假设你有一个在 ISE 中运行并且需要与 SFTP 系统通信的逻辑应用。 创建名为 `LogicApp_ISE_SFTP_Outbound`的网络规则集合，该集合包含名为 `ISE_SFTP_Outbound`的网络规则。 此规则允许来自任何子网的 IP 地址的流量，其中你的 ISE 使用防火墙的专用 IP 地址在虚拟网络中运行到目标 SFTP 系统。

   ![设置防火墙的网络规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **网络规则集合属性**

   | properties | Value | 描述 |
   |----------|-------|-------------|
   | 名称 | <*网络规则集合名称*> | 网络规则集合的名称 |
   | **Priority** | <*优先级级别的*> | 用于运行规则集合的优先级顺序。 有关详细信息，请参阅[什么是 Azure 防火墙概念](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)？ |
   | **Action** | **Allow** | 要为此规则执行的操作类型 |
   |||

   **网络规则属性**

   | properties | Value | 描述 |
   |----------|-------|-------------|
   | 名称 | <*网络规则名称*> | 网络规则的名称 |
   | 协议 | <*连接-协议*> | 要使用的连接协议。 例如，如果使用 NSG 规则，请同时选择**tcp**和**UDP**，而不是仅选择**tcp**。 |
   | **源地址** | <*ISE-子网地址*> | 运行 ISE 的子网 IP 地址，以及来自逻辑应用的流量的来源 |
   | **目标地址** | <*目标 IP 地址*> | 要在其中执行流量的目标系统的 IP 地址 |
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