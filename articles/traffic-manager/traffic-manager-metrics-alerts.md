---
title: Azure 流量管理器中的指标和警报 | Microsoft Docs
description: 本文介绍了 Azure 中针对流量管理器提供的指标。
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 424782be2d814df6d598591198b5005fb494d3da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302362"
---
# <a name="traffic-manager-metrics-and-alerts"></a>流量管理器指标和警报

流量管理器提供了基于 DNS 的负载均衡，这包括多种路由方法和终结点监视选项。 本文介绍了与可供客户使用的警报关联的指标。 

## <a name="metrics-available-in-traffic-manager"></a>流量管理器中提供的指标 

流量管理器针对每个配置文件提供了以下指标，客户可以使用这些指标来了解其流量管理器的使用情况以及该配置文件管理的终结点的状态。  

### <a name="queries-by-endpoint-returned"></a>按终结点返回的查询
使用[此指标](../monitoring-and-diagnostics/monitoring-supported-metrics.md)可以查看流量管理器配置文件在指定的时间段内处理的查询数。 还可以在终结点级别粒度查看同一信息，这可帮助你了解某个终结点在来自流量管理器的查询响应中返回了多少次。

在下面的示例中，图 1 显示了流量管理器配置文件返回的所有查询响应。 

  
![流量管理器指标 - 所有查询的聚合视图](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*图 1：包含所有查询的聚合视图*
  
图 2 显示了同一信息，不过它按终结点进行了拆分。 因此，你可以看到其中返回了某个特定终结点的查询响应的数量。

![流量管理器指标 - 按终结点的查询量拆分视图](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*图 2：其中显示了按终结点返回的查询量的拆分视图*

## <a name="endpoint-status-by-endpoint"></a>按终结点显示的终结点状态
使用[此指标](../monitoring-and-diagnostics/monitoring-supported-metrics.md)可以了解配置文件中的终结点的运行状况状态。 它采用两个值：
 - 如果终结点正常运行，则使用 **1**。
 - 如果终结点发生故障，则使用 **0**。

此指标可以显示为聚合值，表示所有指标的状态（图 3），也可以拆分（参见图 4）来显示特定终结点的状态。 对于前者，如果聚合级别选择为“平均值”，则此指标的值是所有终结点的状态的算术平均值。 例如，如果某个配置文件具有两个终结点并且只有一个终结点正常，则此指标的值将是 **0.50**，如图 3 所示。 


![流量管理器指标 - 终结点状态的复合视图](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*图 3：终结点状态指标的复合视图 – 选择了“平均值”聚合*


![流量管理器指标 - 终结点状态的拆分视图](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*图 4：终结点状态指标的拆分视图*

可以通过 [Azure Monitor 服务](../monitoring-and-diagnostics/monitoring-supported-metrics.md)的门户、[REST API](https://docs.microsoft.com/rest/api/monitor/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 和 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights) 或者通过流量管理器的门户体验的指标部分来使用这些指标。

## <a name="alerts-on-traffic-manager-metrics"></a>针对流量管理器指标的警报
除了处理和显示来自流量管理器的指标外，Azure Monitor 还允许客户配置和接收与这些指标关联的警报。 你可以选择这些指标需要满足什么条件才会发生警报、需要以何频率监视这些条件，以及应当如何向你发送警报。 有关详细信息，请参阅 [Azure Monitor 警报文档](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Monitor 服务](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- 了解如何[使用 Azure Monitor 创建新图表](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)