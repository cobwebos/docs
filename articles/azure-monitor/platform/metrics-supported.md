---
title: Azure Monitor 按资源类型支持的指标
description: 可在 Azure 监视器中为每种资源类型使用的指标的列表。
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 59df49d320b23686a3d053335ea2b95e98125b28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88135549"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure 监视器支持的指标

> [!NOTE]
> 此列表主要通过 Azure Monitor Metrics REST API 自动生成。 通过 GitHub 对此列表所做的任何修改都可能在不发出警告的情况下进行改写。 请与本文的作者联系，以获取有关如何进行永久更新的详细信息。

Azure 监视器提供多种方式来与指标交互，包括在门户中制作指标图表、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 

本文列出了当前可用于 Azure Monitor 的合并指标管道的所有平台（即自动收集的）指标。 此列表上次更新时间为 2020 年 3 月 27 日。 此日期之后更改或添加的指标不会出现在下表。 要以编程方式查询和访问指标列表，请使用 [2018-01-01 API 版本](/rest/api/monitor/metricdefinitions)。 未在此列表列出的其他指标可在门户或旧版 API 中使用。

指标按资源提供程序和资源类型进行了组织。 有关服务和属于它们的资源提供程序的列表，请参阅 [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)。 

## <a name="exporting-platform-metrics-to-other-locations"></a>将平台指标导出到其他位置

可以通过以下两种方式将平台指标从 Azure monitor 管道导出到其他位置。
1. 使用[指标 REST API](/rest/api/monitor/metrics/list)
2. 使用[诊断设置](diagnostic-settings.md)将平台指标路由到以下位置： 
    - Azure 存储
    - Azure Monitor 日志（以及 Log Analytics）
    - 事件中心，这是将它们连接到非 Microsoft 系统的方法 

使用诊断设置是路由指标的最简单方法，但存在一些限制： 

- **无法导出某些指标** - 所有指标都可以使用 REST API 导出，但由于 Azure Monitor 后端的复杂性，有些指标无法使用诊断设置导出。 下表中的“可通过诊断设置导出”列列出了可以用这种方式导出的指标。  

- **多维指标** - 当前不支持通过诊断设置将多维指标发送到其他位置。 多维指标将按平展后的单维指标导出，并跨维值聚合。 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。

## <a name="guest-os-and-host-os-metrics"></a>来宾 OS 和主机 OS 指标

> [!WARNING]
> 此处未列出在 Azure 虚拟机、Service Fabric 和云服务中运行的来宾操作系统（来宾 OS）的指标。 来宾 OS 指标必须通过一个或多个代理进行收集，这些代理在来宾操作系统上运行或作为来宾操作系统一部分运行。  来宾 OS 指标包括性能计数器，该性能计数器跟踪来宾 CPU 百分比或内存使用率，这两者经常用于自动缩放或警报功能。 
>
> **下面列出了可用的主机 OS 指标。** 它们并不相同。 主机 OS 指标与托管来宾 OS 会话的 Hyper-V 会话相关。 

> [!TIP]
> 最佳做法是使用并配置 [Azure 诊断扩展](diagnostics-extension-overview.md)，将来宾 OS 性能指标发送到存储平台指标的同一个 Azure Monitor 指标数据库中。 该扩展通过 [自定义指标](metrics-custom-overview.md) API 路由来宾 OS 指标。 然后，你可以像使用平台指标那样，绘制图表、发送警报以及以其他方式使用来宾 OS 指标。 或者，还可以使用 Log Analytics 代理将来宾 OS 指标发送到 Azure Monitor 日志/Log Analytics。 可以在该位置结合非指标数据查询这些指标。 

有关重要的其他信息，请参阅[监视代理概述](agents-overview.md)。    

## <a name="table-formatting"></a>表格式

