---
title: Azure Log Analytics 中的数据引入时间 | Microsoft Docs
description: 介绍影响 Azure Log Analytics 中收集数据的延迟的不同因素。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: 329472f3edee66db6b12e369ee8f944546ad4734
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900436"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Log Analytics 中的数据引入时间
Azure Log Analytics 是 Azure Monitor 中的一种大规模数据服务，每月为成千上万的客户发送数 TB 的数据，并且此数据仍在不断增长。 关于数据收集后需要多长时间才能在 Log Analytics 中使用，大家通常存有疑问。 本文将对影响此延迟的不同因素进行说明。

## <a name="typical-latency"></a>典型延迟
延迟是指在受监视系统上创建数据的时间以及可在 Log Analytics 中使用该数据进行分析的时间。 将数据引入 Log Analytics 的典型延迟时间为 2 到 5 分钟。 任何特定数据的特定延迟将根据下面介绍的各种因素而变化。


## <a name="factors-affecting-latency"></a>影响延迟的因素
特定数据集的总引入时间可以细分为以下几个高级别区域。 

- 代理时间：发现事件、收集事件，然后将其作为日志记录发送到 Log Analytics 引入点的时间。 大多数情况下，此过程由代理处理。
- 管道时间：引入管道处理日志记录的时间。 包括解析事件属性，并且可能会添加计算信息。
- 索引时间：将日志记录引入到 Log Analytics 大数据存储所花费的时间。

下面将详细介绍该过程中引入的不同延迟。

### <a name="agent-collection-latency"></a>代理收集延迟
代理和管理解决方案使用不同的策略从虚拟机收集数据，这可能会影响延迟。 一些具体示例包括：

- 立即收集 Windows 事件、syslog 事件和性能指标。 Linux 性能计数器每隔 30 秒轮询一次。
- IIS 日志和自定义日志在其时间戳更改后收集。 对于 IIS 日志，这会受 [IIS 上配置的滚动更新计划](../../azure-monitor/platform/data-sources-iis-logs.md)影响。 
- Active Directory 复制解决方案每五天执行一次评估，而 Active Directory 评估解决方案每周对 Active Directory 基础结构进行一次评估。 只有在评估完成后，代理才会收集这些日志。

### <a name="agent-upload-frequency"></a>代理上传频率
为确保 Log Analytics 代理保持轻型，代理会缓冲日志并定期将其上传到 Log Analytics。 上传频率在 30 秒到 2 分钟之间变化，具体取决于数据类型。 大多数数据可在 1 分钟内上传。 网络状况可能会对数据抵达 Log Analytics 引入点的延迟产生负面影响。

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Azure 活动日志、诊断日志和指标
Azure 数据增加了额外的时间，以便在 Log Analytics 引入点处可用于处理：

- 诊断日志中的数据需要 2 到 15 分钟，具体取决于 Azure 服务。 请参阅[下面的查询](#checking-ingestion-time)，以便在你的环境中检查此延迟
- 将 Azure 平台指标发送到 Log Analytics 引入点需要 3 分钟。
- 将活动日志数据发送到 Log Analytics 引入点大约需要 10 到 15 分钟。

数据在引入点处可用后，还需要 2 到 5 分钟才能进行查询。


### <a name="management-solutions-collection"></a>管理解决方案收集
某些解决方案不从代理收集其数据，并且可能使用会引入额外延迟的收集方法。 一些解决方案以固定时间间隔收集数据，而不尝试近实时收集。 具体示例包括：

- Office 365 解决方案使用 Office 365 管理活动 API 轮询活动日志，该方法目前不提供任何近实时延迟保证。
- 该解决方案以每天一次的频率收集 Windows Analytics 解决方案（例如更新符合性）数据。

请参阅各解决方案的文档，确定其收集频率。

### <a name="pipeline-process-time"></a>管道处理时间
将日志记录引入到 Log Analytics 管道后，会将其写入临时存储，以确保租户隔离并确保数据不会丢失。 此过程通常会花费 5-15 秒的时间。 一些管理解决方案实施了更复杂的算法来聚合数据，并在数据流入时获得见解。 例如，网络性能监视器以 3 分钟的时间间隔聚合传入数据，有效地增加了 3 分钟的延迟。 处理自定义日志是另一个增加延迟的过程。 在某些情况下，此过程可能会为代理从文件收集的日志增加几分钟延迟。

### <a name="new-custom-data-types-provisioning"></a>新的自定义数据类型预配
从[自定义日志](data-sources-custom-logs.md)或[数据收集器 API ](data-collector-api.md)创建新的自定义数据类型时，系统会创建专用存储容器。 这是一次性开销，仅在此数据类型第一次出现时支付。

### <a name="surge-protection"></a>激增保护
Log Analytics 的首要任务是确保不会丢失任何客户数据，因此系统具有内置的数据激增保护。 这包括缓冲区，可用于确保即使在巨大的负载下，系统也能继续正常运行。 在正常负载下，这些控件增加的时间不到一分钟，但在极端条件和故障情况下，它们可以增加大量时间，同时确保数据安全。

### <a name="indexing-time"></a>索引任务
对于提供分析和高级搜索功能以及提供对数据的即时访问，每个大数据平台都有内置平衡。 Azure Log Analytics 允许用户对数十亿条记录运行强大的查询，并能在几秒钟内获得结果。 之所以能做到这一点，是因为基础结构能在数据引入过程中急速转换数据并将其存储在独特的紧凑结构中。 系统不断缓冲数据，直到有足够的数据可用于创建这些结构。 必须在日志记录出现在搜索结果中之前完成此操作。

目前，在数据量较少但数据速率较高的情况下，此过程大约需要 5 分钟。 这似乎有悖常理，但此过程可优化大量生产工作负荷的延迟。



## <a name="checking-ingestion-time"></a>检查引入时间
由于在不同情况下，不同资源的引入时间可能会有所不同。 可以使用日志查询来识别环境的特定行为。

### <a name="ingestion-latency-delays"></a>引入延迟延迟
可以通过比较 [ingestion_time()](/azure/kusto/query/ingestiontimefunction) 函数的结果和 TimeGenerated 字段来测量特定记录的延迟。 此数据可用于各种聚合，以查找引入延迟的行为方式。 检查引入时间的某些百分位数，以获取大量数据的见解。 

例如，以下查询将显示哪些计算机当天的引入时间最长： 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
如果想要在一段时间内对特定计算机的引入时间向下钻取，请使用以下可直观显示图形中的数据的查询： 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
使用以下查询显示计算机引入时间，按它们基于 IP 地址所在的国家/地区显示： 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
源自代理的不同数据类型可能具有不同的引入延迟时间，因此先前的查询可以与其他类型一起使用。 使用以下查询来检查各种 Azure 服务的引入时间： 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>停止响应的资源 
在某些情况下，资源无法停止发送数据。 若要了解资源是否正在发送数据，请查看由标准 TimeGenerated 字段标识的最新记录。  

使用检测信号表来检查 VM 的可用性，因为检测信号由代理每分钟发送一次。 使用以下查询列出最近尚未报告过检测信号的活动计算机： 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>后续步骤
* 参阅 Log Analytics 的[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/)。

