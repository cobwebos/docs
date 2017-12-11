---
title: "Azure Monitor 指标 - 每种资源类型支持的指标 | Microsoft Docs"
description: "可在 Azure Monitor 中为每种资源类型使用的指标的列表。"
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: ancav
ms.openlocfilehash: ef27a15bb6a6305f7a762716a20487ef983cb5d1
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor 支持的指标
Azure Monitor 提供多种方式来与指标交互，包括在门户中制作指标图表、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 下面是目前可在 Azure Monitor 的指标管道中使用的完整指标列表。

> [!NOTE]
> 其他指标可在门户或旧版 API 中使用。 此列表仅包含可以通过合并的 Azure Monitor 指标管道使用的指标。 若要查询和访问具有维度的指标，请使用 [2017-05-01-preview API 版本](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|qpu_metric|QPU|计数|平均值|QPU。 S1 范围为 0-100，S2 范围为 0-200，S4 范围为 0-400|无维度|
|memory_metric|内存|字节|平均值|内存。 S1 范围为 0-25 GB，S2 范围为 0-50 GB，S4 范围为 0-100 GB|无维度|
|TotalConnectionRequests|连接请求总数|计数|平均值|连接请求总数。 这些请求是到达的请求。|无维度|
|SuccessfullConnectionsPerSec|每秒成功连接数|每秒计数|平均值|连接成功完成速率。|无维度|
|TotalConnectionFailures|连接失败总数|计数|平均值|失败的连接尝试总数。|无维度|
|CurrentUserSessions|当前用户会话数|计数|平均值|当前已建立的用户会话数。|无维度|
|QueryPoolBusyThreads|查询池繁忙线程数|计数|平均值|查询线程池中的繁忙线程数。|无维度|
|CommandPoolJobQueueLength|命令池作业队列长度|计数|平均值|命令线程池队列中的作业数。|无维度|
|ProcessingPoolJobQueueLength|处理池作业队列长度|计数|平均值|处理线程池的队列中的非 I/O 作业数。|无维度|
|CurrentConnections|连接: 当前连接数|计数|平均值|当前已建立的客户端连接的数量。|无维度|
|CleanerCurrentPrice|内存: 清理器当前价格|计数|平均值|内存的当前价格，$/字节/时间，标准化为 1000。|无维度|
|CleanerMemoryShrinkable|内存: 可收缩的清理器内存|字节|平均值|受后台清理器执行的清除影响的内存量（字节）。|无维度|
|CleanerMemoryNonshrinkable|内存: 不可收缩的清理器内存|字节|平均值|不受后台清理器执行的清除影响的内存量（字节）。|无维度|
|MemoryUsage|内存: 内存使用量|字节|平均值|服务器进程的内存使用量（在计算清理器内存价格时使用）。 等于计数器 Process\PrivateBytes 加上内存映射的数据的大小，并且将忽略由 xVelocity 内存中分析引擎 (VertiPaq) 映射或分配的超出了 xVelocity 引擎内存限制的任何内存。|无维度|
|MemoryLimitHard|内存: 内存硬性限制|字节|平均值|内存硬性限制，来自配置文件。|无维度|
|MemoryLimitHigh|内存: 内存上限|字节|平均值|内存上限，来自配置文件。|无维度|
|MemoryLimitLow|内存: 内存下限|字节|平均值|内存下限，来自配置文件。|无维度|
|MemoryLimitVertiPaq|内存: 内存 VertiPaq 限制|字节|平均值|内存中限制，来自配置文件。|无维度|
|Quota|内存: 配额|字节|平均值|当前内存配额（字节）。 内存配额也称为内存授予或内存预留。|无维度|
|QuotaBlocked|内存: 阻止的配额|计数|平均值|在其他内存配额被释放之前已阻止的当前的配额请求数。|无维度|
|VertiPaqNonpaged|内存: VertiPaq 未分页|字节|平均值|工作集中被锁定的供内存中引擎使用的内存字节数。|无维度|
|VertiPaqPaged|内存: VertiPaq 已分页|字节|平均值|用于内存中数据的已分页内存字节数。|无维度|
|RowsReadPerSec|处理: 每秒读取的行数|每秒计数|平均值|从所有关系数据库中读取行的速率。|无维度|
|RowsConvertedPerSec|处理: 每秒转换的行数|每秒计数|平均值|在处理过程中转换行的速率。|无维度|
|RowsWrittenPerSec|处理: 每秒写入的行数|每秒计数|平均值|在处理过程中写入行的速率。|无维度|
|CommandPoolBusyThreads|线程: 命令池繁忙线程数|计数|平均值|命令线程池中的繁忙线程数。|无维度|
|CommandPoolIdleThreads|线程: 命令池空闲线程数|计数|平均值|命令线程池中的空闲线程数。|无维度|
|LongParsingBusyThreads|线程: 长分析繁忙线程数|计数|平均值|长分析线程池中的繁忙线程数。|无维度|
|LongParsingIdleThreads|线程: 长分析空闲线程数|计数|平均值|长分析线程池中的空闲线程数。|无维度|
|LongParsingJobQueueLength|线程: 长分析作业队列长度|计数|平均值|长分析线程池队列中的作业数。|无维度|
|ProcessingPoolBusyIOJobThreads|线程: 处理池繁忙 I/O 作业线程数|计数|平均值|处理线程池中正在运行 I/O 作业的线程数。|无维度|
|ProcessingPoolBusyNonIOThreads|线程: 处理池繁忙非 I/O 线程数|计数|平均值|处理线程池中正在运行非 I/O 作业的线程数。|无维度|
|ProcessingPoolIOJobQueueLength|线程: 处理池 I/O 作业队列长度|计数|平均值|处理线程池队列中的 I/O 作业数。|无维度|
|ProcessingPoolIdleIOJobThreads|线程: 处理池空闲 I/O 作业线程数|计数|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|无维度|
|ProcessingPoolIdleNonIOThreads|线程: 处理池空闲非 I/O 线程数|计数|平均值|处理线程池中专用于非 I/O 作业的空闲线程数。|无维度|
|QueryPoolIdleThreads|线程: 查询池空闲线程数|计数|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|无维度|
|QueryPoolJobQueueLength|线程: 查询池作业队列长度|计数|平均值|查询线程池队列中的作业数。|无维度|
|ShortParsingBusyThreads|线程: 短分析繁忙线程数|计数|平均值|短分析线程池中的繁忙线程数。|无维度|
|ShortParsingIdleThreads|线程: 短分析空闲线程数|计数|平均值|短分析线程池中的空闲线程数。|无维度|
|ShortParsingJobQueueLength|线程: 短分析作业队列长度|计数|平均值|短分析线程池队列中的作业数。|无维度|
|memory_thrashing_metric|内存抖动|百分比|平均值|平均内存抖动。|无维度|
|mashup_engine_qpu_metric|M 引擎 QPU|计数|平均值|糅合引擎进程的 QPU 使用率|无维度|
|mashup_engine_memory_metric|M 引擎内存|字节|平均值|糅合引擎进程的内存使用率|无维度|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalRequests|网关请求总数|计数|总计|网关请求数|位置、主机名|
|SuccessfulRequests|成功的网关请求数|计数|总计|成功的网关请求数|位置、主机名|
|UnauthorizedRequests|未经授权的网关请求数|计数|总计|未经授权的网关请求数|位置、主机名|
|FailedRequests|失败的网关请求数|计数|总计|失败的网关请求数|位置、主机名|
|OtherRequests|其他网关请求数|计数|总计|其他网关请求数|位置、主机名|
|Duration|网关请求的总持续时间|毫秒|平均值|网关请求的总持续时间，以毫秒为单位|位置、主机名|
|Capacity|容量（预览）|百分比|最大值|ApiManagement 服务的利用率指标|位置|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalJob|作业总数|计数|总计|作业总数|无维度|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CoreCount|专用核心计数|计数|总计|批处理帐户中的专用核心总数|无维度|
|TotalNodeCount|专用节点计数|计数|总计|批处理帐户中的专用节点总数|无维度|
|LowPriorityCoreCount|低优先级核心计数|计数|总计|批处理帐户中的低优先级核心总数|无维度|
|TotalLowPriorityNodeCount|低优先级节点计数|计数|总计|批处理帐户中的低优先级节点总数|无维度|
|CreatingNodeCount|正在创建的节点计数|计数|总计|正在创建的节点数目|无维度|
|StartingNodeCount|正在启动的节点计数|计数|总计|正在启动的节点数目|无维度|
|WaitingForStartTaskNodeCount|正在等待启动任务的节点计数|计数|总计|正在等待启动任务完成的节点数目|无维度|
|StartTaskFailedNodeCount|启动任务失败的节点计数|计数|总计|启动任务失败的节点数目|无维度|
|IdleNodeCount|空闲节点计数|计数|总计|空闲节点数目|无维度|
|OfflineNodeCount|脱机节点计数|计数|总计|脱机节点数目|无维度|
|RebootingNodeCount|正在重新启动的节点计数|计数|总计|正在重新启动的节点数目|无维度|
|ReimagingNodeCount|正在重置映像的节点计数|计数|总计|正在重置映像的节点数目|无维度|
|RunningNodeCount|正在运行的节点计数|计数|总计|正在运行的节点数目|无维度|
|LeavingPoolNodeCount|正在退出池的节点计数|计数|总计|正在退出池的节点数目|无维度|
|UnusableNodeCount|不可用的节点计数|计数|总计|不可用的节点数目|无维度|
|PreemptedNodeCount|已占用节点计数|计数|总计|已占用节点数|无维度|
|TaskStartEvent|任务启动事件数|计数|总计|已启动的任务总数|无维度|
|TaskCompleteEvent|任务完成事件数|计数|总计|已完成的任务总数|无维度|
|TaskFailEvent|任务失败事件数|计数|总计|处于失败状态的已完成任务总数|无维度|
|PoolCreateEvent|池创建事件数|计数|总计|已创建的池总数|无维度|
|PoolResizeStartEvent|池调整大小启动事件数|计数|总计|已启动的池调整大小活动总数|无维度|
|PoolResizeCompleteEvent|池调整大小完成事件数|计数|总计|已完成的池调整大小活动总数|无维度|
|PoolDeleteStartEvent|池删除启动事件数|计数|总计|已启动的池删除活动总数|无维度|
|PoolDeleteCompleteEvent|池删除完成事件数|计数|总计|已完成的池删除活动总数|无维度|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|connectedclients|连接的客户端数|计数|最大值||无维度|
|totalcommandsprocessed|总操作数|计数|总计||无维度|
|cachehits|缓存命中数|计数|总计||无维度|
|cachemisses|缓存未命中数|计数|总计||无维度|
|getcommands|获取数|计数|总计||无维度|
|setcommands|设置数|计数|总计||无维度|
|evictedkeys|逐出的密钥数|计数|总计||无维度|
|totalkeys|总密钥数|计数|最大值||无维度|
|expiredkeys|过期的密钥数|计数|总计||无维度|
|usedmemory|已用内存|字节|最大值||无维度|
|usedmemoryRss|已用内存 RSS|字节|最大值||无维度|
|serverLoad|服务器负载|百分比|最大值||无维度|
|cacheWrite|缓存写入量|每秒字节数|最大值||无维度|
|cacheRead|缓存读取量|每秒字节数|最大值||无维度|
|percentProcessorTime|CPU|百分比|最大值||无维度|
|connectedclients0|连接的客户端数(分片 0)|计数|最大值||无维度|
|totalcommandsprocessed0|总操作数(分片 0)|计数|总计||无维度|
|cachehits0|缓存命中数(分片 0)|计数|总计||无维度|
|cachemisses0|缓存未命中数(分片 0)|计数|总计||无维度|
|getcommands0|Get 数(分片 0)|计数|总计||无维度|
|setcommands0|Set 数(分片 0)|计数|总计||无维度|
|evictedkeys0|逐出的密钥数(分片 0)|计数|总计||无维度|
|totalkeys0|总密钥数（分片 0）|计数|最大值||无维度|
|expiredkeys0|过期的密钥数(分片 0)|计数|总计||无维度|
|usedmemory0|已用内存量(分片 0)|字节|最大值||无维度|
|usedmemoryRss0|已用内存 RSS (分片 0)|字节|最大值||无维度|
|serverLoad0|服务器负载(分片 0)|百分比|最大值||无维度|
|cacheWrite0|缓存写入量(分片 0)|每秒字节数|最大值||无维度|
|cacheRead0|缓存读取量(分片 0)|每秒字节数|最大值||无维度|
|percentProcessorTime0|CPU (分片 0)|百分比|最大值||无维度|
|connectedclients1|连接的客户端数(分片 1)|计数|最大值||无维度|
|totalcommandsprocessed1|总操作数(分片 1)|计数|总计||无维度|
|cachehits1|缓存命中数(分片 1)|计数|总计||无维度|
|cachemisses1|缓存未命中数(分片 1)|计数|总计||无维度|
|getcommands1|Get 数(分片 1)|计数|总计||无维度|
|setcommands1|Set 数(分片 1)|计数|总计||无维度|
|evictedkeys1|逐出的密钥数(分片 1)|计数|总计||无维度|
|totalkeys1|总密钥数（分片 1）|计数|最大值||无维度|
|expiredkeys1|过期的密钥数(分片 1)|计数|总计||无维度|
|usedmemory1|已用内存量(分片 1)|字节|最大值||无维度|
|usedmemoryRss1|已用内存 RSS (分片 1)|字节|最大值||无维度|
|serverLoad1|服务器负载(分片 1)|百分比|最大值||无维度|
|cacheWrite1|缓存写入量(分片 1)|每秒字节数|最大值||无维度|
|cacheRead1|缓存读取量(分片 1)|每秒字节数|最大值||无维度|
|percentProcessorTime1|CPU (分片 1)|百分比|最大值||无维度|
|connectedclients2|连接的客户端数(分片 2)|计数|最大值||无维度|
|totalcommandsprocessed2|总操作数(分片 2)|计数|总计||无维度|
|cachehits2|缓存命中数(分片 2)|计数|总计||无维度|
|cachemisses2|缓存未命中数(分片 2)|计数|总计||无维度|
|getcommands2|Get 数(分片 2)|计数|总计||无维度|
|setcommands2|Set 数(分片 2)|计数|总计||无维度|
|evictedkeys2|逐出的密钥数(分片 2)|计数|总计||无维度|
|totalkeys2|总密钥数（分片 2）|计数|最大值||无维度|
|expiredkeys2|过期的密钥数(分片 2)|计数|总计||无维度|
|usedmemory2|已用内存量(分片 2)|字节|最大值||无维度|
|usedmemoryRss2|已用内存 RSS (分片 2)|字节|最大值||无维度|
|serverLoad2|服务器负载(分片 2)|百分比|最大值||无维度|
|cacheWrite2|缓存写入量(分片 2)|每秒字节数|最大值||无维度|
|cacheRead2|缓存读取量(分片 2)|每秒字节数|最大值||无维度|
|percentProcessorTime2|CPU (分片 2)|百分比|最大值||无维度|
|connectedclients3|连接的客户端数(分片 3)|计数|最大值||无维度|
|totalcommandsprocessed3|总操作数(分片 3)|计数|总计||无维度|
|cachehits3|缓存命中数(分片 3)|计数|总计||无维度|
|cachemisses3|缓存未命中数(分片 3)|计数|总计||无维度|
|getcommands3|Get 数(分片 3)|计数|总计||无维度|
|setcommands3|Set 数(分片 3)|计数|总计||无维度|
|evictedkeys3|逐出的密钥数(分片 3)|计数|总计||无维度|
|totalkeys3|总密钥数（分片 3）|计数|最大值||无维度|
|expiredkeys3|过期的密钥数(分片 3)|计数|总计||无维度|
|usedmemory3|已用内存量(分片 3)|字节|最大值||无维度|
|usedmemoryRss3|已用内存 RSS (分片 3)|字节|最大值||无维度|
|serverLoad3|服务器负载(分片 3)|百分比|最大值||无维度|
|cacheWrite3|缓存写入量(分片 3)|每秒字节数|最大值||无维度|
|cacheRead3|缓存读取量(分片 3)|每秒字节数|最大值||无维度|
|percentProcessorTime3|CPU (分片 3)|百分比|最大值||无维度|
|connectedclients4|连接的客户端数(分片 4)|计数|最大值||无维度|
|totalcommandsprocessed4|总操作数(分片 4)|计数|总计||无维度|
|cachehits4|缓存命中数(分片 4)|计数|总计||无维度|
|cachemisses4|缓存未命中数(分片 4)|计数|总计||无维度|
|getcommands4|Get 数(分片 4)|计数|总计||无维度|
|setcommands4|Set 数(分片 4)|计数|总计||无维度|
|evictedkeys4|逐出的密钥数(分片 4)|计数|总计||无维度|
|totalkeys4|总密钥数（分片 4）|计数|最大值||无维度|
|expiredkeys4|过期的密钥数(分片 4)|计数|总计||无维度|
|usedmemory4|已用内存量(分片 4)|字节|最大值||无维度|
|usedmemoryRss4|已用内存 RSS (分片 4)|字节|最大值||无维度|
|serverLoad4|服务器负载(分片 4)|百分比|最大值||无维度|
|cacheWrite4|缓存写入量(分片 4)|每秒字节数|最大值||无维度|
|cacheRead4|缓存读取量(分片 4)|每秒字节数|最大值||无维度|
|percentProcessorTime4|CPU (分片 4)|百分比|最大值||无维度|
|connectedclients5|连接的客户端数(分片 5)|计数|最大值||无维度|
|totalcommandsprocessed5|总操作数(分片 5)|计数|总计||无维度|
|cachehits5|缓存命中数(分片 5)|计数|总计||无维度|
|cachemisses5|缓存未命中数(分片 5)|计数|总计||无维度|
|getcommands5|Get 数(分片 5)|计数|总计||无维度|
|setcommands5|Set 数(分片 5)|计数|总计||无维度|
|evictedkeys5|逐出的密钥数(分片 5)|计数|总计||无维度|
|totalkeys5|总密钥数（分片 5）|计数|最大值||无维度|
|expiredkeys5|过期的密钥数(分片 5)|计数|总计||无维度|
|usedmemory5|已用内存量(分片 5)|字节|最大值||无维度|
|usedmemoryRss5|已用内存 RSS (分片 5)|字节|最大值||无维度|
|serverLoad5|服务器负载(分片 5)|百分比|最大值||无维度|
|cacheWrite5|缓存写入量(分片 5)|每秒字节数|最大值||无维度|
|cacheRead5|缓存读取量(分片 5)|每秒字节数|最大值||无维度|
|percentProcessorTime5|CPU (分片 5)|百分比|最大值||无维度|
|connectedclients6|连接的客户端数(分片 6)|计数|最大值||无维度|
|totalcommandsprocessed6|总操作数(分片 6)|计数|总计||无维度|
|cachehits6|缓存命中数(分片 6)|计数|总计||无维度|
|cachemisses6|缓存未命中数(分片 6)|计数|总计||无维度|
|getcommands6|Get 数(分片 6)|计数|总计||无维度|
|setcommands6|Set 数(分片 6)|计数|总计||无维度|
|evictedkeys6|逐出的密钥数(分片 6)|计数|总计||无维度|
|totalkeys6|总密钥数（分片 6）|计数|最大值||无维度|
|expiredkeys6|过期的密钥数(分片 6)|计数|总计||无维度|
|usedmemory6|已用内存量(分片 6)|字节|最大值||无维度|
|usedmemoryRss6|已用内存 RSS (分片 6)|字节|最大值||无维度|
|serverLoad6|服务器负载(分片 6)|百分比|最大值||无维度|
|cacheWrite6|缓存写入量(分片 6)|每秒字节数|最大值||无维度|
|cacheRead6|缓存读取量(分片 6)|每秒字节数|最大值||无维度|
|percentProcessorTime6|CPU (分片 6)|百分比|最大值||无维度|
|connectedclients7|连接的客户端数(分片 7)|计数|最大值||无维度|
|totalcommandsprocessed7|总操作数(分片 7)|计数|总计||无维度|
|cachehits7|缓存命中数(分片 7)|计数|总计||无维度|
|cachemisses7|缓存未命中数(分片 7)|计数|总计||无维度|
|getcommands7|Get 数(分片 7)|计数|总计||无维度|
|setcommands7|Set 数(分片 7)|计数|总计||无维度|
|evictedkeys7|逐出的密钥数(分片 7)|计数|总计||无维度|
|totalkeys7|总密钥数（分片 7）|计数|最大值||无维度|
|expiredkeys7|过期的密钥数(分片 7)|计数|总计||无维度|
|usedmemory7|已用内存量(分片 7)|字节|最大值||无维度|
|usedmemoryRss7|已用内存 RSS (分片 7)|字节|最大值||无维度|
|serverLoad7|服务器负载(分片 7)|百分比|最大值||无维度|
|cacheWrite7|缓存写入量(分片 7)|每秒字节数|最大值||无维度|
|cacheRead7|缓存读取量(分片 7)|每秒字节数|最大值||无维度|
|percentProcessorTime7|CPU (分片 7)|百分比|最大值||无维度|
|connectedclients8|连接的客户端数(分片 8)|计数|最大值||无维度|
|totalcommandsprocessed8|总操作数(分片 8)|计数|总计||无维度|
|cachehits8|缓存命中数(分片 8)|计数|总计||无维度|
|cachemisses8|缓存未命中数(分片 8)|计数|总计||无维度|
|getcommands8|Get 数(分片 8)|计数|总计||无维度|
|setcommands8|Set 数(分片 8)|计数|总计||无维度|
|evictedkeys8|逐出的密钥数(分片 8)|计数|总计||无维度|
|totalkeys8|总密钥数（分片 8）|计数|最大值||无维度|
|expiredkeys8|过期的密钥数(分片 8)|计数|总计||无维度|
|usedmemory8|已用内存量(分片 8)|字节|最大值||无维度|
|usedmemoryRss8|已用内存 RSS (分片 8)|字节|最大值||无维度|
|serverLoad8|服务器负载(分片 8)|百分比|最大值||无维度|
|cacheWrite8|缓存写入量(分片 8)|每秒字节数|最大值||无维度|
|cacheRead8|缓存读取量(分片 8)|每秒字节数|最大值||无维度|
|percentProcessorTime8|CPU (分片 8)|百分比|最大值||无维度|
|connectedclients9|连接的客户端数(分片 9)|计数|最大值||无维度|
|totalcommandsprocessed9|总操作数(分片 9)|计数|总计||无维度|
|cachehits9|缓存命中数(分片 9)|计数|总计||无维度|
|cachemisses9|缓存未命中数(分片 9)|计数|总计||无维度|
|getcommands9|Get 数(分片 9)|计数|总计||无维度|
|setcommands9|Set 数(分片 9)|计数|总计||无维度|
|evictedkeys9|逐出的密钥数(分片 9)|计数|总计||无维度|
|totalkeys9|总密钥数（分片 9）|计数|最大值||无维度|
|expiredkeys9|过期的密钥数(分片 9)|计数|总计||无维度|
|usedmemory9|已用内存量(分片 9)|字节|最大值||无维度|
|usedmemoryRss9|已用内存 RSS (分片 9)|字节|最大值||无维度|
|serverLoad9|服务器负载(分片 9)|百分比|最大值||无维度|
|cacheWrite9|缓存写入量(分片 9)|每秒字节数|最大值||无维度|
|cacheRead9|缓存读取量(分片 9)|每秒字节数|最大值||无维度|
|percentProcessorTime9|CPU (分片 9)|百分比|最大值||无维度|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|无维度|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|无维度|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|无维度|
|Disk Read Bytes/Sec|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|无维度|
|Disk Write Bytes/Sec|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|无维度|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalCalls|总调用数|计数|总计|调用总数。|无维度|
|SuccessfulCalls|成功调用数|计数|总计|成功调用数。|无维度|
|TotalErrors|错误总数|计数|总计|引发错误响应（HTTP 响应代码 4xx 或 5xx）的调用总数。|无维度|
|BlockedCalls|阻止的调用数|计数|总计|超过速率或配额限制的调用数。|无维度|
|ServerErrors|服务器错误数|计数|总计|引发服务内部错误（HTTP 响应代码 5xx）的调用数。|无维度|
|ClientErrors|客户端错误数|计数|总计|引发客户端错误（HTTP 响应代码 4xx）的调用数。|无维度|
|DataIn|数据输入|字节|总计|传入数据的大小（字节）。|无维度|
|DataOut|数据输出|字节|总计|传出数据的大小（字节）。|无维度|
|延迟|延迟|毫秒|平均值|延迟（毫秒）。|无维度|
|CharactersTranslated|转换的字符|计数|总计|传入的文本请求中的字符总数。|无维度|
|SpeechSessionDuration|语音会话持续时间|秒|总计|语音会话的总持续时间（以秒计）。|无维度|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无维度|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无维度|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无维度|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节总数|无维度|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节总数|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无维度|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无维度|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无维度|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无维度|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节总数|无维度|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节总数|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无维度|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无维度|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无维度|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无维度|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节总数|无维度|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节总数|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无维度|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API 调用数|计数|总计||无维度|
|DCIMappingImportOperationSuccessfulLines|映射导入操作成功行数|计数|总计||无维度|
|DCIMappingImportOperationFailedLines|映射导入操作失败行数|计数|总计||无维度|
|DCIMappingImportOperationTotalLines|映射导入操作总行数|计数|总计||无维度|
|DCIMappingImportOperationRuntimeInSeconds|映射导入操作运行时（以秒为单位）|秒|总计||无维度|
|DCIOutboundProfileExportSucceeded|出站配置文件导出成功|计数|总计||无维度|
|DCIOutboundProfileExportFailed|出站配置文件导出失败|计数|总计||无维度|
|DCIOutboundProfileExportDuration|出站配置文件导出持续时间|秒|总计||无维度|
|DCIOutboundKpiExportSucceeded|出站 KPI 导出成功|计数|总计||无维度|
|DCIOutboundKpiExportFailed|出站 KPI 导出失败|计数|总计||无维度|
|DCIOutboundKpiExportDuration|出站 KPI 导出持续时间|秒|总计||无维度|
|DCIOutboundKpiExportStarted|出站 KPI 导出已启动|秒|总计||无维度|
|DCIOutboundKpiRecordCount|出站 KPI 记录计数|秒|总计||无维度|
|DCIOutboundProfileExportCount|出站配置文件导出计数|秒|总计||无维度|
|DCIOutboundInitialProfileExportFailed|出站初始配置文件导出失败|秒|总计||无维度|
|DCIOutboundInitialProfileExportSucceeded|出站初始配置文件导出成功|秒|总计||无维度|
|DCIOutboundInitialKpiExportFailed|出站初始 KPI 导出失败|秒|总计||无维度|
|DCIOutboundInitialKpiExportSucceeded|出站初始 KPI 导出成功|秒|总计||无维度|
|DCIOutboundInitialProfileExportDurationInSeconds|出站初始配置文件导出持续时间（以秒为单位）|秒|总计||无维度|
|AdlaJobForStandardKpiFailed|标准 KPI 的 Adla 作业失败时间（以秒为单位）|秒|总计||无维度|
|AdlaJobForStandardKpiTimeOut|标准 KPI 的 Adla 作业超时时间（以秒为单位）|秒|总计||无维度|
|AdlaJobForStandardKpiCompleted|标准 KPI 的 Adla 作业完成时间（以秒为单位）|秒|总计||无维度|
|ImportASAValuesFailed|导入 ASA 值失败计数|计数|总计||无维度|
|ImportASAValuesSucceeded|导入 ASA 值成功计数|计数|总计||无维度|
|DCIProfilesCount|配置文件实例计数|计数|最后一个||无维度|
|DCIInteractionsPerMonthCount|每月计数的交互|计数|最后一个||无维度|
|DCIKpisCount|KPI 计数|计数|最后一个||无维度|
|DCISegmentsCount|段计数|计数|最后一个||无维度|
|DCIPredictiveMatchPoliciesCount|预测匹配计数|计数|最后一个||无维度|
|DCIPredictionsCount|预测计数|计数|最后一个||无维度|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|JobEndedSuccess|成功作业数|计数|总计|成功作业计数。|无维度|
|JobEndedFailure|失败作业数|计数|总计|失败作业计数。|无维度|
|JobEndedCancelled|取消的作业数|计数|总计|取消的作业计数。|无维度|
|JobAUEndedSuccess|成功 AU 时间|秒|总计|成功作业的总 AU 时间。|无维度|
|JobAUEndedFailure|失败的 AU 时间|秒|总计|失败作业的总 AU 时间。|无维度|
|JobAUEndedCancelled|已取消的 AU 时间|秒|总计|取消的作业的总 AU 时间。|无维度|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalStorage|总存储|字节|最大值|帐户中存储的数据总量。|无维度|
|DataWritten|写入的数据量|字节|总计|写入帐户的数据总量。|无维度|
|DataRead|读取的数据量|字节|总计|从帐户中读取的数据总量。|无维度|
|WriteRequests|写入请求数|计数|总计|帐户的数据写入请求计数。|无维度|
|ReadRequests|读取请求数|计数|总计|帐户的数据读取请求计数。|无维度|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|compute_limit|计算单位限制|计数|平均值|计算单位限制|无维度|
|compute_consumption_percent|计算单位百分比|百分比|平均值|计算单位百分比|无维度|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无维度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无维度|
|storage_percent|存储百分比|百分比|平均值|存储百分比|无维度|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无维度|
|storage_limit|存储限制|字节|平均值|存储限制|无维度|
|active_connections|活动连接总数|计数|平均值|活动连接总数|无维度|
|connections_failed|失败连接总数|计数|平均值|失败连接总数|无维度|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|compute_limit|计算单位限制|计数|平均值|计算单位限制|无维度|
|compute_consumption_percent|计算单位百分比|百分比|平均值|计算单位百分比|无维度|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无维度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无维度|
|storage_percent|存储百分比|百分比|平均值|存储百分比|无维度|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无维度|
|storage_limit|存储限制|字节|平均值|存储限制|无维度|
|active_connections|活动连接总数|计数|平均值|活动连接总数|无维度|
|connections_failed|失败连接总数|计数|平均值|失败连接总数|无维度|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遥测消息发送尝试次数|计数|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|d2c.telemetry.ingress.success|已发送的遥测消息数|计数|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|c2d.commands.egress.complete.success|完成的命令数|计数|总计|设备已成功完成的云到设备命令的数目|无维度|
|c2d.commands.egress.abandon.success|放弃的命令数|计数|总计|设备放弃的云到设备命令的数目|无维度|
|c2d.commands.egress.reject.success|拒绝的命令数|计数|总计|设备拒绝的云到设备命令的数目|无维度|
|devices.totalDevices|设备总数|计数|总计|已注册到 IoT 中心的设备数目|无维度|
|devices.connectedDevices.allProtocol|已连接的设备|计数|总计|已连接到 IoT 中心的设备数目|无维度|
|d2c.telemetry.egress.success|发送的遥测消息数|计数|总计|已成功将消息写入到终结点的次数（总数）|无维度|
|d2c.telemetry.egress.dropped|丢弃的消息数|计数|总计|由于传递终结点已死而丢弃的消息数|无维度|
|d2c.telemetry.egress.orphaned|孤立的消息数|计数|总计|不匹配任何路由（包括回退路由）的消息计数|无维度|
|d2c.telemetry.egress.invalid|无效的消息数|计数|总计|由于与终结点不兼容而未传递的消息计数|无维度|
|d2c.telemetry.egress.fallback|符合回退条件的消息数|计数|总计|已写入到回退终结点的消息数|无维度|
|d2c.endpoints.egress.eventHubs|已传递到事件中心终结点的消息数|计数|总计|已成功将消息写入到事件中心终结点的次数|无维度|
|d2c.endpoints.latency.eventHubs|事件中心终结点的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入事件中心终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.egress.serviceBusQueues|已传递到服务总线队列终结点的消息数|计数|总计|已成功将消息写入到服务总线队列终结点的次数|无维度|
|d2c.endpoints.latency.serviceBusQueues|服务总线队列终结点的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入服务总线队列终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.egress.serviceBusTopics|已传递到服务总线主题终结点的消息数|计数|总计|已成功将消息写入到服务总线主题终结点的次数|无维度|
|d2c.endpoints.latency.serviceBusTopics|服务总线主题终结点的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入服务总线主题终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.egress.builtIn.events|已传递到内置终结点的消息数（消息/事件）|计数|总计|已成功将消息写入到内置终结点的次数（消息/事件）|无维度|
|d2c.endpoints.latency.builtIn.events|内置终结点的消息延迟（消息/事件）|毫秒|平均值|消息进入 IoT 中心与进入内置终结点（消息/事件）之间的平均延迟（毫秒） |无维度|
|d2c.endpoints.egress.storage|传递到存储终结点的消息数|计数|总计|成功将消息写入存储终结点的次数|无维度|
|d2c.endpoints.latency.storage|存储终结点的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入存储终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.egress.storage.bytes|写入存储的数据量|字节|总计|写入存储终结点的数据量，以字节为单位|无维度|
|d2c.endpoints.egress.storage.blobs|写入存储的 Blob 数|计数|总计|写入存储终结点的 Blob 数|无维度|
|d2c.twin.read.success|设备的成功克隆读取数|计数|总计|由设备发起的所有成功的克隆读取的计数。|无维度|
|d2c.twin.read.failure|设备的失败克隆读取数|计数|总计|由设备发起的所有失败的克隆读取的计数。|无维度|
|d2c.twin.read.size|设备的克隆读取的响应大小|字节|平均值|由设备发起的所有成功的克隆读取的平均大小、最小大小和最大大小。|无维度|
|d2c.twin.update.success|设备的成功克隆更新数|计数|总计|由设备发起的所有成功的克隆更新的计数。|无维度|
|d2c.twin.update.failure|设备的失败克隆更新数|计数|总计|由设备发起的所有失败的克隆更新的计数。|无维度|
|d2c.twin.update.size|设备的克隆更新的大小|字节|平均值|由设备发起的所有成功的克隆更新的平均大小、最小大小和最大大小。|无维度|
|c2d.methods.success|成功的直接方法调用数|计数|总计|所有成功的直接方法调用的计数。|无维度|
|c2d.methods.failure|失败的直接方法调用数|计数|总计|所有失败的直接方法调用的计数。|无维度|
|c2d.methods.requestSize|直接方法调用的请求大小|字节|平均值|所有成功的直接方法请求的平均大小、最小大小和最大大小。|无维度|
|c2d.methods.responseSize|直接方法调用的响应大小|字节|平均值|所有成功的直接方法响应的平均大小、最小大小和最大大小。|无维度|
|c2d.twin.read.success|后端的成功克隆读取数|计数|总计|由后端发起的所有成功的克隆读取的计数。|无维度|
|c2d.twin.read.failure|后端的失败克隆读取数|计数|总计|由后端发起的所有失败的克隆读取的计数。|无维度|
|c2d.twin.read.size|后端的克隆读取的响应大小|字节|平均值|由后端发起的所有成功的克隆读取的平均大小、最小大小和最大大小。|无维度|
|c2d.twin.update.success|后端的成功克隆更新数|计数|总计|由后端发起的所有成功的克隆更新的计数。|无维度|
|c2d.twin.update.failure|后端的失败克隆更新数|计数|总计|由后端发起的所有失败的克隆更新的计数。|无维度|
|c2d.twin.update.size|后端的克隆更新的大小|字节|平均值|由后端发起的所有成功的克隆更新的平均大小、最小大小和最大大小。|无维度|
|twinQueries.success|成功的克隆查询|计数|总计|所有成功的克隆查询的计数。|无维度|
|twinQueries.failure|失败的克隆查询|计数|总计|所有失败的克隆查询的计数。|无维度|
|twinQueries.resultSize|克隆查询结果大小|字节|平均值|所有成功的克隆查询的结果大小的平均值、最小值和最大值。|无维度|
|jobs.createTwinUpdateJob.success|克隆更新作业创建成功数|计数|总计|克隆更新作业创建成功的所有次数。|无维度|
|jobs.createTwinUpdateJob.failure|克隆更新作业创建失败数|计数|总计|克隆更新作业创建失败的所有次数。|无维度|
|jobs.createDirectMethodJob.success|方法调用作业的创建成功数|计数|总计|直接方法调用作业创建成功的所有次数。|无维度|
|jobs.createDirectMethodJob.failure|方法调用作业的创建失败数|计数|总计|直接方法调用作业创建失败的所有次数。|无维度|
|jobs.listJobs.success|对列出作业的成功调用数|计数|总计|对列出作业的所有成功调用的计数。|无维度|
|jobs.listJobs.failure|对列出作业的失败调用数|计数|总计|对列出作业的所有失败调用的计数。|无维度|
|jobs.cancelJob.success|成功的作业取消数|计数|总计|用来取消作业的调用成功的次数。|无维度|
|jobs.cancelJob.failure|失败的作业取消数|计数|总计|用来取消作业的调用失败的次数。|无维度|
|jobs.queryJobs.success|成功的作业查询数|计数|总计|对查询作业的所有成功调用的计数。|无维度|
|jobs.queryJobs.failure|失败的作业查询数|计数|总计|对查询作业的所有失败调用的计数。|无维度|
|jobs.completed|已完成的作业|计数|总计|所有已完成的作业的计数。|无维度|
|jobs.failed|失败的作业数|计数|总计|所有失败的作业的计数。|无维度|
|d2c.telemetry.ingress.sendThrottle|限制错误数|计数|总计|由于设备吞吐量限制而导致的限制错误数|无维度|
|dailyMessageQuotaUsed|已使用的消息总数|计数|平均值|今天使用的消息总数。 这是累积值，每日 00:00 UTC 重置为零。|无维度|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RegistrationAttempts|注册尝试次数|计数|总计|已尝试的设备注册次数|ProvisioningServiceName、IotHubName、Status|
|DeviceAssignments|已分配设备|计数|总计|已分配给 IoT 中心的设备数|ProvisioningServiceName、IotHubName|
|AttestationAttempts|证明尝试次数|计数|总计|已尝试的设备证明次数|ProvisioningServiceName、Status、Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalRequests|请求总数|计数|计数|已发出的请求数|DatabaseAccount、CollectionName、DatabaseName、Region、StatusCode|
|MongoRequests|Mongo 请求|计数|计数|已发出的 Mongo 请求数|DatabaseAccount、CollectionName、DatabaseName、Region、ErrorCode、CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|INREQS|传入的发送请求数|计数|总计|命名空间的传入发送请求总数|无维度|
|SUCCREQ|成功的请求数|计数|总计|命名空间的成功请求总数|无维度|
|FAILREQ|失败的请求数|计数|总计|命名空间的失败请求总数|无维度|
|SVRBSY|服务器繁忙错误数|计数|总计|命名空间的服务器繁忙错误总数|无维度|
|INTERR|内部服务器错误数|计数|总计|命名空间的内部服务器错误总数|无维度|
|MISCERR|其他错误数|计数|总计|命名空间的失败请求总数|无维度|
|INMSGS|传入消息（已弃用）|计数|总计|命名空间的传入消息总数。 此指标已弃用。 请改用传入消息指标|无维度|
|EHINMSGS|传入消息数|计数|总计|命名空间的传入消息总数|无维度|
|OUTMSGS|传出消息（已弃用）|计数|总计|命名空间的传出消息总数。 此指标已弃用。 请改用传出消息指标|无维度|
|EHOUTMSGS|传出消息数|计数|总计|命名空间的传出消息总数|无维度|
|EHINMBS|传入字节（已弃用）|字节|总计|命名空间的事件中心传入消息吞吐量。 此指标已弃用。 请改用传入字节指标|无维度|
|EHINBYTES|传入字节数|字节|总计|命名空间的事件中心传入消息吞吐量|无维度|
|EHOUTMBS|传出字节（已弃用）|字节|总计|命名空间的事件中心传出消息吞吐量。 此指标已弃用。 请改用传出字节指标|无维度|
|EHOUTBYTES|传出字节数|字节|总计|命名空间的事件中心传出消息吞吐量|无维度|
|EHABL|存档积压工作消息数|计数|总计|命名空间积压工作中的事件中心存档消息数|无维度|
|EHAMSGS|存档消息数|计数|总计|命名空间中的事件中心存档消息数|无维度|
|EHAMBS|存档消息吞吐量|字节|总计|命名空间中的事件中心存档消息吞吐量|无维度|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ObservedMetricValue|观察到的指标值|计数|平均值|执行自动缩放时计算的值|MetricTriggerSource|
|MetricThreshold|指标阈值|计数|平均值|自动缩放运行时已配置的自动缩放阈值。|MetricTriggerRule|
|ObservedCapacity|观察到的容量|计数|平均值|自动缩放执行时报告的容量。|无维度|
|ScaleActionsInitiated|启动的缩放操作|计数|总计|缩放操作的方向。|ScaleDirection|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RunsStarted|已启动的运行数|计数|总计|已启动的工作流运行数目。|无维度|
|RunsCompleted|已完成的运行数|计数|总计|已完成的工作流运行数目。|无维度|
|RunsSucceeded|成功的运行数|计数|总计|成功的工作流运行数目。|无维度|
|RunsFailed|失败的运行数|计数|总计|失败的工作流运行数目。|无维度|
|RunsCancelled|取消的运行数|计数|总计|已取消的工作流运行数目。|无维度|
|RunLatency|运行延迟|秒|平均值|已完成的工作流运行的延迟。|无维度|
|RunSuccessLatency|运行成功延迟|秒|平均值|已成功的工作流运行的延迟。|无维度|
|RunThrottledEvents|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无维度|
|RunFailurePercentage|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无维度|
|ActionsStarted|启动的操作数 |计数|总计|已启动的工作流操作数目。|无维度|
|ActionsCompleted|完成的操作数 |计数|总计|已完成的工作流操作数目。|无维度|
|ActionsSucceeded|成功的操作数 |计数|总计|成功的工作流操作数目。|无维度|
|ActionsFailed|失败的操作数|计数|总计|失败的工作流操作数目。|无维度|
|ActionsSkipped|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无维度|
|ActionLatency|操作延迟 |秒|平均值|已完成的工作流操作的延迟。|无维度|
|ActionSuccessLatency|操作成功延迟 |秒|平均值|已成功的工作流操作的延迟。|无维度|
|ActionThrottledEvents|操作限制事件数|计数|总计|工作流操作限制事件数目。|无维度|
|TriggersStarted|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无维度|
|TriggersCompleted|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无维度|
|TriggersSucceeded|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无维度|
|TriggersFailed|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无维度|
|TriggersSkipped|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无维度|
|TriggersFired|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无维度|
|TriggerLatency|触发器延迟 |秒|平均值|已完成的工作流触发器的延迟。|无维度|
|TriggerFireLatency|触发器激发延迟 |秒|平均值|已激发的工作流触发器的延迟。|无维度|
|TriggerSuccessLatency|触发器成功延迟 |秒|平均值|已成功的工作流触发器的延迟。|无维度|
|TriggerThrottledEvents|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无维度|
|BillableActionExecutions|计费的操作执行数|计数|总计|计费的工作流操作执行数目。|无维度|
|BillableTriggerExecutions|计费的触发器执行数|计数|总计|计费的工作流触发器执行数目。|无维度|
|TotalBillableExecutions|计费的执行总数|计数|总计|计费的工作流执行数目。|无维度|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|VipAvailability|VIP 可用性|计数|平均值|基于探测结果的 VIP 终结点的可用性|VipAddress、VipPort|
|DipAvailability|DIP 可用性|计数|平均值|基于探测结果的 DIP 终结点的可用性|ProtocolType、DipPort、VipAddress、VipPort、DipAddress|
|ByteCount|字节计数|计数|总计|时间段内传输的字节总数|VipAddress、VipPort、Direction|
|PacketCount|数据包计数|计数|总计|时间段内传输的数据包总数|VipAddress、VipPort、Direction|
|SYNCount|SYN 计数|计数|总计|时间段内传输的 SYN 数据包总数|VipAddress、VipPort、Direction|
|SnatConnectionCount|SNAT 连接计数|计数|总计|时间段内创建的新 SNAT 连接的总数|VipAddress、DipAddress、ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PacketsInDDoS|入站数据包 DDoS|每秒计数|平均值|入站数据包 DDoS|无维度|
|PacketsDroppedDDoS|丢弃的入站数据包 DDoS|每秒计数|平均值|丢弃的入站数据包 DDoS|无维度|
|PacketsForwardedDDoS|转发的入站数据包 DDoS|每秒计数|平均值|转发的入站数据包 DDoS|无维度|
|TCPPacketsInDDoS|入站 TCP 数据包 DDoS|每秒计数|平均值|入站 TCP 数据包 DDoS|无维度|
|TCPPacketsDroppedDDoS|丢弃的入站 TCP 数据包 DDoS|每秒计数|平均值|丢弃的入站 TCP 数据包 DDoS|无维度|
|TCPPacketsForwardedDDoS|转发的入站 TCP 数据包 DDoS|每秒计数|平均值|转发的入站 TCP 数据包 DDoS|无维度|
|UDPPacketsInDDoS|入站 UDP 数据包 DDoS|每秒计数|平均值|入站 UDP 数据包 DDoS|无维度|
|UDPPacketsDroppedDDoS|丢弃的入站 UDP 数据包 DDoS|每秒计数|平均值|丢弃的入站 UDP 数据包 DDoS|无维度|
|UDPPacketsForwardedDDoS|转发的入站 UDP 数据包 DDoS|每秒计数|平均值|转发的入站 UDP 数据包 DDoS|无维度|
|BytesInDDoS|入站字节 DDoS|每秒字节数|平均值|入站字节 DDoS|无维度|
|BytesDroppedDDoS|丢弃的入站字节 DDoS|每秒字节数|平均值|丢弃的入站字节 DDoS|无维度|
|BytesForwardedDDoS|转发的入站字节 DDoS|每秒字节数|平均值|转发的入站字节 DDoS|无维度|
|TCPBytesInDDoS|入站 TCP 字节 DDoS|每秒字节数|平均值|入站 TCP 字节 DDoS|无维度|
|TCPBytesDroppedDDoS|丢弃的入站 TCP 字节 DDoS|每秒字节数|平均值|丢弃的入站 TCP 字节 DDoS|无维度|
|TCPBytesForwardedDDoS|转发的入站 TCP 字节 DDoS|每秒字节数|平均值|转发的入站 TCP 字节 DDoS|无维度|
|UDPBytesInDDoS|入站 UDP 字节 DDoS|每秒字节数|平均值|入站 UDP 字节 DDoS|无维度|
|UDPBytesDroppedDDoS|丢弃的入站 UDP 字节 DDoS|每秒字节数|平均值|丢弃的入站 UDP 字节 DDoS|无维度|
|UDPBytesForwardedDDoS|转发的入站 UDP 字节 DDoS|每秒字节数|平均值|转发的入站 UDP 字节 DDoS|无维度|
|IfUnderDDoSAttack|是否遭到 DDoS 攻击|计数|平均值|是否遭到 DDoS 攻击|无维度|
|DDoSTriggerTCPPackets|触发 DDoS 缓解的入站 TCP 数据包|每秒计数|平均值|触发 DDoS 缓解的入站 TCP 数据包|无维度|
|DDoSTriggerUDPPackets|触发 DDoS 缓解的入站 UDP 数据包|每秒计数|平均值|触发 DDoS 缓解的入站 UDP 数据包|无维度|
|VipAvailability|可用性|计数|平均值|时间段内 IPAddress 的平均可用性|端口|
|ByteCount|字节计数|计数|总计|时间段内传输的字节总数|Port、Direction|
|PacketCount|数据包计数|计数|总计|时间段内传输的数据包总数|Port、Direction|
|SynCount|SYN 计数|计数|总计|时间段内传输的 SYN 数据包总数|Port、Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|吞吐量|吞吐量|每秒字节数|平均值|应用程序网关每秒提供的字节数|无维度|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|隧道带宽|每秒字节数|平均值|隧道带宽平均值（以字节/秒为单位）|ConnectionName、RemoteIP|
|TunnelPeakBandwidth|隧道峰值带宽|每秒字节数|平均值|隧道带宽峰值（以字节/秒为单位）|ConnectionName、RemoteIP|
|TunnelEgressBytes|隧道流出字节|字节|平均值|5 分钟时间间隔内隧道的传出字节|ConnectionName、RemoteIP|
|TunnelIngressBytes|隧道流入字节|字节|平均值|5 分钟时间间隔内隧道的传入字节|ConnectionName、RemoteIP|
|TunnelEgressPackets|隧道流出数据包|计数|平均值|5 分钟时间间隔内隧道的传出数据包计数|ConnectionName、RemoteIP|
|TunnelIngressPackets|隧道流入数据包|计数|平均值|5 分钟时间间隔内隧道的传入数据包计数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|隧道流出 TS 不匹配数据包丢弃|计数|平均值|5 分钟时间间隔内来自隧道的不匹配流量选择器的传出数据包丢弃|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|隧道流入 TS 不匹配数据包丢弃|计数|平均值|5 分钟时间间隔内来自隧道的不匹配流量选择器的传入数据包丢弃|ConnectionName、RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BytesIn|BytesIn|计数|总计|传入 Azure 的字节数|无维度|
|BytesOut|BytesOut|计数|总计|传出 Azure 的字节数|无维度|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无维度|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无维度|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QpsByEndpoint|按终结点返回的查询|计数|总计|给定时间范围内返回流量管理器终结点的次数|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|按终结点显示的终结点状态|计数|最大值|如果终结点的探测状态为“已启用”，则值为 1；否则，值为 0。|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|registration.all|注册操作|计数|总计|所有成功的注册操作（创建、更新、查询和删除）的计数。 |无维度|
|registration.create|注册创建操作数|计数|总计|所有成功的注册创建操作的计数。|无维度|
|registration.update|注册更新操作数|计数|总计|所有成功的注册更新操作的计数。|无维度|
|registration.get|注册读取操作数|计数|总计|所有成功的注册查询操作的计数。|无维度|
|registration.delete|注册删除操作数|计数|总计|所有成功的注册删除操作的计数。|无维度|
|incoming|传入消息数|计数|总计|所有成功的发送 API 调用的计数。 |无维度|
|incoming.scheduled|已发送的已安排推送通知数|计数|总计|已取消的已安排推送通知数|无维度|
|incoming.scheduled.cancel|已取消的已安排推送通知数|计数|总计|已取消的已安排推送通知数|无维度|
|scheduled.pending|挂起的已安排通知数|计数|总计|挂起的已安排通知数|无维度|
|installation.all|安装管理操作数|计数|总计|安装管理操作数|无维度|
|installation.get|获取安装操作数|计数|总计|获取安装操作数|无维度|
|installation.upsert|创建或更新安装操作数|计数|总计|创建或更新安装操作数|无维度|
|installation.patch|修补安装操作数|计数|总计|修补安装操作数|无维度|
|installation.delete|删除安装操作数|计数|总计|删除安装操作数|无维度|
|outgoing.allpns.success|成功的通知数|计数|总计|所有成功的通知的计数。|无维度|
|outgoing.allpns.invalidpayload|有效负载错误数|计数|总计|因为 PNS 返回了“有效负载不正确”错误而失败的推送的计数。|无维度|
|outgoing.allpns.pnserror|外部通知系统错误数|计数|总计|因为与 PNS 通信时遇到问题（不包括身份验证问题）而失败的推送的计数。|无维度|
|outgoing.allpns.channelerror|通道错误数|计数|总计|因为通道无效、没有与正确的应用相关联、受限制或已过期而失败的推送的计数。|无维度|
|outgoing.allpns.badorexpiredchannel|坏通道或已过期通道错误数|计数|总计|因为注册中的通道/令牌/registrationId 已过期或无效而失败的推送的计数。|无维度|
|outgoing.wns.success|WNS 成功的通知数|计数|总计|所有成功的通知的计数。|无维度|
|outgoing.wns.invalidcredentials|WNS 授权错误数（凭据无效）|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。 （Windows Live 不能识别凭据）。|无维度|
|outgoing.wns.badchannel|WNS 坏通道错误|计数|总计|因为注册中的 ChannelURI 不可识别（WNS 状态：404 找不到）而失败的推送的计数。|无维度|
|outgoing.wns.expiredchannel|WNS 已过期通道错误|计数|总计|因为 ChannelURI 已过期（WNS 状态：410 不存在）而失败的推送的计数。|无维度|
|outgoing.wns.throttled|WNS 受限的通知数|计数|总计|因为 WNS 限制了此应用（WNS 状态：406 不可接受）而失败的推送的计数。|无维度|
|outgoing.wns.tokenproviderunreachable|WNS 授权错误数（无法访问）|计数|总计|无法访问 Windows Live。|无维度|
|outgoing.wns.invalidtoken|WNS 授权错误数（令牌无效）|计数|总计|提供给 WNS 的令牌无效（WNS 状态：401 未经授权）。|无维度|
|outgoing.wns.wrongtoken|WNS 授权错误数（令牌错误）|计数|总计|提供给 WNS 的令牌有效，但它是用于另一应用程序的（WNS 状态：403 禁止访问）。 如果注册中的 ChannelURI 与另一应用相关联，则可能会发生此情况。 请检查客户端应用是否与其凭据位于通知中心内的同一应用相关联。|无维度|
|outgoing.wns.invalidnotificationformat|WNS 无效的通知格式|计数|总计|通知格式无效（WNS 状态：400）。 请注意，WNS 并不会拒绝所有无效的有效负载。|无维度|
|outgoing.wns.invalidnotificationsize|WNS 无效通知大小错误|计数|总计|通知有效负载太大（WNS 状态：413）。|无维度|
|outgoing.wns.channelthrottled|WNS 通道受限|计数|总计|通知因为注册中的 ChannelURI 受限而被丢弃（WNS 响应标头：X-WNS-NotificationStatus:channelThrottled）。|无维度|
|outgoing.wns.channeldisconnected|WNS 通道断开连接|计数|总计|通知因为注册中的 ChannelURI 受限而被丢弃（WNS 响应标头：X-WNS-DeviceConnectionStatus: disconnected）。|无维度|
|outgoing.wns.dropped|WNS 丢弃的通知数|计数|总计|通知因为注册中的 ChannelURI 受限而被丢弃（X-WNS-NotificationStatus 为 dropped，但 X-WNS-DeviceConnectionStatus 不是 disconnected）。|无维度|
|outgoing.wns.pnserror|WNS 错误数|计数|总计|与 WNS 通信时发生错误，因而未传递通知。|无维度|
|outgoing.wns.authenticationerror|WNS 身份验证错误数|计数|总计|与 Windows Live 通信时因凭据无效或令牌错误而发生错误，因为未传递通知。|无维度|
|outgoing.apns.success|APNS 成功的通知数|计数|总计|所有成功的通知的计数。|无维度|
|outgoing.apns.invalidcredentials|APNS 授权错误数|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.apns.badchannel|APNS 坏通道错误|计数|总计|因令牌无效而失败的推送的计数（APNS 状态代码：8）。|无维度|
|outgoing.apns.expiredchannel|APNS 已过期通道错误|计数|总计|由 APNS 反馈通道致其无效的令牌的计数。|无维度|
|outgoing.apns.invalidnotificationsize|APNS 无效通知大小错误|计数|总计|因有效负载太大而失败的推送的计数（APNS 状态代码：7）。|无维度|
|outgoing.apns.pnserror|APNS 错误数|计数|总计|因为与 APNS 通信时发生错误而失败的推送的计数。|无维度|
|outgoing.gcm.success|GCM 成功的通知数|计数|总计|所有成功的通知的计数。|无维度|
|outgoing.gcm.invalidcredentials|GCM 授权错误数（凭据无效）|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.gcm.badchannel|GCM 坏通道错误|计数|总计|因为注册中的 registrationId 不可识别而失败的推送的计数（GCM 结果：无效的注册）。|无维度|
|outgoing.gcm.expiredchannel|GCM 已过期通道错误|计数|总计|因为注册中的 registrationId 已过期而失败的推送的计数（GCM 结果：NotRegistered）。|无维度|
|outgoing.gcm.throttled|GCM 受限的通知数|计数|总计|因为 GCM 限制了此应用而失败的推送的计数（GCM 状态代码：501-599 或结果：不可用）。|无维度|
|outgoing.gcm.invalidnotificationformat|GCM 无效的通知格式|计数|总计|因为有效负载的格式不正确而失败的推送的计数（GCM 结果：InvalidDataKey 或 InvalidTtl）。|无维度|
|outgoing.gcm.invalidnotificationsize|GCM 无效通知大小错误|计数|总计|因有效负载太大而失败的推送的计数（GCM 结果：MessageTooBig）。|无维度|
|outgoing.gcm.wrongchannel|GCM 通道不正确错误|计数|总计|因为注册中的 registrationId 没有关联到当前应用而失败的推送的计数（GCM 结果：InvalidPackageName）。|无维度|
|outgoing.gcm.pnserror|GCM 错误数|计数|总计|因为与 GCM 通信时发生错误而失败的推送的计数。|无维度|
|outgoing.gcm.authenticationerror|GCM 身份验证错误数|计数|总计|因为 PNS 未接受所提供的凭据、凭据被阻止或者未在应用中正确配置 SenderId 而失败的推送的计数（GCM 结果：MismatchedSenderId）。|无维度|
|outgoing.mpns.success|MPNS 成功的通知数|计数|总计|所有成功的通知的计数。|无维度|
|outgoing.mpns.invalidcredentials|MPNS 无效的凭据|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.mpns.badchannel|MPNS 坏通道错误|计数|总计|因为注册中的 ChannelURI 不可识别（MPNS 状态：404 找不到）而失败的推送的计数。|无维度|
|outgoing.mpns.throttled|MPNS 受限的通知数|计数|总计|因为 MPNS 限制了此应用（MPNS 状态：406 不可接受）而失败的推送的计数。|无维度|
|outgoing.mpns.invalidnotificationformat|MPNS 无效的通知格式|计数|总计|因通知的有效负载太大而失败的推送的计数。|无维度|
|outgoing.mpns.channeldisconnected|MPNS 通道断开连接|计数|总计|因为注册中的 ChannelURI 断开连接（MPNS 状态：412 找不到）而失败的推送的计数。|无维度|
|outgoing.mpns.dropped|MPNS 丢弃的通知数|计数|总计|被 MPNS 丢弃的推送的计数（MPNS 响应标头：X-NotificationStatus：QueueFull 或 Suppressed）。|无维度|
|outgoing.mpns.pnserror|MPNS 错误数|计数|总计|因为与 MPNS 通信时发生错误而失败的推送的计数。|无维度|
|outgoing.mpns.authenticationerror|MPNS 身份验证错误数|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|notificationhub.pushes|所有传出通知|计数|总计|通知中心的所有传出通知|无维度|
|incoming.all.requests|所有传入请求数|计数|总计|通知中心的传入的请求数总计|无维度|
|incoming.all.failedrequests|所有传入的失败请求数|计数|总计|通知中心的传入的失败请求数总计|无维度|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SearchLatency|搜索延迟|秒|平均值|搜索服务的平均搜索延迟|无维度|
|SearchQueriesPerSecond|每秒搜索查询数|每秒计数|平均值|搜索服务的每秒搜索查询数|无维度|
|ThrottledSearchQueriesPercentage|限制的搜索查询百分比|百分比|平均值|为搜索服务限制的搜索查询百分比|无维度|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPUXNS|每个命名空间的 CPU 使用率|百分比|最大值|服务总线高级命名空间 CPU 使用率指标|无维度|
|WSXNS|每个命名空间的内存使用量|百分比|最大值|服务总线高级命名空间内存使用率指标|无维度|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无维度|
|log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比|无维度|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|无维度|
|storage|数据库总大小|字节|最大值|数据库总大小|无维度|
|connection_successful|成功的连接数|计数|总计|成功的连接数|无维度|
|connection_failed|失败的连接数|计数|总计|失败的连接数|无维度|
|blocked_by_firewall|被防火墙阻止|计数|总计|被防火墙阻止|无维度|
|deadlock|死锁数|计数|总计|死锁数|无维度|
|storage_percent|数据库大小百分比|百分比|最大值|数据库大小百分比|无维度|
|xtp_storage_percent|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比|无维度|
|workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比|无维度|
|sessions_percent|会话百分比|百分比|平均值|会话百分比|无维度|
|dtu_limit|DTU 限制|计数|平均值|DTU 限制|无维度|
|dtu_used|已用的 DTU|计数|平均值|已用的 DTU|无维度|
|dwu_limit|DWU 限制|计数|最大值|DWU 限制|无维度|
|dwu_consumption_percent|DWU 百分比|百分比|最大值|DWU 百分比|无维度|
|dwu_used|已用的 DWU|计数|最大值|已用的 DWU|无维度|
|dw_cpu_percent|DW 节点级别 CPU 百分比|百分比|平均值|DW 节点级别 CPU 百分比|dw_logical_node_id|
|dw_physical_data_read_percent|DW 节点级别数据 IO 百分比|百分比|平均值|DW 节点级别数据 IO 百分比|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|database_cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|DatabaseResourceId|
|physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无维度|
|database_physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|DatabaseResourceId|
|log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比|无维度|
|database_log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比|DatabaseResourceId|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|无维度|
|database_dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId|
|storage_percent|存储百分比|百分比|平均值|存储百分比|无维度|
|workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比|无维度|
|database_workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比|DatabaseResourceId|
|sessions_percent|会话百分比|百分比|平均值|会话百分比|无维度|
|database_sessions_percent|会话百分比|百分比|平均值|会话百分比|DatabaseResourceId|
|eDTU_limit|eDTU 限制|计数|平均值|eDTU 限制|无维度|
|storage_limit|存储限制|字节|平均值|存储限制|无维度|
|eDTU_used|已用的 eDTU|计数|平均值|已用的 eDTU|无维度|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无维度|
|database_storage_used|已用的存储量|字节|平均值|已用的存储量|DatabaseResourceId|
|xtp_storage_percent|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比|无维度|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId、ElasticPoolResourceId|
|storage_used|已用的存储量|字节|平均值|已用的存储量|ElasticPoolResourceId|
|database_storage_used|已用的存储量|字节|平均值|已用的存储量|DatabaseResourceId、ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|UsedCapacity|已用容量|字节|平均值|帐户使用的容量|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType、GeoType、ApiName|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType、ApiName|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType、ApiName|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType、ApiName|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType、ApiName|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|字节|平均值|存储帐户的 Blob 服务使用的存储量（以字节为单位）。|/BlobType|
|BlobCount|Blob 计数|计数|平均值|存储帐户的 Blob 服务中的 Blob 数。|/BlobType|
|ContainerCount|Blob 容器计数|计数|平均值|存储帐户的 Blob 服务中的容器数。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType、GeoType、ApiName|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType、ApiName|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType、ApiName|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType、ApiName|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType、ApiName|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TableCapacity|表容量|字节|平均值|存储帐户的表服务使用的存储量（以字节为单位）。|无维度|
|TableCount|表计数|计数|平均值|存储帐户的表服务中的表数。|无维度|
|TableEntityCount|表实体计数|计数|平均值|存储帐户的表服务中的表实体数。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType、GeoType、ApiName|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType、ApiName|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType、ApiName|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType、ApiName|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType、ApiName|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QueueCapacity|队列容量|字节|平均值|存储帐户的队列服务使用的存储量（以字节为单位）。|无维度|
|QueueCount|队列计数|计数|平均值|存储帐户的队列服务中的队列数。|无维度|
|QueueMessageCount|队列消息计数|计数|平均值|存储帐户的队列服务中的队列消息的大致数目。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType、GeoType、ApiName|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType、ApiName|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType、ApiName|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType、ApiName|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType、ApiName|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType、ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|FileCapacity|文件容量|字节|平均值|存储帐户的文件服务使用的存储量（以字节为单位）。|无维度|
|FileCount|文件计数|计数|平均值|存储帐户的文件服务中的文件数。|无维度|
|FileShareCount|文件共享计数|计数|平均值|存储帐户的文件服务中的文件共享数。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType、GeoType、ApiName|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType、ApiName|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType、ApiName|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType、ApiName|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType、ApiName|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType、ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ResourceUtilization|流单元利用率 %|百分比|最大值|流单元利用率 %|无维度|
|InputEvents|输入事件数|计数|总计|输入事件数|无维度|
|InputEventBytes|输入事件字节数|字节|总计|输入事件字节数|无维度|
|LateInputEvents|延迟输入事件数|计数|总计|延迟输入事件数|无维度|
|OutputEvents|输出事件数|计数|总计|输出事件数|无维度|
|ConversionErrors|数据转换错误数|计数|总计|数据转换错误数|无维度|
|Errors|运行时错误|计数|总计|运行时错误|无维度|
|DroppedOrAdjustedEvents|失序事件数|计数|总计|失序事件数|无维度|
|AMLCalloutRequests|函数请求数|计数|总计|函数请求数|无维度|
|AMLCalloutFailedRequests|失败的函数请求数|计数|总计|失败的函数请求数|无维度|
|AMLCalloutInputEvents|函数事件数|计数|总计|函数事件数|无维度|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|
|DiskQueueLength|磁盘队列长度|计数|总计|磁盘队列长度|实例|
|HttpQueueLength|Http 队列长度|计数|总计|Http 队列长度|实例|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites（不包括 Functions）

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuTime|CPU 时间|秒|总计|CPU 时间|实例|
|请求|请求|计数|总计|请求|实例|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|
|Http101|Http 101|计数|总计|Http 101|实例|
|Http2xx|Http 2xx|计数|总计|Http 2xx|实例|
|Http3xx|Http 3xx|计数|总计|Http 3xx|实例|
|Http401|Http 401|计数|总计|Http 401|实例|
|Http403|Http 403|计数|总计|Http 403|实例|
|Http404|Http 404|计数|总计|Http 404|实例|
|Http406|Http 406|计数|总计|Http 406|实例|
|Http4xx|Http 4xx|计数|总计|Http 4xx|实例|
|Http5xx|Http 服务器错误|计数|总计|Http 服务器错误|实例|
|MemoryWorkingSet|内存工作集|字节|平均值|内存工作集|实例|
|AverageMemoryWorkingSet|平均内存工作集|字节|平均值|平均内存工作集|实例|
|AverageResponseTime|平均响应时间|秒|平均值|平均响应时间|实例|
|FunctionExecutionUnits|函数执行单位数|计数|平均值|函数执行单位数|实例|
|FunctionExecutionCount|函数执行计数|计数|平均值|函数执行计数|实例|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|
|Http5xx|Http 服务器错误|计数|总计|Http 服务器错误|实例|
|MemoryWorkingSet|内存工作集|字节|平均值|内存工作集|实例|
|AverageMemoryWorkingSet|平均内存工作集|字节|平均值|平均内存工作集|实例|
|FunctionExecutionUnits|函数执行单位数|计数|平均值|函数执行单位数|实例|
|FunctionExecutionCount|函数执行计数|计数|平均值|函数执行计数|实例|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuTime|CPU 时间|秒|总计|CPU 时间|实例|
|请求|请求|计数|总计|请求|实例|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|
|Http101|Http 101|计数|总计|Http 101|实例|
|Http2xx|Http 2xx|计数|总计|Http 2xx|实例|
|Http3xx|Http 3xx|计数|总计|Http 3xx|实例|
|Http401|Http 401|计数|总计|Http 401|实例|
|Http403|Http 403|计数|总计|Http 403|实例|
|Http404|Http 404|计数|总计|Http 404|实例|
|Http406|Http 406|计数|总计|Http 406|实例|
|Http4xx|Http 4xx|计数|总计|Http 4xx|实例|
|Http5xx|Http 服务器错误|计数|总计|Http 服务器错误|实例|
|MemoryWorkingSet|内存工作集|字节|平均值|内存工作集|实例|
|AverageMemoryWorkingSet|平均内存工作集|字节|平均值|平均内存工作集|实例|
|AverageResponseTime|平均响应时间|秒|平均值|平均响应时间|实例|
|FunctionExecutionUnits|函数执行单位数|计数|平均值|函数执行单位数|实例|
|FunctionExecutionCount|函数执行计数|计数|平均值|函数执行计数|实例|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|请求|请求|计数|总计|请求|实例|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|
|Http101|Http 101|计数|总计|Http 101|实例|
|Http2xx|Http 2xx|计数|总计|Http 2xx|实例|
|Http3xx|Http 3xx|计数|总计|Http 3xx|实例|
|Http401|Http 401|计数|总计|Http 401|实例|
|Http403|Http 403|计数|总计|Http 403|实例|
|Http404|Http 404|计数|总计|Http 404|实例|
|Http406|Http 406|计数|总计|Http 406|实例|
|Http4xx|Http 4xx|计数|总计|Http 4xx|实例|
|Http5xx|Http 服务器错误|计数|总计|Http 服务器错误|实例|
|AverageResponseTime|平均响应时间|秒|平均值|平均响应时间|实例|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|
|DiskQueueLength|磁盘队列长度|计数|总计|磁盘队列长度|实例|
|HttpQueueLength|Http 队列长度|计数|总计|Http 队列长度|实例|
|ActiveRequests|活动请求数|计数|总计|活动请求数|实例|
|TotalFrontEnds|前端总数|计数|平均值|前端总数|实例|
|SmallAppServicePlanInstances|小型应用服务计划工作线程数|计数|平均值|小型应用服务计划工作线程数|实例|
|MediumAppServicePlanInstances|中型应用服务计划工作线程数|计数|平均值|中型应用服务计划工作线程数|实例|
|LargeAppServicePlanInstances|大型应用服务计划工作线程数|计数|平均值|大型应用服务计划工作线程数|实例|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|WorkersTotal|工作线程总数|计数|平均值|工作线程总数|实例|
|WorkersAvailable|可用工作线程数|计数|平均值|可用工作线程数|实例|
|WorkersUsed|使用的工作线程数|计数|平均值|使用的工作线程数|实例|

## <a name="next-steps"></a>后续步骤
* [了解 Azure Monitor 中的指标](monitoring-overview-metrics.md)
* [针对指标创建警报](insights-receive-alert-notifications.md)
* [将指标导出到存储、事件中心或 Log Analytics](monitoring-overview-of-diagnostic-logs.md)