> [!IMPORTANT] 
> 此最新更新添加了一个新列，并将指标重新排序为字母顺序。 添加信息意味着下表可能在底部有一个水平滚动条，具体取决于浏览器窗口的宽度。 如果你认为缺少信息，可以使用滚动条查看整个表。


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|是|内存:清理器当前价格|计数|平均值|内存的当前价格，$/字节/时间，标准化为 1000。|ServerResourceType|
|CleanerMemoryNonshrinkable|是|内存:不可收缩的清理器内存|字节|平均值|不受后台清理器执行的清除影响的内存量（字节）。|ServerResourceType|
|CleanerMemoryShrinkable|是|内存:可收缩的清理器内存|字节|平均值|受后台清理器执行的清除影响的内存量（字节）。|ServerResourceType|
|CommandPoolBusyThreads|是|线程:命令池繁忙线程数|计数|平均值|命令线程池中的繁忙线程数。|ServerResourceType|
|CommandPoolIdleThreads|是|线程:命令池空闲线程数|计数|平均值|命令线程池中的空闲线程数。|ServerResourceType|
|CommandPoolJobQueueLength|是|命令池作业队列长度|计数|平均值|命令线程池队列中的作业数。|ServerResourceType|
|CurrentConnections|是|连接:当前连接数|计数|平均值|当前已建立的客户端连接的数量。|ServerResourceType|
|CurrentUserSessions|是|当前用户会话数|计数|平均值|当前已建立的用户会话数。|ServerResourceType|
|LongParsingBusyThreads|是|线程:长分析繁忙线程数|计数|平均值|长分析线程池中的繁忙线程数。|ServerResourceType|
|LongParsingIdleThreads|是|线程:长分析空闲线程数|计数|平均值|长分析线程池中的空闲线程数。|ServerResourceType|
|LongParsingJobQueueLength|是|线程:长分析作业队列长度|计数|平均值|长分析线程池队列中的作业数。|ServerResourceType|
|mashup_engine_memory_metric|是|M 引擎内存|字节|平均值|糅合引擎进程的内存使用率|ServerResourceType|
|mashup_engine_private_bytes_metric|是|M 引擎专用字节数|字节|平均值|糅合引擎进程使用的专用字节数。|ServerResourceType|
|mashup_engine_qpu_metric|是|M 引擎 QPU|计数|平均值|糅合引擎进程的 QPU 使用率|ServerResourceType|
|mashup_engine_virtual_bytes_metric|是|M 引擎虚拟字节数|字节|平均值|糅合引擎进程使用的虚拟字节数。|ServerResourceType|
|memory_metric|是|内存|字节|平均值|内存。 S1 范围为 0-25 GB，S2 范围为 0-50 GB，S4 范围为 0-100 GB|ServerResourceType|
|memory_thrashing_metric|是|内存抖动|百分比|平均值|平均内存抖动。|ServerResourceType|
|MemoryLimitHard|是|内存:内存硬性限制|字节|平均值|内存硬性限制，来自配置文件。|ServerResourceType|
|MemoryLimitHigh|是|内存:内存上限|字节|平均值|内存上限，来自配置文件。|ServerResourceType|
|MemoryLimitLow|是|内存:内存下限|字节|平均值|内存下限，来自配置文件。|ServerResourceType|
|MemoryLimitVertiPaq|是|内存:内存 VertiPaq 限制|字节|平均值|内存中限制，来自配置文件。|ServerResourceType|
|MemoryUsage|是|内存:内存用量|字节|平均值|服务器进程的内存使用量（在计算清理器内存价格时使用）。 等于计数器 Process\PrivateBytes 加上内存映射的数据的大小，并且将忽略由 xVelocity 内存中分析引擎 (VertiPaq) 映射或分配的超出了 xVelocity 引擎内存限制的任何内存。|ServerResourceType|
|private_bytes_metric|是|专用字节数|字节|平均值|专用字节数。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|是|线程:处理池繁忙 I/O 作业线程数|计数|平均值|处理线程池中正在运行 I/O 作业的线程数。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|是|线程:处理池繁忙非 I/O 线程数|计数|平均值|处理线程池中正在运行非 I/O 作业的线程数。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|是|线程:处理池空闲 I/O 作业线程数|计数|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|是|线程:处理池空闲非 I/O 线程数|计数|平均值|处理线程池中专用于非 I/O 作业的空闲线程数。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|是|线程:处理池 I/O 作业队列长度|计数|平均值|处理线程池队列中的 I/O 作业数。|ServerResourceType|
|ProcessingPoolJobQueueLength|是|处理池作业队列长度|计数|平均值|处理线程池的队列中的非 I/O 作业数。|ServerResourceType|
|qpu_metric|是|QPU|计数|平均值|QPU。 S1 范围为 0-100，S2 范围为 0-200，S4 范围为 0-400|ServerResourceType|
|QueryPoolBusyThreads|是|查询池忙线程数|计数|平均值|查询线程池中的忙线程数。|ServerResourceType|
|QueryPoolIdleThreads|是|线程:查询池空闲线程数|计数|平均值|处理线程池中可用于 I/O 作业的空闲线程数。|ServerResourceType|
|QueryPoolJobQueueLength|是|线程:查询池作业队列长度|计数|平均值|查询线程池队列中的作业数。|ServerResourceType|
|Quota|是|内存:Quota|字节|平均值|当前内存配额（字节）。 内存配额也称为内存授予或内存预留。|ServerResourceType|
|QuotaBlocked|是|内存:阻止的配额|计数|平均值|在其他内存配额被释放之前已阻止的当前的配额请求数。|ServerResourceType|
|RowsConvertedPerSec|是|处理:每秒转换的行数|每秒计数|平均值|在处理过程中转换行的速率。|ServerResourceType|
|RowsReadPerSec|是|处理:每秒读取的行数|每秒计数|平均值|从所有关系数据库中读取行的速率。|ServerResourceType|
|RowsWrittenPerSec|是|处理:每秒写入的行数|每秒计数|平均值|在处理过程中写入行的速率。|ServerResourceType|
|ShortParsingBusyThreads|是|线程:短分析繁忙线程数|计数|平均值|短分析线程池中的繁忙线程数。|ServerResourceType|
|ShortParsingIdleThreads|是|线程:短分析空闲线程数|计数|平均值|短分析线程池中的空闲线程数。|ServerResourceType|
|ShortParsingJobQueueLength|是|线程:短分析作业队列长度|计数|平均值|短分析线程池队列中的作业数。|ServerResourceType|
|SuccessfullConnectionsPerSec|是|每秒成功连接数|每秒计数|平均值|连接成功完成率。|ServerResourceType|
|TotalConnectionFailures|是|连接失败总数|计数|平均值|失败的连接尝试总数。|ServerResourceType|
|TotalConnectionRequests|是|连接请求总数|计数|平均值|连接请求总数。 这些是到达的请求。|ServerResourceType|
|VertiPaqNonpaged|是|内存:VertiPaq 未分页|字节|平均值|工作集中被锁定的供内存中引擎使用的内存字节数。|ServerResourceType|
|VertiPaqPaged|是|内存:VertiPaq 已分页|字节|平均值|用于内存中数据的已分页内存字节数。|ServerResourceType|
|virtual_bytes_metric|是|虚拟字节数|字节|平均值|虚拟字节数。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BackendDuration|是|后端请求持续时间|毫秒|平均值|后端请求持续时间（毫秒）|位置、主机名|
|容量|是|容量|百分比|平均值|ApiManagement 服务的利用率指标|位置|
|持续时间|是|网关请求的总持续时间|毫秒|平均值|网关请求的总持续时间，以毫秒为单位|位置、主机名|
|EventHubDroppedEvents|是|删除的事件中心事件数|计数|总计|由于达到队列大小限制而跳过的事件数|位置|
|EventHubRejectedEvents|是|拒绝的事件中心事件数|计数|总计|拒绝的事件中心事件（配置不当或未授权）数|位置|
|EventHubSuccessfulEvents|是|成功的事件中心事件数|计数|总计|成功的事件中心事件数|位置|
|EventHubThrottledEvents|是|限制的事件中心事件数|计数|总计|限制的事件中心事件数|位置|
|EventHubTimedoutEvents|是|超时的事件中心事件数|计数|总计|超时的事件中心事件数|位置|
|EventHubTotalBytesSent|是|事件中心事件大小|字节|总计|事件中心事件的总大小，以字节为单位|位置|
|EventHubTotalEvents|是|事件中心事件总数|计数|总计|发送到事件中心的事件数|位置|
|EventHubTotalFailedEvents|是|失败的事件中心事件数|计数|总计|失败的事件中心事件数|位置|
|FailedRequests|是|失败的网关请求数（已弃用）|计数|总计|网关请求失败数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|位置、主机名|
|NetworkConnectivity|是|资源的网络连接状态（预览）|计数|平均值|API 管理服务中从属资源类型的网络连接状态|Location, ResourceType|
|OtherRequests|是|其他网关请求数（已弃用）|计数|总计|其他网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|位置、主机名|
|请求|是|请求|计数|总计|包含多个维度的网关请求指标|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|是|成功的网关请求数（已弃用）|计数|总计|成功的网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|位置、主机名|
|TotalRequests|是|网关请求总数（已弃用）|计数|总计|网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|位置、主机名|
|UnauthorizedRequests|是|未经授权的网关请求数（已弃用）|计数|总计|未经授权的网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|位置、主机名|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|是|HttpIncomingRequestCount|计数|计数|传入的 http 请求总数。|StatusCode, Authentication|
|HttpIncomingRequestDuration|是|HttpIncomingRequestDuration|计数|平均值|http 请求延迟。|StatusCode, Authentication|
|ThrottledHttpRequestCount|是|ThrottledHttpRequestCount|计数|计数|限制的 http 请求数。|无维度|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|jvm.gc.live.data.size|是|jvm.gc.live.data.size|字节|平均值|完全 GC 后的旧代内存池大小|Deployment, AppName, Pod|
|jvm.gc.max.data.size|是|jvm.gc.max.data.size|字节|平均值|旧代内存池的最大大小|Deployment, AppName, Pod|
|jvm.gc.memory.allocated|是|jvm.gc.memory.allocated|字节|最大值|运行一个 GC 之后、运行下一个 GC 之前，新代内存池大小增加的递增量|Deployment, AppName, Pod|
|jvm.gc.memory.promoted|是|jvm.gc.memory.promoted|字节|最大值|GC 之前、GC 之后，旧代内存池大小正增加的计数|Deployment, AppName, Pod|
|jvm.gc.pause.total.count|是|jvm.gc.pause.total.count|计数|总计|GC 暂停计数|Deployment, AppName, Pod|
|jvm.gc.pause.total.time|是|jvm.gc.pause.total.time|毫秒|总计|GC 暂停总时间|Deployment, AppName, Pod|
|jvm.memory.committed|是|jvm.memory.committed|字节|平均值|分配给 JVM 的内存（字节）|Deployment, AppName, Pod|
|jvm.memory.max|是|jvm.memory.max|字节|最大值|可用于内存管理的最大内存量（以字节为单位）|Deployment, AppName, Pod|
|jvm.memory.used|是|jvm.memory.used|字节|平均值|已使用的应用内存（字节）|Deployment, AppName, Pod|
|process.cpu.usage|是|process.cpu.usage|百分比|平均值|应用 JVM CPU 使用率百分比|Deployment, AppName, Pod|
|system.cpu.usage|是|system.cpu.usage|百分比|平均值|整个系统的最近 CPU 使用率|Deployment, AppName, Pod|
|tomcat.global.error|是|tomcat.global.error|计数|总计|Tomcat 全局错误|Deployment, AppName, Pod|
|tomcat.global.received|是|tomcat.global.received|字节|总计|Tomcat 收到的总字节数|Deployment, AppName, Pod|
|tomcat.global.request.avg.time|是|tomcat.global.request.avg.time|毫秒|平均值|Tomcat 请求平均时间|Deployment, AppName, Pod|
|tomcat.global.request.max|是|tomcat.global.request.max|毫秒|最大值|Tomcat 请求最大时间|Deployment, AppName, Pod|
|tomcat.global.request.total.count|是|tomcat.global.request.total.count|计数|总计|Tomcat 请求总数|Deployment, AppName, Pod|
|tomcat.global.request.total.time|是|tomcat.global.request.total.time|毫秒|总计|Tomcat 请求总时间|Deployment, AppName, Pod|
|tomcat.global.sent|是|tomcat.global.sent|字节|总计|Tomcat 发送的总字节数|Deployment, AppName, Pod|
|tomcat.sessions.active.current|是|tomcat.sessions.active.current|计数|总计|Tomcat 会话活动计数|Deployment, AppName, Pod|
|tomcat.sessions.active.max|是|tomcat.sessions.active.max|计数|总计|Tomcat 会话最大活动计数|Deployment, AppName, Pod|
|tomcat.sessions.alive.max|是|tomcat.sessions.alive.max|毫秒|最大值|Tomcat 会话最大活动时间|Deployment, AppName, Pod|
|tomcat.sessions.created|是|tomcat.sessions.created|计数|总计|Tomcat 创建的会话数|Deployment, AppName, Pod|
|tomcat.sessions.expired|是|tomcat.sessions.expired|计数|总计|Tomcat 过期的会话数|Deployment, AppName, Pod|
|tomcat.sessions.rejected|是|tomcat.sessions.rejected|计数|总计|Tomcat 拒绝的会话数|Deployment, AppName, Pod|
|tomcat.threads.config.max|是|tomcat.threads.config.max|计数|总计|Tomcat 配置最大线程计数|Deployment, AppName, Pod|
|tomcat.threads.current|是|tomcat.threads.current|计数|总计|Tomcat 当前线程计数|Deployment, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|TotalJob|是|作业总数|计数|总计|作业总数|Runbook、状态|
|TotalUpdateDeploymentMachineRuns|是|更新部署计算机运行总数|计数|总计|软件更新部署运行中的软件更新部署计算机运行总数|SoftwareUpdateConfigurationName、Status、TargetComputer、SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|是|更新部署运行总数|计数|总计|软件更新部署运行总数|SoftwareUpdateConfigurationName、Status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|CoreCount|否|专用核心计数|计数|总计|批处理帐户中的专用核心总数|无维度|
|CreatingNodeCount|否|正在创建的节点计数|计数|总计|正在创建的节点数目|无维度|
|IdleNodeCount|否|空闲节点计数|计数|总计|空闲节点数目|无维度|
|JobDeleteCompleteEvent|是|作业删除完成事件数|计数|总计|已成功删除的作业总数。|jobId|
|JobDeleteStartEvent|是|作业删除启动事件数|计数|总计|已请求删除的作业总数。|jobId|
|JobDisableCompleteEvent|是|作业禁用完成事件数|计数|总计|已成功禁用的作业总数。|jobId|
|JobDisableStartEvent|是|作业禁用启动事件数|计数|总计|已请求禁用的作业总数。|jobId|
|JobStartEvent|是|作业启动事件数|计数|总计|已成功启动的作业总数。|jobId|
|JobTerminateCompleteEvent|是|作业终止完成事件数|计数|总计|已成功终止的作业总数。|jobId|
|JobTerminateStartEvent|是|作业终止启动事件数|计数|总计|已请求终止的作业总数。|jobId|
|LeavingPoolNodeCount|否|正在退出池的节点计数|计数|总计|正在退出池的节点数目|无维度|
|LowPriorityCoreCount|否|低优先级核心计数|计数|总计|批处理帐户中的低优先级核心总数|无维度|
|OfflineNodeCount|否|脱机节点计数|计数|总计|脱机节点数目|无维度|
|PoolCreateEvent|是|池创建事件数|计数|总计|已创建的池总数|poolId|
|PoolDeleteCompleteEvent|是|池删除完成事件数|计数|总计|已完成的池删除活动总数|poolId|
|PoolDeleteStartEvent|是|池删除启动事件数|计数|总计|已启动的池删除活动总数|poolId|
|PoolResizeCompleteEvent|是|池调整大小完成事件数|计数|总计|已完成的池调整大小活动总数|poolId|
|PoolResizeStartEvent|是|池调整大小启动事件数|计数|总计|已启动的池调整大小活动总数|poolId|
|PreemptedNodeCount|否|已占用节点计数|计数|总计|已占用节点数|无维度|
|RebootingNodeCount|否|正在重新启动的节点计数|计数|总计|正在重新启动的节点数目|无维度|
|ReimagingNodeCount|否|正在重置映像的节点计数|计数|总计|正在重置映像的节点数目|无维度|
|RunningNodeCount|否|正在运行的节点计数|计数|总计|正在运行的节点数目|无维度|
|StartingNodeCount|否|正在启动的节点计数|计数|总计|正在启动的节点数目|无维度|
|StartTaskFailedNodeCount|否|启动任务失败的节点计数|计数|总计|启动任务失败的节点数目|无维度|
|TaskCompleteEvent|是|任务完成事件数|计数|总计|已完成的任务总数|poolId, jobId|
|TaskFailEvent|是|任务失败事件数|计数|总计|处于失败状态的已完成任务总数|poolId, jobId|
|TaskStartEvent|是|任务启动事件数|计数|总计|已启动的任务总数|poolId, jobId|
|TotalLowPriorityNodeCount|否|低优先级节点计数|计数|总计|批处理帐户中的低优先级节点总数|无维度|
|TotalNodeCount|否|专用节点计数|计数|总计|批处理帐户中的专用节点总数|无维度|
|UnusableNodeCount|否|不可用的节点计数|计数|总计|不可用的节点数目|无维度|
|WaitingForStartTaskNodeCount|否|正在等待启动任务的节点计数|计数|总计|正在等待启动任务完成的节点数目|无维度|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|活动核心数|是|活动核心数|计数|平均值|活动核心数|Scenario, ClusterName|
|活动节点数|是|活动节点数|计数|平均值|正在运行的节点数目|Scenario, ClusterName|
|空闲核心数|是|空闲核心数|计数|平均值|空闲核心数|Scenario, ClusterName|
|空闲节点数|是|空闲节点数|计数|平均值|空闲节点数目|Scenario, ClusterName|
|已完成的作业|是|已完成的作业|计数|总计|已完成的作业数|Scenario, ClusterName, ResultType|
|提交的作业|是|提交的作业|计数|总计|提交的作业数|Scenario, ClusterName|
|正在退出的核心数|是|正在退出的核心数|计数|平均值|正在退出的核心数|Scenario, ClusterName|
|正在退出的节点数|是|正在退出的节点数|计数|平均值|正在退出的节点数|Scenario, ClusterName|
|占用的核心数|是|占用的核心数|计数|平均值|占用的核心数|Scenario, ClusterName|
|占用的节点数|是|占用的节点数|计数|平均值|已占用节点数|Scenario, ClusterName|
|配额利用率百分比|是|配额利用率百分比|计数|平均值|已利用的配额百分比|Scenario, ClusterName, VmFamilyName, VmPriority|
|核心总数|是|核心总数|计数|平均值|核心总数|Scenario, ClusterName|
|节点总数|是|节点总数|计数|平均值|节点总数|Scenario, ClusterName|
|不可用的核心数|是|不可用的核心数|计数|平均值|不可用的核心数|Scenario, ClusterName|
|不可用的节点数|是|不可用的节点数|计数|平均值|不可用的节点数目|Scenario, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|是|Broadcast Processed Count|计数|平均值|处理的事务数|Node, channel, type, status|
|ConnectionAccepted|是|已接受的连接数|计数|总计|已接受的连接数|节点|
|ConnectionActive|是|活动连接数|计数|平均值|活动连接数|节点|
|ConnectionHandled|是|已处理的连接数|计数|总计|已处理的连接数|节点|
|ConsensusEtcdraftCommittedBlockNumber|是|Consensus Etcdraft Committed Block Number|计数|平均值|最近提交的块的块数|Node, channel|
|CpuUsagePercentageInDouble|是|CPU 使用率百分比|百分比|最大值|CPU 使用率百分比|节点|
|EndorserEndorsementFailures|是|Endorser Endorsement Failures|计数|平均值|背书失败数。|Node, channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|是|Gossip Leader Election Leader|计数|总计|对等方是先导 (1) 或后继 (0)|Node, channel|
|GossipMembershipTotalPeersKnown|是|Gossip Membership Total Peers Known|计数|平均值|已知对等方总计|Node, channel|
|GossipStateHeight|是|Gossip State Height|计数|平均值|当前账本高度|Node, channel|
|IOReadBytes|是|IO 读取字节数|字节|总计|IO 读取字节数|节点|
|IOWriteBytes|是|IO 写入字节数|字节|总计|IO 写入字节数|节点|
|LedgerTransactionCount|是|Ledger Transaction Count|计数|平均值|处理的事务数|Node, channel, transaction_type, chaincode, validation_code|
|MemoryLimit|是|内存限制|字节|平均值|内存限制|节点|
|MemoryUsage|是|内存用量|字节|平均值|内存用量|节点|
|MemoryUsagePercentageInDouble|是|内存使用率百分比|百分比|平均值|内存使用率百分比|节点|
|PendingTransactions|是|挂起的事务数|计数|平均值|挂起的事务数|节点|
|ProcessedBlocks|是|已处理的块数|计数|总计|已处理的块数|节点|
|ProcessedTransactions|是|已处理的事务数|计数|总计|已处理的事务数|节点|
|QueuedTransactions|是|已排队的事务数|计数|平均值|已排队的事务数|节点|
|RequestHandled|是|已处理的请求数|计数|总计|已处理的请求数|节点|
|StorageUsage|是|存储使用率|字节|平均值|存储使用率|节点|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|cachehits|是|缓存命中数|计数|总计||ShardId|
|cachehits0|是|缓存命中数(分片 0)|计数|总计||无维度|
|cachehits1|是|缓存命中数(分片 1)|计数|总计||无维度|
|cachehits2|是|缓存命中数(分片 2)|计数|总计||无维度|
|cachehits3|是|缓存命中数(分片 3)|计数|总计||无维度|
|cachehits4|是|缓存命中数(分片 4)|计数|总计||无维度|
|cachehits5|是|缓存命中数(分片 5)|计数|总计||无维度|
|cachehits6|是|缓存命中数(分片 6)|计数|总计||无维度|
|cachehits7|是|缓存命中数(分片 7)|计数|总计||无维度|
|cachehits8|是|缓存命中数(分片 8)|计数|总计||无维度|
|cachehits9|是|缓存命中数(分片 9)|计数|总计||无维度|
|cacheLatency|是|缓存延迟毫秒数（预览）|计数|平均值||ShardId|
|cachemisses|是|缓存未命中数|计数|总计||ShardId|
|cachemisses0|是|缓存未命中数(分片 0)|计数|总计||无维度|
|cachemisses1|是|缓存未命中数(分片 1)|计数|总计||无维度|
|cachemisses2|是|缓存未命中数(分片 2)|计数|总计||无维度|
|cachemisses3|是|缓存未命中数(分片 3)|计数|总计||无维度|
|cachemisses4|是|缓存未命中数(分片 4)|计数|总计||无维度|
|cachemisses5|是|缓存未命中数(分片 5)|计数|总计||无维度|
|cachemisses6|是|缓存未命中数(分片 6)|计数|总计||无维度|
|cachemisses7|是|缓存未命中数(分片 7)|计数|总计||无维度|
|cachemisses8|是|缓存未命中数(分片 8)|计数|总计||无维度|
|cachemisses9|是|缓存未命中数(分片 9)|计数|总计||无维度|
|cachemissrate|是|缓存未命中率|百分比|cachemissrate||ShardId|
|cacheRead|是|缓存读取量|每秒字节数|最大值||ShardId|
|cacheRead0|是|缓存读取量(分片 0)|每秒字节数|最大值||无维度|
|cacheRead1|是|缓存读取量(分片 1)|每秒字节数|最大值||无维度|
|cacheRead2|是|缓存读取量(分片 2)|每秒字节数|最大值||无维度|
|cacheRead3|是|缓存读取量(分片 3)|每秒字节数|最大值||无维度|
|cacheRead4|是|缓存读取量(分片 4)|每秒字节数|最大值||无维度|
|cacheRead5|是|缓存读取量(分片 5)|每秒字节数|最大值||无维度|
|cacheRead6|是|缓存读取量(分片 6)|每秒字节数|最大值||无维度|
|cacheRead7|是|缓存读取量(分片 7)|每秒字节数|最大值||无维度|
|cacheRead8|是|缓存读取量(分片 8)|每秒字节数|最大值||无维度|
|cacheRead9|是|缓存读取量(分片 9)|每秒字节数|最大值||无维度|
|cacheWrite|是|缓存写入量|每秒字节数|最大值||ShardId|
|cacheWrite0|是|缓存写入量(分片 0)|每秒字节数|最大值||无维度|
|cacheWrite1|是|缓存写入量(分片 1)|每秒字节数|最大值||无维度|
|cacheWrite2|是|缓存写入量(分片 2)|每秒字节数|最大值||无维度|
|cacheWrite3|是|缓存写入量(分片 3)|每秒字节数|最大值||无维度|
|cacheWrite4|是|缓存写入量(分片 4)|每秒字节数|最大值||无维度|
|cacheWrite5|是|缓存写入量(分片 5)|每秒字节数|最大值||无维度|
|cacheWrite6|是|缓存写入量(分片 6)|每秒字节数|最大值||无维度|
|cacheWrite7|是|缓存写入量(分片 7)|每秒字节数|最大值||无维度|
|cacheWrite8|是|缓存写入量(分片 8)|每秒字节数|最大值||无维度|
|cacheWrite9|是|缓存写入量(分片 9)|每秒字节数|最大值||无维度|
|connectedclients|是|连接的客户端数|计数|最大值||ShardId|
|connectedclients0|是|连接的客户端数(分片 0)|计数|最大值||无维度|
|connectedclients1|是|连接的客户端数(分片 1)|计数|最大值||无维度|
|connectedclients2|是|连接的客户端数(分片 2)|计数|最大值||无维度|
|connectedclients3|是|连接的客户端数(分片 3)|计数|最大值||无维度|
|connectedclients4|是|连接的客户端数(分片 4)|计数|最大值||无维度|
|connectedclients5|是|连接的客户端数(分片 5)|计数|最大值||无维度|
|connectedclients6|是|连接的客户端数(分片 6)|计数|最大值||无维度|
|connectedclients7|是|连接的客户端数(分片 7)|计数|最大值||无维度|
|connectedclients8|是|连接的客户端数(分片 8)|计数|最大值||无维度|
|connectedclients9|是|连接的客户端数(分片 9)|计数|最大值||无维度|
|错误|是|错误|计数|最大值||ShardId, ErrorType|
|evictedkeys|是|逐出的密钥数|计数|总计||ShardId|
|evictedkeys0|是|逐出的密钥数(分片 0)|计数|总计||无维度|
|evictedkeys1|是|逐出的密钥数(分片 1)|计数|总计||无维度|
|evictedkeys2|是|逐出的密钥数(分片 2)|计数|总计||无维度|
|evictedkeys3|是|逐出的密钥数(分片 3)|计数|总计||无维度|
|evictedkeys4|是|逐出的密钥数(分片 4)|计数|总计||无维度|
|evictedkeys5|是|逐出的密钥数(分片 5)|计数|总计||无维度|
|evictedkeys6|是|逐出的密钥数(分片 6)|计数|总计||无维度|
|evictedkeys7|是|逐出的密钥数(分片 7)|计数|总计||无维度|
|evictedkeys8|是|逐出的密钥数(分片 8)|计数|总计||无维度|
|evictedkeys9|是|逐出的密钥数(分片 9)|计数|总计||无维度|
|expiredkeys|是|过期的密钥数|计数|总计||ShardId|
|expiredkeys0|是|过期的密钥数(分片 0)|计数|总计||无维度|
|expiredkeys1|是|过期的密钥数(分片 1)|计数|总计||无维度|
|expiredkeys2|是|过期的密钥数(分片 2)|计数|总计||无维度|
|expiredkeys3|是|过期的密钥数(分片 3)|计数|总计||无维度|
|expiredkeys4|是|过期的密钥数(分片 4)|计数|总计||无维度|
|expiredkeys5|是|过期的密钥数(分片 5)|计数|总计||无维度|
|expiredkeys6|是|过期的密钥数(分片 6)|计数|总计||无维度|
|expiredkeys7|是|过期的密钥数(分片 7)|计数|总计||无维度|
|expiredkeys8|是|过期的密钥数(分片 8)|计数|总计||无维度|
|expiredkeys9|是|过期的密钥数(分片 9)|计数|总计||无维度|
|getcommands|是|获取数|计数|总计||ShardId|
|getcommands0|是|Get 数(分片 0)|计数|总计||无维度|
|getcommands1|是|Get 数(分片 1)|计数|总计||无维度|
|getcommands2|是|Get 数(分片 2)|计数|总计||无维度|
|getcommands3|是|Get 数(分片 3)|计数|总计||无维度|
|getcommands4|是|Get 数(分片 4)|计数|总计||无维度|
|getcommands5|是|Get 数(分片 5)|计数|总计||无维度|
|getcommands6|是|Get 数(分片 6)|计数|总计||无维度|
|getcommands7|是|Get 数(分片 7)|计数|总计||无维度|
|getcommands8|是|Get 数(分片 8)|计数|总计||无维度|
|getcommands9|是|Get 数(分片 9)|计数|总计||无维度|
|operationsPerSecond|是|每秒操作数|计数|最大值||ShardId|
|operationsPerSecond0|是|每秒操作数（分片 0）|计数|最大值||无维度|
|operationsPerSecond1|是|每秒操作数（分片 1）|计数|最大值||无维度|
|operationsPerSecond2|是|每秒操作数（分片 2）|计数|最大值||无维度|
|operationsPerSecond3|是|每秒操作数（分片 3）|计数|最大值||无维度|
|operationsPerSecond4|是|每秒操作数（分片 4）|计数|最大值||无维度|
|operationsPerSecond5|是|每秒操作数（分片 5）|计数|最大值||无维度|
|operationsPerSecond6|是|每秒操作数（分片 6）|计数|最大值||无维度|
|operationsPerSecond7|是|每秒操作数（分片 7）|计数|最大值||无维度|
|operationsPerSecond8|是|每秒操作数（分片 8）|计数|最大值||无维度|
|operationsPerSecond9|是|每秒操作数（分片 9）|计数|最大值||无维度|
|percentProcessorTime|是|CPU|百分比|最大值||ShardId|
|percentProcessorTime0|是|CPU (分片 0)|百分比|最大值||无维度|
|percentProcessorTime1|是|CPU (分片 1)|百分比|最大值||无维度|
|percentProcessorTime2|是|CPU (分片 2)|百分比|最大值||无维度|
|percentProcessorTime3|是|CPU (分片 3)|百分比|最大值||无维度|
|percentProcessorTime4|是|CPU (分片 4)|百分比|最大值||无维度|
|percentProcessorTime5|是|CPU (分片 5)|百分比|最大值||无维度|
|percentProcessorTime6|是|CPU (分片 6)|百分比|最大值||无维度|
|percentProcessorTime7|是|CPU (分片 7)|百分比|最大值||无维度|
|percentProcessorTime8|是|CPU (分片 8)|百分比|最大值||无维度|
|percentProcessorTime9|是|CPU (分片 9)|百分比|最大值||无维度|
|serverLoad|是|服务器负载|百分比|最大值||ShardId|
|serverLoad0|是|服务器负载(分片 0)|百分比|最大值||无维度|
|serverLoad1|是|服务器负载(分片 1)|百分比|最大值||无维度|
|serverLoad2|是|服务器负载(分片 2)|百分比|最大值||无维度|
|serverLoad3|是|服务器负载(分片 3)|百分比|最大值||无维度|
|serverLoad4|是|服务器负载(分片 4)|百分比|最大值||无维度|
|serverLoad5|是|服务器负载(分片 5)|百分比|最大值||无维度|
|serverLoad6|是|服务器负载(分片 6)|百分比|最大值||无维度|
|serverLoad7|是|服务器负载(分片 7)|百分比|最大值||无维度|
|serverLoad8|是|服务器负载(分片 8)|百分比|最大值||无维度|
|serverLoad9|是|服务器负载(分片 9)|百分比|最大值||无维度|
|setcommands|是|设置数|计数|总计||ShardId|
|setcommands0|是|Set 数(分片 0)|计数|总计||无维度|
|setcommands1|是|Set 数(分片 1)|计数|总计||无维度|
|setcommands2|是|Set 数(分片 2)|计数|总计||无维度|
|setcommands3|是|Set 数(分片 3)|计数|总计||无维度|
|setcommands4|是|Set 数(分片 4)|计数|总计||无维度|
|setcommands5|是|Set 数(分片 5)|计数|总计||无维度|
|setcommands6|是|Set 数(分片 6)|计数|总计||无维度|
|setcommands7|是|Set 数(分片 7)|计数|总计||无维度|
|setcommands8|是|Set 数(分片 8)|计数|总计||无维度|
|setcommands9|是|Set 数(分片 9)|计数|总计||无维度|
|totalcommandsprocessed|是|总操作数|计数|总计||ShardId|
|totalcommandsprocessed0|是|总操作数(分片 0)|计数|总计||无维度|
|totalcommandsprocessed1|是|总操作数(分片 1)|计数|总计||无维度|
|totalcommandsprocessed2|是|总操作数(分片 2)|计数|总计||无维度|
|totalcommandsprocessed3|是|总操作数(分片 3)|计数|总计||无维度|
|totalcommandsprocessed4|是|总操作数(分片 4)|计数|总计||无维度|
|totalcommandsprocessed5|是|总操作数(分片 5)|计数|总计||无维度|
|totalcommandsprocessed6|是|总操作数(分片 6)|计数|总计||无维度|
|totalcommandsprocessed7|是|总操作数(分片 7)|计数|总计||无维度|
|totalcommandsprocessed8|是|总操作数(分片 8)|计数|总计||无维度|
|totalcommandsprocessed9|是|总操作数(分片 9)|计数|总计||无维度|
|totalkeys|是|总密钥数|计数|最大值||ShardId|
|totalkeys0|是|总密钥数（分片 0）|计数|最大值||无维度|
|totalkeys1|是|总密钥数（分片 1）|计数|最大值||无维度|
|totalkeys2|是|总密钥数（分片 2）|计数|最大值||无维度|
|totalkeys3|是|总密钥数（分片 3）|计数|最大值||无维度|
|totalkeys4|是|总密钥数（分片 4）|计数|最大值||无维度|
|totalkeys5|是|总密钥数（分片 5）|计数|最大值||无维度|
|totalkeys6|是|总密钥数（分片 6）|计数|最大值||无维度|
|totalkeys7|是|总密钥数（分片 7）|计数|最大值||无维度|
|totalkeys8|是|总密钥数（分片 8）|计数|最大值||无维度|
|totalkeys9|是|总密钥数（分片 9）|计数|最大值||无维度|
|usedmemory|是|已用内存|字节|最大值||ShardId|
|usedmemory0|是|已用内存量(分片 0)|字节|最大值||无维度|
|usedmemory1|是|已用内存量(分片 1)|字节|最大值||无维度|
|usedmemory2|是|已用内存量(分片 2)|字节|最大值||无维度|
|usedmemory3|是|已用内存量(分片 3)|字节|最大值||无维度|
|usedmemory4|是|已用内存量(分片 4)|字节|最大值||无维度|
|usedmemory5|是|已用内存量(分片 5)|字节|最大值||无维度|
|usedmemory6|是|已用内存量(分片 6)|字节|最大值||无维度|
|usedmemory7|是|已用内存量(分片 7)|字节|最大值||无维度|
|usedmemory8|是|已用内存量(分片 8)|字节|最大值||无维度|
|usedmemory9|是|已用内存量(分片 9)|字节|最大值||无维度|
|usedmemorypercentage|是|已用内存百分比|百分比|最大值||ShardId|
|usedmemoryRss|是|已用内存 RSS|字节|最大值||ShardId|
|usedmemoryRss0|是|已用内存 RSS (分片 0)|字节|最大值||无维度|
|usedmemoryRss1|是|已用内存 RSS (分片 1)|字节|最大值||无维度|
|usedmemoryRss2|是|已用内存 RSS (分片 2)|字节|最大值||无维度|
|usedmemoryRss3|是|已用内存 RSS (分片 3)|字节|最大值||无维度|
|usedmemoryRss4|是|已用内存 RSS (分片 4)|字节|最大值||无维度|
|usedmemoryRss5|是|已用内存 RSS (分片 5)|字节|最大值||无维度|
|usedmemoryRss6|是|已用内存 RSS (分片 6)|字节|最大值||无维度|
|usedmemoryRss7|是|已用内存 RSS (分片 7)|字节|最大值||无维度|
|usedmemoryRss8|是|已用内存 RSS (分片 8)|字节|最大值||无维度|
|usedmemoryRss9|是|已用内存 RSS (分片 9)|字节|最大值||无维度|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|是|Web 应用程序防火墙请求计数|计数|总计|Web 应用程序防火墙所处理的客户端请求数|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|否|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|RoleInstanceId|
|磁盘读取操作次数/秒|是|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|RoleInstanceId|
|Disk Write Bytes/Sec|否|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|RoleInstanceId|
|磁盘写入操作次数/秒|是|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|RoleInstanceId|
|网络传入|是|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|RoleInstanceId|
|网络传出|是|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|RoleInstanceId|
|CPU 百分比|是|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|否|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|无维度|
|磁盘读取操作次数/秒|是|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|无维度|
|Disk Write Bytes/Sec|否|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|无维度|
|磁盘写入操作次数/秒|是|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|无维度|
|网络传入|是|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|无维度|
|网络传出|是|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|无维度|
|CPU 百分比|是|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|无维度|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|流出量|是|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|事务|是|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|否|已用容量|字节|平均值|帐户使用的容量|无维度|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|BlobCapacity|否|Blob 容量|字节|平均值|存储帐户的 Blob 服务使用的存储量（以字节为单位）。|BlobType、Tier|
|BlobCount|否|Blob 计数|计数|平均值|存储帐户的 Blob 服务中的 Blob 数。|BlobType、Tier|
|ContainerCount|是|Blob 容器计数|计数|平均值|存储帐户的 Blob 服务中的容器数。|无维度|
|流出量|是|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|IndexCapacity|否|索引容量|字节|平均值|ADLS Gen2 (层次结构)索引所使用的存储量(按字节计)。|无维度|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|事务|是|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication, FileShare|
|流出量|是|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|否|文件容量|字节|平均值|存储帐户的文件服务使用的存储量（以字节为单位）。|FileShare|
|FileCount|否|文件计数|计数|平均值|存储帐户的文件服务中的文件数。|FileShare|
|FileShareCount|否|文件共享计数|计数|平均值|存储帐户的文件服务中的文件共享数。|无维度|
|FileShareQuota|否|文件共享配额大小|字节|平均值|Azure 文件服务可使用的存储量上限（以字节为单位）。|FileShare|
|FileShareSnapshotCount|否|文件共享快照计数|计数|平均值|存储帐户文件服务的共享中存在的快照数。|FileShare|
|FileShareSnapshotSize|否|文件共享快照大小|字节|平均值|存储帐户的文件服务中的快照所用存储量（以字节为单位）。|FileShare|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication, FileShare|
|事务|是|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|流出量|是|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|QueueCapacity|是|队列容量|字节|平均值|存储帐户的队列服务使用的存储量（以字节为单位）。|无维度|
|QueueCount|是|队列计数|计数|平均值|存储帐户的队列服务中的队列数。|无维度|
|QueueMessageCount|是|队列消息计数|计数|平均值|存储帐户的队列服务中的队列消息的大致数目。|无维度|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|事务|是|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|流出量|是|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|TableCapacity|是|表容量|字节|平均值|存储帐户的表服务使用的存储量（以字节为单位）。|无维度|
|TableCount|是|表计数|计数|平均值|存储帐户的表服务中的表数。|无维度|
|TableEntityCount|是|表实体计数|计数|平均值|存储帐户的表服务中的表实体数。|无维度|
|事务|是|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BlockedCalls|是|阻止的调用数|计数|总计|超过速率或配额限制的调用数。|ApiName、OperationName、Region|
|CharactersTrained|是|训练的字符数|计数|总计|训练的字符总数。|ApiName、OperationName、Region|
|CharactersTranslated|是|转换的字符|计数|总计|传入的文本请求中的字符总数。|ApiName、OperationName、Region|
|ClientErrors|是|客户端错误数|计数|总计|引发客户端错误（HTTP 响应代码 4xx）的调用数。|ApiName、OperationName、Region|
|DataIn|是|数据输入|字节|总计|传入数据的大小（字节）。|ApiName、OperationName、Region|
|DataOut|是|数据输出|字节|总计|传出数据的大小（字节）。|ApiName、OperationName、Region|
|延迟|是|延迟|毫秒|平均值|延迟（毫秒）。|ApiName、OperationName、Region|
|ProcessedImages|是|已处理的图像|计数|总计| 图像处理的事务数。|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors|是|服务器错误数|计数|总计|引发服务内部错误（HTTP 响应代码 5xx）的调用数。|ApiName、OperationName、Region|
|SpeechSessionDuration|是|语音会话持续时间|秒|总计|语音会话的总持续时间（以秒计）。|ApiName、OperationName、Region|
|SuccessfulCalls|是|成功调用数|计数|总计|成功调用数。|ApiName、OperationName、Region|
|TotalCalls|是|总调用数|计数|总计|调用总数。|ApiName、OperationName、Region|
|TotalErrors|是|错误总数|计数|总计|引发错误响应（HTTP 响应代码 4xx 或 5xx）的调用总数。|ApiName、OperationName、Region|
|TotalTokenCalls|是|令牌调用总数|计数|总计|令牌调用的总数。|ApiName、OperationName、Region|
|TotalTransactions|是|总事务|计数|总计|事务总数。|无维度|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|已用 CPU 信用额度|是|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|
|剩余 CPU 信用额度|是|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|数据磁盘队列深度|是|数据磁盘队列深度（预览版）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN|
|数据磁盘读取字节数/秒|是|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|LUN|
|数据磁盘读取操作数/秒|是|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|LUN|
|数据磁盘写入字节数/秒|是|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|LUN|
|数据磁盘写入操作数/秒|是|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|LUN|
|磁盘读取字节数|是|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|无维度|
|磁盘读取操作次数/秒|是|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无维度|
|磁盘写入字节数|是|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节数|无维度|
|磁盘写入操作次数/秒|是|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无维度|
|入站流数|是|入站流数|计数|平均值|入站流数是入站方向的当前流（传入 VM 的流量）的数目|无维度|
|入站流最大创建速率|是|入站流最大创建速率|每秒计数|平均值|入站流（传入 VM 的流量）的最大创建速率|无维度|
|网络传入|是|可计费网络（已弃用）|字节|总计|虚拟机在所有网络接口上收到的可计费字节数（传入流量）（已弃用）|无维度|
|网络传入流量总计|是|网络传入流量总计|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无维度|
|网络传出|是|可计费网络流出量（已弃用）|字节|总计|通过虚拟机传出的所有网络接口上的可计费字节数（传出流量）（已弃用）|无维度|
|网络传出流量总计|是|网络传出流量总计|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无维度|
|OS 磁盘队列深度|是|OS 磁盘队列深度（预览版）|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|OS 磁盘读取字节数/秒|是|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无维度|
|OS 磁盘读取操作次数/秒|是|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无维度|
|OS 磁盘写入字节数/秒|是|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无维度|
|OS 磁盘写入操作次数/秒|是|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无维度|
|OS 每磁盘 QD|是|OS 磁盘 QD (已弃用)|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|OS 每磁盘读取字节数/秒|是|OS 磁盘读取字节数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无维度|
|OS 每磁盘读取操作数/秒|是|OS 磁盘读取操作数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无维度|
|OS 每磁盘写入字节数/秒|是|OS 磁盘写入字节数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无维度|
|OS 每磁盘写入操作数/秒|是|OS 磁盘写入操作数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无维度|
|出站流|是|出站流|计数|平均值|出站流数是出站方向的当前流（传出 VM 的流量）的数目|无维度|
|出站流最大创建速率|是|出站流最大创建速率|每秒计数|平均值|出站流（传出 VM 的流量）的最大创建速率|无维度|
|每磁盘 QD|是|数据磁盘 QD (已弃用)|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|每磁盘读取字节数/秒|是|数据磁盘读取字节数/秒(已弃用)|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|SlotId|
|每磁盘读取操作数/秒|是|数据磁盘读取操作数/秒(已弃用)|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|SlotId|
|每磁盘写入字节数/秒|是|数据磁盘写入字节数/秒(已弃用)|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|SlotId|
|每磁盘写入操作数/秒|是|数据磁盘写入操作数/秒(已弃用)|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|SlotId|
|CPU 百分比|是|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无维度|
|高级数据磁盘缓存读取命中|是|高级数据磁盘缓存读取命中（预览版）|百分比|平均值|高级数据磁盘缓存读取命中|LUN|
|高级数据磁盘缓存读取未命中|是|高级数据磁盘缓存读取未命中（预览版）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN|
|高级 OS 磁盘缓存读取命中|是|高级 OS 磁盘缓存读取命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取命中|无维度|
|高级 OS 磁盘缓存读取未命中|是|高级 OS 磁盘缓存读取未命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取未命中|无维度|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|已用 CPU 信用额度|是|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|
|剩余 CPU 信用额度|是|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|数据磁盘队列深度|是|数据磁盘队列深度（预览版）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN、VMName|
|数据磁盘读取字节数/秒|是|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|LUN、VMName|
|数据磁盘读取操作数/秒|是|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|LUN、VMName|
|数据磁盘写入字节数/秒|是|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|LUN、VMName|
|数据磁盘写入操作数/秒|是|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|LUN、VMName|
|磁盘读取字节数|是|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|VMName|
|磁盘读取操作次数/秒|是|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|VMName|
|磁盘写入字节数|是|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节数|VMName|
|磁盘写入操作次数/秒|是|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|VMName|
|入站流数|是|入站流量|计数|平均值|入站流数是入站方向的当前流（传入 VM 的流量）的数目|VMName|
|入站流最大创建速率|是|入站流最大创建速率|每秒计数|平均值|入站流（传入 VM 的流量）的最大创建速率|VMName|
|网络传入|是|可计费网络（已弃用）|字节|总计|虚拟机在所有网络接口上收到的可计费字节数（传入流量）（已弃用）|VMName|
|网络传入流量总计|是|网络传入流量总计|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|VMName|
|网络传出|是|可计费网络流出量（已弃用）|字节|总计|通过虚拟机传出的所有网络接口上的可计费字节数（传出流量）（已弃用）|VMName|
|网络传出流量总计|是|网络传出流量总计|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|VMName|
|OS 磁盘队列深度|是|OS 磁盘队列深度（预览版）|计数|平均值|OS 磁盘队列深度(或队列长度)|VMName|
|OS 磁盘读取字节数/秒|是|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|VMName|
|OS 磁盘读取操作次数/秒|是|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|VMName|
|OS 磁盘写入字节数/秒|是|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|VMName|
|OS 磁盘写入操作次数/秒|是|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|VMName|
|OS 每磁盘 QD|是|OS 磁盘 QD (已弃用)|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|OS 每磁盘读取字节数/秒|是|OS 磁盘读取字节数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无维度|
|OS 每磁盘读取操作数/秒|是|OS 磁盘读取操作数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无维度|
|OS 每磁盘写入字节数/秒|是|OS 磁盘写入字节数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无维度|
|OS 每磁盘写入操作数/秒|是|OS 磁盘写入操作数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无维度|
|出站流|是|出站流|计数|平均值|出站流数是出站方向的当前流（传出 VM 的流量）的数目|VMName|
|出站流最大创建速率|是|出站流最大创建速率|每秒计数|平均值|出站流（传出 VM 的流量）的最大创建速率|VMName|
|每磁盘 QD|是|数据磁盘 QD (已弃用)|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|每磁盘读取字节数/秒|是|数据磁盘读取字节数/秒(已弃用)|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|SlotId|
|每磁盘读取操作数/秒|是|数据磁盘读取操作数/秒(已弃用)|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|SlotId|
|每磁盘写入字节数/秒|是|数据磁盘写入字节数/秒(已弃用)|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|SlotId|
|每磁盘写入操作数/秒|是|数据磁盘写入操作数/秒(已弃用)|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|SlotId|
|CPU 百分比|是|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|VMName|
|高级数据磁盘缓存读取命中|是|高级数据磁盘缓存读取命中（预览版）|百分比|平均值|高级数据磁盘缓存读取命中|LUN、VMName|
|高级数据磁盘缓存读取未命中|是|高级数据磁盘缓存读取未命中（预览版）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN、VMName|
|高级 OS 磁盘缓存读取命中|是|高级 OS 磁盘缓存读取命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取命中|VMName|
|高级 OS 磁盘缓存读取未命中|是|高级 OS 磁盘缓存读取未命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取未命中|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|度量值|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|已用 CPU 信用额度|是|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无维度|
|剩余 CPU 信用额度|是|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无维度|
|数据磁盘队列深度|是|数据磁盘队列深度（预览版）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN|
|数据磁盘读取字节数/秒|是|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|LUN|
|数据磁盘读取操作数/秒|是|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|LUN|
|数据磁盘写入字节数/秒|是|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|LUN|
|数据磁盘写入操作数/秒|是|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|LUN|
|磁盘读取字节数|是|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|无维度|
|磁盘读取操作次数/秒|是|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无维度|
|磁盘写入字节数|是|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节数|无维度|
|磁盘写入操作次数/秒|是|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无维度|
|入站流量|是|入站流数|计数|平均值|入站流数是入站方向的当前流（传入 VM 的流量）的数目|无维度|
|入站流最大创建速率|是|入站流最大创建速率|每秒计数|平均值|入站流（传入 VM 的流量）的最大创建速率|无维度|
|网络传入|是|可计费网络（已弃用）|字节|总计|虚拟机在所有网络接口上收到的可计费字节数（传入流量）（已弃用）|无维度|
|网络传入流量总计|是|网络传入流量总计|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无维度|
|网络传出|是|可计费网络流出量（已弃用）|字节|总计|通过虚拟机传出的所有网络接口上的可计费字节数（传出流量）（已弃用）|无维度|
|网络传出流量总计|是|网络传出流量总计|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无维度|
|OS 磁盘队列深度|是|OS 磁盘队列深度（预览版）|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|OS 磁盘读取字节数/秒|是|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无维度|
|OS 磁盘读取操作次数/秒|是|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无维度|
|OS 磁盘写入字节数/秒|是|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无维度|
|OS 磁盘写入操作次数/秒|是|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无维度|
|OS 每磁盘 QD|是|OS 磁盘 QD (已弃用)|计数|平均值|OS 磁盘队列深度(或队列长度)|无维度|
|OS 每磁盘读取字节数/秒|是|OS 磁盘读取字节数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无维度|
|OS 每磁盘读取操作数/秒|是|OS 磁盘读取操作数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无维度|
|OS 每磁盘写入字节数/秒|是|OS 磁盘写入字节数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无维度|
|OS 每磁盘写入操作数/秒|是|OS 磁盘写入操作数/秒(已弃用)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无维度|
|出站流|是|出站流|计数|平均值|出站流数是出站方向的当前流（传出 VM 的流量）的数目|无维度|
|出站流最大创建速率|是|出站流最大创建速率|每秒计数|平均值|出站流（传出 VM 的流量）的最大创建速率|无维度|
|每磁盘 QD|是|数据磁盘 QD (已弃用)|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|每磁盘读取字节数/秒|是|数据磁盘读取字节数/秒(已弃用)|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|SlotId|
|每磁盘读取操作数/秒|是|数据磁盘读取操作数/秒(已弃用)|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|SlotId|
|每磁盘写入字节数/秒|是|数据磁盘写入字节数/秒(已弃用)|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|SlotId|
|每磁盘写入操作数/秒|是|数据磁盘写入操作数/秒(已弃用)|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|SlotId|
|CPU 百分比|是|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无维度|
|高级数据磁盘缓存读取命中|是|高级数据磁盘缓存读取命中（预览版）|百分比|平均值|高级数据磁盘缓存读取命中|LUN|
|高级数据磁盘缓存读取未命中|是|高级数据磁盘缓存读取未命中（预览版）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN|
|高级 OS 磁盘缓存读取命中|是|高级 OS 磁盘缓存读取命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取命中|无维度|
|高级 OS 磁盘缓存读取未命中|是|高级 OS 磁盘缓存读取未命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取未命中|无维度|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|CpuUsage|是|CPU 使用率|计数|平均值|所有核心的 CPU 使用率（以 millicore 为单位）。|containerName|
|MemoryUsage|是|内存用量|字节|平均值|总内存使用量（以字节为单位）。|containerName|
|NetworkBytesReceivedPerSecond|是|每秒接收到的网络字节数|字节|平均值|每秒接收到的网络字节数。|无维度|
|NetworkBytesTransmittedPerSecond|是|每秒传输的网络字节数|字节|平均值|每秒传输的网络字节数。|无维度|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|是|AgentPool CPU Time|秒|总计|代理池 CPU 时间，以秒为单位|无维度|
|RunDuration|是|运行持续时间|毫秒|总计|运行持续时间，以毫秒为单位|无维度|
|SuccessfulPullCount|是|成功提取计数|计数|平均值|成功的映像提取次数|无维度|
|SuccessfulPushCount|是|成功推送计数|计数|平均值|成功的映像推送次数|无维度|
|TotalPullCount|是|提取总数|计数|平均值|映像提取总次数|无维度|
|TotalPushCount|是|推送总数|计数|平均值|映像推送总次数|无维度|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|否|托管群集中可用 CPU 内核的总数|计数|平均值|托管群集中可用 CPU 内核的总数|无维度|
|kube_node_status_allocatable_memory_bytes|否|托管群集中可用内存的总量|字节|平均值|托管群集中可用内存的总量|无维度|
|kube_node_status_condition|否|各种节点条件的状态|计数|平均值|各种节点条件的状态|condition、status、status2、node|
|kube_pod_status_phase|否|依据阶段的 Pod 数|计数|平均值|依据阶段的 Pod 数|阶段、命名空间、Pod|
|kube_pod_status_ready|否|就绪状态下的 Pod 数|计数|平均值|就绪状态下的 Pod 数|命名空间、Pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|FailedRequests|是|失败的请求数|计数|总计|获取自定义资源提供程序的可用日志|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|是|成功的请求数|计数|总计|自定义提供程序发出的成功请求|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AvailableCapacity|是|可用容量|字节|平均值|报告期间可用容量(以字节为单位)。|无维度|
|BytesUploadedToCloud|是|已上传的云字节数（设备）|字节|平均值|报告期间从设备上传到 Azure 的总字节数。|无维度|
|BytesUploadedToCloudPerShare|是|已上传的云字节数（共享）|字节|平均值|报告期间从共享上传到 Azure 的总字节数。|共享|
|CloudReadThroughput|是|云下载吞吐量|每秒字节数|平均值|报告期间到 Azure 的云下载吞吐量。|无维度|
|CloudReadThroughputPerShare|是|云下载吞吐量（共享）|每秒字节数|平均值|报告期间从共享到 Azure 的下载吞吐量。|共享|
|CloudUploadThroughput|是|云上传吞吐量|每秒字节数|平均值|报告期间到 Azure 的云上传吞吐量。|无维度|
|CloudUploadThroughputPerShare|是|云上传吞吐量（共享）|每秒字节数|平均值|报告期间从共享到 Azure 的上传吞吐量。|共享|
|HyperVMemoryUtilization|是|Edge 计算 - 内存使用|百分比|平均值|正在使用的 RAM 量|InstanceName|
|HyperVVirtualProcessorUtilization|是|Edge 计算 - CPU 百分比|百分比|平均值|CPU 使用量的百分比|InstanceName|
|NICReadThroughput|是|读取吞吐量（网络）|每秒字节数|平均值|报告期间，针对网关中的所有卷，设备网络接口的读取吞吐量。|InstanceName|
|NICWriteThroughput|是|写入吞吐量（网络）|每秒字节数|平均值|报告期间，针对网关中的所有卷，设备网络接口的写入吞吐量。|InstanceName|
|TotalCapacity|是|总容量|字节|平均值|总容量|无维度|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|FailedRuns|是|失败的运行次数|计数|总计||pipelineName、activityName|
|SuccessfulRuns|是|成功的运行次数|计数|总计||pipelineName、activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|是|已取消的活动运行指标数|计数|总计||ActivityType、PipelineName、FailureType、Name|
|ActivityFailedRuns|是|失败的活动运行数指标|计数|总计||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|是|成功的活动运行数指标|计数|总计||ActivityType、PipelineName、FailureType、Name|
|FactorySizeInGbUnits|是|总工厂大小（以 GB 为单位）|计数|最大值||无维度|
|IntegrationRuntimeAvailableMemory|是|集成运行时可用内存|字节|平均值||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableNodeNumber|是|集成运行时可用节点计数|计数|平均值||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|是|集成运行时队列持续时间|秒|平均值||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|是|集成运行时 CPU 利用率|百分比|平均值||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeQueueLength|是|集成运行时队列长度|计数|平均值||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|是|允许的最大工厂大小（以 GB 为单位）|计数|最大值||无维度|
|MaxAllowedResourceCount|是|允许的最大实体计数|计数|最大值||无维度|
|PipelineCancelledRuns|是|已取消的管道运行指标数|计数|总计||FailureType、Name|
|PipelineFailedRuns|是|失败的管道运行数指标|计数|总计||FailureType、Name|
|PipelineSucceededRuns|是|成功的管道运行数指标|计数|总计||FailureType、Name|
|ResourceCount|是|实体总数|计数|最大值||无维度|
|TriggerCancelledRuns|是|已取消的触发器运行指标数|计数|总计||Name、FailureType|
|TriggerFailedRuns|是|失败的触发器运行数指标|计数|总计||Name、FailureType|
|TriggerSucceededRuns|是|成功的触发器运行数指标|计数|总计||Name、FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DataRead|是|读取的数据量|字节|总计|从帐户中读取的数据总量。|无维度|
|DataWritten|是|写入的数据量|字节|总计|写入帐户的数据总量。|无维度|
|ReadRequests|是|读取请求数|计数|总计|帐户的数据读取请求计数。|无维度|
|TotalStorage|是|总存储|字节|最大值|帐户中存储的数据总量。|无维度|
|WriteRequests|是|写入请求数|计数|总计|帐户的数据写入请求计数。|无维度|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|active_connections|是|活动连接数|计数|平均值|活动连接数|无维度|
|backup_storage_used|是|已用的备份存储|字节|平均值|已用的备份存储|无维度|
|connections_failed|是|失败的连接数|计数|总计|失败的连接数|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|io_consumption_percent|是|IO 百分比|百分比|平均值|IO 百分比|无维度|
|memory_percent|是|内存百分比|百分比|平均值|内存百分比|无维度|
|network_bytes_egress|是|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|是|网络传入|字节|总计|跨活动连接数的网络传入|无维度|
|seconds_behind_master|是|复制延迟（秒）|计数|最大值|复制延迟（秒）|无维度|
|serverlog_storage_limit|是|服务器存储空间上限|字节|平均值|服务器存储空间上限|无维度|
|serverlog_storage_percent|是|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无维度|
|serverlog_storage_usage|是|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无维度|
|storage_limit|是|存储限制|字节|最大值|存储限制|无维度|
|storage_percent|是|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|是|已用的存储量|字节|平均值|已用的存储量|无维度|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|active_connections|是|活动连接数|计数|平均值|活动连接数|无维度|
|backup_storage_used|是|已用的备份存储|字节|平均值|已用的备份存储|无维度|
|connections_failed|是|失败的连接数|计数|总计|失败的连接数|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|io_consumption_percent|是|IO 百分比|百分比|平均值|IO 百分比|无维度|
|memory_percent|是|内存百分比|百分比|平均值|内存百分比|无维度|
|network_bytes_egress|是|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|是|网络传入|字节|总计|跨活动连接数的网络传入|无维度|
|seconds_behind_master|是|复制延迟（秒）|计数|最大值|复制延迟（秒）|无维度|
|serverlog_storage_limit|是|服务器存储空间上限|字节|最大值|服务器存储空间上限|无维度|
|serverlog_storage_percent|是|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无维度|
|serverlog_storage_usage|是|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无维度|
|storage_limit|是|存储限制|字节|最大值|存储限制|无维度|
|storage_percent|是|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|是|已用的存储量|字节|平均值|已用的存储量|无维度|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|active_connections|是|活动连接数|计数|平均值|活动连接数|无维度|
|backup_storage_used|是|已用的备份存储|字节|平均值|已用的备份存储|无维度|
|connections_failed|是|失败的连接数|计数|总计|失败的连接数|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|io_consumption_percent|是|IO 百分比|百分比|平均值|IO 百分比|无维度|
|memory_percent|是|内存百分比|百分比|平均值|内存百分比|无维度|
|network_bytes_egress|是|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|是|网络传入|字节|总计|跨活动连接数的网络传入|无维度|
|pg_replica_log_delay_in_bytes|是|副本的最大滞后时间|字节|最大值|滞后时间最长的副本的滞后量（字节）|无维度|
|pg_replica_log_delay_in_seconds|是|副本滞后时间|秒|最大值|复制延迟（秒）|无维度|
|serverlog_storage_limit|是|服务器存储空间上限|字节|最大值|服务器存储空间上限|无维度|
|serverlog_storage_percent|是|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无维度|
|serverlog_storage_usage|是|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无维度|
|storage_limit|是|存储限制|字节|最大值|存储限制|无维度|
|storage_percent|是|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|是|已用的存储量|字节|平均值|已用的存储量|无维度|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|active_connections|是|活动连接数|计数|平均值|活动连接数|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|iops|是|IOPS|计数|平均值|每秒 IO 操作次数|无维度|
|memory_percent|是|内存百分比|百分比|平均值|内存百分比|无维度|
|network_bytes_egress|是|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|是|网络传入|字节|总计|跨活动连接数的网络传入|无维度|
|storage_percent|是|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|是|已用的存储量|字节|平均值|已用的存储量|无维度|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|active_connections|是|活动连接数|计数|平均值|活动连接数|无维度|
|connections_failed|是|失败的连接数|计数|总计|失败的连接数|无维度|
|connections_succeeded|是|成功连接数|计数|总计|成功连接数|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|iops|是|IOPS|计数|平均值|每秒 IO 操作次数|无维度|
|maximum_used_transactionIDs|是|最大已用事务 ID 数|计数|平均值|最大已用事务 ID 数|无维度|
|memory_percent|是|内存百分比|百分比|平均值|内存百分比|无维度|
|network_bytes_egress|是|网络传出|字节|总计|跨活动连接数的网络传出|无维度|
|network_bytes_ingress|是|网络传入|字节|总计|跨活动连接数的网络传入|无维度|
|storage_percent|是|存储空间百分比|百分比|平均值|存储空间百分比|无维度|
|storage_used|是|已用的存储量|字节|平均值|已用的存储量|无维度|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|是|丢弃的 C2D 消息数|计数|总计|设备丢弃的云到设备消息数|无维度|
|c2d.commands.egress.complete.success|是|已完成的 C2D 消息传递次数|计数|总计|设备成功完成的云到设备消息传递次数|无维度|
|c2d.commands.egress.reject.success|是|拒绝的 C2D 消息数|计数|总计|设备拒绝的云到设备消息数|无维度|
|c2d.methods.failure|是|失败的直接方法调用数|计数|总计|所有失败直接方法调用的计数。|无维度|
|c2d.methods.requestSize|是|直接方法调用的请求大小|字节|平均值|所有成功直接方法请求的平均、最小和最大大小。|无维度|
|c2d.methods.responseSize|是|直接方法调用的响应大小|字节|平均值|所有成功直接方法响应的平均、最小和最大大小。|无维度|
|c2d.methods.success|是|成功的直接方法调用数|计数|总计|所有成功的直接方法调用的计数。|无维度|
|c2d.twin.read.failure|是|后端的失败孪生读取数|计数|总计|由后端发起的所有失败孪生读取的计数。|无维度|
|c2d.twin.read.size|是|后端的孪生读取的响应大小|字节|平均值|由后端发起的所有成功的孪生读取的平均、最小和最大大小。|无维度|
|c2d.twin.read.success|是|后端的成功孪生读取数|计数|总计|由后端发起的所有成功孪生读取的计数。|无维度|
|c2d.twin.update.failure|是|后端的失败孪生更新数|计数|总计|由后端发起的所有失败孪生更新的计数。|无维度|
|c2d.twin.update.size|是|后端的失败孪生更新大小|字节|平均值|由后端发起的所有成功孪生更新的平均、最小和最大大小。|无维度|
|c2d.twin.update.success|是|后端的成功孪生更新数|计数|总计|由后端发起的所有成功孪生更新的计数。|无维度|
|C2DMessagesExpired|是|过期的 C2D 消息数（预览版）|计数|总计|过期的云到设备消息数|无维度|
|配置|是|配置指标|计数|总计|配置操作的指标|无维度|
|connectedDeviceCount|否|连接设备数（预览）|计数|平均值|已连接到 IoT 中心的设备数目|无维度|
|d2c.endpoints.egress.builtIn.events|是|路由：消息传送到消息/事件的次数|计数|总计|IoT 中心路由成功将消息传送到内置终结点（消息/事件）的次数。|无维度|
|d2c.endpoints.egress.eventHubs|是|路由：消息传送到事件中心的次数|计数|总计|IoT 中心路由成功将消息传送到事件中心终结点的次数。|无维度|
|d2c.endpoints.egress.serviceBusQueues|是|路由：消息传送到服务总线队列的次数|计数|总计|IoT 中心路由成功将消息传送到服务总线队列终结点的次数。|无维度|
|d2c.endpoints.egress.serviceBusTopics|是|路由：消息传送到服务总线主题的次数|计数|总计|IoT 中心路由成功将消息传送到服务总线主题终结点的次数。|无维度|
|d2c.endpoints.egress.storage|是|路由：消息传送到存储的次数|计数|总计|IoT 中心路由成功将消息传送到存储终结点的次数。|无维度|
|d2c.endpoints.egress.storage.blobs|是|路由：将 Blob 传送到存储的次数|计数|总计|IoT 中心路由将 Blob 传送到存储终结点的次数。|无维度|
|d2c.endpoints.egress.storage.bytes|是|路由：传送到存储的数据量|字节|总计|IoT 中心路由传送到存储终结点的数据量（字节）。|无维度|
|d2c.endpoints.latency.builtIn.events|是|路由：消息/事件的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入内置终结点（消息/事件）之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.latency.eventHubs|是|路由：事件中心的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入事件中心终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.latency.serviceBusQueues|是|路由：服务总线队列的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线队列终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.latency.serviceBusTopics|是|路由：服务总线主题的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线主题终结点之间的平均延迟（毫秒）|无维度|
|d2c.endpoints.latency.storage|是|路由：存储的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入存储终结点之间的平均延迟（毫秒）。|无维度|
|d2c.telemetry.egress.dropped|是|路由：遥测消息删除次数 |计数|总计|由于终结点消亡，IoT 中心路由删除消息的次数。 此值不会统计已传送到回退路由的消息，因为已删除的消息不会传送到回退路由。|无维度|
|d2c.telemetry.egress.fallback|是|路由：消息传送到回退路由的次数|计数|总计|IoT 中心路由将消息传送到与回退路由关联的终结点的次数。|无维度|
|d2c.telemetry.egress.invalid|是|路由：遥测消息不兼容|计数|总计|消息由于与终结点不兼容而无法由 IoT 中心路由传送的次数。 此值不包括重试次数。|无维度|
|d2c.telemetry.egress.orphaned|是|路由：遥测消息孤立次数 |计数|总计|消息由于与任何路由规则（包括回退规则）都不匹配而被 IoT 中心路由孤立的次数。 |无维度|
|d2c.telemetry.egress.success|是|路由：遥测消息传送次数|计数|总计|使用 IoT 中心路由将消息成功传送到所有终结点的次数。 如果某条消息已路由到多个终结点，则每成功传送一次，此值就会加 1。 如果某条消息多次路由到同一终结点，则每成功传送一次，此值就会加 1。|无维度|
|d2c.telemetry.ingress.allProtocol|是|遥测消息发送尝试次数|计数|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|d2c.telemetry.ingress.sendThrottle|是|限制错误数|计数|总计|由于设备吞吐量限制而导致的限制错误数|无维度|
|d2c.telemetry.ingress.success|是|发送的遥测消息数|计数|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|d2c.twin.read.failure|是|设备的失败孪生读取数|计数|总计|由设备发起的所有失败孪生读取的计数。|无维度|
|d2c.twin.read.size|是|设备的孪生读取的响应大小|字节|平均值|由设备发起的所有成功的孪生读取的平均、最小和最大大小。|无维度|
|d2c.twin.read.success|是|设备的成功孪生读取数|计数|总计|由设备发起的所有成功孪生读取的计数。|无维度|
|d2c.twin.update.failure|是|设备的失败孪生更新数|计数|总计|由设备发起的所有失败的孪生更新的计数。|无维度|
|d2c.twin.update.size|是|设备的孪生更新的大小|字节|平均值|由设备发起的所有成功孪生更新的平均、最小和最大大小。|无维度|
|d2c.twin.update.success|是|设备的成功孪生更新数|计数|总计|由设备发起的所有成功的孪生更新的计数。|无维度|
|dailyMessageQuotaUsed|是|已使用的消息总数|计数|最大值|今天使用的消息总数|无维度|
|deviceDataUsage|是|设备数据用量总计|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无维度|
|deviceDataUsageV2|是|设备数据用量总计（预览）|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无维度|
|devices.connectedDevices.allProtocol|是|连接的设备数（已弃用） |计数|总计|已连接到 IoT 中心的设备数目|无维度|
|devices.totalDevices|是|设备总数（已弃用）|计数|总计|已注册到 IoT 中心的设备数目|无维度|
|EventGridDeliveries|是|事件网格传送（预览版）|计数|总计|发布到事件网格的 IoT 中心事件的数量。 使用 Result 维度表示成功和失败请求的数量。 EventType 维度显示事件的类型 (https://aka.ms/ioteventgrid) 。|Result、EventType|
|EventGridLatency|是|事件网格延迟（预览）|毫秒|平均值|从生成 IoT 中心事件到将事件发布到事件网格的平均延迟（毫秒）。 此数值是所有事件类型的平均。 若要查看特定事件类型的延迟，请使用 EventType 维度。|EventType|
|jobs.cancelJob.failure|是|失败的作业取消数|计数|总计|用来取消作业的调用失败的次数。|无维度|
|jobs.cancelJob.success|是|成功的作业取消数|计数|总计|用来取消作业的调用成功的次数。|无维度|
|jobs.completed|是|已完成的作业|计数|总计|所有已完成的作业的计数。|无维度|
|jobs.createDirectMethodJob.failure|是|方法调用作业的创建失败数|计数|总计|直接方法调用作业创建失败的所有次数。|无维度|
|jobs.createDirectMethodJob.success|是|方法调用作业的创建成功数|计数|总计|直接方法调用作业创建成功的所有次数。|无维度|
|jobs.createTwinUpdateJob.failure|是|孪生更新作业创建失败数|计数|总计|孪生更新作业创建失败的所有次数。|无维度|
|jobs.createTwinUpdateJob.success|是|孪生更新作业创建成功数|计数|总计|孪生更新作业创建成功的所有次数。|无维度|
|jobs.failed|是|失败的作业数|计数|总计|所有失败的作业的计数。|无维度|
|jobs.listJobs.failure|是|对列出作业的失败调用数|计数|总计|对列出作业的所有失败调用的计数。|无维度|
|jobs.listJobs.success|是|对列出作业的成功调用数|计数|总计|对列出作业的所有成功调用的计数。|无维度|
|jobs.queryJobs.failure|是|失败的作业查询数|计数|总计|对查询作业的所有失败调用的计数。|无维度|
|jobs.queryJobs.success|是|成功的作业查询数|计数|总计|对查询作业的所有成功调用的计数。|无维度|
|RoutingDataSizeInBytesDelivered|是|Routing Delivery Message Size in Bytes (preview)|字节|总计|由 IoT 中心传递到终结点的消息的总大小（以字节为单位）。 可以使用 EndpointName 和 EndpointType 维度查看传递到不同终结点的消息的大小（以字节为单位）。 对于每个传递的消息，指标值都会增加，包括将消息传递到多个终结点或将消息多次传递到同一终结点。|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|是|路由传递（预览版）|计数|总计|IoT 中心尝试使用路由将消息传送到所有终结点的次数。 若要查看成功或失败尝试的次数，请使用 Result 维度。 若要查看失败的原因，例如无效、已删除或孤立，请使用 FailureReasonCategory 维度。 还可以使用 EndpointName 和 EndpointType 维度来了解传递到不同终结点的消息数。 每次传递尝试，指标值都会增加 1，包括将消息传递到多个终结点或将消息多次传递到同一终结点。|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|是|路由传递延迟（预览版）|毫秒|平均值|消息进入 IoT 中心与遥测消息进入终结点之间的平均延迟（毫秒）。 可以使用 EndpointName 和 EndpointType 维度来了解不同终结点的延迟。|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|否|设备总数（预览）|计数|平均值|已注册到 IoT 中心的设备数目|无维度|
|twinQueries.failure|是|失败的孪生查询数|计数|总计|所有失败孪生查询的计数。|无维度|
|twinQueries.resultSize|是|孪生查询结果大小|字节|平均值|所有成功孪生查询的结果大小的平均值、最小值和最大值。|无维度|
|twinQueries.success|是|成功的孪生查询数|计数|总计|所有成功孪生查询的计数。|无维度|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AttestationAttempts|是|证明尝试次数|计数|总计|已尝试的设备证明次数|ProvisioningServiceName、Status、Protocol|
|DeviceAssignments|是|已分配设备|计数|总计|已分配给 IoT 中心的设备数|ProvisioningServiceName、IotHubName|
|RegistrationAttempts|是|注册尝试次数|计数|总计|已尝试的设备注册次数|ProvisioningServiceName、IotHubName、Status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AddRegion|是|添加的区域数|计数|计数|添加的区域数|区域|
|AutoscaleMaxThroughput|否|Autoscale Max Throughput|计数|最大值|自动缩放最大吞吐量|DatabaseName、CollectionName|
|AvailableStorage|否|（已弃用）Available Storage|字节|总计|“Available Storage”将于 2020 年 9 月底从 Azure Monitor 中删除。 Cosmos DB 集合存储大小目前是无限制的。 唯一的限制是每个逻辑分区键的存储大小是 20GB。 可以在诊断日志中启用 PartitionKeyStatistics，了解顶部分区键的存储消耗量。 有关 Cosmos DB 存储配额的详细信息，请查看此文档 https://docs.microsoft.com/azure/cosmos-db/concepts-limits 。 在弃用之后，仍在已弃用指标上定义的其余警报规则将在提交弃用日期后自动禁用。|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|否|Cassandra 连接关闭数|计数|总计|已关闭的 Cassandra 连接数，按 1 分钟粒度报告|Region、ClosureReason|
|CassandraConnectorAvgReplicationLatency|否|Cassandra Connector Average ReplicationLatency|毫秒|平均值|Cassandra 连接器平均复制延迟|无维度|
|CassandraConnectorReplicationHealthStatus|否|Cassandra Connector Replication Health Status|计数|计数|Cassandra 连接器复制运行状况|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|否|已创建 Cassandra 密钥空间|计数|计数|已创建 Cassandra 密钥空间|ResourceName, |
|CassandraKeyspaceDelete|否|已删除 Cassandra 密钥空间|计数|计数|已删除 Cassandra 密钥空间|ResourceName, |
|CassandraKeyspaceThroughputUpdate|否|已更新 Cassandra 密钥空间吞吐量|计数|计数|已更新 Cassandra 密钥空间吞吐量|ResourceName, |
|CassandraKeyspaceUpdate|否|已更新 Cassandra 密钥空间|计数|计数|已更新 Cassandra 密钥空间|ResourceName, |
|CassandraRequestCharges|否|Cassandra 请求费用|计数|总计|发出的 Cassandra 请求所消耗的 RU 数|DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|否|Cassandra 请求数|计数|计数|发出的 Cassandra 请求数|DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|CassandraTableCreate|否|已创建 Cassandra 表|计数|计数|已创建 Cassandra 表|ResourceName, ChildResourceName, |
|CassandraTableDelete|否|Cassandra 表已删除|计数|计数|Cassandra 表已删除|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|否|Cassandra 表吞吐量已更新|计数|计数|Cassandra 表吞吐量已更新|ResourceName, ChildResourceName, |
|CassandraTableUpdate|否|Cassandra 表已更新|计数|计数|Cassandra 表已更新|ResourceName, ChildResourceName, |
|CreateAccount|是|创建的帐户数|计数|计数|创建的帐户数|无维度|
|DataUsage|否|数据使用情况|字节|总计|按 5 分钟粒度报告的总数据用量|CollectionName、DatabaseName、Region|
|DeleteAccount|是|删除的帐户数|计数|计数|删除的帐户数|无维度|
|DocumentCount|否|文档计数|计数|总计|按 5 分钟粒度报告的文档总数|CollectionName、DatabaseName、Region|
|DocumentQuota|否|文档配额|字节|总计|按 5 分钟粒度报告的存储配额总量|CollectionName、DatabaseName、Region|
|GremlinDatabaseCreate|否|已创建 Gremlin 数据库|计数|计数|已创建 Gremlin 数据库|ResourceName, |
|GremlinDatabaseDelete|否|已删除 Gremlin 数据库|计数|计数|已删除 Gremlin 数据库|ResourceName, |
|GremlinDatabaseThroughputUpdate|否|已更新 Gremlin 数据库吞吐量|计数|计数|已更新 Gremlin 数据库吞吐量|ResourceName, |
|GremlinDatabaseUpdate|否|已更新 Gremlin 数据库|计数|计数|已更新 Gremlin 数据库|ResourceName, |
|GremlinGraphCreate|否|已创建 Gremlin 图形|计数|计数|已创建 Gremlin 图形|ResourceName, ChildResourceName, |
|GremlinGraphDelete|否|已删除 Gremlin 图形|计数|计数|已删除 Gremlin 图形|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|否|已更新 Gremlin 图形吞吐量|计数|计数|已更新 Gremlin 图形吞吐量|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|否|已更新 Gremlin 图形|计数|计数|已更新 Gremlin 图形|ResourceName, ChildResourceName, |
|IndexUsage|否|索引使用情况|字节|总计|按 5 分钟粒度报告的总索引用量|CollectionName、DatabaseName、Region|
|MetadataRequests|否|元数据请求|计数|计数|元数据请求的计数。 Cosmos DB 为每个帐户维护系统元数据集合，允许你免费枚举集合、数据库及其配置等等。|DatabaseName、CollectionName、Region、StatusCode |
|MongoCollectionCreate|否|已创建 Mongo 集合|计数|计数|已创建 Mongo 集合|ResourceName, ChildResourceName, |
|MongoCollectionDelete|否|已删除 Mongo 集合|计数|计数|已删除 Mongo 集合|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|否|已更新 Mongo 集合吞吐量|计数|计数|已更新 Mongo 集合吞吐量|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|否|Mongo 集合已更新|计数|计数|Mongo 集合已更新|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|否|Mongo 数据库已删除|计数|计数|Mongo 数据库已删除|ResourceName, |
|MongoDatabaseThroughputUpdate|否|已更新 Mongo 数据库吞吐量|计数|计数|已更新 Mongo 数据库吞吐量|ResourceName, |
|MongoDBDatabaseCreate|否|已创建 Mongo 数据库|计数|计数|已创建 Mongo 数据库|ResourceName, |
|MongoDBDatabaseUpdate|否|已更新 Mongo 数据库|计数|计数|已更新 Mongo 数据库|ResourceName, |
|MongoRequestCharge|是|Mongo 请求费用|计数|总计|Mongo 已消耗的请求单位|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|是|Mongo 请求|计数|计数|已发出的 Mongo 请求数|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|否|Mongo 请求速率|每秒计数|平均值|每秒 Mongo 请求计数|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|否|Mongo 删除请求速率|每秒计数|平均值|每秒 Mongo 删除请求数|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|否|Mongo 插入请求速率|每秒计数|平均值|每秒 Mongo 插入计数|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|否|Mongo 查询请求速率|每秒计数|平均值|每秒 Mongo 查询请求数|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|否|Mongo 更新请求速率|每秒计数|平均值|每秒 Mongo 更新请求数|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|否|规范化 RU 消耗量|百分比|最大值|每分钟最大 RU 消耗百分比|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|否|预配的吞吐量|计数|最大值|预配的吞吐量|DatabaseName、CollectionName|
|RegionFailover|是|故障转移的区域数|计数|计数|故障转移的区域数|无维度|
|RemoveRegion|是|删除的区域数|计数|计数|删除的区域数|区域|
|ReplicationLatency|是|P99 复制延迟|毫秒|平均值|启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟|SourceRegion、TargetRegion|
|ServerSideLatency|否|服务器端延迟|毫秒|平均值|服务器端延迟|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|否|服务可用性|百分比|平均值|一小时、一天或一月粒度的帐户请求可用性|无维度|
|SqlContainerCreate|否|Sql Container Created|计数|计数|已创建 SQL 容器|ResourceName, ChildResourceName, |
|SqlContainerDelete|否|Sql 容器已删除|计数|计数|Sql 容器已删除|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|否|已更新 Sql 容器吞吐量|计数|计数|已更新 Sql 容器吞吐量|ResourceName, ChildResourceName, |
|SqlContainerUpdate|否|已更新 Sql 容器|计数|计数|已更新 Sql 容器|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|否|Sql Database Created|计数|计数|已创建 SQL 数据库|ResourceName, |
|SqlDatabaseDelete|否|Sql 数据库已删除|计数|计数|Sql 数据库已删除|ResourceName, |
|SqlDatabaseThroughputUpdate|否|已更新 Sql 数据库吞吐量|计数|计数|已更新 Sql 数据库吞吐量|ResourceName, |
|SqlDatabaseUpdate|否|已更新 Sql 数据库|计数|计数|已更新 Sql 数据库|ResourceName, |
|TableTableCreate|否|已创建 AzureTable 表|计数|计数|已创建 AzureTable 表|ResourceName, |
|TableTableDelete|否|AzureTable 表已删除|计数|计数|AzureTable 表已删除|ResourceName, |
|TableTableThroughputUpdate|否|AzureTable 表吞吐量已更新|计数|计数|AzureTable 表吞吐量已更新|ResourceName, |
|TableTableUpdate|否|已更新 AzureTable 表|计数|计数|已更新 AzureTable 表|ResourceName, |
|TotalRequests|是|请求总数|计数|计数|已发出的请求数|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|是|总请求单位数|计数|总计|已消耗的请求单位|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|是|更新的帐户密钥数|计数|计数|更新的帐户密钥数|KeyType|
|UpdateAccountNetworkSettings|是|更新的帐户网络设置数|计数|计数|更新的帐户网络设置数|无维度|
|UpdateAccountReplicationSettings|是|更新的帐户复制设置数|计数|计数|更新的帐户复制设置数|无维度|
|UpdateDiagnosticsSettings|否|已更新帐户诊断设置|计数|计数|已更新帐户诊断设置|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|Topic, ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|主题|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|无维度|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|无维度|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|无维度|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
|UnmatchedEventCount|是|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason、EventSubscriptionName|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
|UnmatchedEventCount|是|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason、EventSubscriptionName|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
|UnmatchedEventCount|是|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|计数|平均值|Microsoft.EventHub 的活动连接总数。|无维度|
|AvailableMemory|否|可用内存|百分比|最大值|事件中心群集的可用内存，以总内存量的百分比表示。|角色|
|CaptureBacklog|否|捕获积压工作(backlog)。|计数|总计|捕获有关 Microsoft.EventHub 的积压工作(backlog)。|无维度|
|CapturedBytes|否|已捕获的字节数。|字节|总计|Microsoft.EventHub 已捕获的字节数。|无维度|
|CapturedMessages|否|已捕获的消息数。|计数|总计|Microsoft.EventHub 已捕获的消息数。|无维度|
|ConnectionsClosed|否|已关闭的连接数。|计数|平均值|Microsoft.EventHub 已关闭的连接数。|无维度|
|ConnectionsOpened|否|打开的连接数。|计数|平均值|Microsoft.EventHub 打开的连接数。|无维度|
|CPU|否|CPU|百分比|最大值|事件中心群集的 CPU 使用率（百分比）|角色|
|IncomingBytes|是|传入字节数。|字节|总计|Microsoft.EventHub 传入的字节数。|无维度|
|IncomingMessages|是|传入消息数|计数|总计|Microsoft.EventHub 传入的消息数。|无维度|
|IncomingRequests|是|传入请求数|计数|总计|Microsoft.EventHub 传入的请求数。|无维度|
|OutgoingBytes|是|传出字节数。|字节|总计|Microsoft.EventHub 传出的字节数。|无维度|
|OutgoingMessages|是|传出消息数|计数|总计|Microsoft.EventHub 传出的消息数。|无维度|
|QuotaExceededErrors|否|超过限额错误。|计数|总计|Microsoft.EventHub 的超过限额错误数。|无维度|
|ServerErrors|否|服务器错误数。|计数|总计|Microsoft.EventHub 的服务器错误数。|无维度|
|大小|否|大小|字节|平均值|EventHub 的大小（以字节为单位）。|角色|
|SuccessfulRequests|否|成功的请求数|计数|总计|Microsoft.EventHub 成功请求数。|无维度|
|ThrottledRequests|否|限制的请求数。|计数|总计|Microsoft.EventHub 限制的请求数。|无维度|
|UserErrors|否|用户错误数。|计数|总计|Microsoft.EventHub 用户错误数。|无维度|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|计数|平均值|Microsoft.EventHub 的活动连接总数。|无维度|
|CaptureBacklog|否|捕获积压工作(backlog)。|计数|总计|捕获有关 Microsoft.EventHub 的积压工作(backlog)。|EntityName|
|CapturedBytes|否|已捕获的字节数。|字节|总计|Microsoft.EventHub 已捕获的字节数。|EntityName|
|CapturedMessages|否|已捕获的消息数。|计数|总计|Microsoft.EventHub 已捕获的消息数。|EntityName|
|ConnectionsClosed|否|已关闭的连接数。|计数|平均值|Microsoft.EventHub 已关闭的连接数。|EntityName|
|ConnectionsOpened|否|打开的连接数。|计数|平均值|Microsoft.EventHub 打开的连接数。|EntityName|
|EHABL|是|存档积压工作消息数（已弃用）|计数|总计|命名空间积压工作中的事件中心存档消息数（已弃用）|无维度|
|EHAMBS|是|存档消息吞吐量（已弃用）|字节|总计|命名空间中的事件中心存档消息吞吐量（已弃用）|无维度|
|EHAMSGS|是|存档消息数（已弃用）|计数|总计|命名空间中的事件中心存档消息数（已弃用）|无维度|
|EHINBYTES|是|传入字节（已弃用）|字节|总计|命名空间的事件中心传入消息吞吐量（已弃用）|无维度|
|EHINMBS|是|传入字节数（已过时）（已弃用）|字节|总计|命名空间的事件中心传入消息吞吐量。 此指标已弃用。 请改用传入字节指标（已弃用）|无维度|
|EHINMSGS|是|传入消息（已弃用）|计数|总计|命名空间的传入消息总数（已弃用）|无维度|
|EHOUTBYTES|是|传出字节（已弃用）|字节|总计|命名空间的事件中心传出消息吞吐量（已弃用）|无维度|
|EHOUTMBS|是|传出字节数（已过时）（已弃用）|字节|总计|命名空间的事件中心传出消息吞吐量。 此指标已弃用。 请改用传出字节指标（已弃用）|无维度|
|EHOUTMSGS|是|传出消息（已弃用）|计数|总计|命名空间的传出消息总数（已弃用）|无维度|
|FAILREQ|是|失败的请求数（已弃用）|计数|总计|命名空间的失败请求总数（已弃用）|无维度|
|IncomingBytes|是|传入字节数。|字节|总计|Microsoft.EventHub 传入的字节数。|EntityName|
|IncomingMessages|是|传入消息数|计数|总计|Microsoft.EventHub 传入的消息数。|EntityName|
|IncomingRequests|是|传入请求数|计数|总计|Microsoft.EventHub 传入的请求数。|EntityName|
|INMSGS|是|传入的消息数（已过时）（已弃用）|计数|总计|命名空间的传入消息总数。 此指标已弃用。 请改用传入消息指标（已弃用）|无维度|
|INREQS|是|传入的请求数（已弃用）|计数|总计|命名空间的传入发送请求总数（已弃用）|无维度|
|INTERR|是|内部服务器错误数（已弃用）|计数|总计|命名空间的内部服务器错误总数（已弃用）|无维度|
|MISCERR|是|其他错误（已弃用）|计数|总计|命名空间的失败请求总数（已弃用）|无维度|
|OutgoingBytes|是|传出字节数。|字节|总计|Microsoft.EventHub 传出的字节数。|EntityName|
|OutgoingMessages|是|传出消息数|计数|总计|Microsoft.EventHub 传出的消息数。|EntityName|
|OUTMSGS|是|传出的消息数（已过时）（已弃用）|计数|总计|命名空间的传出消息总数。 此指标已弃用。 请改用传出消息指标（已弃用）|无维度|
|QuotaExceededErrors|否|超过限额错误。|计数|总计|Microsoft.EventHub 的超过限额错误数。|EntityName、 |
|ServerErrors|否|服务器错误数。|计数|总计|Microsoft.EventHub 的服务器错误数。|EntityName、 |
|大小|否|大小|字节|平均值|EventHub 的大小（以字节为单位）。|EntityName|
|SuccessfulRequests|否|成功的请求数|计数|总计|Microsoft.EventHub 成功请求数。|EntityName、 |
|SUCCREQ|是|成功的请求数（已弃用）|计数|总计|命名空间的成功请求总数（已弃用）|无维度|
|SVRBSY|是|服务器繁忙错误数（已弃用）|计数|总计|命名空间的服务器繁忙错误总数（已弃用）|无维度|
|ThrottledRequests|否|限制的请求数。|计数|总计|Microsoft.EventHub 限制的请求数。|EntityName、 |
|UserErrors|否|用户错误数。|计数|总计|Microsoft.EventHub 用户错误数。|EntityName、 |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|是|已分类的网关请求数|计数|总计|按类别（1xx/2xx/3xx/4xx/5xx）统计的网关请求数|HttpStatus|
|GatewayRequests|是|网关请求数|计数|总计|网关请求数|HttpStatus|
|NumActiveWorkers|是|活动辅助角色数目|计数|最大值|活动辅助角色数目|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|MetricThreshold|是|指标阈值|计数|平均值|自动缩放运行时已配置的自动缩放阈值。|MetricTriggerRule|
|ObservedCapacity|是|观察到的容量|计数|平均值|自动缩放执行时报告的容量。|无维度|
|ObservedMetricValue|是|观察到的指标值|计数|平均值|执行自动缩放时计算的值|MetricTriggerSource|
|ScaleActionsInitiated|是|启动的缩放操作|计数|总计|缩放操作的方向。|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|是|可用性|百分比|平均值|已成功完成的可用性测试百分比|availabilityResult/name、availabilityResult/location|
|availabilityResults/count|否|可用性测试|计数|计数|可用性测试计数|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|是|可用性测试持续时间|毫秒|平均值|可用性测试持续时间|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|是|页面加载网络连接时间|毫秒|平均值|用户请求和网络连接之间的时间。 包括 DNS 查找和传输连接。|无维度|
|browserTimings/processingDuration|是|客户端处理时间|毫秒|平均值|从接收文档的最后一个字节到 DOM 加载完之间的时间。 可能仍在处理异步请求。|无维度|
|browserTimings/receiveDuration|是|接收响应时间|毫秒|平均值|第一个和最后一个字节之间的时间，或直至断开连接的时间。|无维度|
|browserTimings/sendDuration|是|发送请求时间|毫秒|平均值|网络连接和接收第一个字节之间的时间。|无维度|
|browserTimings/totalDuration|是|浏览器页面加载时间|毫秒|平均值|从用户请求一直到 DOM、样式表、脚本和映像加载完之间的时间。|无维度|
|dependencies/count|否|依赖项调用|计数|计数|应用程序对外部资源所进行的调用计数。|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|是|依赖项持续时间|毫秒|平均值|应用程序对外部资源所进行的调用持续时间。|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|否|依赖项调用失败次数|计数|计数|应用程序对外部资源所进行的依赖项调用失败的计数。|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|exceptions/browser|否|浏览器异常|计数|计数|浏览器中所引发未捕获异常的计数。|cloud/roleName|
|exceptions/count|是|异常|计数|计数|所有未捕获异常的已合并计数。|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|否|服务器异常|计数|计数|服务器应用程序中引发的未捕获的异常计数。|cloud/roleName, cloud/roleInstance|
|pageViews/count|是|页面视图|计数|计数|页面视图计数。|operation/synthetic, cloud/roleName|
|pageViews/duration|是|页面视图加载时间|毫秒|平均值|页面视图加载时间|operation/synthetic, cloud/roleName|
|performanceCounters/exceptionsPerSecond|是|异常率|每秒计数|平均值|报告给窗口的已处理和未处理的异常的计数，这些异常包括 .NET 异常和转换为 .NET 异常的非托管异常。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|是|可用内存|字节|平均值|可立刻供进程或系统使用的物理内存。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|是|进程 CPU|百分比|平均值|所有进程线程使用处理器执行指令所用的运行时间的百分比。 介于 0 到 100 之间。 此指标仅表示 w3wp 进程的性能。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|是|进程 IO 率|每秒字节数|平均值|每秒读取和写入文件、网络和设备的总字节数。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|是|处理器时间|百分比|平均值|处理器在非空闲线程上所花费的时间百分比。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|是|进程专用字节|字节|平均值|以独占方式分配给受监视应用程序进程的内存。|cloud/roleInstance|
|performanceCounters/requestExecutionTime|是|HTTP 请求执行时间|毫秒|平均值|最近的请求执行时间。|cloud/roleInstance|
|performanceCounters/requestsInQueue|是|应用程序队列中的 HTTP 请求|计数|平均值|应用程序请求队列的长度。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|是|HTTP 请求速率|每秒计数|平均值|每秒从 ASP.NET 发出的应用程序所有请求的速率。|cloud/roleInstance|
|requests/count|否|服务器请求数|计数|计数|已完成的 HTTP 请求计数。|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|是|服务器响应时间|毫秒|平均值|从接收 HTTP 请求到完成响应发送之间的时间。|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|否|失败的请求|计数|计数|标记为失败的 HTTP 请求的计数。 在大多数情况下这些请求的响应代码 >= 400 且不等于 401。|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|否|服务器请求速率|每秒计数|平均值|每秒服务器请求速率|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|是|跟踪|计数|计数|跟踪文档计数|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|c2d.property.read.failure|是|从 IoT Central 发起的失败设备属性读取数|计数|总计|从 IoT Central 发起的所有失败属性读取计数|无维度|
|c2d.property.read.success|是|从 IoT Central 发起的成功设备属性读取数|计数|总计|从 IoT Central 发起的所有成功属性读取计数|无维度|
|c2d.property.update.failure|是|从 IoT Central 发起的失败设备属性更新数|计数|总计|从 IoT Central 发起的所有失败属性更新计数|无维度|
|c2d.property.update.success|是|从 IoT Central 发起的成功设备属性更新数|计数|总计|从 IoT Central 发起的所有成功属性更新计数|无维度|
|connectedDeviceCount|否|已连接的设备总数|计数|平均值|已连接到 IoT Central 的设备数目|无维度|
|d2c.property.read.failure|是|从设备发起的失败设备属性读取数|计数|总计|从设备发起的所有失败属性读取计数|无维度|
|d2c.property.read.success|是|从设备发起的成功设备属性读取数|计数|总计|从设备发起的所有成功属性读取计数|无维度|
|d2c.property.update.failure|是|从设备发起的失败设备属性更新数|计数|总计|从设备发起的所有失败属性更新计数|无维度|
|d2c.property.update.success|是|从设备发起的成功设备属性更新数|计数|总计|从设备发起的所有成功属性更新计数|无维度|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|保管库整体可用性|百分比|平均值|保管库请求可用性|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|否|保管库整体饱和度|百分比|平均值|已使用的保管库容量|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|是|服务 API 命中总计|计数|计数|服务 API 命中总数|ActivityType, ActivityName|
|ServiceApiLatency|是|总体服务 API 延迟|毫秒|平均值|服务 API 请求的总体延迟|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|是|服务 API 结果总计|计数|计数|服务 API 结果总数|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BatchBlobCount|是|Batch Blob Count|计数|平均值|聚合批处理中用于引入的数据源数。|数据库|
|BatchDuration|是|Batch Duration|秒|平均值|引入流中聚合阶段的持续时间。|数据库|
|BatchesProcessed|是|Batches Processed|计数|平均值|引入的聚合批处理数。 批处理完成原因：批处理是否达到批处理策略设置的批处理时间、数据大小或文件数限制|Database, SealReason|
|BatchSize|是|批大小|字节|平均值|引入的聚合批处理中未压缩的预期数据大小。|数据库|
|CacheUtilization|是|缓存利用率|百分比|平均值|群集范围内的使用率级别|无维度|
|ContinuousExportMaxLatenessMinutes|是|连续导出最大延迟|计数|最大值|群集中连续导出作业报告的延迟（分钟）|无维度|
|ContinuousExportNumOfRecordsExported|是|连续导出 – 导出的记录数|计数|总计|导出的记录数，在导出操作期间对每个写入的存储项目激发|ContinuousExportName, Database|
|ContinuousExportPendingCount|是|连续导出挂起计数|计数|最大值|做好执行准备的挂起的连续导出作业数|无维度|
|ContinuousExportResult|是|连续导出结果|计数|计数|指示连续导出是成功还是失败|ContinuousExportName, Result, Database|
|CPU|是|CPU|百分比|平均值|CPU 使用率级别|无维度|
|EventsProcessedForEventHubs|是|处理的事件数（适用于事件中心/IoT 中心）|计数|总计|从事件中心/IoT 中心引入时，由群集处理的事件数|EventStatus|
|ExportUtilization|是|导出利用率|百分比|最大值|导出利用率|无维度|
|IngestionLatencyInSeconds|是|引入延迟（秒）|秒|平均值|从源（例如消息位于事件中心）到群集的引入时间（秒）|无维度|
|IngestionResult|是|引入结果|计数|计数|引入操作的数量|IngestionResultDetails|
|IngestionUtilization|是|引入利用率|百分比|平均值|群集中已使用引入槽的比率|无维度|
|IngestionVolumeInMB|是|引入量 (MB)|计数|总计|已引入群集的数据总量 (MB)|无维度|
|InstanceCount|是|实例计数|计数|平均值|实例总计数|无维度|
|KeepAlive|是|保持活动状态|计数|平均值|完整性检查表明群集对查询做出了响应|无维度|
|QueryDuration|是|查询持续时间|毫秒|平均值|队列持续时间（秒）|QueryStatus|
|SteamingIngestRequestRate|是|流引入请求速率|计数|RateRequestsPerSecond|流引入请求速率（每秒请求数）|无维度|
|StreamingIngestDataRate|是|流引入数据速率|计数|平均值|流引入数据速率（MB/秒）|无维度|
|StreamingIngestDuration|是|流引入持续时间|毫秒|平均值|流引入持续时间（毫秒）|无维度|
|StreamingIngestResults|是|流引入结果|计数|平均值|流引入结果|结果|
|TotalNumberOfConcurrentQueries|是|并发查询总数|计数|总计|并发查询总数|无维度|
|TotalNumberOfExtents|是|盘区总数|计数|总计|数据盘区总数|无维度|
|TotalNumberOfThrottledCommands|是|受限制的命令总数|计数|总计|受限制的命令总数|CommandType|
|TotalNumberOfThrottledQueries|是|受限制的查询总数|计数|总计|受限制的查询总数|无维度|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActionLatency|是|操作延迟 |秒|平均值|已完成的工作流操作的延迟。|无维度|
|ActionsCompleted|是|完成的操作数 |计数|总计|已完成的工作流操作数目。|无维度|
|ActionsFailed|是|失败的操作数 |计数|总计|失败的工作流操作数目。|无维度|
|ActionsSkipped|是|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无维度|
|ActionsStarted|是|启动的操作数 |计数|总计|已启动的工作流操作数目。|无维度|
|ActionsSucceeded|是|成功的操作数 |计数|总计|成功的工作流操作数目。|无维度|
|ActionSuccessLatency|是|操作成功延迟 |秒|平均值|已成功的工作流操作的延迟。|无维度|
|ActionThrottledEvents|是|操作限制事件数|计数|总计|工作流操作限制事件数目。|无维度|
|IntegrationServiceEnvironmentConnectorMemoryUsage|是|集成服务环境的连接器内存使用情况|百分比|平均值|集成服务环境的连接器内存使用情况。|无维度|
|IntegrationServiceEnvironmentConnectorProcessorUsage|是|集成服务环境的连接器处理器使用情况|百分比|平均值|集成服务环境的连接器处理器使用情况。|无维度|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|是|集成服务环境的工作流内存使用情况|百分比|平均值|集成服务环境的工作流内存使用情况。|无维度|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|是|集成服务环境的工作流处理器使用情况|百分比|平均值|集成服务环境的工作流处理器使用情况。|无维度|
|RunFailurePercentage|是|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无维度|
|RunLatency|是|运行延迟|秒|平均值|已完成的工作流运行的延迟。|无维度|
|RunsCancelled|是|取消的运行数|计数|总计|已取消的工作流运行数目。|无维度|
|RunsCompleted|是|已完成的运行数|计数|总计|已完成的工作流运行数目。|无维度|
|RunsFailed|是|失败的运行数|计数|总计|失败的工作流运行数目。|无维度|
|RunsStarted|是|已启动的运行数|计数|总计|已启动的工作流运行数目。|无维度|
|RunsSucceeded|是|成功的运行数|计数|总计|成功的工作流运行数目。|无维度|
|RunStartThrottledEvents|是|运行启动限制事件|计数|总计|工作流运行启动限制事件数。|无维度|
|RunSuccessLatency|是|运行成功延迟|秒|平均值|已成功的工作流运行的延迟。|无维度|
|RunThrottledEvents|是|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无维度|
|TriggerFireLatency|是|触发器激发延迟 |秒|平均值|已激发的工作流触发器的延迟。|无维度|
|TriggerLatency|是|触发器延迟 |秒|平均值|已完成的工作流触发器的延迟。|无维度|
|TriggersCompleted|是|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无维度|
|TriggersFailed|是|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无维度|
|TriggersFired|是|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无维度|
|TriggersSkipped|是|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无维度|
|TriggersStarted|是|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无维度|
|TriggersSucceeded|是|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无维度|
|TriggerSuccessLatency|是|触发器成功延迟 |秒|平均值|已成功的工作流触发器的延迟。|无维度|
|TriggerThrottledEvents|是|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无维度|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActionLatency|是|操作延迟 |秒|平均值|已完成的工作流操作的延迟。|无维度|
|ActionsCompleted|是|完成的操作数 |计数|总计|已完成的工作流操作数目。|无维度|
|ActionsFailed|是|失败的操作数 |计数|总计|失败的工作流操作数目。|无维度|
|ActionsSkipped|是|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无维度|
|ActionsStarted|是|启动的操作数 |计数|总计|已启动的工作流操作数目。|无维度|
|ActionsSucceeded|是|成功的操作数 |计数|总计|成功的工作流操作数目。|无维度|
|ActionSuccessLatency|是|操作成功延迟 |秒|平均值|已成功的工作流操作的延迟。|无维度|
|ActionThrottledEvents|是|操作限制事件数|计数|总计|工作流操作限制事件数目。|无维度|
|BillableActionExecutions|是|计费的操作执行数|计数|总计|计费的工作流操作执行数目。|无维度|
|BillableTriggerExecutions|是|计费的触发器执行数|计数|总计|计费的工作流触发器执行数目。|无维度|
|BillingUsageNativeOperation|是|本机操作执行的计费使用情况|计数|总计|已计费的本机操作执行次数。|无维度|
|BillingUsageStandardConnector|是|标准连接器执行的计费使用情况|计数|总计|已计费的标准连接器执行次数。|无维度|
|BillingUsageStorageConsumption|是|存储使用执行的计费使用情况|计数|总计|已计费的存储使用执行次数。|无维度|
|RunFailurePercentage|是|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无维度|
|RunLatency|是|运行延迟|秒|平均值|已完成的工作流运行的延迟。|无维度|
|RunsCancelled|是|取消的运行数|计数|总计|已取消的工作流运行数目。|无维度|
|RunsCompleted|是|已完成的运行数|计数|总计|已完成的工作流运行数目。|无维度|
|RunsFailed|是|失败的运行数|计数|总计|失败的工作流运行数目。|无维度|
|RunsStarted|是|已启动的运行数|计数|总计|已启动的工作流运行数目。|无维度|
|RunsSucceeded|是|成功的运行数|计数|总计|成功的工作流运行数目。|无维度|
|RunStartThrottledEvents|是|运行启动限制事件|计数|总计|工作流运行启动限制事件数。|无维度|
|RunSuccessLatency|是|运行成功延迟|秒|平均值|已成功的工作流运行的延迟。|无维度|
|RunThrottledEvents|是|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无维度|
|TotalBillableExecutions|是|计费的执行总数|计数|总计|计费的工作流执行数目。|无维度|
|TriggerFireLatency|是|触发器激发延迟 |秒|平均值|已激发的工作流触发器的延迟。|无维度|
|TriggerLatency|是|触发器延迟 |秒|平均值|已完成的工作流触发器的延迟。|无维度|
|TriggersCompleted|是|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无维度|
|TriggersFailed|是|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无维度|
|TriggersFired|是|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无维度|
|TriggersSkipped|是|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无维度|
|TriggersStarted|是|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无维度|
|TriggersSucceeded|是|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无维度|
|TriggerSuccessLatency|是|触发器成功延迟 |秒|平均值|已成功的工作流触发器的延迟。|无维度|
|TriggerThrottledEvents|是|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无维度|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|活动核心数|是|活动核心数|计数|平均值|活动核心数|Scenario, ClusterName|
|活动节点数|是|活动节点数|计数|平均值|活动节点数。 这些节点是正在运行作业的节点。|Scenario, ClusterName|
|请求取消运行数|是|请求取消运行数|计数|总计|此工作区请求取消的运行数。 当收到用于运行的取消请求时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|已取消运行数|是|已取消运行数|计数|总计|此工作区取消的运行数。 成功取消运行时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|已完成的运行数|是|已完成的运行数|计数|总计|已成功为此工作区完成的运行数。 当运行完成并收集输出时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilization|是|CpuUtilization|计数|平均值|CPU 节点上内存利用率的百分比。 利用率每分钟报告一次。|Scenario, runId, NodeId, ClusterName|
|错误|是|错误|计数|总计|此工作区中的运行错误数。 运行遇到错误时，将更新计数。|方案|
|失败的运行次数|是|失败的运行次数|计数|总计|此工作区失败的运行数。 运行失败时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|完成状态运行数|是|完成状态运行数|计数|总计|此工作区的进入完成状态的运行数。 运行已完成但输出收集仍在进行时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilization|是|GpuUtilization|计数|平均值|GPU 节点上内存利用率的百分比。 利用率每分钟报告一次。|Scenario, runId, NodeId, DeviceId, ClusterName|
|空闲核心数|是|空闲核心数|计数|平均值|空闲核心数|Scenario, ClusterName|
|空闲节点数|是|空闲节点数|计数|平均值|空闲节点数。 空闲节点是指未运行任何作业，但可以接受新作业（如果可用）的节点。|Scenario, ClusterName|
|正在退出的核心数|是|正在退出的核心数|计数|平均值|正在退出的核心数|Scenario, ClusterName|
|正在退出的节点数|是|正在退出的节点数|计数|平均值|正在退出的节点数。 正在退出的节点是指刚刚完成了作业处理，将进入空闲状态的节点。|Scenario, ClusterName|
|失败的模型部署数|是|失败的模型部署数|计数|总计|此工作区中失败的模型部署数|Scenario, StatusCode|
|已启动的模型部署数|是|已启动的模型部署数|计数|总计|此工作区中已启动的模型部署数|方案|
|成功的模型部署数|是|成功的模型部署数|计数|总计|此工作区中成功的模型部署数|方案|
|失败的模型注册数|是|失败的模型注册数|计数|总计|此工作区中失败的模型注册数|Scenario, StatusCode|
|成功的模型注册数|是|成功的模型注册数|计数|总计|此工作区中成功的模型注册数|方案|
|无响应运行数|是|无响应运行数|计数|总计|此工作区无响应的运行数。 当运行进入无响应状态时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|未启动运行|是|未启动运行|计数|总计|此工作区处于“未启动”状态的运行数。 当收到创建运行的请求但尚未填充运行信息时，将更新计数。 |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|占用的核心数|是|占用的核心数|计数|平均值|占用的核心数|Scenario, ClusterName|
|占用的节点数|是|占用的节点数|计数|平均值|占用的节点数。 这些节点是已从可用节点池中取出的低优先级节点。|Scenario, ClusterName|
|准备的运行数|是|准备的运行数|计数|总计|为此工作区准备的运行数。 准备运行环境时，当运行进入准备状态时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|预配运行数|是|预配运行数|计数|总计|为此工作区预配的运行数。 当运行正在等待创建或预配计算目标时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|排队运行数|是|排队运行数|计数|总计|此工作区已排队的运行数。 当运行在计算目标中排队时，将更新计数。 等待所需计算节点准备就绪时，可能会发生。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|配额利用率百分比|是|配额利用率百分比|计数|平均值|已利用的配额百分比|Scenario, ClusterName, VmFamilyName, VmPriority|
|已启动的运行数|是|已启动的运行数|计数|总计|为此工作区运行的运行数。 运行在所需资源上开始运行时，将更新计数。|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|启动运行数|是|启动运行数|计数|总计|为此工作区启动的运行数。 在请求创建运行和运行信息（如运行 ID）已填充后，将更新计数|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|核心总数|是|核心总数|计数|平均值|核心总数|Scenario, ClusterName|
|节点总数|是|节点总数|计数|平均值|节点总数。 此总数包括一些活动节点、空闲节点、不可用的节点、占用的节点和正在退出的节点|Scenario, ClusterName|
|不可用的核心数|是|不可用的核心数|计数|平均值|不可用的核心数|Scenario, ClusterName|
|不可用的节点数|是|不可用的节点数|计数|平均值|不可用的节点数。 不可用的节点是由于存在某种不可解决的问题而无法正常运行的节点。 Azure 将回收这些节点。|Scenario, ClusterName|
|警告|是|警告|计数|总计|此工作区中的运行警告数。 运行遇到警告时，将更新计数。|方案|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|API 可用性|ApiCategory, ApiName|
|使用情况|否|使用情况|计数|计数|API 调用计数|ApiCategory、ApiName、ResultType、ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AssetCount|是|资产计数|计数|平均值|已在当前媒体服务帐户中创建的资产数量|无维度|
|AssetQuota|是|资产配额|计数|平均值|当前媒体服务帐户允许的资产数量|无维度|
|AssetQuotaUsedPercentage|是|资产配额已用百分比|百分比|平均值|当前媒体服务帐户中的资产已用百分比|无维度|
|ContentKeyPolicyCount|是|内容密钥策略计数|计数|平均值|当前媒体服务帐户中已创建的内容密钥策略数量|无维度|
|ContentKeyPolicyQuota|是|内容密钥策略配额|计数|平均值|当前媒体服务帐户允许的内容密钥策略数量|无维度|
|ContentKeyPolicyQuotaUsedPercentage|是|内容密钥策略配额已用百分比|百分比|平均值|当前媒体服务帐户中的内容密钥策略已用百分比|无维度|
|StreamingPolicyCount|是|流式处理策略计数|计数|平均值|当前媒体服务帐户中已创建的流式处理策略数量|无维度|
|StreamingPolicyQuota|是|流式处理策略配额|计数|平均值|当前媒体服务帐户允许的流式处理策略数量|无维度|
|StreamingPolicyQuotaUsedPercentage|是|流式处理策略配额已用百分比|百分比|平均值|当前媒体服务帐户中的流式处理策略已用百分比|无维度|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|流出量|是|流出量|字节|总计|流出的数据量（以字节为单位）。|OutputFormat|
|请求|是|请求|计数|总计|对流式处理终结点的请求。|OutputFormat、HttpStatusCode、ErrorCode|
|SuccessE2ELatency|是|成功端到端延迟|毫秒|平均值|成功请求的平均延迟（以毫秒为单位）。|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|是|Pool Allocated Size|字节|平均值|此池的预配大小|无维度|
|VolumePoolAllocatedUsed|是|分配到卷的池的大小|字节|平均值|分配池已使用的大小|无维度|
|VolumePoolTotalLogicalSize|是|池消耗大小|字节|平均值|属于该池的所有卷的总逻辑大小|无维度|
|VolumePoolTotalSnapshotSize|是|Total Snapshot size for the pool|字节|平均值|此池中所有卷的快照大小总和|无维度|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AverageReadLatency|是|平均读取延迟|毫秒|平均值|每个操作的平均读取延迟（毫秒）|无维度|
|AverageWriteLatency|是|平均写入延迟|毫秒|平均值|每个操作的平均写入延迟（毫秒）|无维度|
|CbsVolumeBackupActive|是|Volume backup active state|计数|平均值|当前卷的备份是否处于挂起状态。|无维度|
|CbsVolumeLogicalBackupBytes|是|Logical bytes backed up|字节|平均值|为此卷备份的所有未压缩/未加密字节。|无维度|
|CbsVolumeOperationComplete|是|Operation state|计数|平均值|上次备份/还原操作是否成功。|无维度|
|CbsVolumeOperationTransferredBytes|是|Bytes transferred for operation|字节|平均值|上次备份/还原操作传输的总字节数。|无维度|
|CbsVolumeProtected|是|Volume protected state|计数|平均值|卷是否受云备份服务保护。|无维度|
|ReadIops|是|读取 IOPS|每秒计数|平均值|每秒读取输入/输出操作数|无维度|
|VolumeAllocatedSize|是|卷分配大小|字节|平均值|卷的预配大小|无维度|
|VolumeLogicalSize|是|卷消耗大小|字节|平均值|卷的逻辑大小（所用字节数）|无维度|
|VolumeSnapshotSize|是|卷快照大小|字节|平均值|卷中所有快照的大小|无维度|
|WriteIops|是|写入 IOPS|每秒计数|平均值|每秒写入输入/输出操作数|无维度|
|XregionReplicationHealthy|是|Is volume replication status healthy|计数|平均值|关系的条件是 1 还是 0。|无维度|
|XregionReplicationLagTime|是|Volume replication lag time|秒|平均值|镜像上的数据落后于源的时间（秒）。|无维度|
|XregionReplicationLastTransferDuration|是|Volume replication last transfer duration|秒|平均值|上次传输完成所用的时间（秒）。|无维度|
|XregionReplicationLastTransferSize|是|Volume replication last transfer size|字节|平均值|作为上次传输的一部分传输的总字节数。|无维度|
|XregionReplicationRelationshipProgress|是|Volume replication progress|字节|平均值|当前传输操作传输的总数据量。|无维度|
|XregionReplicationRelationshipTransferring|是|Is volume replication transferring|计数|平均值|卷复制的状态是否为“正在传输”。|无维度|
|XregionReplicationTotalTransferBytes|是|Volume replication total transfer|字节|平均值|为关系传输的累计字节数。|无维度|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|否|应用程序网关总时间|毫秒|平均值|处理请求和发送响应平均花费的时间。 此时间按特定的平均时间间隔（从应用程序网关接收第一个 HTTP 请求字节到完成响应发送操作所需的时间）来计算。 必须注意，这通常包括应用程序网关处理时间、请求和响应数据包遍历网络的时间，以及后端服务器做出响应所花费的时间。|侦听器|
|AvgRequestCountPerHealthyHost|否|每个正常主机每分钟的请求数|计数|平均值|池中每个正常后端主机每分钟的平均请求计数|BackendSettingsPool|
|BackendConnectTime|否|后端连接时间|毫秒|平均值|与后端服务器建立连接所花费的时间|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|否|后端第一个字节响应时间|毫秒|平均值|从开始与后端服务器建立连接，到收到响应标头的第一个字节的间隔时间，大致相当于后端服务器的处理时间|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|否|后端最后一个字节响应时间|毫秒|平均值|从开始与后端服务器建立连接，到收到响应正文的最后一个字节的间隔时间|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|是|后端响应状态|计数|总计|后端成员生成的 HTTP 响应代码数。 这不包括应用程序网关生成的任何响应代码。|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|是|Web 应用程序防火墙阻止的请求规则分发|计数|总计|Web 应用程序防火墙阻止的请求规则分发|RuleGroup, RuleId|
|BlockedReqCount|是|Web 应用程序防火墙阻止的请求计数|计数|总计|Web 应用程序防火墙阻止的请求计数|无维度|
|BytesReceived|是|接收的字节数|字节|总计|应用程序网关从客户端收到的字节总数|侦听器|
|BytesSent|是|发送的字节数|字节|总计|应用程序网关向客户端发送的字节总数|侦听器|
|CapacityUnits|否|当前容量单位数|计数|平均值|已消耗的请求单位数|无维度|
|ClientRtt|否|客户端 RTT|毫秒|平均值|客户端与应用程序网关之间的平均往返时间。 此指标指示建立连接和返回确认所用的时间|侦听器|
|ComputeUnits|否|当前计算单位数|计数|平均值|消耗的计算单位数|无维度|
|CpuUtilization|否|CPU 使用率|百分比|平均值|应用程序网关的当前 CPU 利用率|无维度|
|CurrentConnections|是|当前连接数|计数|总计|使用应用程序网关建立的当前连接计数|无维度|
|EstimatedBilledCapacityUnits|否|预估计费容量单位数|计数|平均值|预估收费容量单位数|无维度|
|FailedRequests|是|失败的请求数|计数|总计|应用程序网关已提供服务的失败请求计数|BackendSettingsPool|
|FixedBillableCapacityUnits|否|固定的可计费容量单位|计数|平均值|最小收费容量单位数|无维度|
|HealthyHostCount|是|正常的主机计数|计数|平均值|正常的后端主机数|BackendSettingsPool|
|MatchedCount|是|Web 应用程序防火墙规则分发总数|计数|总计|传入流量的 Web 应用程序防火墙规则分发总数|RuleGroup, RuleId|
|NewConnectionsPerSecond|否|每秒新连接数|每秒计数|平均值|每秒使用应用程序网关建立的新连接数|无维度|
|ResponseStatus|是|响应状态|计数|总计|应用程序网关返回的 Http 响应状态|HttpStatusGroup|
|吞吐量|否|吞吐量|每秒字节数|平均值|应用程序网关每秒提供的字节数|无维度|
|TlsProtocol|是|客户端 TLS 协议|计数|总计|与应用程序网关建立了连接的客户端发起的 TLS 和非 TLS 请求数。 若要查看 TLS 协议分布，请按“TLS 协议”维度进行筛选。|Listener, TlsProtocol|
|TotalRequests|是|请求总数|计数|总计|应用程序网关已提供服务的成功请求计数|BackendSettingsPool|
|UnhealthyHostCount|是|不正常的主机计数|计数|平均值|不正常的后端主机数|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|是|应用程序规则命中次数|计数|总计|应用程序规则的命中次数|Status、Reason、Protocol|
|DataProcessed|是|已处理的数据|字节|总计|此防火墙处理的数据总量|无维度|
|FirewallHealth|是|防火墙运行状况|百分比|平均值|指示此防火墙的总体运行状况|Status, Reason|
|NetworkRuleHit|是|网络规则命中次数|计数|总计|网络规则的命中次数|Status、Reason、Protocol|
|SNATPortUtilization|是|SNAT 端口利用率|百分比|平均值|当前使用的出站 SNAT 端口的百分比|协议|
|吞吐量|否|吞吐量|BitsPerSecond|平均值|此防火墙处理的吞吐量|无维度|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BitsInPerSecond|是|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无维度|
|BitsOutPerSecond|是|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无维度|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|QueryVolume|是|查询量|计数|总计|为 DNS 区域提供服务的查询数|无维度|
|RecordSetCapacityUtilization|否|记录集容量使用率|百分比|最大值|DNS 区域利用的记录集容量的百分比|无维度|
|RecordSetCount|是|记录集计数|计数|最大值|DNS 区域中的记录集数|无维度|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ArpAvailability|是|Arp 可用性|百分比|平均值|从 MSEE 到所有对等方的 ARP 可用性。|PeeringType, Peer|
|BgpAvailability|是|Bgp 可用性|百分比|平均值|从 MSEE 到所有对等方的 BGP 可用性。|PeeringType, Peer|
|BitsInPerSecond|否|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|PeeringType|
|BitsOutPerSecond|否|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|PeeringType|
|GlobalReachBitsInPerSecond|否|GlobalReachBitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|否|GlobalReachBitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|PeeredCircuitSKey|
|QosDropBitsInPerSecond|否|DroppedInBitsPerSecond|每秒计数|平均值|每秒丢弃的数据流入位数|无维度|
|QosDropBitsOutPerSecond|否|DroppedOutBitsPerSecond|每秒计数|平均值|每秒丢弃的数据流出位数|无维度|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BitsInPerSecond|是|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无维度|
|BitsOutPerSecond|是|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无维度|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|否|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|否|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AdminState|是|AdminState|Count|平均值|端口的管理状态|链接|
|LineProtocol|是|LineProtocol|Count|平均值|端口的线路协议状态|链接|
|PortBitsInPerSecond|是|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|链接|
|PortBitsOutPerSecond|是|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|链接|
|RxLightLevel|是|RxLightLevel|Count|平均值|Rx 光能级（以 dBm 为单位）|Link, Lane|
|TxLightLevel|是|TxLightLevel|Count|平均值|Tx 光能级（以 dBm 为单位）|Link, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|是|后端运行状况百分比|百分比|平均值|从 HTTP/S 代理到后端，成功运行状况探测的百分比|Backend, BackendPool|
|BackendRequestCount|是|后端请求计数|Count|总计|从 HTTP/S 代理发送到后端的请求数|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|是|后端请求延迟|毫秒|平均值|自请求由 HTTP/S 代理发送到后端直至 HTTP/S 代理从后端收到最后一个响应字节为止，所计算的时间|后端|
|BillableResponseSize|是|计费响应大小|字节|总计|作为响应从 HTTP/S 代理发送到客户端的计费字节数（每个请求最小 2KB）。|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|是|请求计数|Count|总计|HTTP/S 代理提供客户端请求数|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|是|请求大小|字节|总计|以请求的形式从客户端发送到 HTTP/S 代理的字节数|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|是|响应大小|字节|总计|以响应的形式从 HTTP/S 代理发送到客户端的字节数|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|是|总延迟|毫秒|平均值|自请求由 HTTP/S 代理接收后到客户端确认来自 HTTP/S 代理的最后一个响应字节止，所计算的时间|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|是|Web 应用程序防火墙请求计数|Count|总计|Web 应用程序防火墙所处理的客户端请求数|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|否|Allocated SNAT Ports|计数|平均值|时间段内已分配的 SNAT 端口总数|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|是|字节计数|Count|总计|时间段内传输的字节总数|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|是|运行状况探测状态|Count|平均值|每个持续时间的负载均衡器运行状况探测的平均状态|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|是|数据包计数|Count|总计|时间段内传输的数据包总数|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|是|SNAT 连接计数|Count|总计|时间段内创建的新 SNAT 连接的总数|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|是|SYN 计数|Count|总计|时间段内传输的 SYN 数据包总数|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|否|Used SNAT Ports|计数|平均值|时间段内 SNAT 端口的总数|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|是|数据路径可用性|Count|平均值|每个持续时间的负载均衡器数据路径的平均可用性|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BytesReceivedRate|是|接收的字节数|字节|总计|网络接口接收的字节数|无维度|
|BytesSentRate|是|发送的字节数|字节|总计|网络接口发送的字节数|无维度|
|PacketsReceivedRate|是|已接收的数据包数|Count|总计|网络接口接收的数据包数|无维度|
|PacketsSentRate|是|发送的数据包数|Count|总计|网络接口发送的数据包数|无维度|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|是|平均值往返时间（毫秒）|毫秒|平均值|源和目标之间发送的连接监视探测的平均网络往返时间（毫秒）|无维度|
|ChecksFailedPercent|是|检查失败百分比（预览版）|百分比|平均值|连接监视检查失败百分比|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|是|失败的探测百分比|百分比|平均值|失败的连接监视探测百分比|无维度|
|RoundTripTimeMs|是|往返时间（毫秒）（预览版）|毫秒|平均值|连接监视检查的往返时间（以毫秒为单位）|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ByteCount|是|字节计数|Count|总计|时间段内传输的字节总数|Port、Direction|
|BytesDroppedDDoS|是|丢弃的入站字节 DDoS|每秒字节数|最大值|丢弃的入站字节 DDoS|无维度|
|BytesForwardedDDoS|是|转发的入站字节 DDoS|每秒字节数|最大值|转发的入站字节 DDoS|无维度|
|BytesInDDoS|是|入站字节 DDoS|每秒字节数|最大值|入站字节 DDoS|无维度|
|DDoSTriggerSYNPackets|是|触发 DDoS 缓解的入站 SYN 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 SYN 数据包|无维度|
|DDoSTriggerTCPPackets|是|触发 DDoS 缓解的入站 TCP 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 TCP 数据包|无维度|
|DDoSTriggerUDPPackets|是|触发 DDoS 缓解的入站 UDP 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 UDP 数据包|无维度|
|IfUnderDDoSAttack|是|是否遭到 DDoS 攻击|Count|最大值|是否遭到 DDoS 攻击|无维度|
|PacketCount|是|数据包计数|Count|总计|时间段内传输的数据包总数|Port、Direction|
|PacketsDroppedDDoS|是|丢弃的入站数据包 DDoS|每秒计数|最大值|丢弃的入站数据包 DDoS|无维度|
|PacketsForwardedDDoS|是|转发的入站数据包 DDoS|每秒计数|最大值|转发的入站数据包 DDoS|无维度|
|PacketsInDDoS|是|入站数据包 DDoS|每秒计数|最大值|入站数据包 DDoS|无维度|
|SynCount|是|SYN 计数|Count|总计|时间段内传输的 SYN 数据包总数|Port、Direction|
|TCPBytesDroppedDDoS|是|丢弃的入站 TCP 字节 DDoS|每秒字节数|最大值|丢弃的入站 TCP 字节 DDoS|无维度|
|TCPBytesForwardedDDoS|是|转发的入站 TCP 字节 DDoS|每秒字节数|最大值|转发的入站 TCP 字节 DDoS|无维度|
|TCPBytesInDDoS|是|入站 TCP 字节 DDoS|每秒字节数|最大值|入站 TCP 字节 DDoS|无维度|
|TCPPacketsDroppedDDoS|是|丢弃的入站 TCP 数据包 DDoS|每秒计数|最大值|丢弃的入站 TCP 数据包 DDoS|无维度|
|TCPPacketsForwardedDDoS|是|转发的入站 TCP 数据包 DDoS|每秒计数|最大值|转发的入站 TCP 数据包 DDoS|无维度|
|TCPPacketsInDDoS|是|入站 TCP 数据包 DDoS|每秒计数|最大值|入站 TCP 数据包 DDoS|无维度|
|UDPBytesDroppedDDoS|是|丢弃的入站 UDP 字节 DDoS|每秒字节数|最大值|丢弃的入站 UDP 字节 DDoS|无维度|
|UDPBytesForwardedDDoS|是|转发的入站 UDP 字节 DDoS|每秒字节数|最大值|转发的入站 UDP 字节 DDoS|无维度|
|UDPBytesInDDoS|是|入站 UDP 字节 DDoS|每秒字节数|最大值|入站 UDP 字节 DDoS|无维度|
|UDPPacketsDroppedDDoS|是|丢弃的入站 UDP 数据包 DDoS|每秒计数|最大值|丢弃的入站 UDP 数据包 DDoS|无维度|
|UDPPacketsForwardedDDoS|是|转发的入站 UDP 数据包 DDoS|每秒计数|最大值|转发的入站 UDP 数据包 DDoS|无维度|
|UDPPacketsInDDoS|是|入站 UDP 数据包 DDoS|每秒计数|最大值|入站 UDP 数据包 DDoS|无维度|
|VipAvailability|是|数据路径可用性|Count|平均值|每个持续时间的 IP 地址的平均可用性|端口|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|是|按终结点显示的终结点状态|Count|最大值|如果终结点的探测状态为“已启用”，则值为 1；否则，值为 0。|EndpointName|
|QpsByEndpoint|是|按终结点返回的查询|Count|总计|给定时间范围内返回流量管理器终结点的次数|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AverageBandwidth|是|网关 S2S 带宽|每秒字节数|平均值|网关站点到站点的平均带宽（字节/秒）|无维度|
|P2SBandwidth|是|网关 P2S 带宽|每秒字节数|平均值|网关点到站点的平均带宽（字节/秒）|无维度|
|P2SConnectionCount|是|P2S 连接计数|Count|最大值|网关的点到站点连接计数|协议|
|TunnelAverageBandwidth|是|隧道带宽|每秒字节数|平均值|隧道带宽平均值（以字节/秒为单位）|ConnectionName、RemoteIP|
|TunnelEgressBytes|是|隧道流出字节|字节|总计|隧道的传出字节数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|是|隧道流出 TS 不匹配数据包丢弃|Count|总计|来自隧道的不匹配流量选择器的传出数据包丢弃|ConnectionName、RemoteIP|
|TunnelEgressPackets|是|隧道流出数据包|Count|总计|隧道的传出数据包计数|ConnectionName、RemoteIP|
|TunnelIngressBytes|是|隧道流入字节|字节|总计|隧道的传入字节数|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|是|隧道流入 TS 不匹配数据包丢弃|Count|总计|来自隧道的不匹配流量选择器的传入数据包丢弃|ConnectionName、RemoteIP|
|TunnelIngressPackets|是|隧道流入数据包|Count|总计|隧道的传入数据包计数|ConnectionName、RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|是|Ping 某个 VM 所需的往返时间|毫秒|平均值|向目标 VM 发送 Ping 所需的往返时间|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|是|VM Ping 失败次数|百分比|平均值|失败的 Ping 次数占发送到目标 VM 的 Ping 总数的百分比|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|incoming|是|传入消息数|Count|总计|所有成功的发送 API 调用的计数。 |无维度|
|incoming.all.failedrequests|是|所有传入的失败请求数|Count|总计|通知中心的传入的失败请求数总计|无维度|
|incoming.all.requests|是|所有传入请求数|Count|总计|通知中心的传入的请求数总计|无维度|
|incoming.scheduled|是|已发送的已安排推送通知数|Count|总计|已取消的已安排推送通知数|无维度|
|incoming.scheduled.cancel|是|已取消的已安排推送通知数|Count|总计|已取消的已安排推送通知数|无维度|
|installation.all|是|安装管理操作数目|Count|总计|安装管理操作数目|无维度|
|installation.delete|是|删除安装操作数目|Count|总计|删除安装操作数目|无维度|
|installation.get|是|获取安装操作数目|Count|总计|获取安装操作数目|无维度|
|installation.patch|是|修补安装操作数目|Count|总计|修补安装操作数目|无维度|
|installation.upsert|是|创建或更新安装操作数目|Count|总计|创建或更新安装操作数目|无维度|
|notificationhub.pushes|是|所有传出通知|Count|总计|通知中心的所有传出通知|无维度|
|outgoing.allpns.badorexpiredchannel|是|坏通道或已过期通道错误数|Count|总计|因为注册中的通道/令牌/registrationId 已过期或无效而失败的推送的计数。|无维度|
|outgoing.allpns.channelerror|是|通道错误数|Count|总计|因为通道无效、没有与正确的应用相关联、受限制或已过期而失败的推送的计数。|无维度|
|outgoing.allpns.invalidpayload|是|有效负载错误数|Count|总计|因为 PNS 返回了“有效负载不正确”错误而失败的推送的计数。|无维度|
|outgoing.allpns.pnserror|是|外部通知系统错误数|Count|总计|因为与 PNS 通信时遇到问题（不包括身份验证问题）而失败的推送的计数。|无维度|
|outgoing.allpns.success|是|成功的通知数|Count|总计|所有成功的通知的计数。|无维度|
|outgoing.apns.badchannel|是|APNS 坏通道错误|Count|总计|因令牌无效而失败的推送的计数（APNS 状态代码：8）。|无维度|
|outgoing.apns.expiredchannel|是|APNS 已过期通道错误|Count|总计|由 APNS 反馈通道致其无效的令牌的计数。|无维度|
|outgoing.apns.invalidcredentials|是|APNS 授权错误数|Count|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.apns.invalidnotificationsize|是|APNS 无效通知大小错误|Count|总计|因有效负载太大而失败的推送的计数（APNS 状态代码：7）。|无维度|
|outgoing.apns.pnserror|是|APNS 错误数|Count|总计|因为与 APNS 通信时发生错误而失败的推送的计数。|无维度|
|outgoing.apns.success|是|APNS 成功的通知数|Count|总计|所有成功的通知的计数。|无维度|
|outgoing.gcm.authenticationerror|是|GCM 身份验证错误数|Count|总计|因为 PNS 未接受所提供的凭据、凭据被阻止或者未在应用中正确配置 SenderId 而失败的推送的计数（GCM 结果：MismatchedSenderId）。|无维度|
|outgoing.gcm.badchannel|是|GCM 坏通道错误|Count|总计|因为注册中的 registrationId 不可识别而失败的推送的计数（GCM 结果：无效的注册）。|无维度|
|outgoing.gcm.expiredchannel|是|GCM 已过期通道错误|Count|总计|因为注册中的 registrationId 已过期而失败的推送的计数（GCM 结果：NotRegistered）。|无维度|
|outgoing.gcm.invalidcredentials|是|GCM 授权错误数（凭据无效）|Count|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.gcm.invalidnotificationformat|是|GCM 无效的通知格式|Count|总计|因为有效负载的格式不正确而失败的推送的计数（GCM 结果：InvalidDataKey 或 InvalidTtl）。|无维度|
|outgoing.gcm.invalidnotificationsize|是|GCM 无效通知大小错误|Count|总计|因有效负载太大而失败的推送的计数（GCM 结果：MessageTooBig）。|无维度|
|outgoing.gcm.pnserror|是|GCM 错误数|Count|总计|因为与 GCM 通信时发生错误而失败的推送的计数。|无维度|
|outgoing.gcm.success|是|GCM 成功的通知数|Count|总计|所有成功的通知的计数。|无维度|
|outgoing.gcm.throttled|是|GCM 受限的通知数|Count|总计|因为 GCM 限制了此应用而失败的推送的计数（GCM 状态代码：501-599 或结果：不可用）。|无维度|
|outgoing.gcm.wrongchannel|是|GCM 通道不正确错误|Count|总计|因为注册中的 registrationId 没有关联到当前应用而失败的推送的计数（GCM 结果：InvalidPackageName）。|无维度|
|outgoing.mpns.authenticationerror|是|MPNS 身份验证错误数|Count|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.mpns.badchannel|是|MPNS 坏通道错误|Count|总计|因为注册中的 ChannelURI 不可识别（MPNS 状态：404 找不到）而失败的推送的计数。|无维度|
|outgoing.mpns.channeldisconnected|是|MPNS 通道断开连接|Count|总计|因为注册中的 ChannelURI 断开连接（MPNS 状态：412 找不到）而失败的推送的计数。|无维度|
|outgoing.mpns.dropped|是|MPNS 丢弃的通知数|Count|总计|MPNS 丢弃的推送的计数（MPNS 响应标头：X-NotificationStatus：QueueFull 或 Suppressed）。|无维度|
|outgoing.mpns.invalidcredentials|是|MPNS 无效的凭据|Count|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无维度|
|outgoing.mpns.invalidnotificationformat|是|MPNS 无效的通知格式|Count|总计|因通知的有效负载太大而失败的推送的计数。|无维度|
|outgoing.mpns.pnserror|是|MPNS 错误数|Count|总计|因为与 MPNS 通信时发生错误而失败的推送的计数。|无维度|
|outgoing.mpns.success|是|MPNS 成功的通知数|Count|总计|所有成功的通知的计数。|无维度|
|outgoing.mpns.throttled|是|MPNS 受限的通知数|Count|总计|因为 MPNS 限制了此应用（WNS MPNS：406 不可接受）而失败的推送的计数。|无维度|
|outgoing.wns.authenticationerror|是|WNS 身份验证错误数|Count|总计|与 Windows Live 通信时因凭据无效或令牌错误而发生错误，因为未传递通知。|无维度|
|outgoing.wns.badchannel|是|WNS 坏通道错误|Count|总计|因为注册中的 ChannelURI 不可识别（WNS 状态：404 找不到）而失败的推送的计数。|无维度|
|outgoing.wns.channeldisconnected|是|WNS 通道断开连接|Count|总计|通知因为注册中的 ChannelURI 受限而被丢弃（WNS 响应标头：X-WNS-DeviceConnectionStatus: disconnected）。|无维度|
|outgoing.wns.channelthrottled|是|WNS 通道受限|Count|总计|通知因为注册中的 ChannelURI 受限而被丢弃（WNS 响应标头：X-WNS-NotificationStatus:channelThrottled）。|无维度|
|outgoing.wns.dropped|是|WNS 丢弃的通知数|Count|总计|通知因为注册中的 ChannelURI 受限而被丢弃（X-WNS-NotificationStatus 为 dropped，但 X-WNS-DeviceConnectionStatus 不是 disconnected）。|无维度|
|outgoing.wns.expiredchannel|是|WNS 已过期通道错误|Count|总计|因为 ChannelURI 已过期（WNS 状态：410 不存在）而失败的推送的计数。|无维度|
|outgoing.wns.invalidcredentials|是|WNS 授权错误数（凭据无效）|Count|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。 （Windows Live 不能识别凭据）。|无维度|
|outgoing.wns.invalidnotificationformat|是|WNS 无效的通知格式|Count|总计|通知格式无效（WNS 状态：400）。 请注意，WNS 并不会拒绝所有无效的有效负载。|无维度|
|outgoing.wns.invalidnotificationsize|是|WNS 无效通知大小错误|Count|总计|通知有效负载太大（WNS 状态：413）。|无维度|
|outgoing.wns.invalidtoken|是|WNS 授权错误数（令牌无效）|Count|总计|提供给 WNS 的令牌无效（WNS 状态：401 未经授权）。|无维度|
|outgoing.wns.pnserror|是|WNS 错误数|Count|总计|与 WNS 通信时发生错误，因而未传递通知。|无维度|
|outgoing.wns.success|是|WNS 成功的通知数|Count|总计|所有成功的通知的计数。|无维度|
|outgoing.wns.throttled|是|WNS 受限的通知数|Count|总计|因为 WNS 限制了此应用（WNS 状态：406 不可接受）而失败的推送的计数。|无维度|
|outgoing.wns.tokenproviderunreachable|是|WNS 授权错误数（无法访问）|Count|总计|无法访问 Windows Live。|无维度|
|outgoing.wns.wrongtoken|是|WNS 授权错误数（令牌错误）|Count|总计|提供给 WNS 的令牌有效，但它是用于另一应用程序的（WNS 状态：403 禁止访问）。 如果注册中的 ChannelURI 与另一应用相关联，则可能会发生此情况。 请检查客户端应用是否与其凭据位于通知中心内的同一应用相关联。|无维度|
|registration.all|是|注册操作|Count|总计|所有成功的注册操作（创建、更新、查询和删除）的计数。 |无维度|
|registration.create|是|注册创建操作数目|Count|总计|所有成功的注册创建操作的计数。|无维度|
|registration.delete|是|注册删除操作数目|Count|总计|所有成功的注册删除操作的计数。|无维度|
|registration.get|是|注册读取操作数目|Count|总计|所有成功的注册查询操作的计数。|无维度|
|registration.update|是|注册更新操作数目|Count|总计|所有成功的注册更新操作的计数。|无维度|
|scheduled.pending|是|挂起的已计划通知数|Count|总计|挂起的已计划通知数|无维度|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|Average_% Available Memory|是|可用内存百分比|Count|平均值|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Swap Space|是|可用交换空间百分比|Count|平均值|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Committed Bytes In Use|是|提交的在用字节数百分比|Count|平均值|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% DPC Time|是|DPC 时间百分比|Count|平均值|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Inodes|是|可用 Inode 百分比|Count|平均值|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|是|可用空间百分比|Count|平均值|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Idle Time|是|空闲时间百分比|Count|平均值|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Interrupt Time|是|中断时间百分比|Count|平均值|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% IO Wait Time|是|IO 等待时间百分比|Count|平均值|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Nice Time|是|良好时间百分比|Count|平均值|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Privileged Time|是|特权时间百分比|Count|平均值|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|是|处理器时间百分比|Count|平均值|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Inodes|是|已用 Inode 百分比|Count|平均值|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Memory|是|已用内存百分比|Count|平均值|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Space|是|已用空间百分比|Count|平均值|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Swap Space|是|已用交换空间百分比|Count|平均值|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% User Time|是|用户时间百分比|Count|平均值|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes|是|可用兆字节数|Count|平均值|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Memory|是|可用内存 MB 数|Count|平均值|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Swap|是|可用交换空间 MB 数|Count|平均值|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/读取|是|平均值磁盘秒数/读取|Count|平均值|Average_Avg. 磁盘秒数/读取|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/传输|是|平均值磁盘秒数/传输|Count|平均值|Average_Avg. 磁盘秒数/传输|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. 磁盘秒数/写入|是|平均值磁盘秒数/写入|Count|平均值|Average_Avg. 磁盘秒数/写入|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Received/sec|是|收到的字节数/秒|Count|平均值|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Sent/sec|是|发送的字节数/秒|Count|平均值|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Total/sec|是|字节总数/秒|Count|平均值|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Current Disk Queue Length|是|当前的磁盘队列长度|Count|平均值|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Read Bytes/sec|是|磁盘读取字节数/秒|Count|平均值|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|是|磁盘读取数/秒|Count|平均值|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|是|磁盘传输数/秒|Count|平均值|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Write Bytes/sec|是|磁盘写入字节数/秒|Count|平均值|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|是|磁盘写入数/秒|Count|平均值|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|是|可用 MB 数|Count|平均值|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Physical Memory|是|可用物理内存|Count|平均值|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Space in Paging Files|是|分页文件中的可用空间|Count|平均值|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Virtual Memory|是|可用虚拟内存|Count|平均值|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Logical Disk Bytes/sec|是|逻辑磁盘字节数/秒|Count|平均值|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Reads/sec|是|页面读取数/秒|Count|平均值|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Writes/sec|是|页面写入数/秒|Count|平均值|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pages/sec|是|页面数/秒|Count|平均值|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct Privileged Time|是|特权时间百分比|Count|平均值|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct User Time|是|用户时间百分比|Count|平均值|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Physical Disk Bytes/sec|是|物理磁盘字节数/秒|Count|平均值|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processes|是|进程|Count|平均值|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processor Queue Length|是|处理器队列长度|Count|平均值|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Size Stored In Paging Files|是|分页文件中存储的大小|Count|平均值|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes|是|字节数总计|Count|平均值|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Received|是|已接收的字节数总计|Count|平均值|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Transmitted|是|已传输的字节数总计|Count|平均值|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Collisions|是|冲突数总计|Count|平均值|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Received|是|已接收的包数总计|Count|平均值|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Transmitted|是|已传输的包数总计|Count|平均值|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Rx Errors|是|Rx 错误数总计|Count|平均值|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Tx Errors|是|Tx 错误数总计|Count|平均值|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Uptime|是|运行时间|Count|平均值|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used MBytes Swap Space|是|已用交换空间 MB 数|Count|平均值|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory kBytes|是|已用内存 KB 数|Count|平均值|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory MBytes|是|已用内存 MB 数|Count|平均值|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Users|是|用户|Count|平均值|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Virtual Shared Memory|是|虚拟共享内存|Count|平均值|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|事件|是|事件|Count|平均值|事件|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|检测信号|是|检测信号|Count|总计|检测信号|Computer、OSType、Version、SourceComputerId|
|更新|是|更新|Count|平均值|更新|Computer、Product、Classification、UpdateState、Optional、Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|EgressTrafficRate|是|出口流量速率|BitsPerSecond|平均值|出口流量速率（位/秒）|ConnectionId|
|IngressTrafficRate|是|入口流量速率|BitsPerSecond|平均值|入口流量速率（位/秒）|ConnectionId|
|SessionAvailabilityV4|是|会话可用性 V4|百分比|平均值|V4 会话的可用性|ConnectionId|
|SessionAvailabilityV6|是|会话可用性 V6|百分比|平均值|V6 会话的可用性|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|PrefixLatency|是|前缀延迟|毫秒|平均值|中间前缀延迟|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|memory_metric|是|内存|字节|平均值|内存。 A1 的范围为 0-3 GB，A2 为 0-5 GB，A3 为 0-10 GB，A4 为 0-25 GB，A5 为 0-50 GB，A6 为 0-100 GB|无维度|
|memory_thrashing_metric|是|内存抖动（数据集）|百分比|平均值|平均内存抖动。|无维度|
|qpu_high_utilization_metric|是|QPU 高利用率|Count|总计|最后一分钟内 QPU 高利用率，1 为高 QPU 利用率，反之为 0|无维度|
|QueryDuration|是|查询持续时间（数据集）|毫秒|平均值|上一个间隔的 DAX 查询持续时间|无维度|
|QueryPoolJobQueueLength|是|查询池作业队列长度（数据集）|Count|平均值|查询线程池队列中的作业数。|无维度|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|Count|总计|Microsoft.Relay 的活动连接总数。|EntityName|
|ActiveListeners|否|ActiveListeners|Count|总计|Microsoft.Relay 的活动侦听程序总数。|EntityName|
|BytesTransferred|是|BytesTransferred|Count|总计|Microsoft.Relay 的已传输字节总数。|EntityName|
|ListenerConnections-ClientError|否|ListenerConnections-ClientError|Count|总计|关于 Microsoft.Relay 的侦听程序连接的客户端错误。|EntityName、 |
|ListenerConnections-ServerError|否|ListenerConnections-ServerError|Count|总计|关于 Microsoft.Relay 的侦听程序连接的服务器错误。|EntityName、 |
|ListenerConnections-Success|否|ListenerConnections-Success|Count|总计|Microsoft.Relay 的侦听程序成功连接数。|EntityName、 |
|ListenerConnections-TotalRequests|否|ListenerConnections-TotalRequests|Count|总计|Microsoft.Relay 的侦听程序连接总数。|EntityName|
|ListenerDisconnects|否|ListenerDisconnects|Count|总计|Microsoft.Relay 的侦听程序断开连接总数。|EntityName|
|SenderConnections-ClientError|否|SenderConnections-ClientError|Count|总计|关于 Microsoft.Relay 的发送方连接的客户端错误。|EntityName、 |
|SenderConnections-ServerError|否|SenderConnections-ServerError|Count|总计|关于 Microsoft.Relay 的发送方连接的服务器错误。|EntityName、 |
|SenderConnections-Success|否|SenderConnections-Success|Count|总计|Microsoft.Relay 的发送方成功连接数。|EntityName、 |
|SenderConnections-TotalRequests|否|SenderConnections-TotalRequests|Count|总计|Microsoft.Relay 的发送方连接请求总数。|EntityName|
|SenderDisconnects|否|SenderDisconnects|Count|总计|Microsoft.Relay 的发送方断开连接总数。|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|SearchLatency|是|搜索延迟|秒|平均值|搜索服务的平均搜索延迟|无维度|
|SearchQueriesPerSecond|是|每秒搜索查询数|每秒计数|平均值|搜索服务的每秒搜索查询数|无维度|
|ThrottledSearchQueriesPercentage|是|限制的搜索查询百分比|百分比|平均值|为搜索服务限制的搜索查询百分比|无维度|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|Count|总计|Microsoft.ServiceBus 的活动连接总数。|无维度|
|ActiveMessages|否|队列/主题中的活动消息计数。|Count|平均值|队列/主题中的活动消息计数。|EntityName|
|ConnectionsClosed|否|已关闭的连接数。|Count|平均值|Microsoft.ServiceBus 已关闭的连接数。|EntityName|
|ConnectionsOpened|否|打开的连接数。|Count|平均值|Microsoft.ServiceBus 打开的连接数。|EntityName|
|CPUXNS|否|CPU（已弃用）|百分比|最大值|服务总线高级命名空间 CPU 使用率指标。 此指标已弃用。 请改用 CPU 指标 (NamespaceCpuUsage)。|无维度|
|DeadletteredMessages|否|队列/主题中的死信消息计数。|Count|平均值|队列/主题中的死信消息计数。|EntityName|
|IncomingMessages|是|传入消息数|Count|总计|Microsoft.ServiceBus 传入的消息数。|EntityName|
|IncomingRequests|是|传入请求数|Count|总计|Microsoft.ServiceBus 传入的请求数。|EntityName|
|消息|否|队列/主题中的消息计数。|Count|平均值|队列/主题中的消息计数。|EntityName|
|NamespaceCpuUsage|否|CPU|百分比|最大值|服务总线高级命名空间 CPU 使用率指标。|无维度|
|NamespaceMemoryUsage|否|内存用量|百分比|最大值|服务总线高级命名空间内存使用率指标。|无维度|
|OutgoingMessages|是|传出消息数|Count|总计|Microsoft.ServiceBus 传出的消息数。|EntityName|
|ScheduledMessages|否|队列/主题中的计划消息计数。|Count|平均值|队列/主题中的计划消息计数。|EntityName|
|ServerErrors|否|服务器错误数。|Count|总计|Microsoft.ServiceBus 的服务器错误数。|EntityName、 |
|大小|否|大小|字节|平均值|队列/主题的大小（以字节为单位）。|EntityName|
|SuccessfulRequests|否|成功的请求数|Count|总计|命名空间的成功请求总数|EntityName、 |
|ThrottledRequests|否|限制的请求数。|Count|总计|Microsoft.ServiceBus 限制的请求数。|EntityName、 |
|UserErrors|否|用户错误数。|Count|总计|Microsoft.ServiceBus 的用户错误数。|EntityName、 |
|WSXNS|否|内存使用率（已弃用）|百分比|最大值|服务总线高级命名空间内存使用率指标。 此指标已弃用。 请改用内存使用率 (NamespaceMemoryUsage) 指标。|无维度|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActualCpu|否|ActualCpu|Count|平均值|实际 CPU 用量（毫核心）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|否|ActualMemory|字节|平均值|实际内存用量 (MB)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedCpu|否|AllocatedCpu|Count|平均值|分配给此容器的 CPU（毫核心）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|否|AllocatedMemory|字节|平均值|分配给此容器的内存 (MB)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|否|ApplicationStatus|Count|平均值|Service Fabric 网格应用程序的状态|ApplicationName、Status|
|ContainerStatus|否|ContainerStatus|Count|平均值|Service Fabric 网格应用程序中的容器状态|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|CpuUtilization|否|CpuUtilization|百分比|平均值|此容器的 CPU 利用率，以 AllocatedCpu 的百分比表示|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|否|MemoryUtilization|百分比|平均值|此容器的 CPU 利用率，以 AllocatedCpu 的百分比表示|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|RestartCount|否|RestartCount|Count|平均值|Service Fabric 网格应用程序中某个容器的重启计数|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|
|ServiceReplicaStatus|否|ServiceReplicaStatus|Count|平均值|Service Fabric 网格应用程序中某个服务副本的运行状态|ApplicationName、Status、ServiceName、ServiceReplicaName|
|ServiceStatus|否|ServiceStatus|Count|平均值|Service Fabric 网格应用程序中某个服务的运行状态|ApplicationName、Status、ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ConnectionCount|是|连接计数|Count|最大值|用户连接量。|终结点|
|InboundTraffic|是|入站流量|字节|总计|服务的入站流量|无维度|
|MessageCount|是|消息计数|Count|总计|消息总量。|无维度|
|OutboundTraffic|是|出站流量|字节|总计|服务的出站流量|无维度|
|SystemErrors|是|系统错误数|百分比|最大值|系统错误数的百分比|无维度|
|UserErrors|是|用户错误数|百分比|最大值|用户错误数的百分比|无维度|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|avg_cpu_percent|是|CPU 平均百分比|百分比|平均值|CPU 平均百分比|无维度|
|io_bytes_read|是|已读取的 IO 字节数|字节|平均值|已读取的 IO 字节数|无维度|
|io_bytes_written|是|已写入的 IO 字节数|字节|平均值|已写入的 IO 字节数|无维度|
|io_requests|是|IO 请求计数|Count|平均值|IO 请求计数|无维度|
|reserved_storage_mb|是|预留的存储空间|Count|平均值|预留的存储空间|无维度|
|storage_space_used_mb|是|已使用的存储空间|Count|平均值|已使用的存储空间|无维度|
|virtual_core_count|是|虚拟核心计数|Count|平均值|虚拟核心计数|无维度|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|active_queries|是|活动查询数|计数|总计|所有工作负荷组的活动查询数。 仅适用于数据仓库。|无维度|
|allocated_data_storage|是|已分配的数据空间|字节|平均值|已分配的数据存储。 不适用于数据仓库。|无维度|
|app_cpu_billed|是|计费的应用 CPU|Count|总计|计费的应用 CPU。 适用于无服务器数据库。|无维度|
|app_cpu_percent|是|应用 CPU 百分比|百分比|平均值|应用 CPU 百分比。 适用于无服务器数据库。|无维度|
|app_memory_percent|是|应用内存百分比|百分比|平均值|应用内存百分比。 适用于无服务器数据库。|无维度|
|base_blob_size_bytes|是|基本 Blob 存储大小|字节|最大值|基本 Blob 存储大小。 适用于超大规模数据库。|无维度|
|blocked_by_firewall|是|被防火墙阻止|Count|总计|被防火墙阻止|无维度|
|cache_hit_percent|是|缓存命中百分比|百分比|最大值|缓存命中百分比。 仅适用于数据仓库。|无维度|
|cache_used_percent|是|缓存使用百分比|百分比|最大值|缓存使用百分比。 仅适用于数据仓库。|无维度|
|connection_failed|是|失败的连接数|Count|总计|失败的连接数|无维度|
|connection_successful|是|成功的连接数|Count|总计|成功的连接数|无维度|
|cpu_limit|是|CPU 限制|Count|平均值|CPU 限制。 适用于基于 vCore 的数据库。|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|cpu_used|是|已用 CPU|Count|平均值|已用 CPU。 适用于基于 vCore 的数据库。|无维度|
|deadlock|是|死锁数|Count|总计|死锁数。 不适用于数据仓库。|无维度|
|diff_backup_size_bytes|是|差异备份存储大小|字节|最大值|累积的差异备份存储大小。 适用于基于 vCore 的数据库。 不适用于超大规模数据库。|无维度|
|dtu_consumption_percent|是|DTU 百分比|百分比|平均值|DTU 百分比。 适用于基于 DTU 的数据库。|无维度|
|dtu_limit|是|DTU 限制|Count|平均值|DTU 限制。 适用于基于 DTU 的数据库。|无维度|
|dtu_used|是|已用的 DTU|Count|平均值|已用 DTU。 适用于基于 DTU 的数据库。|无维度|
|dw_backup_size_gb|是|数据存储大小(GB)|计数|总计|数据存储大小由数据和事务日志的大小组成。 该指标计入账单的“存储”部分。 仅适用于数据仓库。|无维度|
|dw_geosnapshot_size_gb|是|Disaster Recovery Storage Size (GB)|计数|总计|灾难恢复存储大小在账单中显示为“灾难恢复存储”。 仅适用于数据仓库。|无维度|
|dw_snapshot_size_gb|是|Snapshot Storage Size (GB)|计数|总计|快照存储大小是快照捕获的增量更改的大小，用于创建用户定义的还原点和自动还原点。 该指标计入账单的“存储”部分。 仅适用于数据仓库。|无维度|
|dwu_consumption_percent|是|DWU 百分比|百分比|最大值|DWU 百分比。 仅适用于数据仓库。|无维度|
|dwu_limit|是|DWU 限制|Count|最大值|DWU 限制。 仅适用于数据仓库。|无维度|
|dwu_used|是|已用的 DWU|Count|最大值|已用 DWU。 仅适用于数据仓库。|无维度|
|full_backup_size_bytes|是|完整备份存储大小|字节|最大值|累积的完整备份存储大小。 适用于基于 vCore 的数据库。 不适用于超大规模数据库。|无维度|
|local_tempdb_usage_percent|是|本地 tempdb 百分比|百分比|平均值|本地 tempdb 百分比。 仅适用于数据仓库。|无维度|
|log_backup_size_bytes|是|日志备份存储大小|字节|最大值|累积的日志备份存储大小。 适用于基于 vCore 和超大规模的数据库。|无维度|
|log_write_percent|是|日志 IO 百分比|百分比|平均值|日志 IO 百分比。 不适用于数据仓库。|无维度|
|memory_usage_percent|是|内存百分比|百分比|最大值|内存百分比。 仅适用于数据仓库。|无维度|
|physical_data_read_percent|是|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无维度|
|queued_queries|是|排队的查询数|计数|总计|跨所有工作负荷组的排队查询数。 仅适用于数据仓库。|无维度|
|sessions_percent|是|会话百分比|百分比|平均值|会话百分比。 不适用于数据仓库。|无维度|
|snapshot_backup_size_bytes|是|快照备份存储大小|字节|最大值|累积快照备份存储大小。 适用于超大规模数据库。|无维度|
|sqlserver_process_core_percent|是|SQL Server 进程核心百分比|百分比|最大值|CPU 使用率，以 SQL 数据库进程百分比表示。 不适用于数据仓库。|无维度|
|sqlserver_process_memory_percent|是|SQL Server 进程内存百分比|百分比|最大值|内存使用率，以 SQL 数据库进程百分比表示。 不适用于数据仓库。|无维度|
|存储|是|已用数据空间|字节|最大值|已用数据空间。 不适用于数据仓库。|无维度|
|storage_percent|是|已用数据空间百分比|百分比|最大值|已用数据空间百分比。 不适用于数据仓库或超大规模数据库。|无维度|
|tempdb_data_size|是|Tempdb 数据文件大小 (KB)|Count|最大值|Tempdb 数据文件大小 (KB)。 不适用于数据仓库。|无维度|
|tempdb_log_size|是|Tempdb 日志文件大小 (KB)|Count|最大值|Tempdb 日志文件大小 (KB)。 不适用于数据仓库。|无维度|
|tempdb_log_used_percent|是|Tempdb 日志已用百分比|百分比|最大值|Tempdb 日志已用百分比。 不适用于数据仓库。|无维度|
|wlg_active_queries|是|工作负荷组活动查询|计数|总计|工作负荷组中的活动查询数。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|是|工作负荷组查询超时|计数|总计|已超时的工作负荷组查询数。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|是|按系统百分比列出的工作负荷组分配|百分比|最大值|每个工作负荷组的相对于整个系统的资源分配百分比。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|是|按上限资源百分比列出的工作负荷组分配|百分比|最大值|每个工作负荷组的相对于指定资源上限的资源分配百分比。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|是|有效上限资源百分比|百分比|最大值|对工作负荷组可访问的资源百分比施加的硬性限制，其中考虑了为其他工作负荷组分配的“有效最小资源百分比”。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|是|有效最小资源百分比|百分比|最大值|在考虑了最低服务级别的前提下，为工作负荷组保留并隔离的资源的最小百分比。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|是|工作负荷组排队查询|计数|总计|工作负荷组中的排队查询数。 仅适用于数据仓库。|WorkloadGroupName, IsUserDefined|
|workers_percent|是|辅助角色百分比|百分比|平均值|辅助角色百分比。 不适用于数据仓库。|无维度|
|xtp_storage_percent|是|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比。 不适用于数据仓库。|无维度|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|allocated_data_storage|是|已分配的数据空间|字节|平均值|已分配的数据空间|无维度|
|allocated_data_storage_percent|是|分配的数据空间百分比|百分比|最大值|分配的数据空间百分比|无维度|
|cpu_limit|是|CPU 限制|Count|平均值|CPU 限制。 适用于基于 vCore 的弹性池。|无维度|
|cpu_percent|是|CPU 百分比|百分比|平均值|CPU 百分比|无维度|
|cpu_used|是|已用 CPU|Count|平均值|已用 CPU。 适用于基于 vCore 的弹性池。|无维度|
|database_allocated_data_storage|否|已分配的数据空间|字节|平均值|已分配的数据空间|DatabaseResourceId|
|database_cpu_limit|否|CPU 限制|Count|平均值|CPU 限制|DatabaseResourceId|
|database_cpu_percent|否|CPU 百分比|百分比|平均值|CPU 百分比|DatabaseResourceId|
|database_cpu_used|否|已用 CPU|Count|平均值|已用 CPU|DatabaseResourceId|
|database_dtu_consumption_percent|否|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId|
|database_eDTU_used|否|已用的 eDTU|Count|平均值|已用的 eDTU|DatabaseResourceId|
|database_log_write_percent|否|日志 IO 百分比|百分比|平均值|日志 IO 百分比|DatabaseResourceId|
|database_physical_data_read_percent|否|数据 IO 百分比|百分比|平均值|数据 IO 百分比|DatabaseResourceId|
|database_sessions_percent|否|会话百分比|百分比|平均值|会话百分比|DatabaseResourceId|
|database_storage_used|否|已用数据空间|字节|平均值|已用数据空间|DatabaseResourceId|
|database_workers_percent|否|辅助角色百分比|百分比|平均值|辅助角色百分比|DatabaseResourceId|
|dtu_consumption_percent|是|DTU 百分比|百分比|平均值|DTU 百分比。 适用于基于 DTU 的弹性池。|无维度|
|eDTU_limit|是|eDTU 限制|Count|平均值|eDTU 限制。 适用于基于 DTU 的弹性池。|无维度|
|eDTU_used|是|已用的 eDTU|Count|平均值|已用 eDTU。 适用于基于 DTU 的弹性池。|无维度|
|log_write_percent|是|日志 IO 百分比|百分比|平均值|日志 IO 百分比|无维度|
|physical_data_read_percent|是|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无维度|
|sessions_percent|是|会话百分比|百分比|平均值|会话百分比|无维度|
|sqlserver_process_core_percent|是|SQL Server 进程核心百分比|百分比|最大值|CPU 使用率，以 SQL 数据库进程百分比表示。 适用于弹性池。|无维度|
|sqlserver_process_memory_percent|是|SQL Server 进程内存百分比|百分比|最大值|内存使用率，以 SQL 数据库进程百分比表示。 适用于弹性池。|无维度|
|storage_limit|是|数据最大大小|字节|平均值|数据最大大小|无维度|
|storage_percent|是|已用数据空间百分比|百分比|平均值|已用数据空间百分比|无维度|
|storage_used|是|已用数据空间|字节|平均值|已用数据空间|无维度|
|tempdb_data_size|是|Tempdb 数据文件大小 (KB)|Count|最大值|Tempdb 数据文件大小 (KB)|无维度|
|tempdb_log_size|是|Tempdb 日志文件大小 (KB)|Count|最大值|Tempdb 日志文件大小 (KB)|无维度|
|tempdb_log_used_percent|是|Tempdb 日志已用百分比|百分比|最大值|Tempdb 日志已用百分比|无维度|
|workers_percent|是|辅助角色百分比|百分比|平均值|辅助角色百分比|无维度|
|xtp_storage_percent|是|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比|无维度|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|流出量|是|流出量|字节|总计|流出数据量。 此数字包括从 Azure 存储到外部客户端流出的数据量，以及 Azure 内部的流出数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|事务|是|事务|Count|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|否|已用容量|字节|平均值|存储帐户使用的存储量。 对于标准存储帐户，该指标是 Blob、表、文件和队列使用的容量总和。 对于高级存储帐户和 Blob 存储帐户，它与 BlobCapacity 或 FileCapacity 相同。|无维度|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|BlobCapacity|否|Blob 容量|字节|平均值|存储帐户的 Blob 服务使用的存储量（以字节为单位）。|BlobType、Tier|
|BlobCount|否|Blob 计数|Count|平均值|在存储帐户中存储的 Blob 对象数。|BlobType、Tier|
|BlobProvisionedSize|否|Blob Provisioned Size|字节|平均值|存储帐户的 Blob 服务中预配的存储量（以字节为单位）。|BlobType、Tier|
|ContainerCount|是|Blob 容器计数|Count|平均值|存储帐户中的容器数。|无维度|
|流出量|是|流出量|字节|总计|流出数据量。 此数字包括从 Azure 存储到外部客户端流出的数据量，以及 Azure 内部的流出数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|IndexCapacity|否|索引容量|字节|平均值|Azure Data Lake Storage Gen2 分层索引使用的存储量。|无维度|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|事务|是|事务|Count|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication, FileShare|
|流出量|是|流出量|字节|总计|流出数据量。 此数字包括从 Azure 存储到外部客户端流出的数据量，以及 Azure 内部的流出数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|否|文件容量|字节|平均值|存储帐户使用的文件存储量。|FileShare|
|FileCount|否|文件计数|Count|平均值|存储帐户中的文件数目。|FileShare|
|FileShareCapacityQuota|否|File Share Capacity Quota|字节|平均值|Azure 文件服务可使用的存储量上限（以字节为单位）。|FileShare|
|FileShareCount|否|文件共享计数|Count|平均值|存储帐户中的文件共享数目。|无维度|
|FileShareProvisionedIOPS|否|File Share Provisioned IOPS|字节|平均值|高级文件存储帐户中高级文件共享预配的 IOPS 的基线数。 此数字是根据共享容量的配置大小（配额）计算的。|FileShare|
|FileShareSnapshotCount|否|文件共享快照计数|Count|平均值|存储帐户文件服务的共享中存在的快照数。|FileShare|
|FileShareSnapshotSize|否|文件共享快照大小|字节|平均值|存储帐户的文件服务中的快照所用存储量（以字节为单位）。|FileShare|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication, FileShare|
|事务|是|事务|Count|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|流出量|是|流出量|字节|总计|流出数据量。 此数字包括从 Azure 存储到外部客户端流出的数据量，以及 Azure 内部的流出数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|QueueCapacity|是|队列容量|字节|平均值|存储帐户使用的队列存储量。|无维度|
|QueueCount|是|队列计数|Count|平均值|存储帐户中的队列数目。|无维度|
|QueueMessageCount|是|队列消息计数|Count|平均值|存储帐户中未失效的队列消息数目。|无维度|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|事务|是|事务|Count|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType, ApiName, Authentication|
|流出量|是|流出量|字节|总计|流出数据量。 此数字包括从 Azure 存储到外部客户端流出的数据量，以及 Azure 内部的流出数据量。 因此，此数字不反映计费的流出量。|GeoType, ApiName, Authentication|
|流入量|是|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType, ApiName, Authentication|
|SuccessE2ELatency|是|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType, ApiName, Authentication|
|SuccessServerLatency|是|成功服务器延迟|毫秒|平均值|Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType, ApiName, Authentication|
|TableCapacity|是|表容量|字节|平均值|存储帐户使用的表存储量。|无维度|
|TableCount|是|表计数|Count|平均值|存储帐户中的表数目。|无维度|
|TableEntityCount|是|表实体计数|Count|平均值|存储帐户中的表实体数目。|无维度|
|事务|是|事务|Count|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|是|同步会话结果|Count|平均值|每当服务器终结点在云终结点中成功完成同步会话时，记录值 1 的指标|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncBatchTransferredFileBytes|是|同步的字节数|字节|总计|为同步会话传输的总文件大小|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|是|应用程序的云分层重调大小|字节|总计|应用程序重调的数据大小|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|是|云分层重调大小|字节|总计|回调的数据的大小|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|是|云分层回调|字节|总计|服务器重新调用的数据总大小|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|是|云分层重调吞吐量|每秒字节数|平均值|数据重调吞吐量大小|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|是|服务器联机状态|Count|最大值|每当已注册的服务器在云终结点中成功记录检测信号时，记录值 1 的指标|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|是|同步的文件|Count|总计|已同步的文件计数|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|是|未同步的文件|Count|总计|无法同步的文件计数|SyncGroupName、ServerEndpointName、SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ServerHeartbeat|是|服务器联机状态|Count|最大值|每当已注册的服务器在云终结点中成功记录检测信号时，记录值 1 的指标|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|是|云分层回调|字节|总计|服务器重新调用的数据总大小|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|是|同步的字节数|字节|总计|为同步会话传输的总文件大小|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|是|同步的文件|Count|总计|已同步的文件计数|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|是|未同步的文件|Count|总计|无法同步的文件计数|SyncGroupName、ServerEndpointName、SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|是|同步的字节数|字节|总计|为同步会话传输的总文件大小|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|是|同步的文件|Count|总计|已同步的文件计数|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|是|未同步的文件|Count|总计|无法同步的文件计数|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|是|失败的函数请求数|Count|总计|失败的函数请求数|LogicalName、PartitionId|
|AMLCalloutInputEvents|是|函数事件数|Count|总计|函数事件数|LogicalName、PartitionId|
|AMLCalloutRequests|是|函数请求数|Count|总计|函数请求数|LogicalName、PartitionId|
|ConversionErrors|是|数据转换错误数|Count|总计|数据转换错误数|LogicalName、PartitionId|
|DeserializationError|是|输入反序列化错误|Count|总计|输入反序列化错误|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|是|失序事件数|Count|总计|失序事件数|LogicalName、PartitionId|
|EarlyInputEvents|是|早期输入事件数|Count|总计|早期输入事件数|LogicalName、PartitionId|
|错误|是|运行时错误|Count|总计|运行时错误|LogicalName、PartitionId|
|InputEventBytes|是|输入事件字节数|字节|总计|输入事件字节数|LogicalName、PartitionId|
|InputEvents|是|输入事件数|Count|总计|输入事件数|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|是|积压的输入事件数|Count|最大值|积压的输入事件数|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|是|收到的输入源数|Count|总计|收到的输入源数|LogicalName、PartitionId|
|LateInputEvents|是|延迟输入事件数|Count|总计|延迟输入事件数|LogicalName、PartitionId|
|OutputEvents|是|输出事件数|Count|总计|输出事件数|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|是|水印延迟|秒|最大值|水印延迟|LogicalName、PartitionId|
|ResourceUtilization|是|流单元利用率 %|百分比|最大值|流单元利用率 %|LogicalName、PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|否|已结束活动运行数|计数|总计|成功、失败或取消的业务流程活动计数|Result, FailureType, Activity, ActivityType, Pipeline|
|OrchestrationPipelineRunsEnded|否|已结束管道运行数|计数|总计|成功、失败或取消的业务流程管道运行计数|Result, FailureType, Pipeline|
|OrchestrationTriggersEnded|否|已结束触发数|计数|总计|成功、失败或取消的业务流程触发计数|Result, FailureType, Trigger|
|SQLOnDemandLoginAttempts|否|登录尝试数|计数|总计|成功或失败的登录尝试计数|结果|
|SQLOnDemandQueriesEnded|否|已结束查询数|计数|总计|成功、失败或取消的查询计数|结果|
|SQLOnDemandQueryProcessedBytes|否|已处理的数据|字节|总计|查询处理的数据量|无维度|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|CoresCapacity|否|核心容量|计数|最大值|核心容量|无维度|
|MemoryCapacityGB|否|内存容量 (GB)|计数|最大值|内存容量 (GB)|无维度|
|SparkJobsEnded|是|已结束应用程序数|计数|总计|已结束的应用程序计数|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|否|自适应缓存命中百分比|百分比|最大值|度量工作负荷使用自适应缓存的效果。 将此指标与缓存命中百分比指标一起使用，以确定是要扩展以增加容量还是重新运行工作负荷以生成缓存|无维度|
|AdaptiveCacheUsedPercent|否|自适应缓存使用百分比|百分比|最大值|度量工作负荷使用自适应缓存的效果。 将此指标与缓存已用百分比指标一起使用，以确定是要扩展以增加容量还是重新运行工作负荷以生成缓存|无维度|
|连接|是|连接|Count|总计|SQL 池的总登录数|结果|
|ConnectionsBlockedByFirewall|否|防火墙阻止的连接数|计数|总计|防火墙规则阻止的连接计数。 重新访问 SQL 池的访问控制策略，并在计数较高时监视这些连接|无维度|
|DWULimit|否|DWU 限制|Count|最大值|SQL 池的服务级别目标|无维度|
|DWUUsed|否|已用的 DWU|Count|最大值|整个 SQL 池中使用情况的高级表示形式。 按“DWU 限制”*“DWU 百分比”来度量|无维度|
|DWUUsedPercent|否|DWU 使用百分比|百分比|最大值|整个 SQL 池中使用情况的高级表示形式。 通过选择 CPU 百分比与数据 IO 百分比这二者中的最大值来度量|无维度|
|LocalTempDBUsedPercent|否|本地 tempdb 已用百分比|百分比|最大值|所有计算节点上的本地 tempdb 利用率 - 每五分钟发出一次值|无维度|
|MemoryUsedPercent|否|已用内存百分比|百分比|最大值|SQL 池中所有节点的内存使用率|无维度|
|wlg_effective_min_resource_percent|是|有效最小资源百分比|百分比|最小值|在考虑了服务级别和工作负荷组设置的情况下，允许的有效最小资源百分比设置。 在较低的服务级别可将有效 min_percentage_resource 调整为更高|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|否|工作负荷组活动查询|计数|总计|工作负荷组中的活动查询。 如果使用此指标时不进行筛选和拆分，会显示系统上运行的所有活动查询|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|否|工作负荷组查询超时|计数|总计|已超时的工作负荷组查询。只有在查询开始执行查询后，此指标才报告查询超时（不包括由于锁定或资源等待而导致的等待时间）|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|否|按最大资源百分比列出的工作负荷组分配|百分比|最大值|显示每个工作负荷组的相对于“有效上限资源百分比”的资源分配百分比。 此指标提供工作负荷组的有效利用率|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|否|按系统百分比列出的工作负荷组分配|百分比|最大值|相对于整个系统的资源分配百分比|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|是|有效上限资源百分比|百分比|最大值|工作负荷组的有效上限资源百分比。 如果存在其他 min_percentage_resource > 0 的工作负荷组，effective_cap_percentage_resource 按比例降低|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|否|工作负荷组排队查询|计数|总计|达到最大并发限制后排队请求的累计计数|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|是|入口收到的字节数|字节|总计|从所有事件源读取的字节数的计数|无维度|
|IngressReceivedInvalidMessages|是|入口收到的无效消息数|计数|总计|从所有事件中心或 IoT 中心事件源读取的无效消息的计数|无维度|
|IngressReceivedMessages|是|入口收到的消息数|计数|总计|从所有事件中心或 IoT 中心事件源读取的消息的计数|无维度|
|IngressReceivedMessagesCountLag|是|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|无维度|
|IngressReceivedMessagesTimeLag|是|入口收到的消息数时间延迟|秒|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|无维度|
|IngressStoredBytes|是|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|无维度|
|IngressStoredEvents|是|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|无维度|
|WarmStorageMaxProperties|是|暖存储最大属性数|计数|最大值|S1/S2 SKU 环境允许使用的最大属性数，以及 PAYG SKU 暖存储允许的最大属性数|无维度|
|WarmStorageUsedProperties|是|暖存储已用属性数 |计数|最大值|S1/S2 SKU 环境已使用的属性数，以及 PAYG SKU 暖存储已使用的属性数|无维度|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|是|入口收到的字节数|字节|总计|从事件源读取的字节数的计数|无维度|
|IngressReceivedInvalidMessages|是|入口收到的无效消息数|计数|总计|从事件源中读取的无效消息的计数|无维度|
|IngressReceivedMessages|是|入口收到的消息数|计数|总计|从事件源中读取的消息的计数|无维度|
|IngressReceivedMessagesCountLag|是|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|无维度|
|IngressReceivedMessagesTimeLag|是|入口收到的消息数时间延迟|秒|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|无维度|
|IngressStoredBytes|是|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|无维度|
|IngressStoredEvents|是|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|无维度|
|WarmStorageMaxProperties|是|暖存储最大属性数|计数|最大值|S1/S2 SKU 环境允许使用的最大属性数，以及 PAYG SKU 暖存储允许的最大属性数|无维度|
|WarmStorageUsedProperties|是|暖存储已用属性数 |计数|最大值|S1/S2 SKU 环境已使用的属性数，以及 PAYG SKU 暖存储已使用的属性数|无维度|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|磁盘读取字节数|是|磁盘读取字节数|字节|总计|由于读取操作超过采样期限而限制的总磁盘吞吐量。|无维度|
|磁盘读取操作次数/秒|是|磁盘读取操作次数/秒|每秒计数|平均值|上一个采样周期的平均 IO 读取操作次数。 请注意，这些操作的大小可能是可变的。|无维度|
|磁盘写入字节数|是|磁盘写入字节数|字节|总计|由于写入操作超过采样期限而限制的总磁盘吞吐量。|无维度|
|磁盘写入操作次数/秒|是|磁盘写入操作次数/秒|每秒计数|平均值|上一个采样周期的平均 IO 写入操作次数。 请注意，这些操作的大小可能是可变的。|无维度|
|DiskReadBytesPerSecond|是|Disk Read Bytes/Sec|每秒字节数|平均值|由于读取操作超过采样期限而限制的平均磁盘吞吐量。|无维度|
|DiskReadLatency|是|磁盘读取延迟|毫秒|平均值|总读取延迟。 设备和内核读取延迟之和。|无维度|
|DiskReadOperations|是|磁盘读取操作次数|Count|总计|上一个采样周期的 IO 读取操作次数。 请注意，这些操作的大小可能是可变的。|无维度|
|DiskWriteBytesPerSecond|是|Disk Write Bytes/Sec|每秒字节数|平均值|由于写入操作超过采样期限而限制的平均磁盘吞吐量。|无维度|
|DiskWriteLatency|是|磁盘写入延迟|毫秒|平均值|总写入延迟。 设备和内核写入延迟之和。|无维度|
|DiskWriteOperations|是|磁盘写入操作次数|Count|总计|上一个采样周期的 IO 写入操作次数。 请注意，这些操作的大小可能是可变的。|无维度|
|MemoryActive|是|活动的内存量|字节|平均值|VM 在过去的小段时间范围内使用的内存量。 这是 VM 当前所需的“真正”内存量。 此外，未使用的内存可能会换出或消逝，而不会影响来宾的性能。|无维度|
|MemoryGranted|是|授予的内存量|字节|平均值|由主机授予 VM 的内存量。 在至少使用主机一次之前，不会向主机授予内存；如果 VMkernel 需要内存，则授予的内存可能会换出或消逝。|无维度|
|MemoryUsed|是|已用内存量|字节|平均值|VM 已使用的计算机内存量。|无维度|
|网络传入|是|网络传入|字节|总计|收到的流量的总网络吞吐量。|无维度|
|网络传出|是|网络传出|字节|总计|传输的流量的总网络吞吐量。|无维度|
|NetworkInBytesPerSecond|是|网络输入字节数/秒|每秒字节数|平均值|收到的流量的平均网络吞吐量。|无维度|
|NetworkOutBytesPerSecond|是|网络输出字节数/秒|每秒字节数|平均值|传输的流量的平均网络吞吐量。|无维度|
|CPU 百分比|是|CPU 百分比|百分比|平均值|CPU 利用率。 此值按 100% 的量（表示系统上的所有处理器核心）报告。 例如，使用四核系统的 50% CPU 的双向 VM 将完全占用两个核心。|无维度|
|PercentageCpuReady|是|已就绪 CPU 百分比|毫秒|总计|就绪时间是指在过去的更新间隔内，等待 CPU 可供使用所花费的时间。|无维度|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|ActiveRequests|是|活动请求数|Count|总计|活动请求数|实例|
|AverageResponseTime|是|平均响应时间|秒|平均值|平均响应时间|实例|
|BytesReceived|是|数据输入|字节|总计|数据输入|实例|
|BytesSent|是|数据输出|字节|总计|数据输出|实例|
|CpuPercentage|是|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|DiskQueueLength|是|磁盘队列长度|Count|平均值|磁盘队列长度|实例|
|Http101|是|Http 101|Count|总计|Http 101|实例|
|Http2xx|是|Http 2xx|Count|总计|Http 2xx|实例|
|Http3xx|是|Http 3xx|Count|总计|Http 3xx|实例|
|Http401|是|Http 401|Count|总计|Http 401|实例|
|Http403|是|Http 403|Count|总计|Http 403|实例|
|Http404|是|Http 404|Count|总计|Http 404|实例|
|Http406|是|Http 406|Count|总计|Http 406|实例|
|Http4xx|是|Http 4xx|Count|总计|Http 4xx|实例|
|Http5xx|是|Http 服务器错误|Count|总计|Http 服务器错误|实例|
|HttpQueueLength|是|Http 队列长度|Count|平均值|Http 队列长度|实例|
|LargeAppServicePlanInstances|是|大型应用服务计划工作线程数|Count|平均值|大型应用服务计划工作线程数|无维度|
|MediumAppServicePlanInstances|是|中型应用服务计划工作线程数|Count|平均值|中型应用服务计划工作线程数|无维度|
|MemoryPercentage|是|内存百分比|百分比|平均值|内存百分比|实例|
|请求|是|请求|Count|总计|请求|实例|
|SmallAppServicePlanInstances|是|小型应用服务计划工作线程数|Count|平均值|小型应用服务计划工作线程数|无维度|
|TotalFrontEnds|是|前端总数|Count|平均值|前端总数|无维度|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|CpuPercentage|是|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|是|内存百分比|百分比|平均值|内存百分比|实例|
|WorkersAvailable|是|可用工作线程数|Count|平均值|可用工作线程数|无维度|
|WorkersTotal|是|工作线程总数|Count|平均值|工作线程总数|无维度|
|WorkersUsed|是|使用的工作线程数|Count|平均值|使用的工作线程数|无维度|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|BytesReceived|是|数据输入|字节|总计|数据输入|实例|
|BytesSent|是|数据输出|字节|总计|数据输出|实例|
|CpuPercentage|是|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|DiskQueueLength|是|磁盘队列长度|Count|平均值|磁盘队列长度|实例|
|HttpQueueLength|是|Http 队列长度|Count|平均值|Http 队列长度|实例|
|MemoryPercentage|是|内存百分比|百分比|平均值|内存百分比|实例|
|SocketInboundAll|是|SocketInboundAll|计数|平均值|SocketInboundAll|实例|
|SocketLoopback|是|SocketLoopback|计数|平均值|SocketLoopback|实例|
|SocketOutboundAll|是|SocketOutboundAll|计数|平均值|SocketOutboundAll|实例|
|SocketOutboundEstablished|是|SocketOutboundEstablished|计数|平均值|SocketOutboundEstablished|实例|
|SocketOutboundTimeWait|是|SocketOutboundTimeWait|计数|平均值|SocketOutboundTimeWait|实例|
|TcpCloseWait|是|TCP 关闭等待|Count|平均值|TCP 关闭等待|实例|
|TcpClosing|是|TCP 正在关闭|Count|平均值|TCP 正在关闭|实例|
|TcpEstablished|是|已建立的 TCP|Count|平均值|已建立的 TCP|实例|
|TcpFinWait1|是|TCP Fin 等待 1|Count|平均值|TCP Fin 等待 1|实例|
|TcpFinWait2|是|TCP Fin 等待 2|Count|平均值|TCP Fin 等待 2|实例|
|TcpLastAck|是|TCP 上一次的 Ack|Count|平均值|TCP 上一次的 Ack|实例|
|TcpSynReceived|是|收到的 TCP Syn|Count|平均值|收到的 TCP Syn|实例|
|TcpSynSent|是|发送的 TCP Syn|Count|平均值|发送的 TCP Syn|实例|
|TcpTimeWait|是|TCP 时间等待|Count|平均值|TCP 时间等待|实例|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites（不包括 Functions） 

> [!NOTE]
> **文件系统使用情况**是一个向全球推出的新指标，除非我们已将你加入专用预览版的允许列表，否则你不会获得数据。

> [!IMPORTANT]
> “平均响应时间”将弃用，以避免与指标聚合混淆。 使用“响应时间”作为替代。

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AppConnections|是|连接|Count|平均值|连接|实例|
|AverageMemoryWorkingSet|是|平均内存工作集|字节|平均值|平均内存工作集|实例|
|AverageResponseTime|是|Average Response Time（已弃用）|秒|平均值|平均响应时间|实例|
|BytesReceived|是|数据输入|字节|总计|数据输入|实例|
|BytesSent|是|数据输出|字节|总计|数据输出|实例|
|CpuTime|是|CPU 时间|秒|总计|CPU 时间|实例|
|CurrentAssemblies|是|当前程序集|Count|平均值|当前程序集|实例|
|FileSystemUsage|是|文件系统使用情况|字节|平均值|文件系统使用情况|无维度|
|Gen0Collections|是|第 0 代垃圾回收|Count|总计|第 0 代垃圾回收|实例|
|Gen1Collections|是|第 1 代垃圾回收|Count|总计|第 1 代垃圾回收|实例|
|Gen2Collections|是|第 2 代垃圾回收|Count|总计|第 2 代垃圾回收|实例|
|句柄数|是|句柄计数|Count|平均值|句柄计数|实例|
|HealthCheckStatus|是|运行状况检查状态|Count|平均值|运行状况检查状态|实例|
|Http101|是|Http 101|Count|总计|Http 101|实例|
|Http2xx|是|Http 2xx|Count|总计|Http 2xx|实例|
|Http3xx|Http 3xx|Count|总计|Http 3xx|实例|
|Http401|是|Http 401|Count|总计|Http 401|实例|
|Http403|是|Http 403|Count|总计|Http 403|实例|
|Http404|是|Http 404|Count|总计|Http 404|实例|
|Http406|是|Http 406|Count|总计|Http 406|实例|
|Http4xx|是|Http 4xx|Count|总计|Http 4xx|实例|
|Http5xx|是|Http 服务器错误|Count|总计|Http 服务器错误|实例|
|HttpResponseTime|是|响应时间|秒|平均值|响应时间|实例|
|IoOtherBytesPerSecond|是|IO 每秒其他字节数|每秒字节数|总计|IO 每秒其他字节数|实例|
|IoOtherOperationsPerSecond|是|IO 每秒其他操作数|每秒字节数|总计|IO 每秒其他操作数|实例|
|IoReadBytesPerSecond|是|IO 每秒读取字节数|每秒字节数|总计|IO 每秒读取字节数|实例|
|IoReadOperationsPerSecond|是|IO 每秒读取操作数|每秒字节数|总计|IO 每秒读取操作数|实例|
|IoWriteBytesPerSecond|是|IO 每秒写入字节数|每秒字节数|总计|IO 每秒写入字节数|实例|
|IoWriteOperationsPerSecond|是|IO 每秒写入操作数|每秒字节数|总计|IO 每秒写入操作数|实例|
|MemoryWorkingSet|是|内存工作集|字节|平均值|内存工作集|实例|
|PrivateBytes|是|专用字节数|字节|平均值|专用字节数|实例|
|请求|是|请求|Count|总计|请求|实例|
|RequestsInApplicationQueue|是|应用程序队列中的请求数|Count|平均值|应用程序队列中的请求数|实例|
|线程数|是|线程计数|Count|平均值|线程计数|实例|
|TotalAppDomains|是|应用程序域总数|Count|平均值|应用程序域总数|实例|
|TotalAppDomainsUnloaded|是|卸载的应用程序域总数|Count|平均值|卸载的应用程序域总数|实例|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

> [!NOTE]
> **文件系统使用情况**是一个向全球推出的新指标，除非我们已将你加入专用预览版的允许列表，否则你不会获得数据。

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|是|平均内存工作集|字节|平均值|平均内存工作集|实例|
|BytesReceived|是|数据输入|字节|总计|数据输入|实例|
|BytesSent|是|数据输出|字节|总计|数据输出|实例|
|CurrentAssemblies|是|当前程序集|Count|平均值|当前程序集|实例|
|FileSystemUsage|是|文件系统使用情况|字节|平均值|文件系统使用情况|无维度|
|FunctionExecutionCount|是|函数执行计数|Count|总计|函数执行计数|实例|
|FunctionExecutionUnits|是|函数执行单位数|Count|总计|[函数执行单位数](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|实例|
|Gen0Collections|是|第 0 代垃圾回收|Count|总计|第 0 代垃圾回收|实例|
|Gen1Collections|是|第 1 代垃圾回收|Count|总计|第 1 代垃圾回收|实例|
|Gen2Collections|是|第 2 代垃圾回收|Count|总计|第 2 代垃圾回收|实例|
|HealthCheckStatus|是|运行状况检查状态|Count|平均值|运行状况检查状态|实例|
|Http5xx|是|Http 服务器错误|Count|总计|Http 服务器错误|实例|
|IoOtherBytesPerSecond|是|IO 每秒其他字节数|每秒字节数|总计|IO 每秒其他字节数|实例|
|IoOtherOperationsPerSecond|是|IO 每秒其他操作数|每秒字节数|总计|IO 每秒其他操作数|实例|
|IoReadBytesPerSecond|是|IO 每秒读取字节数|每秒字节数|总计|IO 每秒读取字节数|实例|
|IoReadOperationsPerSecond|是|IO 每秒读取操作数|每秒字节数|总计|IO 每秒读取操作数|实例|
|IoWriteBytesPerSecond|是|IO 每秒写入字节数|每秒字节数|总计|IO 每秒写入字节数|实例|
|IoWriteOperationsPerSecond|是|IO 每秒写入操作数|每秒字节数|总计|IO 每秒写入操作数|实例|
|MemoryWorkingSet|是|内存工作集|字节|平均值|内存工作集|实例|
|PrivateBytes|是|专用字节数|字节|平均值|专用字节数|实例|
|RequestsInApplicationQueue|是|应用程序队列中的请求数|Count|平均值|应用程序队列中的请求数|实例|
|TotalAppDomains|是|应用程序域总数|Count|平均值|应用程序域总数|实例|
|TotalAppDomainsUnloaded|是|卸载的应用程序域总数|Count|平均值|卸载的应用程序域总数|实例|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|AppConnections|是|连接|Count|平均值|连接|实例|
|AverageMemoryWorkingSet|是|平均内存工作集|字节|平均值|平均内存工作集|实例|
|AverageResponseTime|是|平均响应时间|秒|平均值|平均响应时间|实例|
|BytesReceived|是|数据输入|字节|总计|数据输入|实例|
|BytesSent|是|数据输出|字节|总计|数据输出|实例|
|CpuTime|是|CPU 时间|秒|总计|CPU 时间|实例|
|CurrentAssemblies|是|当前程序集|Count|平均值|当前程序集|实例|
|FileSystemUsage|是|文件系统使用情况|字节|平均值|文件系统使用情况|无维度|
|FunctionExecutionCount|是|函数执行计数|Count|总计|函数执行计数|实例|
|FunctionExecutionUnits|是|函数执行单位数|Count|总计|函数执行单位数|实例|
|Gen0Collections|是|第 0 代垃圾回收|Count|总计|第 0 代垃圾回收|实例|
|Gen1Collections|是|第 1 代垃圾回收|Count|总计|第 1 代垃圾回收|实例|
|Gen2Collections|是|第 2 代垃圾回收|Count|总计|第 2 代垃圾回收|实例|
|句柄数|是|句柄计数|Count|平均值|句柄计数|实例|
|HealthCheckStatus|是|运行状况检查状态|Count|平均值|运行状况检查状态|实例|
|Http101|是|Http 101|Count|总计|Http 101|实例|
|Http2xx|是|Http 2xx|Count|总计|Http 2xx|实例|
|Http3xx|是|Http 3xx|Count|总计|Http 3xx|实例|
|Http401|是|Http 401|Count|总计|Http 401|实例|
|Http403|是|Http 403|Count|总计|Http 403|实例|
|Http404|是|Http 404|Count|总计|Http 404|实例|
|Http406|是|Http 406|Count|总计|Http 406|实例|
|Http4xx|是|Http 4xx|Count|总计|Http 4xx|实例|
|Http5xx|是|Http 服务器错误|Count|总计|Http 服务器错误|实例|
|HttpResponseTime|是|响应时间|秒|平均值|响应时间|实例|
|IoOtherBytesPerSecond|是|IO 每秒其他字节数|每秒字节数|总计|IO 每秒其他字节数|实例|
|IoOtherOperationsPerSecond|是|IO 每秒其他操作数|每秒字节数|总计|IO 每秒其他操作数|实例|
|IoReadBytesPerSecond|是|IO 每秒读取字节数|每秒字节数|总计|IO 每秒读取字节数|实例|
|IoReadOperationsPerSecond|是|IO 每秒读取操作数|每秒字节数|总计|IO 每秒读取操作数|实例|
|IoWriteBytesPerSecond|是|IO 每秒写入字节数|每秒字节数|总计|IO 每秒写入字节数|实例|
|IoWriteOperationsPerSecond|是|IO 每秒写入操作数|每秒字节数|总计|IO 每秒写入操作数|实例|
|MemoryWorkingSet|是|内存工作集|字节|平均值|内存工作集|实例|
|PrivateBytes|是|专用字节数|字节|平均值|专用字节数|实例|
|请求|是|请求|Count|总计|请求|实例|
|RequestsInApplicationQueue|是|应用程序队列中的请求数|Count|平均值|应用程序队列中的请求数|实例|
|线程数|是|线程计数|Count|平均值|线程计数|实例|
|TotalAppDomains|是|应用程序域总数|Count|平均值|应用程序域总数|实例|
|TotalAppDomainsUnloaded|是|卸载的应用程序域总数|Count|平均值|卸载的应用程序域总数|实例|


## <a name="next-steps"></a>后续步骤
* [了解 Azure 监视器中的指标](data-platform.md)
* [针对指标创建警报](alerts-overview.md)
* [将指标导出到存储、事件中心或 Log Analytics](platform-logs-overview.md)
