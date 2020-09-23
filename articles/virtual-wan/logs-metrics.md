---
title: 日志和指标
titleSuffix: Azure Virtual WAN
description: 了解 Azure 虚拟 WAN 日志和指标
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: f0ff647581447ad4436daf6c8b1d2ecbf817f7f8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983629"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure 虚拟广域网日志和指标

可以使用 Azure Monitor 来监视 Azure 虚拟 WAN。 虚拟 WAN 是一种网络服务，它汇集了许多网络、安全性和路由功能，以提供单个操作接口。 虚拟 WAN VPN 网关、ExpressRoute 网关和 Azure 防火墙都具有通过 Azure Monitor 提供的日志记录和指标。 有关 Azure 防火墙的信息，请参阅 [Azure 防火墙日志和指标](../firewall/logs-and-metrics.md)。

本文介绍可通过门户获得的指标和诊断。 指标是轻型的，可以支持近实时方案，使其对警报和快速的问题检测非常有用。

## <a name="metrics"></a>指标

Azure Monitor 中的指标是数字值，用于描述系统某些方面在特定时间的情况。 指标每分钟收集一次，可用于警报，因为可对其频繁采样。 可以使用相对简单的逻辑快速激发警报。

### <a name="site-to-site-vpn-gateways"></a>站点到站点 VPN 网关

Azure 站点到站点 VPN 网关提供以下指标：

* **网关带宽** –网关的平均站点到站点聚合带宽（以每秒字节数为单位）。
* **隧道带宽** –隧道的平均带宽（以字节/秒为单位）。
* **隧道传出字节** 数–隧道的传出字节数。 
* **隧道传出数据包** –隧道的传出数据包计数。 
* **隧道传出 TS 不匹配数据包丢弃** –隧道的流量选择器的传出数据包丢弃计数。 
* **隧道入口字节** –隧道的传入字节数。 
* **隧道入口包** –隧道的传入数据包计数。 
* **隧道入口 TS 不匹配数据包丢弃** –来自隧道的流量选择器的传入数据包丢弃计数。 

### <a name="point-to-site-vpn-gateways"></a>点到站点 VPN 网关

Azure 点到站点 VPN 网关提供以下指标：

* **网关 P2S 带宽** –网关的平均点到站点聚合带宽（以字节/秒为单位）。
* **P2S 连接计数** -网关的点到站点连接计数。

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute 网关

以下指标适用于 Azure ExpressRoute 网关：

* **BitsInPerSecond** – Bits 引入 Azure per second。
* **BitsOutPerSecond** – Bits 传出 Azure per second。

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>查看网关指标

以下步骤可帮助你查找和查看指标：

1. 在门户中，导航到具有该网关的虚拟中心。

2. 选择 " **VPN (站点到站点) ** " 以查找站点到站点网关， **expressroute** 查找 expressroute 网关，或使用 " **用户 VPN" (指向 "站点) ** " 以找到点到站点网关。 在页面上，可以看到网关信息。 复制此信息。 稍后你将使用它来查看使用 Azure Monitor 的诊断。

3. 选择“指标”。

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="屏幕截图显示了 "站点到站点 P N" 窗格，其中 Azure Monitor 选中 "视图。":::

4. 在 " **指标** " 页上，你可以查看你感兴趣的指标。

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="指标页":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>诊断日志

### <a name="site-to-site-vpn-gateways"></a>站点到站点 VPN 网关

以下诊断适用于 Azure 站点到站点 VPN 网关：

* **网关诊断日志** -特定于网关的诊断，例如运行状况、配置、服务更新以及其他诊断。
* **隧道诊断日志** –这些日志是与 ipsec 隧道相关的日志，例如站点到站点 IPsec 隧道、协商 SAs、断开连接原因以及其他诊断的连接和断开连接事件。
* **路由诊断日志** –这些日志与静态路由、BGP、路由更新以及其他诊断事件相关。
* **Ike 诊断日志** –适用于 IPsec 连接的特定于 ike 的诊断。

### <a name="point-to-site-vpn-gateways"></a>点到站点 VPN 网关

以下诊断适用于 Azure 点到站点 VPN 网关：

* **网关诊断日志** -特定于网关的诊断，例如运行状况、配置、服务更新以及其他诊断。
* **Ike 诊断日志** –适用于 IPsec 连接的特定于 ike 的诊断。
* **P2S 诊断日志** –这些是用户 VPN (点到站点) P2S 配置和客户端事件。 它们包括客户端连接/断开连接、VPN 客户端地址分配以及其他诊断。

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>查看诊断日志

以下步骤可帮助你查找和查看诊断：

1. 在门户中，导航到虚拟 WAN 资源。 在门户的 "虚拟 WAN" 页面的 " **概述** " 部分中，选择 " **Essentials** " 展开视图并获取资源组信息。 复制资源组信息。

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="指标页":::

2. 在 "监视" 部分中，导航到资源组。 选择 " **诊断设置**"，然后输入资源信息。 这是你在步骤2中从本文前面的 " [查看网关指标](#metrics-steps) " 部分中复制的资源信息。

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="指标页":::

3. 在 "结果" 页上，选择 " **+ 添加诊断设置**"，然后选择一个选项。 你可以选择发送到 Log Analytics、流式传输到事件中心，或者只是存档到存储帐户。

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="指标页":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 示例查询

日志位于 **Azure Log Analytics 工作区**中。 可以在 Log Analytics 中设置查询。 以下示例包含一个查询，用于获取站点到站点路由诊断。

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

根据需要在 **= =** 之后替换下面的值。

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>活动日志

默认情况下会收集**活动日志**条目，并且可以在 Azure 门户中查看。 可以使用 Azure 活动日志 (以前称为 *操作日志* 和 *审核日志*) 来查看提交到 Azure 订阅的所有操作。

## <a name="next-steps"></a>后续步骤

* 若要了解如何监视 Azure 防火墙日志和指标，请参阅[教程：监视 Azure 防火墙日志](../firewall/tutorial-diagnostics.md)。
* 若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](../azure-monitor/platform/data-platform-metrics.md)。
