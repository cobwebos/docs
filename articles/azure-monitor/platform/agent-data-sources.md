---
title: 在 Azure Monitor 中配置代理数据源 | Microsoft Docs
description: 数据源定义 Azure Monitor 从代理和其他已连接的源收集的日志数据。  本文介绍有关 Azure Monitor 如何使用数据源的概念，详细解释如何配置数据源，并对不同的可用数据源进行概要介绍。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: ad4270700712311d8bea2e2014701a99e44c735f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992439"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Azure Monitor 中的代理数据源
Azure Monitor 从代理收集的数据是由你配置的数据源定义的。  来自代理的数据存储为包含记录集的[日志数据](data-collection.md)。  每个数据源将创建具有某种特殊类型的记录，而每个类型都具有自己的一组属性。

![日志数据收集](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>数据源概要介绍
下表列出了 Azure Monitor 中当前可用的代理数据源。  每个数据源都链接到一篇单独的文章，提供该数据源的详细信息。   它还提供了有关收集方法和收集频率的信息。 


| 数据源 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [自定义日志](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 到达时 |
| [自定义日志](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 到达时 |
| [IIS 日志](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |依赖于日志文件滚动更新设置 |
| [性能计数器](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |根据计划，最小值为 10 秒 |
| [性能计数器](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |根据计划，最小值为 10 秒 |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |来自 Azure 存储：10 分钟；来自代理：到达时 |
| [Windows 事件日志](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 到达时 |


## <a name="configuring-data-sources"></a>配置数据源
可以从工作区“高级设置”中的“数据”菜单配置数据源。  任何配置都将传送到工作区中所有已连接的数据源。  当前不能从此配置中排除任何代理。

![配置 Windows 事件](media/agent-data-sources/configure-events.png)

1. 在 Azure 门户中，选择“Log Analytics 工作区”> 你的工作区 >“高级设置”。
2. 选择“**数据**”。
3. 单击要配置的数据源。
4. 按照上表中每个数据源链接到的文档，了解有关其配置的详细信息。


## <a name="data-collection"></a>数据收集
数据源配置会在几分钟内传送到与 Azure Monitor 直接连接的各个代理。  指定的数据从代理收集，并按特定于每个数据源的时间间隔直接传送到 Azure Monitor。  请参阅每个数据源的文档以了解详情。

对于已连接管理组中的 System Center Operations Manager 代理，数据源配置默认以每 5 分钟的间隔转换成管理包并传送到管理组。  代理会下载任何其他的管理包，并收集指定的数据。 根据数据源的不同，数据或者被发送到管理服务器，再由管理服务器转发到 Azure Monitor；或者不通过管理服务器，由代理将数据发送到 Azure Monitor。 有关详细信息，请参阅 [Azure 中的监视解决方案的数据收集详细信息](../insights/solutions-inventory.md)。  可以在[配置与 System Center Operations Manager 的集成](om-agents.md)中阅读有关连接 Operations Manager 和 Azure Monitor 以及修改配置传送频率的详细信息。

如果代理无法连接到 Azure Monitor 或 Operations Manager，将继续收集在建立连接时传送的数据。  如果数据量达到客户端的最大缓存大小，或者如果代理无法在 24 小时内建立连接，则可能会丢失数据。

## <a name="log-records"></a>日志记录
Azure Monitor 收集的所有日志数据都作为记录存储在工作区中。  按不同数据源收集的记录具有其自己的属性集，并由其“**类型**”属性来识别。  有关每种记录类型的详细信息，请参阅每个数据源和解决方案的相关文档。

## <a name="next-steps"></a>后续步骤
* 了解[监视解决方案](../insights/solutions.md)如何将功能添加到 Azure Monitor，以及如何将数据收集到工作区中。
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和监视解决方案中收集的数据。  
* 配置[警报](alerts-overview.md)以便主动向你通知从数据源和监视解决方案中收集的关键数据。