---
title: 有关 Azure 网络观察程序的常见问题解答（FAQ） |Microsoft Docs
description: 本文解答了有关 Azure 网络观察程序服务的常见问题解答。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: ef46c1a631a79dd1c50b2bf7d263538298de233f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333308"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>有关 Azure 网络观察程序的常见问题解答（FAQ）
[Azure 网络观察](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)程序服务提供了一套工具，用于监视、诊断、查看指标，并为 Azure 虚拟网络中的资源启用或禁用日志。 本文解答了有关该服务的常见问题。

## <a name="general"></a>一般信息

### <a name="what-is-network-watcher"></a>什么是网络观察程序？
网络观察程序用于监视和修复 IaaS （基础结构即服务）组件的网络运行状况，其中包括虚拟机、虚拟网络、应用程序网关、负载均衡器和 Azure 虚拟网络中的其他资源。 它不是用于监视 PaaS （平台即服务）基础结构或获取 web/移动分析的解决方案。

### <a name="what-tools-does-network-watcher-provide"></a>网络观察程序提供了哪些工具？
网络观察程序提供三个主要功能集
* 监视
  * [拓扑视图](https://docs.microsoft.com/azure/network-watcher/view-network-topology)显示了虚拟网络中的资源以及它们之间的关系。
  * 使用[连接监视器](https://docs.microsoft.com/azure/network-watcher/connection-monitor)可以监视 VM 与其他网络资源之间的连接性和延迟时间。
  * [网络性能监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor)使你能够跨混合网络体系结构、Expressroute 线路和服务/应用程序终结点监视连接性和延迟时间。  
* 诊断
  * 通过[IP 流验证](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)，可以在 VM 级别检测流量筛选问题。
  * [下一个跃点](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)有助于验证流量路由并检测路由问题。
  * 通过[连接故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal)，可以在 VM 和其他网络资源之间进行一次性连接和延迟检查。
  * [数据包捕获](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)使你能够捕获虚拟网络中的 VM 上的所有流量。
  * [Vpn 故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview)在 vpn 网关和连接上运行多个诊断检查，以帮助调试问题。
* 日志记录
  * [NSG Flow 日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)可用于记录[网络安全组（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)中的所有流量
  * [流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)处理 NSG 流日志数据，使你能够可视化、查询、分析和了解你的网络流量。


有关更多详细信息，请参阅[网络观察程序概述页](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)。


### <a name="how-does-network-watcher-pricing-work"></a>网络观察程序定价如何工作？
有关网络观察程序组件及其定价，请访问[定价页](https://azure.microsoft.com/pricing/details/network-watcher/)。

### <a name="which-regions-is-network-watcher-available-in"></a>哪些区域是可用的网络观察程序？
可以在[Azure 服务可用性页](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)上查看最新的区域可用性

## <a name="nsg-flow-logs"></a>NSG 流日志

### <a name="what-does-nsg-flow-logs-do"></a>NSG 流日志有什么作用？
可以通过[网络安全组（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)合并和管理 Azure 网络资源。 NSG Flow 日志使你可以记录有关通过 Nsg 的所有流量的5元组流信息。 将原始流日志写入 Azure 存储帐户，以便可以根据需要进一步处理、分析、查询或导出这些日志。

### <a name="are-there-caveats-for-using-nsg-flow-logs"></a>使用 NSG 流日志是否有警告？
不存在使用 NSG 流日志的先决条件。 但是，有两个限制
- **VNET 中不得存在服务终结点**： NSG 流日志是从 vm 上的代理发送到存储帐户。 但是，现在只能将日志直接发送到存储帐户，并且不能使用添加到 VNET 的服务终结点。

可通过两种方法解决此问题：

*选项1：重新配置 NSG 流日志，以发送到没有 VNET 终结点的 Azure 存储帐户*

* 查找包含终结点的子网：

    - 在 Azure 门户顶部的全局搜索框中搜索“资源组”
    - 导航到包含要使用的 NSG 的资源组
    - 使用第二个下拉列表按类型进行筛选，然后选择 "**虚拟网络**"
    - 单击包含服务终结点的虚拟网络
    - 在左窗格中的“设置”下，选择“服务终结点”
    - 记下在其中启用了 **Microsoft.Storage** 的子网

* 禁用服务终结点：

    - 继续上面的操作，在左窗格中的“设置”下选择“子网”
    * 单击包含服务终结点的子网
    - 在“服务终结点”部分的“服务”下面，取消选中“Microsoft.Storage”

可以在数分钟后检查存储日志，应该会看到时间戳已更新，或者会看到新的 JSON 文件已创建。

*选项2：禁用 NSG 流日志*

如果 Microsoft.Storage 服务终结点是必需项，则需禁用 NSG 流日志。


- **存储帐户不能是防火墙处理**的：由于内部限制，必须通过公共 Internet 访问存储帐户才能使用 NSG 流日志。 流量仍会在内部通过 Azure 路由，并且不会面临额外的出口费用。

通过启用 "所有网络" 访问存储帐户可以解决此问题：

* 在 [NSG 流日志概览页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)上找到 NSG 即可找到存储帐户的名称
* 在门户的全局搜索框中键入存储帐户的名称，导航到存储帐户
* 在“设置”部分，选择“防火墙和虚拟网络”
* 选择“所有网络”，然后进行保存。 如果已选中，则不需进行更改。  

这两项限制都预计会在2020年1月进行处理。

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>流日志版本 1 & 2 之间有何区别？
流日志版本2引入了*流状态*& 存储有关传输的字节和数据包的信息。 [了解详细信息](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)。

## <a name="next-steps"></a>后续步骤
 - 有关帮助你开始了解网络观察程序的一些教程，请转到我们的[文档概述页](https://docs.microsoft.com/azure/network-watcher/)。
