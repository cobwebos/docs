---
title: 监视 Azure Analysis Services 服务器指标 | Microsoft Docs
description: 了解如何在 Azure 门户中监视 Analysis Services 服务器指标。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c7aaf911930f83775f66c47377bc68edb059519
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958981"
---
# <a name="monitor-server-metrics"></a>监视服务器指标

Analysis Services 在 Azure 指标资源管理器（门户中的一个免费工具）中提供指标，以帮助你监视服务器的性能和运行状况。 例如，监视内存和 CPU 使用率、客户端连接数和查询资源消耗量。 Analysis Services 使用与大多数其他 Azure 服务相同的监视框架。 若要了解详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/platform/metrics-getting-started.md)。

若要执行更深入的诊断、跟踪性能，并确定跨资源组或订阅中的多个服务资源的趋势，请使用 [Azure Monitor](../azure-monitor/overview.md)。 Azure Monitor（服务）可能会导致计费服务。


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>监视 Analysis Services 服务器指标

1. 在 Azure 门户中，选择“指标”。

    ![在 Azure 门户中监视](./media/analysis-services-monitor/aas-monitor-portal.png)

2. 在**可用指标**中，选择要在图表中包括的指标。 

    ![监视图表](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>服务器指标

使用此表可确定哪些指标最适用于监视方案。 在同一图表上只能显示相同单位的指标。

|指标|指标显示名称|单位|聚合类型|描述|
|---|---|---|---|---|
|CommandPoolJobQueueLength|命令池作业队列长度|Count|Average|命令线程池队列中的作业数。|
|CurrentConnections|连接: 当前连接数|Count|Average|当前已建立的客户端连接的数量。|
|CurrentUserSessions|当前用户会话数|Count|Average|当前已建立的用户会话数。|
|mashup_engine_memory_metric|M 引擎内存|字节|Average|糅合引擎进程的内存使用率|
|mashup_engine_qpu_metric|M 引擎 QPU|Count|Average|糅合引擎进程的 QPU 使用率|
|memory_metric|内存|字节|Average|内存。 S1 范围为 0-25 GB，S2 范围为 0-50 GB，S4 范围为 0-100 GB|
|memory_thrashing_metric|内存抖动|Percent|Average|平均内存抖动。|
|CleanerCurrentPrice|内存: 清理器当前价格|Count|Average|内存的当前价格，$/字节/时间，标准化为 1000。|
|CleanerMemoryNonshrinkable|内存: 不可收缩的清理器内存|字节|Average|不受后台清理器执行的清除影响的内存量（字节）。|
|CleanerMemoryShrinkable|内存: 可收缩的清理器内存|字节|Average|受后台清理器执行的清除影响的内存量（字节）。|
|MemoryLimitHard|内存: 内存硬性限制|字节|Average|内存硬性限制，来自配置文件。|
|MemoryLimitHigh|内存: 内存上限|字节|Average|内存上限，来自配置文件。|
|MemoryLimitLow|内存: 内存下限|字节|Average|内存下限，来自配置文件。|
|MemoryLimitVertiPaq|内存: 内存 VertiPaq 限制|字节|Average|内存中限制，来自配置文件。|
|MemoryUsage|内存: 内存使用率|字节|Average|服务器进程的内存使用量（在计算清理器内存价格时使用）。 等于计数器 Process\PrivateBytes 加上内存映射的数据的大小，并且将忽略由内存中分析引擎 (VertiPaq) 映射或分配的超出了引擎内存限制的任何内存。|
|private_bytes_metric|专用字节数 |字节|Average|Analysis Services 引擎进程和混合容器进程分配的内存总量，不包括与其他进程共享的内存。|
|virtual_bytes_metric|虚拟字节 |字节|Average|Analysis Services 引擎进程和混合容器进程使用的虚拟地址空间的当前大小。|
|mashup_engine_private_bytes_metric|M 引擎专用字节数 |字节|Average|已分配的内存混合容器进程的总量，不包括与其他进程共享的内存。|
|mashup_engine_virtual_bytes_metric|M 引擎虚拟字节数 |字节|Average|当前正在使用的虚拟地址空间混合容器进程的大小。|
|配额|内存: 配额|字节|Average|当前内存配额（字节）。 内存配额也称为内存授予或内存预留。|
|QuotaBlocked|内存: 阻止的配额|Count|Average|在其他内存配额被释放之前已阻止的当前的配额请求数。|
|VertiPaqNonpaged|内存: VertiPaq 未分页|字节|Average|工作集中被锁定的供内存中引擎使用的内存字节数。|
|VertiPaqPaged|内存: VertiPaq 已分页|字节|Average|用于内存中数据的已分页内存字节数。|
|ProcessingPoolJobQueueLength|处理池作业队列长度|Count|Average|处理线程池的队列中的非 I/O 作业数。|
|RowsConvertedPerSec|处理: 每秒转换的行数|每秒计数|Average|在处理过程中转换行的速率。|
|RowsReadPerSec|处理: 每秒读取的行数|每秒计数|Average|从所有关系数据库中读取行的速率。|
|RowsWrittenPerSec|处理: 每秒写入的行数|每秒计数|Average|在处理过程中写入行的速率。|
|qpu_metric|QPU|Count|Average|QPU。 S1 范围为 0-100，S2 范围为 0-200，S4 范围为 0-400|
|QueryPoolBusyThreads|查询池繁忙线程数|Count|Average|查询线程池中的繁忙线程数。|
|SuccessfullConnectionsPerSec|每秒成功连接数|每秒计数|Average|连接成功完成速率。|
|CommandPoolBusyThreads|线程: 命令池繁忙线程数|Count|Average|命令线程池中的繁忙线程数。|
|CommandPoolIdleThreads|线程: 命令池空闲线程数|Count|Average|命令线程池中的空闲线程数。|
|LongParsingBusyThreads|线程: 长分析繁忙线程数|Count|Average|长分析线程池中的繁忙线程数。|
|LongParsingIdleThreads|线程: 长分析空闲线程数|Count|Average|长分析线程池中的空闲线程数。|
|LongParsingJobQueueLength|线程: 长分析作业队列长度|Count|Average|长分析线程池队列中的作业数。|
|ProcessingPoolIOJobQueueLength|线程: 处理池 I/O 作业队列长度|Count|Average|处理线程池队列中的 I/O 作业数。|
|ProcessingPoolBusyIOJobThreads|线程: 处理池繁忙 I/O 作业线程数|Count|Average|处理线程池中正在运行 I/O 作业的线程数。|
|ProcessingPoolBusyNonIOThreads|线程: 处理池繁忙非 I/O 线程数|Count|Average|处理线程池中正在运行非 I/O 作业的线程数。|
|ProcessingPoolIdleIOJobThreads|线程: 处理池空闲 I/O 作业线程数|Count|Average|处理线程池中可用于 I/O 作业的空闲线程数。|
|ProcessingPoolIdleNonIOThreads|线程: 处理池空闲非 I/O 线程数|Count|Average|处理线程池中专用于非 I/O 作业的空闲线程数。|
|QueryPoolIdleThreads|线程: 查询池空闲线程数|Count|Average|处理线程池中可用于 I/O 作业的空闲线程数。|
|QueryPoolJobQueueLength|线程: 查询池作业队列长度|Count|Average|查询线程池队列中的作业数。|
|ShortParsingBusyThreads|线程: 短分析繁忙线程数|Count|Average|短分析线程池中的繁忙线程数。|
|ShortParsingIdleThreads|线程: 短分析空闲线程数|Count|Average|短分析线程池中的空闲线程数。|
|ShortParsingJobQueueLength|线程: 短分析作业队列长度|Count|Average|短分析线程池队列中的作业数。|
|TotalConnectionFailures|连接失败总数|Count|Average|失败的连接尝试总数。|
|TotalConnectionRequests|连接请求总数|Count|Average|连接请求总数。 |

## <a name="next-steps"></a>后续步骤
[Azure Monitor 概述](../azure-monitor/overview.md)      
[Azure 指标资源管理器入门](../azure-monitor/platform/metrics-getting-started.md)      
[Azure Monitor REST API 中的指标](/rest/api/monitor/metrics)
