---
title: 为 ISI 设置公共出站 IP 地址
description: 了解如何在 Azure 逻辑应用中为集成服务环境 （ISEs） 设置单个公共出站 IP 地址
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191483"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>为 Azure 逻辑应用中的一个或多个集成服务环境设置单个 IP 地址

使用 Azure 逻辑应用时，可以设置[*集成服务环境*（ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)以托管需要访问[Azure 虚拟网络中](../virtual-network/virtual-networks-overview.md)的资源的逻辑应用。 如果有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将[Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络，并通过该防火墙或网络虚拟设备路由出站流量。 然后，您可以让虚拟网络中的所有 ISE 实例使用单个、公共、静态和可预测的 IP 地址与目标系统通信。 这样，您就不必为每个 ISE 在这些目标系统设置额外的防火墙打开。

本主题演示如何通过 Azure 防火墙路由出站流量，但可以将类似概念应用于网络虚拟设备（如 Azure 应用商店中的第三方防火墙）。 虽然本主题侧重于多个 ISE 实例的设置，但当方案需要限制需要访问的 IP 地址数时，也可以对单个 ISE 使用此方法。 考虑防火墙或虚拟网络设备的额外成本是否适合您的方案。 了解有关[Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)的信息。

## <a name="prerequisites"></a>先决条件

* 与 ISE 在同一虚拟网络中运行的 Azure 防火墙。 如果没有防火墙，请先添加已命名的`AzureFirewallSubnet`[子网](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)到虚拟网络。 然后，您可以在虚拟网络中[创建和部署防火墙](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

* Azure[路由表](../virtual-network/manage-route-table.md)。 如果没有路由表，请先[创建一个路由表](../virtual-network/manage-route-table.md#create-a-route-table)。 有关路由的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="set-up-route-table"></a>设置路由表

1. 在[Azure 门户](https://portal.azure.com)中，选择路由表，例如：

   ![选择具有定向出站流量规则的路由表](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 要在路由表菜单上[添加新路由](../virtual-network/manage-route-table.md#create-a-route)，请选择 **"路由** > **添加**"。

   ![添加用于定向出站流量的路由](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 在 **"添加路由"** 窗格中，使用规则[设置新路由](../virtual-network/manage-route-table.md#create-a-route)，该规则指定到目标系统的所有传出流量都遵循此行为：

   * 使用[**虚拟设备**](../virtual-network/virtual-networks-udr-overview.md#user-defined)作为下一个跃点类型。

   * 转到防火墙实例的专用 IP 地址作为下一个跃点地址。

     要查找此 IP 地址，请在防火墙菜单上选择 **"概述**"，查找**专用 IP 地址**下的地址，例如：

     ![查找防火墙专用 IP 地址](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   下面是一个示例，该示例显示了此类规则的外观：

   ![设置定向出站流量的规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | properties | “值” | 描述 |
   |----------|-------|-------------|
   | **路由名称** | <*唯一路由名称*> | 路由表中路由的唯一名称 |
   | **地址前缀** | <*目标地址*> | 目标系统的地址，您希望流量转到的位置。 请确保为此地址使用[无类域间路由 （CIDR） 表示法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 |
   | **下一个跃点类型** | **虚拟设备** | 出站流量使用的[跃点类型](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **下一跃点地址** | <*防火墙-专用 IP 地址*> | 防火墙的专用 IP 地址 |
   |||

## <a name="set-up-network-rule"></a>设置网络规则

1. 在 Azure 门户中，查找并选择防火墙。 在防火墙菜单上，**在"设置"** 下，选择 **"规则**"。 在规则窗格中，选择**网络规则集合** > **"添加网络规则集合**"。

   ![将网络规则集合添加到防火墙](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 在集合中，添加允许流量到目标系统的规则。

   例如，假设您有一个在 ISE 中运行的逻辑应用，并且需要与 SFTP 系统通信。 创建名为`LogicApp_ISE_SFTP_Outbound`的网络规则集合，其中包含名为 的网络`ISE_SFTP_Outbound`规则。 此规则允许使用防火墙的专用 IP 地址从 ISE 在虚拟网络中运行的任何子网的 IP 地址到目标 SFTP 系统进行流量。

   ![为防火墙设置网络规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **网络规则集合属性**

   | properties | “值” | 描述 |
   |----------|-------|-------------|
   | **名称** | <*网络规则集合名称*> | 网络规则集合的名称 |
   | **优先** | <*优先级*> | 用于运行规则集合的优先级顺序。 有关详细信息，请参阅什么是[Azure 防火墙概念](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)？ |
   | **操作** | **允许** | 要为此规则执行的操作类型 |
   |||

   **网络规则属性**

   | properties | “值” | 描述 |
   |----------|-------|-------------|
   | **名称** | <*网络规则名称*> | 网络规则的名称 |
   | **协议** | <*连接协议*> | 要使用的连接协议。 例如，如果您使用的是 NSG 规则，请选择**TCP**和**UDP**，而不仅仅是**TCP**。 |
   | **源地址** | <*ISE 子网地址*> | ISE 运行的子网 IP 地址以及逻辑应用的流量来源 |
   | **目标地址** | <*目标 IP 地址*> | 您希望流量所在的目标系统的 IP 地址 |
   | **目标端口** | <*目的地端口*> | 目标系统用于入站通信的任何端口 |
   |||

   有关网络规则的详细信息，请参阅以下文章：

   * [配置网络规则](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure 防火墙规则处理逻辑](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure 防火墙常见问题解答](../firewall/firewall-faq.md)
   * [Azure 电源外壳：新-A 防火墙网络规则](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI：az 网络防火墙网络规则](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>后续步骤

* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)