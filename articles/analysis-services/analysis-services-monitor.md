---
title: 监视 Azure Analysis Services 服务器指标 | Microsoft Docs
description: 了解如何在 Azure 门户中监视 Analysis Services 服务器指标。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b07bd41ee3aa67924256d694c1f9dadd39b038cc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-server-metrics"></a>监视服务器指标

Analysis Services 提供的指标可帮助你监视服务器的性能和运行状况。 例如，监视内存和 CPU 使用率、客户端连接数和查询资源消耗量。 Analysis Services 使用与大多数其他 Azure 服务相同的监视框架。 若要了解详细信息，请参阅 [Microsoft Azure 中的指标](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

若要执行更深入的诊断、跟踪性能，并确定跨资源组或订阅中的多个服务资源的趋势，请使用 [Azure Monitor](https://azure.microsoft.com/services/monitor/)。 Azure Monitor（服务）可能会导致计费服务。


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>监视 Analysis Services 服务器指标

1. 在 Azure 门户中，选择“指标”。

    ![在 Azure 门户中监视](./media/analysis-services-monitor/aas-monitor-portal.png)

2. 在**可用指标**中，选择要在图表中包括的指标。 

    ![监视图表](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>
## <a name="server-metrics"></a>服务器指标
使用此表可确定哪些指标最适用于监视方案。 在同一图表上只能显示相同单位的指标。

|指标|指标显示名称|单位|聚合类型|说明|
|---|---|---|---|---|
|CommandPoolJobQueueLength|命令池作业队列长度|Count|平均值|命令线程池队列中的作业数。|
|CurrentConnections|连接: 当前连接数|Count|平均值|当前已建立的客户端连接的数量。|
|CurrentUserSessions|当前用户会话数|Count|平均值|当前已建立的用户会话数。|
|mashup_engine_memory_metric|M 引擎内存|字节|平均值|糅合引擎进程的内存使用率|
|mashup_engine_qpu_metric|M 引擎 QPU|Count|平均值|糅合引擎进程的 QPU 使用率|
|memory_metric|内存|字节|平均值|内存。 S1 范围为 0-25 GB，S2 范围为 0-50 GB，S4 范围为 0-100 GB|
|memory_thrashing_metric|内存抖动|百分比|平均值|平均内存抖动。|
|CleanerCurrentPrice|内存: 清理器当前价格|Count|平均值|内存的当前价格，$/字节/时间，标准化为 1000。|
|CleanerMemoryNonshrinkable|内存: 不可收缩的清理器内存|字节|平均值|不受后台清理器执行的清除影响的内存量（字节）。|
|CleanerMemoryShrinkable|内存: 可收缩的清理器内存|字节|平均值|受后台清理器执行的清除影响的内存量（字节）。|
|MemoryLimitHard|内存: 内存硬性限制|字节|平均值|内存硬性限制，来自配置文件。|
|MemoryLimitHigh|内存: 内存上限|字节|平均值|内存上限，来自配置文件。|
|MemoryLimitLow|内存: 内存下限|字节|平均值|内存下限，来自配置文件。|
|MemoryLimitVertiPaq|内存: 内存 VertiPaq 限制|字节|平均值|内存中限制，来自配置文件。|
|MemoryUsage|内存: 内存使用量|字节|平均值|服务器进程的内存使用量（在计算清理器内存价格时使用）。 等于计数器 Process\PrivateBytes 加上内存映射的数据的大小，并且将忽略由内存中分析引擎 (VertiPaq) 映射或分配的超出了引擎内存限制的任何内存。|
|Quota|内存: 配额|字节|平均值|当前内存配额（字节）。 内存配额也称为内存授予或内存预留。|
|QuotaBlocked|内存: 阻止的配额|Count|平均值|在其他内存配额被释放之前已阻止的当前的配额请求数。|
|VertiPaqNonpaged|内存: VertiPaq 未分页|字节|平均值|工作集中被锁定的供内存中引擎使用的内存字节数。|
|VertiPaqPaged|内存: VertiPaq 已分页|字节|平均值|用于内存中数据的已分页内存字节数。|
|ProcessingPoolJobQueueLength|处理池作业队列长度|Count|平均值|处理线程池的队列中的非 I/O 作业数。|
|RowsConvertedPerSec|处理: 每秒转换的行数|每秒计数|平均值|在处理过程中转换行的速率。|
|RowsReadPerSec|处理: 每秒读取的行数|每秒计数|平均值|从所有关系数据库中读取行的速率。|
|RowsWrittenPerSec|处理: 每秒写入的行数|每秒计数|平均值|在处理过程中写入行的速率。|
|qpu_metric|QPU|Count|平均值|QPU。 S1 范围为 0-100，S2 范围为 0-200，S4 范围为 0-400|
|QueryPoolBusyThreads|查询池繁忙线程数|Count|平均值|查询线程池中的繁忙线程数。|
|SuccessfullConnectionsPerSec|每秒成功连接数|每秒计数|平均值|连接成功完成速率。|
|CommandPoolBusyThreads|线程: 命令池繁忙线程数|Count|平均值|命令线程池中的繁忙线程数。|
|CommandPoolIdleThreads|线程: 命令池空闲线程数|Count|平均值|命令线程池中的空闲线程数。|
|LongParsingBusyThreads|线程: 长分析繁忙线程数|Count|平均值|长分析线程池中的繁忙线程数。|
|LongParsingIdleThreads|线程: 长分析空闲线程数|Count|平均值|长分析线程池中的空闲线程数。|
|LongParsingJobQueueLength|线程: 长分析作业队列长度|Count|平均值|长分析线程池队列中的作业数。|
|ProcessingPoolIOJobQueueLength|线程: 处理池 I/O 作业队列长度|Count|平均值|处理线程池队列中的 I/O 作业数。|
|ProcessingPoolBusyIOJobThreads|线程: 处理池繁忙 I/O 作业线程数|Count|平均值|处理线程池中正在运行 I/O 作业的线程数。|
|ProcessingPoolBusyNonIOThreads|线程: 处理池繁忙非 I/O 线程数|Count|平均值|处理线程池中正在运行非 I/O 作业的线程数。|
|ProcessingPoolIdleIOJobThreads|线程: 处理池空闲 I/O 作业线程数|Count|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|
|ProcessingPoolIdleNonIOThreads|线程: 处理池空闲非 I/O 线程数|Count|平均值|处理线程池中专用于非 I/O 作业的空闲线程数。|
|QueryPoolIdleThreads|线程: 查询池空闲线程数|Count|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|
|QueryPoolJobQueueLength|线程: 查询池作业队列长度|Count|平均值|查询线程池队列中的作业数。|
|ShortParsingBusyThreads|线程: 短分析繁忙线程数|Count|平均值|短分析线程池中的繁忙线程数。|
|ShortParsingIdleThreads|线程: 短分析空闲线程数|Count|平均值|短分析线程池中的空闲线程数。|
|ShortParsingJobQueueLength|线程: 短分析作业队列长度|Count|平均值|短分析线程池队列中的作业数。|
|TotalConnectionFailures|连接失败总数|Count|平均值|失败的连接尝试总数。|
|TotalConnectionRequests|连接请求总数|Count|平均值|连接请求总数。 |

## <a name="next-steps"></a>后续步骤
[Microsoft Azure 中的监视](../monitoring-and-diagnostics/monitoring-overview.md)   
[Microsoft Azure 中的指标](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Azure Monitor REST API 中的指标](https://msdn.microsoft.com/library/azure/dn931930.aspx)