---
title: Azure Monitor 按资源类型支持的指标
description: 可在 Azure Monitor 中为每种资源类型使用的指标的列表。
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: aab5d9a4cb7527e8a2085f826febc64bbd74854c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551962"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor 支持的指标

Azure Monitor 提供多种方式来与指标交互，包括在门户中制作指标图表、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 下面是目前可在 Azure Monitor 的指标管道中使用的完整指标列表。 其他指标可在门户或旧版 API 中使用。 下面的此列表仅包含可以通过合并的 Azure Monitor 指标管道使用的指标。 若要查询和访问这些指标，请使用 [2018-01-01 API 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|qpu_metric|QPU|计数|平均值|QPU。 S1 范围为 0-100，S2 范围为 0-200，S4 范围为 0-400|ServerResourceType|
|memory_metric|内存|字节|平均值|内存。 S1 范围为 0-25 GB，S2 范围为 0-50 GB，S4 范围为 0-100 GB|ServerResourceType|
|private_bytes_metric|专用字节 |字节|平均值|Analysis Services 引擎进程和混合容器进程分配的内存总量，不包括与其他进程共享的内存。|ServerResourceType|
|virtual_bytes_metric|虚拟字节 |字节|平均值|Analysis Services 引擎进程和混合容器进程使用的虚拟地址空间的当前大小。|ServerResourceType|
|TotalConnectionRequests|连接请求总数|计数|平均值|连接请求总数。 这些请求是到达的请求。|ServerResourceType|
|SuccessfullConnectionsPerSec|每秒成功连接数|每秒计数|平均值|连接成功完成速率。|ServerResourceType|
|TotalConnectionFailures|连接失败总数|计数|平均值|失败的连接尝试总数。|ServerResourceType|
|CurrentUserSessions|当前用户会话数|计数|平均值|当前已建立的用户会话数。|ServerResourceType|
|QueryPoolBusyThreads|查询池繁忙线程数|计数|平均值|查询线程池中的繁忙线程数。|ServerResourceType|
|CommandPoolJobQueueLength|命令池作业队列长度|计数|平均值|命令线程池队列中的作业数。|ServerResourceType|
|ProcessingPoolJobQueueLength|处理池作业队列长度|计数|平均值|处理线程池的队列中的非 I/O 作业数。|ServerResourceType|
|CurrentConnections|连接: 当前连接数|计数|平均值|当前已建立的客户端连接的数量。|ServerResourceType|
|CleanerCurrentPrice|内存: 清理器当前价格|计数|平均值|内存的当前价格，$/字节/时间，标准化为 1000。|ServerResourceType|
|CleanerMemoryShrinkable|内存: 可收缩的清理器内存|字节|平均值|受后台清理器执行的清除影响的内存量（字节）。|ServerResourceType|
|CleanerMemoryNonshrinkable|内存: 不可收缩的清理器内存|字节|平均值|不受后台清理器执行的清除影响的内存量（字节）。|ServerResourceType|
|MemoryUsage|内存: 内存使用量|字节|平均值|服务器进程的内存使用量（在计算清理器内存价格时使用）。 等于计数器 Process\PrivateBytes 加上内存映射的数据的大小，并且将忽略由 xVelocity 内存中分析引擎 (VertiPaq) 映射或分配的超出了 xVelocity 引擎内存限制的任何内存。|ServerResourceType|
|MemoryLimitHard|内存: 内存硬性限制|字节|平均值|内存硬性限制，来自配置文件。|ServerResourceType|
|MemoryLimitHigh|内存: 内存上限|字节|平均值|内存上限，来自配置文件。|ServerResourceType|
|MemoryLimitLow|内存: 内存下限|字节|平均值|内存下限，来自配置文件。|ServerResourceType|
|MemoryLimitVertiPaq|内存: 内存 VertiPaq 限制|字节|平均值|内存中限制，来自配置文件。|ServerResourceType|
|Quota|内存: 配额|字节|平均值|当前内存配额（字节）。 内存配额也称为内存授予或内存预留。|ServerResourceType|
|QuotaBlocked|内存: 阻止的配额|计数|平均值|在其他内存配额被释放之前已阻止的当前的配额请求数。|ServerResourceType|
|VertiPaqNonpaged|内存: VertiPaq 未分页|字节|平均值|工作集中被锁定的供内存中引擎使用的内存字节数。|ServerResourceType|
|VertiPaqPaged|内存: VertiPaq 已分页|字节|平均值|用于内存中数据的已分页内存字节数。|ServerResourceType|
|RowsReadPerSec|处理: 每秒读取的行数|每秒计数|平均值|从所有关系数据库中读取行的速率。|ServerResourceType|
|RowsConvertedPerSec|处理: 每秒转换的行数|每秒计数|平均值|在处理过程中转换行的速率。|ServerResourceType|
|RowsWrittenPerSec|处理: 每秒写入的行数|每秒计数|平均值|在处理过程中写入行的速率。|ServerResourceType|
|CommandPoolBusyThreads|线程: 命令池繁忙线程数|计数|平均值|命令线程池中的繁忙线程数。|ServerResourceType|
|CommandPoolIdleThreads|线程: 命令池空闲线程数|计数|平均值|命令线程池中的空闲线程数。|ServerResourceType|
|LongParsingBusyThreads|线程: 长分析繁忙线程数|计数|平均值|长分析线程池中的繁忙线程数。|ServerResourceType|
|LongParsingIdleThreads|线程: 长分析空闲线程数|计数|平均值|长分析线程池中的空闲线程数。|ServerResourceType|
|LongParsingJobQueueLength|线程: 长分析作业队列长度|计数|平均值|长分析线程池队列中的作业数。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|线程: 处理池繁忙 I/O 作业线程数|计数|平均值|处理线程池中正在运行 I/O 作业的线程数。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|线程: 处理池繁忙非 I/O 线程数|计数|平均值|处理线程池中正在运行非 I/O 作业的线程数。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|线程: 处理池 I/O 作业队列长度|计数|平均值|处理线程池队列中的 I/O 作业数。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|线程: 处理池空闲 I/O 作业线程数|计数|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|线程: 处理池空闲非 I/O 线程数|计数|平均值|处理线程池中专用于非 I/O 作业的空闲线程数。|ServerResourceType|
|QueryPoolIdleThreads|线程: 查询池空闲线程数|计数|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|ServerResourceType|
|QueryPoolJobQueueLength|线程: 查询池作业队列长度|计数|平均值|查询线程池队列中的作业数。|ServerResourceType|
|ShortParsingBusyThreads|线程: 短分析繁忙线程数|计数|平均值|短分析线程池中的繁忙线程数。|ServerResourceType|
|ShortParsingIdleThreads|线程: 短分析空闲线程数|计数|平均值|短分析线程池中的空闲线程数。|ServerResourceType|
|ShortParsingJobQueueLength|线程: 短分析作业队列长度|计数|平均值|短分析线程池队列中的作业数。|ServerResourceType|
|memory_thrashing_metric|内存抖动|百分比|平均值|平均内存抖动。|ServerResourceType|
|mashup_engine_qpu_metric|M 引擎 QPU|计数|平均值|糅合引擎进程的 QPU 使用率|ServerResourceType|
|mashup_engine_memory_metric|M 引擎内存|字节|平均值|糅合引擎进程的内存使用率|ServerResourceType|
|mashup_engine_private_bytes_metric|M 引擎专用字节数 |字节|平均值|已分配的内存混合容器进程的总量，不包括与其他进程共享的内存。|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M 引擎虚拟字节数 |字节|平均值|当前正在使用的虚拟地址空间混合容器进程的大小。|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|Requests|Requests|计数|总计|给定时间段内的网关请求总数。 它可以由各种维度进行切片，以帮助您诊断问题。 |Location、BackendResponseCode、LastErrorReason、GatewayResponseCode|
|TotalRequests|网关请求总数|计数|总计|给定时间段内的网关请求总数。 此指标已弃用，我们建议使用新的 `Requests` 指标。 |位置、主机名|
|SuccessfulRequests|成功的网关请求数|计数|总计|给定时间段内成功的网关请求总数。 此指标已弃用，我们建议使用新的 `Requests` 指标。|位置、主机名|
|UnauthorizedRequests|未经授权的网关请求数|计数|总计| 给定时间段内未经授权的网关请求总数。 此指标已弃用，我们建议使用新的 `Requests` 指标。|位置、主机名|
|FailedRequests|失败的网关请求数|计数|总计|给定时间段内失败的网关请求总数。 此指标已弃用，我们建议使用新的 `Requests` 指标。|位置、主机名|
|OtherRequests|其他网关请求数|计数|总计|给定期间内不属于成功、未授权或失败类别的网关请求总数。 此指标已弃用，我们建议使用新的 `Requests` 指标。 |位置、主机名|
|Duration|网关请求的总持续时间|毫秒|平均值|API 管理接收来自客户端的请求与向客户端返回响应之间的时间。|位置、主机名|
|Capacity|Capacity|百分比|平均值|API 管理实例上的负载指示器，用于作出明智的决策，确定是否缩放实例以适应更多负载。|Location|
|EventHubTotalEvents|EventHub 事件总数|计数|总计|给定期间内从 API 管理发送到 EventHub 的事件总数。|Location|
|EventHubSuccessfulEvents|成功的 EventHub 事件|计数|总计|给定期间内成功的 EventHub 事件总数。|Location|
|EventHubTotalFailedEvents|故障 EventHub 事件|计数|总计|给定时间段内失败的 EventHub 事件总数。|Location|
|EventHubRejectedEvents|拒绝 EventHub 事件|计数|总计|给定时间段内拒绝的 EventHub 事件（错误的配置或未授权）的总数。|Location|
|EventHubThrottledEvents|限制 EventHub 事件|计数|总计|给定期间内限制的 EventHub 事件总数。|Location|
|EventHubTimedoutEvents|已超时 EventHub 事件|计数|总计|给定期间内的超时 EventHub 事件总数。|Location|
|EventHubDroppedEvents|已丢弃 EventHub 事件|计数|总计|由于在给定时间段内达到队列大小限制而跳过的事件总数。|Location|
|EventHubTotalBytesSent|EventHub 事件的大小|字节|总计|给定时间段内 EventHub 事件的总大小（以字节为单位）。|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|TotalJob|作业总数|计数|总计|作业总数|Runbook、状态|
|TotalUpdateDeploymentRuns|更新部署运行总数|计数|总计|软件更新部署的运行总计|SoftwareUpdateConfigurationName，状态|
|TotalUpdateDeploymentMachineRuns|运行更新部署计算机总数|计数|总计|在软件更新部署运行中运行的软件更新部署计算机总数|SoftwareUpdateConfigurationName，Status，TargetComputer，SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
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
|JobDeleteCompleteEvent|作业删除完成事件数|计数|总计|已成功删除的作业总数。|无维度|
|JobDeleteStartEvent|作业删除启动事件数|计数|总计|已请求删除的作业总数。|无维度|
|JobDisableCompleteEvent|作业禁用完成事件数|计数|总计|已成功禁用的作业总数。|无维度|
|JobDisableStartEvent|作业禁用启动事件数|计数|总计|已请求禁用的作业总数。|无维度|
|JobStartEvent|作业启动事件数|计数|总计|已成功启动的作业总数。|无维度|
|JobTerminateCompleteEvent|作业终止完成事件数|计数|总计|已成功终止的作业总数。|无维度|
|JobTerminateStartEvent|作业终止启动事件数|计数|总计|已请求终止的作业总数。|无维度|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|connectedclients|连接的客户端数|计数|最大值||ShardId|
|totalcommandsprocessed|总操作数|计数|总计||ShardId|
|cachehits|缓存命中数|计数|总计||ShardId|
|cachemisses|缓存未命中数|计数|总计||ShardId|
|getcommands|获取数|计数|总计||ShardId|
|setcommands|设置数|计数|总计||ShardId|
|operationsPerSecond|每秒操作数|计数|最大值||ShardId|
|evictedkeys|逐出的密钥数|计数|总计||ShardId|
|totalkeys|总密钥数|计数|最大值||ShardId|
|expiredkeys|过期的密钥数|计数|总计||ShardId|
|usedmemory|已用内存|字节|最大值||ShardId|
|usedmemorypercentage|已用内存百分比|百分比|最大值||ShardId|
|usedmemoryRss|已用内存 RSS|字节|最大值||ShardId|
|serverLoad|服务器负载|百分比|最大值||ShardId|
|cacheWrite|缓存写入量|每秒字节数|最大值||ShardId|
|cacheRead|缓存读取量|每秒字节数|最大值||ShardId|
|percentProcessorTime|CPU|百分比|最大值||ShardId|
|cacheLatency|缓存延迟毫秒数（预览）|计数|平均值||ShardId, SampleType|
|错误|Errors|计数|最大值||ShardId, ErrorType|
|connectedclients0|连接的客户端数(分片 0)|计数|最大值||无维度|
|totalcommandsprocessed0|总操作数(分片 0)|计数|总计||无维度|
|cachehits0|缓存命中数(分片 0)|计数|总计||无维度|
|cachemisses0|缓存未命中数(分片 0)|计数|总计||无维度|
|getcommands0|Get 数(分片 0)|计数|总计||无维度|
|setcommands0|Set 数(分片 0)|计数|总计||无维度|
|operationsPerSecond0|每秒操作数（分片 0）|计数|最大值||无维度|
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
|operationsPerSecond1|每秒操作数（分片 1）|计数|最大值||无维度|
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
|operationsPerSecond2|每秒操作数（分片 2）|计数|最大值||无维度|
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
|operationsPerSecond3|每秒操作数（分片 3）|计数|最大值||无维度|
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
|operationsPerSecond4|每秒操作数（分片 4）|计数|最大值||无维度|
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
|operationsPerSecond5|每秒操作数（分片 5）|计数|最大值||无维度|
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
|operationsPerSecond6|每秒操作数（分片 6）|计数|最大值||无维度|
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
|operationsPerSecond7|每秒操作数（分片 7）|计数|最大值||无维度|
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
|operationsPerSecond8|每秒操作数（分片 8）|计数|最大值||无维度|
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
|operationsPerSecond9|每秒操作数（分片 9）|计数|最大值||无维度|
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

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|无维度|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|无维度|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|无维度|
|Disk Read Bytes/Sec|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|无维度|
|Disk Write Bytes/Sec|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|无维度|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|RoleInstanceId|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|RoleInstanceId|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|RoleInstanceId|
|Disk Read Bytes/Sec|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|RoleInstanceId|
|Disk Write Bytes/Sec|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|RoleInstanceId|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|RoleInstanceId|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|TotalCalls|总调用数|计数|总计|调用总数。|ApiName，OperationName，Region|
|SuccessfulCalls|成功调用数|计数|总计|成功调用数。|ApiName，OperationName，Region|
|TotalErrors|错误总数|计数|总计|引发错误响应（HTTP 响应代码 4xx 或 5xx）的调用总数。|ApiName，OperationName，Region|
|BlockedCalls|阻止的调用数|计数|总计|超过速率或配额限制的调用数。|ApiName，OperationName，Region|
|ServerErrors|服务器错误数|计数|总计|引发服务内部错误（HTTP 响应代码 5xx）的调用数。|ApiName，OperationName，Region|
|ClientErrors|客户端错误数|计数|总计|引发客户端错误（HTTP 响应代码 4xx）的调用数。|ApiName，OperationName，Region|
|DataIn|数据输入|字节|总计|传入数据的大小（字节）。|ApiName，OperationName，Region|
|DataOut|数据输出|字节|总计|传出数据的大小（字节）。|ApiName，OperationName，Region|
|延迟|延迟|毫秒|平均值|延迟（毫秒）。|ApiName，OperationName，Region|
|CharactersTranslated|转换的字符|计数|总计|传入的文本请求中的字符总数。|ApiName，OperationName，Region|
|CharactersTrained|训练的字符|计数|总计|训练的字符总数。|ApiName，OperationName，Region|
|SpeechSessionDuration|语音会话持续时间|秒数|总计|语音会话的总持续时间（以秒计）。|ApiName，OperationName，Region|
|TotalTransactions|总事务|计数|总计|事务总数。|无维度|
|TotalTokenCalls|令牌调用总数|计数|总计|令牌调用的总数。|ApiName，OperationName，Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无维度|
|网络传入|按计费的网络|字节|总计|虚拟机在所有网络接口上接收的可计费字节数（传入流量）|无维度|
|网络传出|网络出量计费|字节|总计|虚拟机在所有网络接口上的可计费字节数（传出流量）|无维度|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|无维度|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间写入磁盘的字节数|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无维度|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|
|每磁盘读取字节数/秒|数据磁盘读取字节数/秒（不推荐使用）|每秒计数|平均值|在监视期间从单个磁盘读取的字节数/秒|SlotId|
|每磁盘写入字节数/秒|数据磁盘写入字节数/秒（不推荐使用）|每秒计数|平均值|在监视期间写入单个磁盘的字节数/秒|SlotId|
|每磁盘读取操作数/秒|数据磁盘读取操作数/秒（不推荐使用）|每秒计数|平均值|在监视期间从单个磁盘读取 IOPS|SlotId|
|每磁盘写入操作数/秒|数据磁盘写入操作数/秒（不推荐使用）|每秒计数|平均值|在监视期间从单个磁盘写入 IOPS|SlotId|
|每磁盘 QD|数据磁盘 QD （已弃用）|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|OS 每磁盘读取字节数/秒|OS 磁盘读取字节数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取的字节数/秒|无维度|
|OS 每磁盘写入字节数/秒|OS 磁盘写入字节数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间写入单个磁盘的字节数/秒|无维度|
|OS 每磁盘读取操作数/秒|OS 磁盘读取操作数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取 IOPS|无维度|
|OS 每磁盘写入操作数/秒|OS 磁盘写入操作数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘写入 IOPS|无维度|
|OS 每磁盘 QD|OS Disk QD （已弃用）|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|数据磁盘读取字节数/秒|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|在监视期间从单个磁盘读取的字节数/秒|LUN|
|数据磁盘写入字节数/秒|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|在监视期间写入单个磁盘的字节数/秒|LUN|
|数据磁盘读取操作数/秒|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|在监视期间从单个磁盘读取 IOPS|LUN|
|数据磁盘写入操作数/秒|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|在监视期间从单个磁盘写入 IOPS|LUN|
|数据磁盘队列深度|数据磁盘队列深度（预览）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN|
|OS 磁盘读取字节数/秒|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取的字节数/秒|无维度|
|OS 磁盘写入字节数/秒|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间写入单个磁盘的字节数/秒|无维度|
|OS 磁盘读取操作数/秒|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取 IOPS|无维度|
|OS 磁盘写入操作数/秒|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘写入 IOPS|无维度|
|OS 磁盘队列深度|OS 磁盘队列深度（预览）|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|入站流|入站流（预览）|计数|平均值|入站流是入站方向的当前流数（传入 VM 的流量）|无维度|
|出站流|出站流（预览）|计数|平均值|出站流是以出站方向（从 VM 传出的流量）为当前流的数目|无维度|
|入站流最大创建速率|入站流最大创建速率（预览）|每秒计数|平均值|入站流的最大创建速率（传入 VM 的流量）|无维度|
|出站流的最大创建速率|出站流的最大创建速率（预览）|每秒计数|平均值|出站流的最大创建速率（从 VM 传出的流量）|无维度|
|高级数据磁盘缓存读取命中|高级数据磁盘缓存读取命中（预览）|百分比|平均值|高级数据磁盘缓存读取命中|LUN|
|高级数据磁盘缓存读取未命中|高级数据磁盘缓存读取未命中（预览）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN|
|高级 OS 磁盘缓存读取命中|高级 OS 磁盘缓存读取命中（预览）|百分比|平均值|高级 OS 磁盘缓存读取命中|无维度|
|高级 OS 磁盘缓存读取未命中|高级 OS 磁盘缓存读取未命中（预览）|百分比|平均值|高级 OS 磁盘缓存读取未命中|无维度|
|网络总数|网络总数|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无维度|
|网络传出总数|网络传出总数|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无维度|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|VMName|
|网络传入|按计费的网络|字节|总计|虚拟机在所有网络接口上接收的可计费字节数（传入流量）|VMName|
|网络传出|网络出量计费|字节|总计|虚拟机在所有网络接口上的可计费字节数（传出流量）|VMName|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|VMName|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间写入磁盘的字节数|VMName|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|VMName|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|VMName|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|
|每磁盘读取字节数/秒|数据磁盘读取字节数/秒（不推荐使用）|每秒计数|平均值|在监视期间从单个磁盘读取的字节数/秒|SlotId|
|每磁盘写入字节数/秒|数据磁盘写入字节数/秒（不推荐使用）|每秒计数|平均值|在监视期间写入单个磁盘的字节数/秒|SlotId|
|每磁盘读取操作数/秒|数据磁盘读取操作数/秒（不推荐使用）|每秒计数|平均值|在监视期间从单个磁盘读取 IOPS|SlotId|
|每磁盘写入操作数/秒|数据磁盘写入操作数/秒（不推荐使用）|每秒计数|平均值|在监视期间从单个磁盘写入 IOPS|SlotId|
|每磁盘 QD|数据磁盘 QD （已弃用）|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|OS 每磁盘读取字节数/秒|OS 磁盘读取字节数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取的字节数/秒|无维度|
|OS 每磁盘写入字节数/秒|OS 磁盘写入字节数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间写入单个磁盘的字节数/秒|无维度|
|OS 每磁盘读取操作数/秒|OS 磁盘读取操作数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取 IOPS|无维度|
|OS 每磁盘写入操作数/秒|OS 磁盘写入操作数/秒（不推荐使用）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘写入 IOPS|无维度|
|OS 每磁盘 QD|OS Disk QD （已弃用）|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|数据磁盘读取字节数/秒|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|在监视期间从单个磁盘读取的字节数/秒|LUN，VMName|
|数据磁盘写入字节数/秒|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|在监视期间写入单个磁盘的字节数/秒|LUN，VMName|
|数据磁盘读取操作数/秒|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|在监视期间从单个磁盘读取 IOPS|LUN，VMName|
|数据磁盘写入操作数/秒|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|在监视期间从单个磁盘写入 IOPS|LUN，VMName|
|数据磁盘队列深度|数据磁盘队列深度（预览）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN，VMName|
|OS 磁盘读取字节数/秒|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取的字节数/秒|VMName|
|OS 磁盘写入字节数/秒|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间写入单个磁盘的字节数/秒|VMName|
|OS 磁盘读取操作数/秒|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘读取 IOPS|VMName|
|OS 磁盘写入操作数/秒|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘写入 IOPS|VMName|
|OS 磁盘队列深度|OS 磁盘队列深度（预览）|计数|平均值|OS 磁盘队列深度(或队列长度)|VMName|
|入站流|入站流（预览）|计数|平均值|入站流是入站方向的当前流数（传入 VM 的流量）|VMName|
|出站流|出站流（预览）|计数|平均值|出站流是以出站方向（从 VM 传出的流量）为当前流的数目|VMName|
|入站流最大创建速率|入站流最大创建速率（预览）|每秒计数|平均值|入站流的最大创建速率（传入 VM 的流量）|VMName|
|出站流的最大创建速率|出站流的最大创建速率（预览）|每秒计数|平均值|出站流的最大创建速率（从 VM 传出的流量）|VMName|
|高级数据磁盘缓存读取命中|高级数据磁盘缓存读取命中（预览）|百分比|平均值|高级数据磁盘缓存读取命中|LUN，VMName|
|高级数据磁盘缓存读取未命中|高级数据磁盘缓存读取未命中（预览）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN，VMName|
|高级 OS 磁盘缓存读取命中|高级 OS 磁盘缓存读取命中（预览）|百分比|平均值|高级 OS 磁盘缓存读取命中|VMName|
|高级 OS 磁盘缓存读取未命中|高级 OS 磁盘缓存读取未命中（预览）|百分比|平均值|高级 OS 磁盘缓存读取未命中|VMName|
|网络总数|网络总数|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|VMName|
|网络传出总数|网络传出总数|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|计数|平均值|所有核心的 CPU 使用率（以 millicore 为单位）。|containerName|
|MemoryUsage|内存用量|字节|平均值|总内存使用量（以字节为单位）。|containerName|
|NetworkBytesReceivedPerSecond|每秒接收到的网络字节数|字节|平均值|每秒接收到的网络字节数。|无维度|
|NetworkBytesTransmittedPerSecond|每秒传输的网络字节数|字节|平均值|每秒传输的网络字节数。|无维度|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|TotalPullCount|请求总数|计数|平均值|映像提取总数|无维度|
|SuccessfulPullCount|成功的请求计数|计数|平均值|成功的图像提取次数|无维度|
|TotalPushCount|推送计数总计|计数|平均值|映像推送数总计|无维度|
|SuccessfulPushCount|推送计数成功|计数|平均值|成功的映像推送数|无维度|
|RunDuration|运行持续时间|毫秒|总计|运行持续时间（毫秒）|无维度|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|托管群集中可用 CPU 内核的总数|计数|总计|托管群集中可用 CPU 内核的总数|无维度|
|kube_node_status_allocatable_memory_bytes|托管群集中可用内存的总量|字节|总计|托管群集中可用内存的总量|无维度|
|kube_pod_status_ready|就绪状态下的 Pod 数|计数|总计|就绪状态下的 Pod 数|命名空间、Pod|
|kube_node_status_condition|各种节点条件的状态|计数|总计|各种节点条件的状态|条件，状态，status2，节点|
|kube_pod_status_phase|依据阶段的 Pod 数|计数|总计|依据阶段的 Pod 数|阶段、命名空间、Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API 调用数|计数|总计||无维度|
|DCIMappingImportOperationSuccessfulLines|映射导入操作成功行数|计数|总计||无维度|
|DCIMappingImportOperationFailedLines|映射导入操作失败行数|计数|总计||无维度|
|DCIMappingImportOperationTotalLines|映射导入操作总行数|计数|总计||无维度|
|DCIMappingImportOperationRuntimeInSeconds|映射导入操作运行时（以秒为单位）|秒数|总计||无维度|
|DCIOutboundProfileExportSucceeded|出站配置文件导出成功|计数|总计||无维度|
|DCIOutboundProfileExportFailed|出站配置文件导出失败|计数|总计||无维度|
|DCIOutboundProfileExportDuration|出站配置文件导出持续时间|秒数|总计||无维度|
|DCIOutboundKpiExportSucceeded|出站 KPI 导出成功|计数|总计||无维度|
|DCIOutboundKpiExportFailed|出站 KPI 导出失败|计数|总计||无维度|
|DCIOutboundKpiExportDuration|出站 KPI 导出持续时间|秒数|总计||无维度|
|DCIOutboundKpiExportStarted|出站 KPI 导出已启动|秒数|总计||无维度|
|DCIOutboundKpiRecordCount|出站 KPI 记录计数|秒数|总计||无维度|
|DCIOutboundProfileExportCount|出站配置文件导出计数|秒数|总计||无维度|
|DCIOutboundInitialProfileExportFailed|出站初始配置文件导出失败|秒数|总计||无维度|
|DCIOutboundInitialProfileExportSucceeded|出站初始配置文件导出成功|秒数|总计||无维度|
|DCIOutboundInitialKpiExportFailed|出站初始 KPI 导出失败|秒数|总计||无维度|
|DCIOutboundInitialKpiExportSucceeded|出站初始 KPI 导出成功|秒数|总计||无维度|
|DCIOutboundInitialProfileExportDurationInSeconds|出站初始配置文件导出持续时间（以秒为单位）|秒数|总计||无维度|
|AdlaJobForStandardKpiFailed|标准 KPI 的 Adla 作业失败时间（以秒为单位）|秒数|总计||无维度|
|AdlaJobForStandardKpiTimeOut|标准 KPI 的 Adla 作业超时时间（以秒为单位）|秒数|总计||无维度|
|AdlaJobForStandardKpiCompleted|标准 KPI 的 Adla 作业完成时间（以秒为单位）|秒数|总计||无维度|
|ImportASAValuesFailed|导入 ASA 值失败计数|计数|总计||无维度|
|ImportASAValuesSucceeded|导入 ASA 值成功计数|计数|总计||无维度|
|DCIProfilesCount|配置文件实例计数|计数|最后一个||无维度|
|DCIInteractionsPerMonthCount|每月计数的交互|计数|最后一个||无维度|
|DCIKpisCount|KPI 计数|计数|最后一个||无维度|
|DCISegmentsCount|段计数|计数|最后一个||无维度|
|DCIPredictiveMatchPoliciesCount|预测匹配计数|计数|最后一个||无维度|
|DCIPredictionsCount|预测计数|计数|最后一个||无维度|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>DataBoxEdge/dataBoxEdgeDevices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|NICReadThroughput|读取吞吐量（网络）|每秒字节数|平均值|网关上中所有卷的报告期间设备上的网络接口的读取吞吐量。|InstanceName|
|NICWriteThroughput|写入吞吐量（网络）|每秒字节数|平均值|网关上中所有卷的报告期间设备上的网络接口的写入吞吐量。|InstanceName|
|CloudReadThroughputPerShare|云下载吞吐量（共享）|每秒字节数|平均值|报告期间从共享下载到 Azure 的吞吐量。|共享|
|CloudUploadThroughputPerShare|云上传吞吐量（共享）|每秒字节数|平均值|报告期间从共享到 Azure 的上传吞吐量。|共享|
|BytesUploadedToCloudPerShare|已上传云字节数（共享）|字节|平均值|报表期间从共享上传到 Azure 的总字节数。|共享|
|TotalCapacity|总容量|字节|平均值|总容量|无维度|
|AvailableCapacity|可用容量|字节|平均值|报告期间的可用容量（以字节为单位）。|无维度|
|CloudUploadThroughput|云上传吞吐量|每秒字节数|平均值|在报告期间，云上传到 Azure 的吞吐量。|无维度|
|CloudReadThroughput|云下载吞吐量|每秒字节数|平均值|云在报表期间将吞吐量下载到 Azure。|无维度|
|BytesUploadedToCloud|已上传云字节（设备）|字节|平均值|报告期间从设备上传到 Azure 的总字节数。|无维度|
|HyperVVirtualProcessorUtilization|边缘计算-CPU 百分比|百分比|平均值|CPU 使用率百分比|InstanceName|
|HyperVMemoryUtilization|边缘计算-内存使用情况|百分比|平均值|正在使用的 RAM 量|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|FailedRuns|失败的运行次数|计数|总计||pipelineName、activityName|
|SuccessfulRuns|成功的运行次数|计数|总计||pipelineName、activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|PipelineFailedRuns|失败的管道运行数指标|计数|总计||FailureType、Name|
|PipelineSucceededRuns|成功的管道运行数指标|计数|总计||FailureType、Name|
|ActivityFailedRuns|失败的活动运行数指标|计数|总计||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|成功的活动运行数指标|计数|总计||ActivityType、PipelineName、FailureType、Name|
|TriggerFailedRuns|失败的触发器运行数指标|计数|总计||Name、FailureType|
|TriggerSucceededRuns|成功的触发器运行数指标|计数|总计||Name、FailureType|
|IntegrationRuntimeCpuPercentage|集成运行时 CPU 利用率|百分比|平均值||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableMemory|集成运行时可用内存|字节|平均值||IntegrationRuntimeName、NodeName|
|MaxAllowedResourceCount|允许的最大实体计数|计数|最大值||无维度|
|MaxAllowedFactorySizeInGbUnits|允许的最大工厂大小（GB 单位）|计数|最大值||无维度|
|ResourceCount|实体总数|计数|最大值||无维度|
|FactorySizeInGbUnits|总工厂大小（GB 单位）|计数|最大值||无维度|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|JobEndedSuccess|成功作业数|计数|总计|成功作业计数。|无维度|
|JobEndedFailure|失败作业数|计数|总计|失败作业计数。|无维度|
|JobEndedCancelled|已取消作业|计数|总计|已取消的作业的计数。|无维度|
|JobAUEndedSuccess|成功 AU 时间|秒数|总计|成功作业的总 AU 时间。|无维度|
|JobAUEndedFailure|失败的 AU 时间|秒数|总计|失败作业的总 AU 时间。|无维度|
|JobAUEndedCancelled|已取消 AU 时间|秒数|总计|已取消作业的总 AU 时间。|无维度|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|TotalStorage|总存储|字节|最大值|帐户中存储的数据总量。|无维度|
|DataWritten|写入的数据量|字节|总计|写入帐户的数据总量。|无维度|
|DataRead|读取的数据量|字节|总计|从帐户中读取的数据总量。|无维度|
|WriteRequests|写入请求数|计数|总计|帐户的数据写入请求计数。|无维度|
|ReadRequests|读取请求数|计数|总计|帐户的数据读取请求计数。|无维度|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无维度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无维度|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|已用存储|字节|平均值|已用存储|无维度|
|storage_limit|存储限制|字节|平均值|存储限制|无维度|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无维度|
|serverlog_storage_usage|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无维度|
|serverlog_storage_limit|服务器存储空间上限|字节|平均值|服务器存储空间上限|无维度|
|active_connections|活动连接数|计数|平均值|活动连接数|无维度|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无维度|
|seconds_behind_master|复制延迟（秒）|计数|平均值|复制延迟（秒）|无维度|
|backup_storage_used|使用的备份存储|字节|平均值|使用的备份存储|无维度|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无维度|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无维度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无维度|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|已用存储|字节|平均值|已用存储|无维度|
|storage_limit|存储限制|字节|平均值|存储限制|无维度|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无维度|
|serverlog_storage_usage|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无维度|
|serverlog_storage_limit|服务器存储空间上限|字节|平均值|服务器存储空间上限|无维度|
|active_connections|活动连接数|计数|平均值|活动连接数|无维度|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无维度|
|seconds_behind_master|复制延迟（秒）|计数|平均值|复制延迟（秒）|无维度|
|backup_storage_used|使用的备份存储|字节|平均值|使用的备份存储|无维度|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无维度|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无维度|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无维度|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|已用存储|字节|平均值|已用存储|无维度|
|storage_limit|存储限制|字节|平均值|存储限制|无维度|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无维度|
|serverlog_storage_usage|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无维度|
|serverlog_storage_limit|服务器存储空间上限|字节|平均值|服务器存储空间上限|无维度|
|active_connections|活动连接数|计数|平均值|活动连接数|无维度|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无维度|
|backup_storage_used|使用的备份存储|字节|平均值|使用的备份存储|无维度|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无维度|
|pg_replica_log_delay_in_seconds|副本滞后时间|秒数|最大值|副本滞后时间（秒）|无维度|
|pg_replica_log_delay_in_bytes|副本的最大滞后时间|字节|最大值|最滞后副本的滞后时间（字节）|无维度|

## <a name="microsoftdbforpostgresqlserversv2"></a>DBforPostgreSQL/serversv2

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无维度|
|iops|IOPS|计数|平均值|每秒 IO 操作数|无维度|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|已用存储|字节|平均值|已用存储|无维度|
|active_connections|活动连接数|计数|平均值|活动连接数|无维度|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无维度|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遥测消息发送尝试次数|计数|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|d2c.telemetry.ingress.success|已发送的遥测消息数|计数|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|c2d.commands.egress.complete.success|完成的命令数|计数|总计|设备已成功完成的云到设备命令的数目|无维度|
|c2d.commands.egress.abandon.success|放弃的命令数|计数|总计|设备放弃的云到设备命令的数目|无维度|
|c2d.commands.egress.reject.success|拒绝的命令数|计数|总计|设备拒绝的云到设备命令的数目|无维度|
|devices.totalDevices|（已弃用）设备总数|计数|总计|已注册到 IoT 中心的设备数目|无维度|
|devices.connectedDevices.allProtocol|（已弃用）连接设备数 |计数|总计|已连接到 IoT 中心的设备数目|无维度|
|d2c.telemetry.egress.success|路由：已发送的遥测消息数|计数|总计|已使用 IoT 中心路由将消息成功发送到所有终结点的次数。 如果消息路由到多个终结点，每次成功发送后，此值将逐一增加。 如果消息多次路由到同一终结点，每次成功发送后，此值将逐一增加。|无维度|
|d2c.telemetry.egress.dropped|路由：已删除的遥测消息数 |计数|总计|由于死终结点，IoT 中心路由删除消息的次数。 此值不会计算已发送到回退路由的消息，因为已删除的消息未发送到该位置。|无维度|
|d2c.telemetry.egress.orphaned|路由：已孤立的遥测消息数 |计数|总计|由于消息不匹配任何传递规则（包括回退规则），IoT 中心路由孤立消息的次数。 |无维度|
|d2c.telemetry.egress.invalid|路由：不兼容的遥测消息数|计数|总计|由于与终结点不兼容，IoT 中心路由未能发送消息的次数。 此值不包括重试次数。|无维度|
|d2c.telemetry.egress.fallback|路由：发送到回退的消息数|计数|总计|IoT 中心路由将消息发送到与回退路由相关联的终结点的次数。|无维度|
|d2c.endpoints.egress.eventHubs|路由：已发送到事件中心的消息数|计数|总计|IoT 中心路由成功将消息发送到事件中心终结点的次数。|无维度|
|d2c.endpoints.latency.eventHubs|路由：事件中心的消息延迟|毫秒|平均值|消息进入 IoT 中心与消息进入事件中心终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.serviceBusQueues|路由：已发送到服务总线队列的消息数|计数|总计|IoT 中心路由成功将消息发送到服务总线队列终结点的次数。|无维度|
|d2c.endpoints.latency.serviceBusQueues|路由：服务总线队列的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线队列终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.serviceBusTopics|路由：发送到服务总线主题的消息数|计数|总计|IoT 中心路由将消息成功发送到服务总线主题终结点的次数。|无维度|
|d2c.endpoints.latency.serviceBusTopics|路由：服务总线主题的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线主题终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.builtIn.events|路由：发送到消息/事件的消息数|计数|总计|IoT 中心路由成功将消息发送到内置终结点（消息/事件）的次数。 此指标仅在启用路由时开始工作（为 IoT 中心 https://aka.ms/iotrouting) 。|无维度|
|d2c.endpoints.latency.builtIn.events|路由：消息/事件的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入内置终结点（消息/事件）之间的平均延迟时间（毫秒）。 此指标仅在启用路由时开始工作（为 IoT 中心 https://aka.ms/iotrouting) 。|无维度|
|d2c.endpoints.egress.storage|路由：发送到存储器的消息数|计数|总计|IoT 中心路由成功将消息发送到存储器终结点的次数。|无维度|
|d2c.endpoints.latency.storage|路由：存储器的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入存储器终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.storage.bytes|路由：发送到存储器的数据数|字节|总计|IoT 中心路由发送到存储器终结点的数据量（字节）。|无维度|
|d2c.endpoints.egress.storage.blobs|路由：发送到存储器的 blob 数|计数|总计|IoT 中心路由将 blob 发送到存储器终结点的次数。|无维度|
|EventGridDeliveries|事件网格传递（预览）|计数|总计|发布到事件网格的 IoT 中心事件数。 对于成功和失败的请求数，请使用结果维度。 事件类型维度显示事件的类型（ https://aka.ms/ioteventgrid) 。|Result、事件|
|EventGridLatency|将事件发布到事件网格时，从生成 Iot 中心事件到的平均延迟时间（毫秒）。 此数字是所有事件类型的平均数。 使用 "事件类型" 维度来查看特定事件类型的延迟。|EventType|
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
|deviceDataUsage|设备数据总使用量|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无维度|
|totalDeviceCount|设备总数（预览）|计数|平均值|已注册到 IoT 中心的设备数目|无维度|
|connectedDeviceCount|已连接设备数（预览）|计数|平均值|已连接到 IoT 中心的设备数目|无维度|
|配置|配置指标|计数|总计|配置操作的指标|无维度|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|RegistrationAttempts|注册尝试次数|计数|总计|已尝试的设备注册次数|ProvisioningServiceName、IotHubName、Status|
|DeviceAssignments|已分配设备|计数|总计|已分配给 IoT 中心的设备数|ProvisioningServiceName、IotHubName|
|AttestationAttempts|证明尝试次数|计数|总计|已尝试的设备证明次数|ProvisioningServiceName、Status、Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|AvailableStorage|可用存储|字节|总计|5分钟粒度报告的可用存储总数|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|Cassandra 连接关闭数|计数|总计|在1分钟的粒度报告的已关闭的 Cassandra 连接数|Region、ClosureReason|
|CassandraRequestCharges|Cassandra 请求费用|计数|总计|发出的 Cassandra 请求所使用的 ru 数|DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|Cassandra 请求数|计数|计数|发出的 Cassandra 请求数|DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|DataUsage|数据使用情况|字节|总计|在5分钟粒度内报告的总数据用量|CollectionName、DatabaseName、Region|
|DocumentCount|文档计数|计数|总计|5分钟粒度报告的总文档数|CollectionName、DatabaseName、Region|
|DocumentQuota|文档配额|字节|总计|5分钟粒度报告的总存储配额|CollectionName、DatabaseName、Region|
|IndexUsage|索引使用情况|字节|总计|5分钟粒度报告的总索引使用情况|CollectionName、DatabaseName、Region|
|MetadataRequests|元数据请求|计数|计数|元数据请求的计数。 Cosmos DB 维护每个帐户的系统元数据收集，这使你可以免费枚举集合、数据库等。|DatabaseName、CollectionName、Region、StatusCode、 |
|MongoRequestCharge|Mongo 请求费用|计数|总计|Mongo 已消耗的请求单位|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo 请求|计数|计数|已发出的 Mongo 请求数|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|预配的吞吐量|计数|最大值|预配的吞吐量|DatabaseName、CollectionName|
|ReplicationLatency|P99 复制延迟|毫秒|平均值|启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟|SourceRegion、TargetRegion|
|ServiceAvailability|服务可用性|百分比|平均值|帐户请求可用性一小时、天或月粒度|无维度|
|TotalRequestUnits|总请求单位数|计数|总计|已消耗的请求单位|DatabaseName、CollectionName、Region、StatusCode、OperationType|
|TotalRequests|请求总数|计数|计数|已发出的请求数|DatabaseName、CollectionName、Region、StatusCode、OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishFailCount|发布失败事件|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|
|PublishSuccessLatencyInMs|发布成功延迟|计数|总计|发布成功延迟时间（毫秒）|无维度|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|无维度|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|无维度|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|无维度|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishFailCount|失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|
|PublishSuccessLatencyInMs|发布成功延迟|计数|总计|发布成功延迟时间（毫秒）|无维度|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的请求数|计数|总计|Microsoft.EventHub 成功请求数。|EntityName、 |
|ServerErrors|服务器错误数。|计数|总计|Microsoft.EventHub 的服务器错误数。|EntityName、 |
|UserErrors|用户错误数。|计数|总计|Microsoft.EventHub 用户错误数。|EntityName、 |
|QuotaExceededErrors|超过限额错误。|计数|总计|Microsoft.EventHub 的超过限额错误数。|EntityName、 |
|ThrottledRequests|限制的请求数。|计数|总计|Microsoft.EventHub 限制的请求数。|EntityName、 |
|IncomingRequests|传入请求数|计数|总计|Microsoft.EventHub 传入的请求数。|EntityName|
|IncomingMessages|传入消息数|计数|总计|Microsoft.EventHub 传入的消息数。|EntityName|
|OutgoingMessages|传出消息数|计数|总计|Microsoft.EventHub 传出的消息数。|EntityName|
|IncomingBytes|传入字节数。|字节|总计|Microsoft.EventHub 传入的字节数。|EntityName|
|OutgoingBytes|传出字节数。|字节|总计|Microsoft.EventHub 传出的字节数。|EntityName|
|ActiveConnections|ActiveConnections|计数|平均值|Microsoft.EventHub 的活动连接总数。|无维度|
|ConnectionsOpened|打开的连接数。|计数|平均值|Microsoft.EventHub 打开的连接数。|EntityName|
|ConnectionsClosed|已关闭的连接数。|计数|平均值|Microsoft.EventHub 已关闭的连接数。|EntityName|
|CaptureBacklog|捕获积压工作(backlog)。|计数|总计|捕获有关 Microsoft.EventHub 的积压工作(backlog)。|EntityName|
|CapturedMessages|已捕获的消息数。|计数|总计|Microsoft.EventHub 已捕获的消息数。|EntityName|
|CapturedBytes|已捕获的字节数。|字节|总计|Microsoft.EventHub 已捕获的字节数。|EntityName|
|大小|大小|字节|平均值|EventHub 的大小（以字节为单位）。|EntityName|
|INREQS|传入请求（已弃用）|计数|总计|命名空间的传入发送请求总数（不推荐使用）|无维度|
|SUCCREQ|成功的请求数（已弃用）|计数|总计|命名空间的成功请求总数（不推荐使用）|无维度|
|FAILREQ|失败的请求数（已弃用）|计数|总计|命名空间的失败请求总数（不推荐使用）|无维度|
|SVRBSY|服务器忙错误（已弃用）|计数|总计|命名空间的服务器繁忙错误总数（不推荐使用）|无维度|
|INTERR|内部服务器错误（已弃用）|计数|总计|命名空间的内部服务器错误总数（不推荐使用）|无维度|
|MISCERR|其他错误（不推荐使用）|计数|总计|命名空间的失败请求总数（不推荐使用）|无维度|
|INMSGS|传入消息（已弃用）（不推荐使用）|计数|总计|命名空间的传入消息总数。 此指标已弃用。 请改用传入消息度量值（不推荐使用）|无维度|
|EHINMSGS|传入消息（已弃用）|计数|总计|命名空间的传入消息总数（不推荐使用）|无维度|
|OUTMSGS|传出消息（已弃用）（不推荐使用）|计数|总计|命名空间的传出消息总数。 此指标已弃用。 请改用传出消息度量值（不推荐使用）|无维度|
|EHOUTMSGS|传出消息（已弃用）|计数|总计|命名空间的传出消息总数（不推荐使用）|无维度|
|EHINMBS|传入字节（已弃用）（不推荐使用）|字节|总计|命名空间的事件中心传入消息吞吐量。 此指标已弃用。 请改用传入的字节度量值（不推荐使用）|无维度|
|EHINBYTES|传入字节（已弃用）|字节|总计|命名空间的事件中心传入消息吞吐量（不推荐使用）|无维度|
|EHOUTMBS|传出字节（已弃用）（不推荐使用）|字节|总计|命名空间的事件中心传出消息吞吐量。 此指标已弃用。 请改用传出字节度量值（不推荐使用）|无维度|
|EHOUTBYTES|传出字节（已弃用）|字节|总计|命名空间的事件中心传出消息吞吐量（不推荐使用）|无维度|
|EHABL|存档积压消息（已弃用）|计数|总计|命名空间的积压工作（backlog）中的事件中心存档消息（不推荐使用）|无维度|
|EHAMSGS|存档消息（已弃用）|计数|总计|命名空间中的事件中心存档消息（不推荐使用）|无维度|
|EHAMBS|存档消息吞吐量（不推荐使用）|字节|总计|命名空间中的事件中心存档消息吞吐量（不推荐使用）|无维度|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的请求数（预览版）|计数|总计|Microsoft.EventHub 成功请求数。 （预览）|无维度|
|ServerErrors|服务器错误数。 （预览）|计数|总计|Microsoft.EventHub 的服务器错误数。 （预览）|无维度|
|UserErrors|用户错误数。 （预览）|计数|总计|Microsoft.EventHub 用户错误数。 （预览）|无维度|
|QuotaExceededErrors|超过限额错误。 （预览）|计数|总计|Microsoft.EventHub 的超过限额错误数。 （预览）|无维度|
|ThrottledRequests|限制的请求数。 （预览）|计数|总计|Microsoft.EventHub 限制的请求数。 （预览）|无维度|
|IncomingRequests|传入的请求数（预览版）|计数|总计|Microsoft.EventHub 传入的请求数。 （预览）|无维度|
|IncomingMessages|传入的消息数（预览版）|计数|总计|Microsoft.EventHub 传入的消息数。 （预览）|无维度|
|OutgoingMessages|传出的消息数（预览版）|计数|总计|Microsoft.EventHub 传出的消息数。 （预览）|无维度|
|IncomingBytes|传入字节数。 （预览）|字节|总计|Microsoft.EventHub 传入的字节数。 （预览）|无维度|
|OutgoingBytes|传出字节数。 （预览）|字节|总计|Microsoft.EventHub 传出的字节数。 （预览）|无维度|
|ActiveConnections|ActiveConnections（预览版）|计数|平均值|Microsoft.EventHub 的活动连接总数。 （预览）|无维度|
|ConnectionsOpened|打开的连接数。 （预览）|计数|平均值|Microsoft.EventHub 打开的连接数。 （预览）|无维度|
|ConnectionsClosed|已关闭的连接数。 （预览）|计数|平均值|Microsoft.EventHub 已关闭的连接数。 （预览）|无维度|
|CaptureBacklog|捕获积压工作(backlog)。 （预览）|计数|总计|捕获有关 Microsoft.EventHub 的积压工作(backlog)。 （预览）|无维度|
|CapturedMessages|已捕获的消息数。 （预览）|计数|总计|Microsoft.EventHub 已捕获的消息数。 （预览）|无维度|
|CapturedBytes|已捕获的字节数。 （预览）|字节|总计|Microsoft.EventHub 已捕获的字节数。 （预览）|无维度|
|CPU|CPU（预览）|百分比|最大值|事件中心群集的 CPU 使用率（百分比）|角色|
|AvailableMemory|可用内存（预览）|计数|最大值|事件中心群集的可用内存（字节）|角色|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|GatewayRequests|网关请求数|计数|总计|网关请求数|ClusterDnsName、HttpStatus|
|CategorizedGatewayRequests|已分类的网关请求数|计数|总计|按类别（1xx/2xx/3xx/4xx/5xx）统计的网关请求数|ClusterDnsName、HttpStatus|
|NumActiveWorkers|活动工作线程数|计数|最大值|活动工作线程数|ClusterDnsName、MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ObservedMetricValue|观察到的指标值|计数|平均值|执行自动缩放时计算的值|MetricTriggerSource|
|MetricThreshold|指标阈值|计数|平均值|自动缩放运行时已配置的自动缩放阈值。|MetricTriggerRule|
|ObservedCapacity|观察到的容量|计数|平均值|自动缩放执行时报告的容量。|无维度|
|ScaleActionsInitiated|启动的缩放操作|计数|总计|缩放操作的方向。|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

（公共预览版）

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|可用性|百分比|平均值|已成功完成的可用性测试的百分比|availabilityResult/name、availabilityResult/location|
|availabilityResults/count|可用性测试|计数|计数|可用性测试计数|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|可用性测试持续时间|毫秒|平均值|可用性测试持续时间|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|页面加载网络连接时间|毫秒|平均值|用户请求和网络连接之间的时间。 包括 DNS 查找和传输连接。|无维度|
|browserTimings/processingDuration|客户端处理时间|毫秒|平均值|从接收文档的最后一个字节到 DOM 加载完之间的时间。 可能仍在处理异步请求。|无维度|
|browserTimings/receiveDuration|接收响应时间|毫秒|平均值|第一个和最后一个字节之间的时间，或直至断开连接的时间。|无维度|
|browserTimings/sendDuration|发送请求时间|毫秒|平均值|网络连接和接收第一个字节之间的时间。|无维度|
|browserTimings/totalDuration|浏览器页面加载时间|毫秒|平均值|从用户请求一直到 DOM、样式表、脚本和映像加载完之间的时间。|无维度|
|dependencies/count|依赖项调用|计数|计数|应用程序对外部资源所进行的调用计数。|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|依赖项持续时间|毫秒|平均值|应用程序对外部资源所进行的调用持续时间。|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|依赖项调用失败|计数|计数|应用程序对外部资源所进行的依赖项调用失败的计数。|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|页面视图|计数|计数|页面视图计数。|operation/synthetic|
|pageViews/duration|页面视图加载时间|毫秒|平均值|页面视图加载时间|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP 请求执行时间|毫秒|平均值|最近的请求执行时间。|cloud/roleInstance|
|performanceCounters/requestsInQueue|应用程序队列中的 HTTP 请求|计数|平均值|应用程序请求队列的长度。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 请求速率|每秒计数|平均值|每秒从 ASP.NET 发出的应用程序所有请求的速率。|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|异常率|每秒计数|平均值|报告给窗口的已处理和未处理的异常的计数，这些异常包括 .NET 异常和转换为 .NET 异常的非托管异常。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|进程 IO 率|每秒字节数|平均值|每秒读取和写入文件、网络和设备的总字节数。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|进程 CPU|百分比|平均值|所有进程线程使用处理器执行指令所用的运行时间的百分比。 介于 0 到 100 之间。 此指标仅表示 w3wp 进程的性能。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|处理器时间|百分比|平均值|处理器在非空闲线程上所花费的时间百分比。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|可用内存|字节|平均值|可立刻供进程或系统使用的物理内存。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|进程专用字节|字节|平均值|以独占方式分配给受监视应用程序进程的内存。|cloud/roleInstance|
|requests/duration|服务器响应时间|毫秒|平均值|从接收 HTTP 请求到完成响应发送之间的时间。|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|服务器请求数|计数|计数|已完成的 HTTP 请求计数。|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|失败的请求|计数|计数|标记为失败的 HTTP 请求的计数。 在大多数情况下这些请求的响应代码 >= 400 且不等于 401。|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|请求/速率|服务器请求速率|每秒计数|平均值|每秒服务器请求的速率|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|例外|计数|计数|所有未捕获异常的已合并计数。|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|浏览器异常|计数|计数|浏览器中所引发未捕获异常的计数。|无维度|
|exceptions/server|服务器异常|计数|计数|服务器应用程序中引发的未捕获的异常计数。|cloud/roleName, cloud/roleInstance|
|traces/count|跟踪|计数|计数|跟踪文档计数|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ServiceApiHit|服务 API 命中总计|计数|计数|服务 API 命中总数|ActivityType, ActivityName|
|ServiceApiLatency|总体服务 API 延迟|毫秒|平均值|服务 API 请求的总体延迟|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|服务 API 结果总计|计数|计数|服务 API 结果总数|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CacheUtilization|缓存使用率|百分比|平均值|群集范围内的使用率级别|None|
|QueryDuration|查询持续时间|毫秒|平均值|队列持续时间（秒）|查询状态|
|IngestionUtilization|引入使用率|百分比|平均值|群集中已使用引入槽的比率|None|
|KeepAlive|保持活动状态|计数|平均值|完整性检查表示群集响应查询|None|
|IngestionVolumeInMB|引入量 (MB)|计数|总计|已引入群集的数据总量 (MB)|数据库|
|IngestionLatencyInSeconds|引入延迟（秒）|秒数|平均值|从源（例如消息位于事件中心）到群集的引入时间（秒）|None|
|EventProcessedForEventHubs|处理的事件数（针对事件中心）|计数|总计|从事件中心引入时，由群集处理的事件数|None|
|IngestionResult|引入结果|计数|计数|引入操作的数量|状态|
|CPU|CPU|百分比|平均值|CPU 使用率级别|None|
| ContinuousExportNumOfRecordsExported | 连续导出中导出的记录数 | 计数 | 总计 | 导出操作期间写入的每个存储项目的已导出记录数  | None |
| ExportUtilization | 导出利用率 | 百分比 | 最大值 | 导出利用率 | None |
| ContinuousExportPendingCount | 连续导出挂起计数 | 计数 | 最大值 | 准备执行的挂起连续导出作业的数目 | None |
| ContinuousExportMaxLatenessMinutes | 连续导出最大迟到分钟数 | 计数 | 最大值 | 等待并准备执行的所有连续导出的最大时间（分钟） | None |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|使用情况|使用情况|计数|计数|API 调用计数|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|RunsStarted|已启动的运行数|计数|总计|已启动的工作流运行数目。|无维度|
|RunsCompleted|已完成的运行数|计数|总计|已完成的工作流运行数目。|无维度|
|RunsSucceeded|成功的运行数|计数|总计|成功的工作流运行数目。|无维度|
|RunsFailed|失败的运行数|计数|总计|失败的工作流运行数目。|无维度|
|RunsCancelled|已取消运行|计数|总计|已取消的工作流运行数。|无维度|
|RunLatency|运行延迟|秒数|平均值|已完成的工作流运行的延迟。|无维度|
|RunSuccessLatency|运行成功延迟|秒数|平均值|已成功的工作流运行的延迟。|无维度|
|RunThrottledEvents|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无维度|
|RunFailurePercentage|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无维度|
|ActionsStarted|启动的操作数 |计数|总计|已启动的工作流操作数目。|无维度|
|ActionsCompleted|完成的操作数 |计数|总计|已完成的工作流操作数目。|无维度|
|ActionsSucceeded|成功的操作数 |计数|总计|成功的工作流操作数目。|无维度|
|ActionsFailed|失败的操作数|计数|总计|失败的工作流操作数目。|无维度|
|ActionsSkipped|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无维度|
|ActionLatency|操作延迟 |秒数|平均值|已完成的工作流操作的延迟。|无维度|
|ActionSuccessLatency|操作成功延迟 |秒数|平均值|已成功的工作流操作的延迟。|无维度|
|ActionThrottledEvents|操作限制事件数|计数|总计|工作流操作限制事件数目。|无维度|
|TriggersStarted|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无维度|
|TriggersCompleted|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无维度|
|TriggersSucceeded|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无维度|
|TriggersFailed|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无维度|
|TriggersSkipped|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无维度|
|TriggersFired|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无维度|
|TriggerLatency|触发器延迟 |秒数|平均值|已完成的工作流触发器的延迟。|无维度|
|TriggerFireLatency|触发器激发延迟 |秒数|平均值|已激发的工作流触发器的延迟。|无维度|
|TriggerSuccessLatency|触发器成功延迟 |秒数|平均值|已成功的工作流触发器的延迟。|无维度|
|TriggerThrottledEvents|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无维度|
|BillableActionExecutions|计费的操作执行数|计数|总计|计费的工作流操作执行数目。|无维度|
|BillableTriggerExecutions|计费的触发器执行数|计数|总计|计费的工作流触发器执行数目。|无维度|
|TotalBillableExecutions|计费的执行总数|计数|总计|计费的工作流执行数目。|无维度|
|BillingUsageNativeOperation|本机操作执行的计费使用情况|计数|总计|已计费的本机操作执行次数。|无维度|
|BillingUsageStandardConnector|标准连接器执行的计费使用情况|计数|总计|已计费的标准连接器执行次数。|无维度|
|BillingUsageStorageConsumption|存储使用执行的计费使用情况|计数|总计|已计费的存储使用执行次数。|无维度|
|BillingUsageNativeOperation|本机操作执行的计费使用情况|计数|总计|已计费的本机操作执行次数。|无维度|
|BillingUsageStandardConnector|标准连接器执行的计费使用情况|计数|总计|已计费的标准连接器执行次数。|无维度|
|BillingUsageStorageConsumption|存储使用执行的计费使用情况|计数|总计|已计费的存储使用执行次数。|无维度|

## <a name="microsoftlogicintegrationserviceenvironments"></a>IntegrationServiceEnvironments/

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|RunsStarted|已启动的运行数|计数|总计|已启动的工作流运行数目。|无维度|
|RunsCompleted|已完成的运行数|计数|总计|已完成的工作流运行数目。|无维度|
|RunsSucceeded|成功的运行数|计数|总计|成功的工作流运行数目。|无维度|
|RunsFailed|失败的运行数|计数|总计|失败的工作流运行数目。|无维度|
|RunsCancelled|已取消运行|计数|总计|已取消的工作流运行数。|无维度|
|RunLatency|运行延迟|秒数|平均值|已完成的工作流运行的延迟。|无维度|
|RunSuccessLatency|运行成功延迟|秒数|平均值|已成功的工作流运行的延迟。|无维度|
|RunThrottledEvents|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无维度|
|RunStartThrottledEvents|运行启动限制事件|计数|总计|工作流运行启动限制事件的数目。|无维度|
|RunFailurePercentage|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无维度|
|ActionsStarted|启动的操作数 |计数|总计|已启动的工作流操作数目。|无维度|
|ActionsCompleted|完成的操作数 |计数|总计|已完成的工作流操作数目。|无维度|
|ActionsSucceeded|成功的操作数 |计数|总计|成功的工作流操作数目。|无维度|
|ActionsFailed|失败的操作数 |计数|总计|失败的工作流操作数目。|无维度|
|ActionsSkipped|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无维度|
|ActionLatency|操作延迟 |秒数|平均值|已完成的工作流操作的延迟。|无维度|
|ActionSuccessLatency|操作成功延迟 |秒数|平均值|已成功的工作流操作的延迟。|无维度|
|ActionThrottledEvents|操作限制事件数|计数|总计|工作流操作限制事件数目。|无维度|
|TriggersStarted|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无维度|
|TriggersCompleted|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无维度|
|TriggersSucceeded|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无维度|
|TriggersFailed|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无维度|
|TriggersSkipped|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无维度|
|TriggersFired|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无维度|
|TriggerLatency|触发器延迟 |秒数|平均值|已完成的工作流触发器的延迟。|无维度|
|TriggerFireLatency|触发器激发延迟 |秒数|平均值|已激发的工作流触发器的延迟。|无维度|
|TriggerSuccessLatency|触发器成功延迟 |秒数|平均值|已成功的工作流触发器的延迟。|无维度|
|TriggerThrottledEvents|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无维度|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|集成服务环境的工作流处理器使用情况|百分比|平均值|Integration service 环境的工作流处理器使用情况。|无维度|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|集成服务环境的工作流内存使用情况|百分比|平均值|Integration service 环境的工作流内存使用情况。|无维度|
|IntegrationServiceEnvironmentConnectorProcessorUsage|集成服务环境的连接器处理器使用情况|百分比|平均值|Integration service 环境的连接器处理器使用情况。|无维度|
|IntegrationServiceEnvironmentConnectorMemoryUsage|集成服务环境的连接器内存使用情况|百分比|平均值|Integration service 环境的连接器内存使用情况。|无维度|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|已完成运行|已完成运行|计数|总计|已成功为此工作区完成的运行数|场景|
|已开始运行|已开始运行|计数|总计|为此工作区启动的运行数|场景|
|失败的运行次数|失败的运行次数|计数|总计|此工作区的运行失败次数|场景|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|使用情况|使用情况|计数|计数|API 调用计数|ApiCategory、ApiName、ResultType、ResponseCode|
|可用性|可用性|百分比|平均值|Api 的可用性|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|AverageOtherLatency|其他平均延迟|ms/op|平均值|每个操作的其他平均延迟（非读取或写入）（毫秒）|无维度|
|AverageReadLatency|平均读取延迟|ms/op|平均值|每个操作的平均读取延迟（毫秒）|无维度|
|AverageTotalLatency|总平均延迟|ms/op|平均值|每个操作的总平均延迟（毫秒）|无维度|
|AverageWriteLatency|平均写入延迟|ms/op|平均值|每个操作的平均写入延迟（毫秒）|无维度|
|FilesystemOtherOps|文件系统其他操作数|ops|平均值|文件系统的其他操作（非读取或写入）数|无维度|
|FilesystemReadOps|文件系统读取操作数|ops|平均值|文件系统的读取操作数|无维度|
|FilesystemTotalOps|文件系统操作总数|ops|平均值|全部文件系统的操作总数|无维度|
|FilesystemWriteOps|文件系统写入操作数|ops|平均值|文件系统的写入操作数|无维度|
|IoBytesPerOtherOps|每个其他操作的 IO 字节数|bytes/op|平均值|每个其他操作（非读取或写入）的输入/输出字节数|无维度|
|IoBytesPerReadOps|每个读取操作的 IO 字节数|bytes/op|平均值|每个读取操作的输入/输出字节数|无维度|
|IoBytesPerTotalOps|经所有操作的每个操作的 IO 字节数|bytes/op|平均值|所有输入/输出字节操作的总数|无维度|
|IoBytesPerWriteOps|每个写入操作的 IO 字节数|bytes/op|平均值|每个写入操作的输入/输出字节数|无维度|
|OtherIops|其他 IOPS|operations/second|平均值|每秒其他输入/输出操作数|无维度|
|OtherThroughput|其他吞吐量|MBps|平均值|其他（非读取或写入）吞吐量（兆字节/秒）|无维度|
|ReadIops|读取 IOPS|operations/second|平均值|每秒读取输入/输出操作数|无维度|
|ReadThroughput|读取吞吐量|MBps|平均值|读取吞吐量（兆字节/秒）|无维度|
|TotalIops|IOPS 总数|operations/second|平均值|每秒所有输入/输出操作的总数|无维度|
|TotalThroughput|总吞吐量|MBps|平均值|所有吞吐量的总数（兆字节/秒）|无维度|
|VolumeAllocatedSize|卷分配大小|字节|平均值|分配的卷大小（非实际使用的字节数）|无维度|
|VolumeLogicalSize|卷逻辑大小|字节|平均值|卷的逻辑大小（所用字节数）|无维度|
|VolumeSnapshotSize|卷快照大小|字节|平均值|卷中所有快照的大小|无维度|
|WriteIops|写入 IOPS|operations/second|平均值|每秒写入输入/输出操作数|无维度|
|WriteThroughput|写入吞吐量|MBps|平均值|写入吞吐量（兆字节/秒）|无维度|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|卷池分配大小|字节|平均值|分配的池大小（非实际使用的字节数）|无维度|
|VolumePoolAllocatedUsed|使用的分配卷池|字节|平均值|分配池已使用的大小|无维度|
|VolumePoolTotalLogicalSize|卷池的总逻辑大小|字节|平均值|属于该池的所有卷的总逻辑大小|无维度|
|VolumePoolTotalSnapshotSize|卷池快照总大小|字节|平均值|池中所有快照的总数|无维度|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|BytesSentRate|发送的字节数|计数|总计|网络接口发送的字节数|无维度|
|BytesReceivedRate|接收的字节数|计数|总计|网络接口接收的字节数|无维度|
|PacketsSentRate|发送的数据包数|计数|总计|网络接口发送的数据包数|无维度|
|PacketsReceivedRate|已接收的数据包数|计数|总计|网络接口接收的数据包数|无维度|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|VipAvailability|数据路径可用性|计数|平均值|每个持续时间的负载均衡器数据路径的平均可用性|FrontendIPAddress, FrontendPort|
|DipAvailability|运行状况探测状态|计数|平均值|每个持续时间的负载均衡器运行状况探测的平均状态|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|字节计数|计数|总计|时间段内传输的字节总数|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|数据包计数|计数|总计|时间段内传输的数据包总数|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN 计数|计数|总计|时间段内传输的 SYN 数据包总数|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT 连接计数|计数|总计|时间段内创建的新 SNAT 连接的总数|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|已分配的 SNAT 端口数（预览）|计数|总计|时间段内已分配的 SNAT 端口总数|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|已使用的 SNAT 端口数（预览）|计数|总计|时间段内 SNAT 端口的总数|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|QueryVolume|查询量|计数|总计|为 DNS 区域提供服务的查询数|无维度|
|RecordSetCount|记录集计数|计数|最大值|DNS 区域中的记录集数|无维度|
|RecordSetCapacityUtilization|记录集容量使用率|百分比|最大值|DNS 区域利用的记录集容量的百分比|无维度|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|PacketsInDDoS|入站数据包 DDoS|每秒计数|最大值|入站数据包 DDoS|无维度|
|PacketsDroppedDDoS|丢弃的入站数据包 DDoS|每秒计数|最大值|丢弃的入站数据包 DDoS|无维度|
|PacketsForwardedDDoS|转发的入站数据包 DDoS|每秒计数|最大值|转发的入站数据包 DDoS|无维度|
|TCPPacketsInDDoS|入站 TCP 数据包 DDoS|每秒计数|最大值|入站 TCP 数据包 DDoS|无维度|
|TCPPacketsDroppedDDoS|丢弃的入站 TCP 数据包 DDoS|每秒计数|最大值|丢弃的入站 TCP 数据包 DDoS|无维度|
|TCPPacketsForwardedDDoS|转发的入站 TCP 数据包 DDoS|每秒计数|最大值|转发的入站 TCP 数据包 DDoS|无维度|
|UDPPacketsInDDoS|入站 UDP 数据包 DDoS|每秒计数|最大值|入站 UDP 数据包 DDoS|无维度|
|UDPPacketsDroppedDDoS|丢弃的入站 UDP 数据包 DDoS|每秒计数|最大值|丢弃的入站 UDP 数据包 DDoS|无维度|
|UDPPacketsForwardedDDoS|转发的入站 UDP 数据包 DDoS|每秒计数|最大值|转发的入站 UDP 数据包 DDoS|无维度|
|BytesInDDoS|入站字节 DDoS|每秒字节数|最大值|入站字节 DDoS|无维度|
|BytesDroppedDDoS|丢弃的入站字节 DDoS|每秒字节数|最大值|丢弃的入站字节 DDoS|无维度|
|BytesForwardedDDoS|转发的入站字节 DDoS|每秒字节数|最大值|转发的入站字节 DDoS|无维度|
|TCPBytesInDDoS|入站 TCP 字节 DDoS|每秒字节数|最大值|入站 TCP 字节 DDoS|无维度|
|TCPBytesDroppedDDoS|丢弃的入站 TCP 字节 DDoS|每秒字节数|最大值|丢弃的入站 TCP 字节 DDoS|无维度|
|TCPBytesForwardedDDoS|转发的入站 TCP 字节 DDoS|每秒字节数|最大值|转发的入站 TCP 字节 DDoS|无维度|
|UDPBytesInDDoS|入站 UDP 字节 DDoS|每秒字节数|最大值|入站 UDP 字节 DDoS|无维度|
|UDPBytesDroppedDDoS|丢弃的入站 UDP 字节 DDoS|每秒字节数|最大值|丢弃的入站 UDP 字节 DDoS|无维度|
|UDPBytesForwardedDDoS|转发的入站 UDP 字节 DDoS|每秒字节数|最大值|转发的入站 UDP 字节 DDoS|无维度|
|IfUnderDDoSAttack|是否遭到 DDoS 攻击|计数|最大值|是否遭到 DDoS 攻击|无维度|
|DDoSTriggerTCPPackets|触发 DDoS 缓解的入站 TCP 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 TCP 数据包|无维度|
|DDoSTriggerUDPPackets|触发 DDoS 缓解的入站 UDP 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 UDP 数据包|无维度|
|DDoSTriggerSYNPackets|触发 DDoS 缓解的入站 SYN 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 SYN 数据包|无维度|
|VipAvailability|数据路径可用性|计数|平均值|每个持续时间的 IP 地址的平均可用性|Port|
|ByteCount|字节计数|计数|总计|时间段内传输的字节总数|Port、Direction|
|PacketCount|数据包计数|计数|总计|时间段内传输的数据包总数|Port、Direction|
|SynCount|SYN 计数|计数|总计|时间段内传输的 SYN 数据包总数|Port、Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ApplicationRuleHit|应用程序规则命中计数|计数|总计|应用程序规则命中次数|状态、原因、协议|
|NetworkRuleHit|网络规则命中计数|计数|总计|命中网络规则的次数|状态、原因、协议|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|吞吐量|吞吐量|每秒字节数|总计|应用程序网关每秒提供的字节数|无维度|
|UnhealthyHostCount|不正常的主机计数|计数|平均值|不正常的后端主机数|BackendSettingsPool|
|HealthyHostCount|正常的主机计数|计数|平均值|正常的后端主机数|BackendSettingsPool|
|TotalRequests|请求总数|计数|总计|应用程序网关已提供服务的成功请求计数|BackendSettingsPool|
|FailedRequests|失败的请求数|计数|总计|应用程序网关已提供服务的失败请求计数|BackendSettingsPool|
|ResponseStatus|响应状态|计数|总计|应用程序网关返回的 Http 响应状态|HttpStatusGroup|
|CurrentConnections|当前连接数|计数|总计|使用应用程序网关建立的当前连接计数|无维度|
|CapacityUnits|当前容量单位|计数|平均值|使用的容量单位|无维度|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|AverageBandwidth|网关 S2S 带宽|每秒字节数|平均值|网关站点到站点的平均带宽（字节/秒）|无维度|
|P2SBandwidth|网关 P2S 带宽|每秒字节数|平均值|网关点到站点的平均带宽（字节/秒）|无维度|
|P2SConnectionCount|P2S 连接计数|计数|最大值|网关的点到站点连接计数|协议|
|TunnelAverageBandwidth|隧道带宽|每秒字节数|平均值|隧道带宽平均值（以字节/秒为单位）|ConnectionName、RemoteIP|
|TunnelEgressBytes|隧道流出字节|字节|总计|隧道的传出字节数|ConnectionName、RemoteIP|
|TunnelIngressBytes|隧道流入字节|字节|总计|隧道的传入字节数|ConnectionName、RemoteIP|
|TunnelEgressPackets|隧道流出数据包|计数|总计|隧道的传出数据包计数|ConnectionName、RemoteIP|
|TunnelIngressPackets|隧道流入数据包|计数|总计|隧道的传入数据包计数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|隧道流出 TS 不匹配数据包丢弃|计数|总计|来自隧道的不匹配流量选择器的传出数据包丢弃|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|隧道流入 TS 不匹配数据包丢弃|计数|总计|来自隧道的不匹配流量选择器的传入数据包丢弃|ConnectionName、RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无维度|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无维度|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无维度|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无维度|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|QpsByEndpoint|按终结点返回的查询|计数|总计|给定时间范围内返回流量管理器终结点的次数|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|按终结点显示的终结点状态|计数|最大值|如果终结点的探测状态为“已启用”，则值为 1；否则，值为 0。|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ProbesFailedPercent|失败的探测百分比|百分比|平均值|失败的连接监视探测百分比|无维度|
|AverageRoundtripMs|平均往返时间（毫秒）|毫秒|平均值|源和目标之间发送的连接监视探测的平均网络往返时间（毫秒）|无维度|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|RequestCount|请求计数|计数|总计|HTTP/S 代理提供客户端请求数|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|请求大小|字节|总计|以请求的形式从客户端发送到 HTTP/S 代理的字节数|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|响应大小|字节|总计|以响应的形式从 HTTP/S 代理发送到客户端的字节数|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|后端请求计数|计数|总计|从 HTTP/S 代理发送到后端的请求数|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|后端请求延迟|毫秒|平均值|自请求由 HTTP/S 代理发送到后端直至 HTTP/S 代理从后端收到最后一个响应字节为止，所计算的时间|后端|
|TotalLatency|总延迟|毫秒|平均值|自请求由 HTTP/S 代理接收后到客户端确认来自 HTTP/S 代理的最后一个响应字节止，所计算的时间|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|后端运行状况百分比|百分比|平均值|从 HTTP/S 代理到后端，成功运行状况探测的百分比|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web 应用防火墙请求计数|计数|总计|Web 应用程序防火墙所处理的客户端请求数|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|registration.all|注册操作|计数|总计|所有成功的注册操作（创建、更新、查询和删除）的计数。 |无维度|
|registration.create|注册创建操作数|计数|总计|所有成功的注册创建操作的计数。|无维度|
|registration.update|注册更新操作数|计数|总计|所有成功的注册更新操作的计数。|无维度|
|registration.get|注册读取操作数|计数|总计|所有成功的注册查询操作的计数。|无维度|
|registration.delete|注册删除操作数|计数|总计|所有成功的注册删除操作的计数。|无维度|
|incoming|传入消息数|计数|总计|所有成功的发送 API 调用的计数。 |无维度|
|incoming.scheduled|已发送的已安排推送通知数|计数|总计|已取消计划推送通知|无维度|
|incoming.scheduled.cancel|已取消计划推送通知|计数|总计|已取消计划推送通知|无维度|
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
|outgoing.apns.badchannel|APNS 坏通道错误|计数|总计|因令牌无效而失败的推送计数（APNS 二进制协议状态代码：8。 APNS HTTP 协议状态代码：400，其中包含 "BadDeviceToken"）。|无维度|
|outgoing.apns.expiredchannel|APNS 已过期通道错误|计数|总计|由 APNS 反馈通道致其无效的令牌的计数。|无维度|
|outgoing.apns.invalidnotificationsize|APNS 无效通知大小错误|计数|总计|因负载过大而失败的推送计数（APNS 二进制协议状态代码：7）。|无维度|
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

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|Average_% Free Inodes|可用 Inode 百分比|计数|平均值|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|可用空间百分比|计数|平均值|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Inodes|已用 Inode 百分比|计数|平均值|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Space|已用空间百分比|计数|平均值|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Read Bytes/sec|磁盘读取字节数/秒|计数|平均值|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|磁盘读取数/秒|计数|平均值|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|磁盘传输数/秒|计数|平均值|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Write Bytes/sec|磁盘写入字节数/秒|计数|平均值|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|磁盘写入数/秒|计数|平均值|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|可用 MB 数|计数|平均值|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Logical Disk Bytes/sec|逻辑磁盘字节数/秒|计数|平均值|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Memory|可用内存百分比|计数|平均值|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Swap Space|可用交换空间百分比|计数|平均值|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Memory|已用内存百分比|计数|平均值|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Swap Space|已用交换空间百分比|计数|平均值|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Memory|可用内存 MB 数|计数|平均值|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Swap|可用交换空间 MB 数|计数|平均值|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Reads/sec|页面读取数/秒|计数|平均值|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Writes/sec|页面写入数/秒|计数|平均值|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pages/sec|页面数/秒|计数|平均值|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used MBytes Swap Space|已用交换空间 MB 数|计数|平均值|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory MBytes|已用内存 MB 数|计数|平均值|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Transmitted|已传输的字节数总计|计数|平均值|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Received|已接收的字节数总计|计数|平均值|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes|字节数总计|计数|平均值|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Transmitted|已传输的包数总计|计数|平均值|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Received|已接收的包数总计|计数|平均值|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Rx Errors|Rx 错误数总计|计数|平均值|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Tx Errors|Tx 错误数总计|计数|平均值|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Collisions|冲突数总计|计数|平均值|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/读取|Avg. Disk sec/Read|计数|平均值|Average_Avg. 磁盘秒数/读取|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/传输|Avg. Disk sec/Transfer|计数|平均值|Average_Avg. 磁盘秒数/传输|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/写入|Avg. Disk sec/Write|计数|平均值|Average_Avg. 磁盘秒数/写入|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Physical Disk Bytes/sec|物理磁盘字节数/秒|计数|平均值|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct Privileged Time|特权时间百分比|计数|平均值|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct User Time|用户时间百分比|计数|平均值|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory kBytes|已用内存 KB 数|计数|平均值|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Virtual Shared Memory|虚拟共享内存|计数|平均值|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% DPC Time|DPC 时间百分比|计数|平均值|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Idle Time|空闲时间百分比|计数|平均值|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Interrupt Time|中断时间百分比|计数|平均值|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% IO Wait Time|IO 等待时间百分比|计数|平均值|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Nice Time|良好时间百分比|计数|平均值|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Privileged Time|特权时间百分比|计数|平均值|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|处理器时间百分比|计数|平均值|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% User Time|用户时间百分比|计数|平均值|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Physical Memory|可用物理内存|计数|平均值|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Space in Paging Files|分页文件中的可用空间|计数|平均值|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Virtual Memory|可用虚拟内存|计数|平均值|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processes|流程|计数|平均值|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Size Stored In Paging Files|分页文件中存储的大小|计数|平均值|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Uptime|运行时间|计数|平均值|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Users|用户|计数|平均值|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/读取|Avg. Disk sec/Read|计数|平均值|Average_Avg. 磁盘秒数/读取|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/写入|Avg. Disk sec/Write|计数|平均值|Average_Avg. 磁盘秒数/写入|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Current Disk Queue Length|当前的磁盘队列长度|计数|平均值|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|磁盘读取数/秒|计数|平均值|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|磁盘传输数/秒|计数|平均值|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|磁盘写入数/秒|计数|平均值|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|可用 MB 数|计数|平均值|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|可用空间百分比|计数|平均值|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes|可用兆字节数|计数|平均值|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Committed Bytes In Use|提交的在用字节数百分比|计数|平均值|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Received/sec|收到的字节数/秒|计数|平均值|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Sent/sec|发送的字节数/秒|计数|平均值|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Total/sec|字节总数/秒|计数|平均值|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|处理器时间百分比|计数|平均值|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processor Queue Length|处理器队列长度|计数|平均值|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|检测信号|检测信号|计数|总计|检测信号|Computer、OSType、Version、SourceComputerId|
|更新|更新|计数|平均值|更新|Computer、Product、Classification、UpdateState、Optional、Approved|
|事件|事件|计数|平均值|事件|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|QueryDuration|查询持续时间|毫秒|平均值|上一个间隔的 DAX 查询持续时间|无维度|
|QueryPoolJobQueueLength|线程: 查询池作业队列长度|计数|平均值|查询线程池队列中的作业数。|无维度|
|qpu_high_utilization_metric|QPU 高利用率|计数|总计|最后一分钟内 QPU 高利用率，1 为高 QPU 利用率，反之为 0|无维度|
|memory_metric|内存|字节|平均值|内存。 A1 的范围为 0-3 GB，A2 为 0-5 GB，A3 为 0-10 GB，A4 为 0-25 GB，A5 为 0-50 GB，A6 为 0-100 GB|无维度|
|memory_thrashing_metric|内存抖动|百分比|平均值|平均内存抖动。|无维度|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|计数|总计|Microsoft.Relay 的侦听程序成功连接数。|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|计数|总计|关于 Microsoft.Relay 的侦听程序连接的客户端错误。|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|计数|总计|关于 Microsoft.Relay 的侦听程序连接的服务器错误。|EntityName|
|SenderConnections-Success|SenderConnections-Success|计数|总计|Microsoft.Relay 的发送方成功连接数。|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|计数|总计|关于 Microsoft.Relay 的发送方连接的客户端错误。|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|计数|总计|关于 Microsoft.Relay 的发送方连接的服务器错误。|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|计数|总计|Microsoft.Relay 的侦听程序连接总数。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|计数|总计|Microsoft.Relay 的发送方连接请求总数。|EntityName|
|ActiveConnections|ActiveConnections|计数|总计|Microsoft.Relay 的活动连接总数。|EntityName|
|ActiveListeners|ActiveListeners|计数|总计|Microsoft.Relay 的活动侦听程序总数。|EntityName|
|BytesTransferred|BytesTransferred|计数|总计|Microsoft.Relay 的已传输字节总数。|EntityName|
|ListenerDisconnects|ListenerDisconnects|计数|总计|Microsoft.Relay 的侦听程序断开连接总数。|EntityName|
|SenderDisconnects|SenderDisconnects|计数|总计|Microsoft.Relay 的发送方断开连接总数。|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|SearchLatency|搜索延迟|秒数|平均值|搜索服务的平均搜索延迟|无维度|
|SearchQueriesPerSecond|每秒搜索查询数|每秒计数|平均值|搜索服务的每秒搜索查询数|无维度|
|ThrottledSearchQueriesPercentage|限制的搜索查询百分比|百分比|平均值|为搜索服务限制的搜索查询百分比|无维度|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的请求数（预览版）|计数|总计|命名空间的成功请求总数（预览版）|EntityName|
|ServerErrors|服务器错误数。 （预览）|计数|总计|Microsoft.ServiceBus 的服务器错误数。 （预览）|EntityName|
|UserErrors|用户错误数。 （预览）|计数|总计|Microsoft.ServiceBus 的用户错误数。 （预览）|EntityName|
|ThrottledRequests|限制的请求数。 （预览）|计数|总计|Microsoft.ServiceBus 限制的请求数。 （预览）|EntityName|
|IncomingRequests|传入的请求数（预览版）|计数|总计|Microsoft.ServiceBus 传入的请求数。 （预览）|EntityName|
|IncomingMessages|传入的消息数（预览版）|计数|总计|Microsoft.ServiceBus 传入的消息数。 （预览）|EntityName|
|OutgoingMessages|传出的消息数（预览版）|计数|总计|Microsoft.ServiceBus 传出的消息数。 （预览）|EntityName|
|ActiveConnections|ActiveConnections（预览版）|计数|总计|Microsoft.ServiceBus 的活动连接总数。 （预览）|无维度|
|大小|大小（预览版）|字节|平均值|队列/主题的大小（以字节为单位）。 （预览）|EntityName|
|消息数|队列/主题中的消息计数。 （预览）|计数|平均值|队列/主题中的消息计数。 （预览）|EntityName|
|ActiveMessages|队列/主题中的活动消息计数。 （预览）|计数|平均值|队列/主题中的活动消息计数。 （预览）|EntityName|
|DeadletteredMessages|队列/主题中的死信消息计数。 （预览）|计数|平均值|队列/主题中的死信消息计数。 （预览）|EntityName|
|ScheduledMessages|队列/主题中计划消息的计数。 （预览）|计数|平均值|队列/主题中计划消息的计数。 （预览）|EntityName|
|CPUXNS|每个命名空间的 CPU 使用率|百分比|最大值|服务总线高级命名空间 CPU 使用率指标|无维度|
|WSXNS|每个命名空间的内存使用量|百分比|最大值|服务总线高级命名空间内存使用率指标|无维度|

## <a name="microsoftservicefabricmeshapplications"></a>ServiceFabricMesh/应用程序

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|计数|平均值|在 millicores 中分配给此容器的 Cpu|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|字节|平均值|分配给此容器的内存（MB）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualCpu|ActualCpu|计数|平均值|Millicores 中的实际 CPU 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|ActualMemory|字节|平均值|实际内存使用率（MB）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|Cpu 利用率|Cpu 利用率|百分比|平均值|此容器的 CPU 利用率（百分比） AllocatedCpu|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|百分比|平均值|此容器的 CPU 利用率（百分比） AllocatedCpu|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|计数|平均值|Service Fabric 网格应用程序的状态|ApplicationName，状态|
|ServiceStatus|ServiceStatus|计数|平均值|Service Fabric 网格应用程序中服务的运行状况状态|ApplicationName、Status、ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|计数|平均值|Service Fabric 网格应用程序中服务副本的运行状况状态|ApplicationName，Status，ServiceName，ServiceReplicaName|
|容器状态|容器状态|计数|平均值|Service Fabric 网格应用程序中容器的状态|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|RestartCount|RestartCount|计数|平均值|Service Fabric 网格应用程序中的容器重启计数|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ConnectionCount|连接计数|计数|最大值|用户连接量。|终结点|
|MessageCount|消息计数|计数|总计|消息总量。|无维度|
|InboundTraffic|入站流量|字节|总计|服务的入站流量|无维度|
|OutboundTraffic|出站流量|字节|总计|服务的出站流量|无维度|
|UserErrors|用户错误数|百分比|最大值|用户错误数的百分比|无维度|
|SystemErrors|系统错误数|百分比|最大值|系统错误数的百分比|无维度|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|allocated_data_storage|已分配数据空间|字节|平均值|已分配数据空间。 不适用于数据仓库。|无维度|
|app_cpu_billed|应用 CPU 计费|计数|总计|应用 CPU 计费。 适用于无服务器数据库。|无维度|
|app_cpu_percent|应用 CPU 百分比|百分比|平均值|应用 CPU 百分比。 适用于无服务器数据库。|无维度|
|app_memory_percent|应用内存已用百分比|百分比|平均值|应用内存已用百分比。 适用于无服务器数据库。|无维度|
|blocked_by_firewall|被防火墙阻止|计数|总计|被防火墙阻止|无维度|
|cache_hit_percent|缓存命中百分比|百分比|最大值|缓存命中百分比。 仅适用于数据仓库。|无维度|
|cache_used_percent|缓存使用百分比|百分比|最大值|已用缓存百分比。 仅适用于数据仓库。|无维度|
|connection_failed|失败的连接数|计数|总计|失败的连接数|无维度|
|connection_successful|成功的连接数|计数|总计|成功的连接数|无维度|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|cpu_limit|CPU 限制|计数|平均值|CPU 限制。 适用于基于 vCore 的数据库。|无维度|
|cpu_used|使用的 CPU|计数|平均值|使用的 CPU。 适用于基于 vCore 的数据库。|无维度|
|deadlock|死锁数|计数|总计|死锁. 不适用于数据仓库。|无维度|
|dtu_limit|DTU 限制|计数|平均值|DTU 限制。 适用于基于 DTU 的数据库。|无维度|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比。 适用于基于 DTU 的数据库。|无维度|
|dtu_used|已用的 DTU|计数|平均值|使用的 DTU。 适用于基于 DTU 的数据库。|无维度|
|dw_cpu_percent|DW 节点级别 CPU 百分比|百分比|平均值|DW 节点级别 CPU 百分比|DwLogicalNodeId|
|dw_physical_data_read_percent|DW 节点级别数据 IO 百分比|百分比|平均值|DW 节点级别数据 IO 百分比|DwLogicalNodeId|
|dwu_consumption_percent|DWU 百分比|百分比|最大值|DWU 百分比。 仅适用于数据仓库。|无维度|
|dwu_limit|DWU 限制|计数|最大值|DWU 限制。 仅适用于数据仓库。|无维度|
|dwu_used|已用的 DWU|计数|最大值|使用的 DWU。 仅适用于数据仓库。|无维度|
|local_tempdb_usage_percent|本地 tempdb 百分比|百分比|平均值|本地 tempdb 百分比。 仅适用于数据仓库。|无维度|
|log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比。 不适用于数据仓库。|无维度|
|physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无维度|
|sessions_percent|会话百分比|百分比|平均值|会话百分比。 不适用于数据仓库。|无维度|
|sqlserver_process_core_percent|SQL Server 处理核心百分比|百分比|最大值|此指标是占位符，此时不填充。|无维度|
|sqlserver_process_memory_percent|SQL Server 处理内存百分比|百分比|最大值|此指标是占位符，此时不填充。|无维度|
|存储|已用数据空间|字节|最大值|数据库总大小。 不适用于数据仓库。|无维度|
|storage_percent|已用数据空间百分比|百分比|最大值|数据库大小百分比。 不适用于数据仓库或超大规模数据库。|无维度|
|tempdb_data_size|Tempdb 数据文件大小（Kb）|计数|最大值|Tempdb 数据文件大小（Kb）。 不适用于数据仓库。 对于基于 DTU 的购买模型，此指标适用于使用 vCore 购买模型的数据库或 100 DTU 和更高版本。|无维度|
|tempdb_log_size|Tempdb 日志文件大小（Kb）|计数|最大值|Tempdb 日志文件大小（Kb）。 不适用于数据仓库。 对于基于 DTU 的购买模型，此指标适用于使用 vCore 购买模型的数据库或 100 DTU 和更高版本。|无维度|
|tempdb_log_used_percent|使用的 Tempdb 百分比日志|百分比|最大值|使用的 Tempdb 百分比日志。 不适用于数据仓库。 对于基于 DTU 的购买模型，此指标适用于使用 vCore 购买模型的数据库或 100 DTU 和更高版本。|无维度|
|workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比。 不适用于数据仓库。|无维度|
|xtp_storage_percent|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比。 不适用于数据仓库。|无维度|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|allocated_data_storage|已分配数据空间|字节|平均值|已分配数据空间|无维度|
|allocated_data_storage_percent|已分配的数据空间百分比|百分比|最大值|已分配的数据空间百分比|无维度|
|cpu_limit|CPU 限制|计数|平均值|CPU 限制。 适用于基于 vCore 的弹性池。|无维度|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|cpu_used|使用的 CPU|计数|平均值|使用的 CPU。 适用于基于 vCore 的弹性池。|无维度|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比。 适用于基于 DTU 的弹性池。|无维度|
|eDTU_limit|eDTU 限制|计数|平均值|eDTU 限制。 适用于基于 DTU 的弹性池。|无维度|
|eDTU_used|已用的 eDTU|计数|平均值|使用的 eDTU。 适用于基于 DTU 的弹性池。|无维度|
|log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比|无维度|
|physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无维度|
|sessions_percent|会话百分比|百分比|平均值|会话百分比|无维度|
|storage_limit|数据最大大小|字节|平均值|存储限制|无维度|
|storage_percent|已用数据空间百分比||百分比|平均值|存储百分比|无维度|
|storage_used|已用数据空间|字节|平均值|已用存储|无维度|
|sqlserver_process_core_percent|SQL Server 处理核心百分比|百分比|最大值|此指标是占位符，此时不填充。|无维度|
|sqlserver_process_memory_percent|SQL Server 处理内存百分比|百分比|最大值|此指标是占位符，此时不填充。|无维度|
|tempdb_data_size|Tempdb 数据文件大小（Kb）|计数|最大值|Tempdb 数据文件大小（Kb）。 不适用于数据仓库。 对于基于 DTU 的购买模型，此指标适用于使用 vCore 购买模型的数据库或 100 DTU 和更高版本。|无维度|
|tempdb_log_size|Tempdb 日志文件大小（Kb）|计数|最大值|Tempdb 日志文件大小（Kb）。 不适用于数据仓库。 对于基于 DTU 的购买模型，此指标适用于使用 vCore 购买模型的数据库或 100 DTU 和更高版本。|无维度|
|tempdb_log_used_percent|使用的 Tempdb 百分比日志|百分比|最大值|使用的 Tempdb 百分比日志。 不适用于数据仓库。 对于基于 DTU 的购买模型，此指标适用于使用 vCore 购买模型的数据库或 100 DTU 和更高版本。|无维度|
|workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比|无维度|
|xtp_storage_percent|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比|无维度|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|avg_cpu_percent|CPU 平均百分比|百分比|平均值|CPU 平均百分比|无维度|
|io_bytes_read|已读取的 IO 字节数|字节|平均值|已读取的 IO 字节数|无维度|
|io_requests|IO 请求计数|计数|平均值|IO 请求计数|无维度|
|io_bytes_written|已写入的 IO 字节数|字节|平均值|已写入的 IO 字节数|无维度|
|reserved_storage_mb|预留的存储空间|计数|平均值|预留的存储空间|无维度|
|storage_space_used_mb|已使用的存储空间|计数|平均值|已使用的存储空间|无维度|
|virtual_core_count|虚拟核心计数|计数|平均值|虚拟核心计数|无维度|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|UsedCapacity|已用容量|字节|平均值|帐户使用的容量|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|入口|入口|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType, ApiName, Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|字节|平均值|存储帐户的 Blob 服务使用的存储量（以字节为单位）。|BlobType，层|
|BlobCount|Blob 计数|计数|总计|存储帐户的 Blob 服务中的 Blob 数。|/BlobType|       |BlobCount|Blob 计数|计数|平均值|存储帐户的 Blob 服务中的 Blob 数。|BlobType，层|
|ContainerCount|Blob 容器计数|计数|平均值|存储帐户的 Blob 服务中的容器数。|无维度|
|IndexCapacity|索引容量|字节|平均值|ADLS Gen2 （分层）索引使用的存储量（以字节为单位）。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|入口|入口|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType, ApiName, Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|FileCapacity|文件容量|字节|平均值|存储帐户的文件服务使用的存储量（以字节为单位）。|无维度|
|FileCount|文件计数|计数|平均值|存储帐户的文件服务中的文件数。|无维度|
|FileShareCount|文件共享计数|计数|平均值|存储帐户的文件服务中的文件共享数。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|入口|入口|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType, ApiName, Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|QueueCapacity|队列容量|字节|平均值|存储帐户的队列服务使用的存储量（以字节为单位）。|无维度|
|QueueCount|队列计数|计数|平均值|存储帐户的队列服务中的队列数。|无维度|
|QueueMessageCount|队列消息计数|计数|平均值|存储帐户的队列服务中的队列消息的大致数目。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|入口|入口|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType, ApiName, Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|TableCapacity|表容量|字节|平均值|存储帐户的表服务使用的存储量（以字节为单位）。|无维度|
|TableCount|表计数|计数|平均值|存储帐户的表服务中的表数。|无维度|
|TableEntityCount|表实体计数|计数|平均值|存储帐户的表服务中的表实体数。|无维度|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|入口|入口|字节|总计|流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。|GeoType, ApiName, Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>storagesync.sys/storageSyncServices

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ServerSyncSessionResult|同步会话结果|计数|平均值|每次服务器终结点成功完成与云终结点的同步会话时记录值1的指标|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|已同步文件|计数|总计|同步的文件计数|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|未同步的文件|计数|总计|未能同步的文件计数|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|同步的字节数|字节|总计|为同步会话传输的总文件大小|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|服务器联机状态|计数|最大值|每次已注册的服务器使用云终结点成功记录检测信号时记录值1的指标|ServerName|
|StorageSyncRecallIOTotalSizeBytes|云分层回调|字节|总计|服务器撤回的数据的总大小|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|ResourceUtilization|流单元利用率 %|百分比|最大值|流单元利用率 %|LogicalName、PartitionId|
|InputEvents|输入事件数|计数|总计|输入事件数|LogicalName、PartitionId|
|InputEventBytes|输入事件字节数|字节|总计|输入事件字节数|LogicalName、PartitionId|
|LateInputEvents|延迟输入事件数|计数|总计|延迟输入事件数|LogicalName、PartitionId|
|OutputEvents|输出事件数|计数|总计|输出事件数|LogicalName、PartitionId|
|ConversionErrors|数据转换错误数|计数|总计|数据转换错误数|LogicalName、PartitionId|
|Errors|运行时错误|计数|总计|运行时错误|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|失序事件数|计数|总计|失序事件数|LogicalName、PartitionId|
|AMLCalloutRequests|函数请求数|计数|总计|函数请求数|LogicalName、PartitionId|
|AMLCalloutFailedRequests|失败的函数请求数|计数|总计|失败的函数请求数|LogicalName、PartitionId|
|AMLCalloutInputEvents|函数事件数|计数|总计|函数事件数|LogicalName、PartitionId|
|DeserializationError|输入反序列化错误|计数|总计|输入反序列化错误|LogicalName、PartitionId|
|EarlyInputEvents|早期输入事件数|计数|总计|早期输入事件数|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|水印延迟|秒数|最大值|水印延迟|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|积压的输入事件数|计数|最大值|积压的输入事件数|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|收到的输入源数|计数|总计|收到的输入源数|LogicalName、PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|IngressReceivedMessages|入口收到的消息数|计数|总计|从所有事件中心或 IoT 中心事件源读取的消息的计数|无维度|
|IngressReceivedInvalidMessages|入口收到的无效消息数|计数|总计|从所有事件中心或 IoT 中心事件源读取的无效消息的计数|无维度|
|IngressReceivedBytes|入口收到的字节数|字节|总计|从所有事件源读取的字节数的计数|无维度|
|IngressStoredBytes|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|无维度|
|IngressStoredEvents|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|无维度|
|IngressReceivedMessagesTimeLag|入口收到的消息数时间延迟|秒数|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|无维度|
|IngressReceivedMessagesCountLag|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|无维度|
|WarmStorageMaxProperties|热存储最大属性|计数|最大值|用于 S1/S2 SKU 的环境允许的最大属性数，以及 PAYG SKU 的热商店允许的最大属性数|无维度|
|WarmStorageUsedProperties|热存储使用属性 |计数|最大值|用于 S1/S2 SKU 的环境使用的属性数，以及用于 PAYG SKU 的热商店使用的属性数|无维度|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|IngressReceivedMessages|入口收到的消息数|计数|总计|从事件源中读取的消息的计数|无维度|
|IngressReceivedInvalidMessages|入口收到的无效消息数|计数|总计|从事件源中读取的无效消息的计数|无维度|
|IngressReceivedBytes|入口收到的字节数|字节|总计|从事件源读取的字节数的计数|无维度|
|IngressStoredBytes|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|无维度|
|IngressStoredEvents|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|无维度|
|IngressReceivedMessagesTimeLag|入口收到的消息数时间延迟|秒数|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|无维度|
|IngressReceivedMessagesCountLag|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|无维度|
|WarmStorageMaxProperties|热存储最大属性|计数|最大值|用于 S1/S2 SKU 的环境允许的最大属性数，以及 PAYG SKU 的热商店允许的最大属性数|无维度|
|WarmStorageUsedProperties|热存储使用属性 |计数|最大值|用于 S1/S2 SKU 的环境使用的属性数，以及用于 PAYG SKU 的热商店使用的属性数|无维度|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>VMwareCloudSimple/virtualMachines

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|每秒字节数|平均值|由于在采样期间读取操作而导致的平均磁盘吞吐量。|无维度|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|每秒字节数|平均值|由于在采样期间写入操作而导致的平均磁盘吞吐量。|无维度|
|磁盘读取字节数|磁盘读取字节数|字节|总计|由于在采样期间读取操作而导致的总磁盘吞吐量。|无维度|
|磁盘写入字节数|磁盘写入字节数|字节|总计|由于在采样期间写入操作而导致的总磁盘吞吐量。|无维度|
|DiskReadOperations|磁盘读取操作|计数|总计|上一个采样期间内的 IO 读取操作数。 请注意，这些操作可能会改变大小。|无维度|
|DiskWriteOperations|磁盘写入操作|计数|总计|上一个采样期间内的 IO 写入操作数。 请注意，这些操作可能会改变大小。|无维度|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|上一个采样期间内的平均 IO 读取操作数。 请注意，这些操作可能会改变大小。|无维度|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|上一个采样期间内的平均 IO 写入操作数。 请注意，这些操作可能会改变大小。|无维度|
|DiskReadLatency|磁盘读取延迟|毫秒|平均值|总读取延迟。 设备和内核读取延迟的总和。|无维度|
|DiskWriteLatency|磁盘写入延迟|毫秒|平均值|写入延迟总计。 设备和内核写入延迟的总和。|无维度|
|NetworkInBytesPerSecond|网络字节数/秒|每秒字节数|平均值|接收流量的平均网络吞吐量。|无维度|
|NetworkOutBytesPerSecond|网络输出字节数/秒|每秒字节数|平均值|传输的流量的平均网络吞吐量。|无维度|
|网络传入|网络传入|字节|总计|接收的流量的总网络吞吐量。|无维度|
|网络传出|网络传出|字节|总计|传输的流量的总网络吞吐量。|无维度|
|MemoryUsed|使用的内存|字节|平均值|VM 正在使用的计算机内存量。|无维度|
|MemoryGranted|已授予内存|字节|平均值|主机向 VM 授予的内存量。 在此之前，不会向主机授予内存，直到 VMkernel 需要内存时，才会将内存向外换出或 ballooned。|无维度|
|MemoryActive|活动内存|字节|平均值|过去小时间窗口中 VM 使用的内存量。 这是 VM 当前所需的内存量 "true"。 其他未使用的内存可能会被换出或 ballooned，而不会影响来宾的性能。|无维度|
|CPU 百分比|CPU 百分比|百分比|平均值|CPU 使用率。 此值报告了100%，表示系统上的所有处理器核心。 例如，使用四核系统50% 的双向 VM 完全使用两个内核。|无维度|
|PercentageCpuReady|CPU 可用百分比|毫秒|总计|"就绪时间" 是指等待 CPU 在过去更新间隔中变得可用的时间。|无维度|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|
|DiskQueueLength|磁盘队列长度|计数|平均值|磁盘队列长度|实例|
|HttpQueueLength|Http 队列长度|计数|平均值|Http 队列长度|实例|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites（不包括 Functions）

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CpuTime|CPU 时间|秒数|总计|CPU 时间|实例|
|Requests|Requests|计数|总计|Requests|实例|
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
|AverageResponseTime|平均响应时间|秒数|平均值|平均响应时间|实例|
|AppConnections|连接|计数|平均值|连接|实例|
|句柄数|句柄计数|计数|平均值|句柄计数|实例|
|线程数|线程计数|计数|平均值|线程计数|实例|
|PrivateBytes|专用字节|字节|平均值|专用字节|实例|
|IoReadBytesPerSecond|IO 每秒读取字节数|每秒字节数|总计|IO 每秒读取字节数|实例|
|IoWriteBytesPerSecond|IO 每秒写入字节数|每秒字节数|总计|IO 每秒写入字节数|实例|
|IoOtherBytesPerSecond|IO 每秒其他字节数|每秒字节数|总计|IO 每秒其他字节数|实例|
|IoReadOperationsPerSecond|IO 每秒读取操作数|每秒字节数|总计|IO 每秒读取操作数|实例|
|IoWriteOperationsPerSecond|IO 每秒写入操作数|每秒字节数|总计|IO 每秒写入操作数|实例|
|IoOtherOperationsPerSecond|IO 每秒其他操作数|每秒字节数|总计|IO 每秒其他操作数|实例|
|RequestsInApplicationQueue|应用程序队列中的请求数|计数|平均值|应用程序队列中的请求数|实例|
|CurrentAssemblies|当前程序集|计数|平均值|当前程序集|实例|
|TotalAppDomains|应用程序域总数|计数|平均值|应用程序域总数|实例|
|TotalAppDomainsUnloaded|卸载的应用程序域总数|计数|平均值|卸载的应用程序域总数|实例|
|Gen0Collections|第 0 代垃圾回收|计数|总计|第 0 代垃圾回收|实例|
|Gen1Collections|第 1 代垃圾回收|计数|总计|第 1 代垃圾回收|实例|
|Gen2Collections|第 2 代垃圾回收|计数|总计|第 2 代垃圾回收|实例|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|
|Http5xx|Http 服务器错误|计数|总计|Http 服务器错误|实例|
|MemoryWorkingSet|内存工作集|字节|平均值|内存工作集|实例|
|AverageMemoryWorkingSet|平均内存工作集|字节|平均值|平均内存工作集|实例|
|FunctionExecutionUnits|函数执行单位数|MB/毫秒|总计|[函数执行单位数](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|实例|
|FunctionExecutionCount|函数执行计数|计数|总计|函数执行计数|实例|
|PrivateBytes|专用字节|字节|平均值|专用字节|实例|
|IoReadBytesPerSecond|IO 每秒读取字节数|每秒字节数|总计|IO 每秒读取字节数|实例|
|IoWriteBytesPerSecond|IO 每秒写入字节数|每秒字节数|总计|IO 每秒写入字节数|实例|
|IoOtherBytesPerSecond|IO 每秒其他字节数|每秒字节数|总计|IO 每秒其他字节数|实例|
|IoReadOperationsPerSecond|IO 每秒读取操作数|每秒字节数|总计|IO 每秒读取操作数|实例|
|IoWriteOperationsPerSecond|IO 每秒写入操作数|每秒字节数|总计|IO 每秒写入操作数|实例|
|IoOtherOperationsPerSecond|IO 每秒其他操作数|每秒字节数|总计|IO 每秒其他操作数|实例|
|RequestsInApplicationQueue|应用程序队列中的请求数|计数|平均值|应用程序队列中的请求数|实例|
|CurrentAssemblies|当前程序集|计数|平均值|当前程序集|实例|
|TotalAppDomains|应用程序域总数|计数|平均值|应用程序域总数|实例|
|TotalAppDomainsUnloaded|卸载的应用程序域总数|计数|平均值|卸载的应用程序域总数|实例|
|Gen0Collections|第 0 代垃圾回收|计数|总计|第 0 代垃圾回收|实例|
|Gen1Collections|第 1 代垃圾回收|计数|总计|第 1 代垃圾回收|实例|
|Gen2Collections|第 2 代垃圾回收|计数|总计|第 2 代垃圾回收|实例|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|CpuTime|CPU 时间|秒数|总计|CPU 时间|实例|
|Requests|Requests|计数|总计|Requests|实例|
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
|AverageResponseTime|平均响应时间|秒数|平均值|平均响应时间|实例|
|FunctionExecutionUnits|函数执行单位数|计数|总计|函数执行单位数|实例|
|FunctionExecutionCount|函数执行计数|计数|总计|函数执行计数|实例|
|AppConnections|连接|计数|平均值|连接|实例|
|句柄数|句柄计数|计数|平均值|句柄计数|实例|
|线程数|线程计数|计数|平均值|线程计数|实例|
|PrivateBytes|专用字节|字节|平均值|专用字节|实例|
|IoReadBytesPerSecond|IO 每秒读取字节数|每秒字节数|总计|IO 每秒读取字节数|实例|
|IoWriteBytesPerSecond|IO 每秒写入字节数|每秒字节数|总计|IO 每秒写入字节数|实例|
|IoOtherBytesPerSecond|IO 每秒其他字节数|每秒字节数|总计|IO 每秒其他字节数|实例|
|IoReadOperationsPerSecond|IO 每秒读取操作数|每秒字节数|总计|IO 每秒读取操作数|实例|
|IoWriteOperationsPerSecond|IO 每秒写入操作数|每秒字节数|总计|IO 每秒写入操作数|实例|
|IoOtherOperationsPerSecond|IO 每秒其他操作数|每秒字节数|总计|IO 每秒其他操作数|实例|
|RequestsInApplicationQueue|应用程序队列中的请求数|计数|平均值|应用程序队列中的请求数|实例|
|CurrentAssemblies|当前程序集|计数|平均值|当前程序集|实例|
|TotalAppDomains|应用程序域总数|计数|平均值|应用程序域总数|实例|
|TotalAppDomainsUnloaded|卸载的应用程序域总数|计数|平均值|卸载的应用程序域总数|实例|
|Gen0Collections|第 0 代垃圾回收|计数|总计|第 0 代垃圾回收|实例|
|Gen1Collections|第 1 代垃圾回收|计数|总计|第 1 代垃圾回收|实例|
|Gen2Collections|第 2 代垃圾回收|计数|总计|第 2 代垃圾回收|实例|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|Requests|Requests|计数|总计|Requests|实例|
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
|AverageResponseTime|平均响应时间|秒数|平均值|平均响应时间|实例|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|
|DiskQueueLength|磁盘队列长度|计数|平均值|磁盘队列长度|实例|
|HttpQueueLength|Http 队列长度|计数|平均值|Http 队列长度|实例|
|ActiveRequests|活动请求数|计数|总计|活动请求数|实例|
|TotalFrontEnds|前端总数|计数|平均值|前端总数|无维度|
|SmallAppServicePlanInstances|小型应用服务计划工作线程数|计数|平均值|小型应用服务计划工作线程数|无维度|
|MediumAppServicePlanInstances|中型应用服务计划工作线程数|计数|平均值|中型应用服务计划工作线程数|无维度|
|LargeAppServicePlanInstances|大型应用服务计划工作线程数|计数|平均值|大型应用服务计划工作线程数|无维度|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|指标|指标显示名称|计价单位|聚合类型|描述|维度|
|---|---|---|---|---|---|
|WorkersTotal|工作线程总数|计数|平均值|工作线程总数|无维度|
|WorkersAvailable|可用工作线程数|计数|平均值|可用工作线程数|无维度|
|WorkersUsed|使用的工作线程数|计数|平均值|使用的工作线程数|无维度|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|

## <a name="next-steps"></a>后续步骤
* [了解 Azure Monitor 中的指标](data-platform.md)
* [针对指标创建警报](alerts-overview.md)
* [将指标导出到存储、事件中心或 Log Analytics](resource-logs-overview.md)
