---
title: "Azure 网络观察程序简介 | Microsoft 文档"
description: "本页面概述可在 Azure 中监视和可视化联网资源的网络观察程序服务"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 18aa9837742082535a115efd47bdc4b8dfda8a6b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---

# <a name="azure-network-monitoring-overview"></a>Azure 网络监视概述

客户在 Azure 中通过协调与组建 VNet、ExpressRoute、应用程序网关、负载均衡器等各种网络资源来构建端到端网络。 监视适用于每个网络资源。 我们将这种监视称作资源级监视。

端到端网络可能采用了复杂的配置并要求在资源之间交互，创建的复杂方案需要通过网络观察程序进行基于方案的监视。

本文介绍方案和资源级的监视。 Azure 中的网络监视是一个综合性的功能，包括两大类别：

* [**网络观察程序**](#network-watcher) - 网络观察程序中的功能提供基于方案的监视。 此服务包括数据包捕获、下一跃点、IP 流验证、安全组视图和 NSG 流日志。 与单独网络资源的监视不同，方案级监视提供网络资源的端到端视图。
* [**资源监视**](#network-resource-level-monitoring) - 资源级监视包括四项功能：诊断日志、指标、故障排除和资源运行状况。 所有这些功能都在网络资源级别构建。

## <a name="network-watcher"></a>网络观察程序

网络观察程序是一个区域性的服务，可用于在网络级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。

网络观察程序目前提供以下功能：

* **[拓扑](network-watcher-topology-overview.md)** - 提供网络级视图，显示资源组中网络资源之间的各种互连和关联。
* **[可变数据包捕获](network-watcher-packet-capture-overview.md)** - 捕获传入和传出虚拟机的数据包数据。 高级筛选选项和精细控制（例如设置时间与大小限制）提供了多样性。 数据包数据可以存储在 Blob 存储中，或者以 .cap 格式存储在本地磁盘上。
* **[IP 流验证](network-watcher-ip-flow-verify-overview.md)** - 根据流信息的 5 元组数据包参数（目标 IP、源 IP、目标端口、源端口和协议）检查数据包是被允许还是被拒绝。 如果数据包被安全组拒绝，则返回拒绝数据包的规则和组。
* **[下一跃点](network-watcher-next-hop-overview.md)** - 确定 Azure 网络结构中路由的数据包的下一跃点，以便诊断任何错误配置的用户定义路由。
* **[安全组视图](network-watcher-security-group-view-overview.md)** - 获取在 VM 上应用的有效安全规则。
* **[NSG 流日志](network-watcher-nsg-flow-logging-overview.md)** - 使用网络安全组的流日志可以捕获被组中的安全规则允许或拒绝的流量的相关日志。 流由 5 元组信息（源 IP、目标 IP、源端口、目标端口和协议）定义。
* **[虚拟网络网关和连接故障排除](network-watcher-troubleshoot-manage-rest.md)** - 提供排查虚拟网络网关和连接问题的功能。
* **[网络订阅限制](#network-subscription-limits)** - 用于查看网络资源用量与限制。
* **[配置诊断日志](#diagnostic-logs)** – 提供单个窗格来为资源组中的网络资源启用或禁用诊断日志。
* **[连接性（预览）](network-watcher-connectivity-overview.md)**- 验证是否可以建立从虚拟机到给定终结点的直接 TCP 连接。

### <a name="role-based-access-control-rbac-in-network-watcher"></a>网络观察程序中基于角色的访问控制 (RBAC)

网络观察程序使用 [Azure 基于角色的访问控制 (RBAC) 模型](../active-directory/role-based-access-control-what-is.md)。 网络观察程序需要以下权限。 请务必确保用于启动网络观察程序 API 或者在门户中使用网络观察程序的角色具有所需的权限。

|资源| 权限|
|---|---| 
|Microsoft.Storage/ |读取|
|Microsoft.Authorization/| 读取| 
|Microsoft.Resources/subscriptions/resourceGroups/| 读取|
|Microsoft.Storage/storageAccounts/listServiceSas/ | 操作|
|Microsoft.Storage/storageAccounts/listAccountSas/ |操作|
|Microsoft.Storage/storageAccounts/listKeys/ | 操作|
|Microsoft.Compute/virtualMachines/ |读取|
|Microsoft.Compute/virtualMachines/ |写入|
|Microsoft.Compute/virtualMachineScaleSets/ |读取|
|Microsoft.Compute/virtualMachineScaleSets/ |写入|
|Microsoft.Network/networkWatchers/packetCaptures/ |读取|
|Microsoft.Network/networkWatchers/packetCaptures/| 写入|
|Microsoft.Network/networkWatchers/packetCaptures/| 删除|
|Microsoft.Network/networkWatchers/ |写入 |
|Microsoft.Network/networkWatchers/| 读取 |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>网络订阅限制

网络订阅限制提供区域中订阅内每个网络资源的用量详细信息，以及可用资源数上限。

![网络订阅限制][nsl]

## <a name="network-resource-level-monitoring"></a>网络资源级监视

提供以下功能进行资源级监视：

### <a name="audit-log"></a>审核日志

记录网络配置过程中执行的操作。 可在 Azure 门户中查看，或者使用 Power BI 等 Microsoft 工具或第三方工具检索这些日志。 可通过门户、PowerShell、CLI 和 REST API 获取审核日志。 有关审核日志的详细信息，请参阅 [Audit operations with Resource Manager](../resource-group-audit.md)（使用 Resource Manager 执行审核操作）。

针对所有网络资源执行的操作都有相应的审核日志。

### <a name="metrics"></a>指标

指标是在一段时间内收集的性能度量值与计数器。 指标目前适用于应用程序网关。 使用指标可以基于阈值触发警报。 请参阅 [Application Gateway Diagnostics](../application-gateway/application-gateway-diagnostics.md)（应用程序网关诊断），了解如何使用指标创建警报。

![指标视图][metrics]

### <a name="diagnostic-logs"></a>诊断日志

定期和自发性事件由网络资源创建，记录在存储帐户中并发送到事件中心或 Log Analytics。 这些日志提供资源运行状况的见解。 可在 Power BI 和 Log Analytics 等工具中查看这些日志。 若要了解如何查看诊断日志，请访问 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)。

诊断日志适用于[负载均衡器](../load-balancer/load-balancer-monitor-log.md)、[网络安全组](../virtual-network/virtual-network-nsg-manage-log.md)、路由和[应用程序网关](../application-gateway/application-gateway-diagnostics.md)。

网络观察程序提供诊断日志视图。 此视图包含所有支持诊断日志记录的网络资源。 从此视图中，可以快速方便地启用和禁用网络资源。

![日志][logs]

### <a name="troubleshooting"></a>故障排除

现已针对网络资源提供了故障排除边栏选项卡，这是门户中的一个体验，可以诊断与单个资源相关的常见问题。 此体验适用于以下网络资源 - ExpressRoute、VPN 网关、应用程序网关、网络安全日志、路由、DNS、负载均衡器和流量管理器。 若要详细了解资源级故障排除，请访问 [Diagnose and resolve issues with Azure Troubleshooting](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)（使用 Azure 故障排除诊断和解决问题）

![故障排除信息][TS]

### <a name="resource-health"></a>资源运行状况

将定期提供网络资源的运行状况。 此类资源包括 VPN 网关和 VPN 隧道。 可在 Azure 门户中访问资源运行状况。 若要详细了解资源运行状况，请访问 [Resource Health Overview](../resource-health/resource-health-overview.md)（资源运行状况概述）

## <a name="next-steps"></a>后续步骤

了解网络观察程序后，可以继续学习以下知识：

访问 [Variable packet capture in the Azure portal](network-watcher-packet-capture-manage-portal.md)（Azure 门户中的可变数据包捕获），在 VM 上执行数据包捕获

使用[警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)执行主动监视和诊断。

在开源工具中[使用 Wireshark 分析数据包捕获](network-watcher-deep-packet-inspection.md)来检测安全漏洞。

了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png












