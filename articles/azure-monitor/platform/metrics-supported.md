---
title: Azure Monitor 按资源类型支持的指标
description: 可在 Azure 监视器中为每种资源类型使用的指标的列表。
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 3d7ab9d4e7a7c560fa05bdc06c7d1c357a2c2767
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196632"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure 监视器支持的指标

> [!NOTE]
> 此列表主要从 Azure Monitor 度量值自动生成 REST API。 通过 Github 对此列表所做的任何修改都可以进行编写，而不会发出警告。 请与本文的作者联系，以获取有关如何进行永久更新的详细信息。

Azure Monitor 提供多种方式来与指标交互，包括在门户中制作指标图表、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 

本文列出了当前可用于 Azure Monitor 的合并指标管道的所有平台（即，自动收集的）指标。 此列表是最近一次更新的，27日，2020。 此日期之后更改或添加的指标可能不会出现在下面。 若要以编程方式查询和访问指标列表，请使用[2018-01-01 api 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)。 不在此列表中的其他指标可在门户中或使用旧版 Api 提供。

度量标准按资源提供程序和资源类型进行组织。 有关服务及其所属的资源提供程序的列表，请参阅[Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)。 


## <a name="guest-os-metrics"></a>来宾 OS 指标

此处**未**列出在 Azure 虚拟机、Service Fabric 和云服务中运行的来宾操作系统（来宾 os）的指标。 相反，必须通过在或作为来宾操作系统的一部分运行的一个或多个代理收集来宾 os 性能指标。  来宾 os 指标包括跟踪来宾 CPU 百分比或内存使用率的性能计数器，两者都经常用于自动缩放或警报。  使用[Azure 诊断扩展](diagnostics-extension-overview.md)，可将来宾 os 性能指标发送到存储平台指标的同一数据库。 它通过[自定义指标](metrics-custom-overview.md)API 来路由来宾 os 指标。 然后，你可以对诸如平台指标之类的来宾 os 指标进行图表、警报和其他使用。 有关详细信息，请参阅[监视代理概述](agents-overview.md)。    

## <a name="routing-platform-metrics-to-other-locations"></a>将平台指标路由到其他位置

你可以使用[诊断设置](diagnostic-settings.md)将平台指标路由到 Azure 存储、Azure Monitor 日志（以及因此 Log Analytics）和事件中心。  

可以路由的内容以及存储它们的窗体存在一些限制。 
- 并非所有指标都可导出到其他位置。 有关通过诊断设置导出的平台指标列表，请参阅[此文](metrics-supported-export-diagnostic-settings.md)。

- 当前不支持通过诊断设置将多维指标发送到其他位置。 多维指标将按平展后的单维指标导出，并跨维值聚合。
** 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|qpu_metric|QPU|计数|平均值|QPU。 S1 范围为 0-100，S2 范围为 0-200，S4 范围为 0-400|ServerResourceType|
|memory_metric|内存|字节|平均值|内存。 S1 范围为 0-25 GB，S2 范围为 0-50 GB，S4 范围为 0-100 GB|ServerResourceType|
|private_bytes_metric|专用字节|字节|平均值|专用字节数。|ServerResourceType|
|virtual_bytes_metric|虚拟字节|字节|平均值|虚拟字节数。|ServerResourceType|
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
|mashup_engine_private_bytes_metric|M 引擎专用字节数|字节|平均值|糅合引擎进程使用的专用字节数。|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M 引擎虚拟字节数|字节|平均值|糅合引擎进程使用的虚拟字节数。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalRequests|网关请求总数（已弃用）|计数|总计|网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|Location,Hostname|
|SuccessfulRequests|成功的网关请求数（已弃用）|计数|总计|成功的网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|Location,Hostname|
|UnauthorizedRequests|未经授权的网关请求数（已弃用）|计数|总计|未经授权的网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|Location,Hostname|
|FailedRequests|失败的网关请求数（已弃用）|计数|总计|网关请求失败数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|Location,Hostname|
|OtherRequests|其他网关请求数（已弃用）|计数|总计|其他网关请求数 - 请改用包含 GatewayResponseCodeCategory 维度的多维请求指标|Location,Hostname|
|持续时间|网关请求的总持续时间|毫秒|平均值|网关请求的总持续时间，以毫秒为单位|Location,Hostname|
|BackendDuration|后端请求持续时间|毫秒|平均值|后端请求持续时间（毫秒）|Location,Hostname|
|容量|容量|百分比|平均值|ApiManagement 服务的利用率指标|位置|
|EventHubTotalEvents|事件中心事件总数|计数|总计|发送到事件中心的事件数|位置|
|EventHubSuccessfulEvents|成功的事件中心事件数|计数|总计|成功的事件中心事件数|位置|
|EventHubTotalFailedEvents|失败的事件中心事件数|计数|总计|失败的事件中心事件数|位置|
|EventHubRejectedEvents|拒绝的事件中心事件数|计数|总计|拒绝的事件中心事件（配置不当或未授权）数|位置|
|EventHubThrottledEvents|限制的事件中心事件数|计数|总计|限制的事件中心事件数|位置|
|EventHubTimedoutEvents|超时的事件中心事件数|计数|总计|超时的事件中心事件数|位置|
|EventHubDroppedEvents|删除的事件中心事件数|计数|总计|由于达到队列大小限制而跳过的事件数|位置|
|EventHubTotalBytesSent|事件中心事件大小|字节|总计|事件中心事件的总大小，以字节为单位|位置|
|请求|请求|计数|总计|包含多个维度的网关请求指标|Location,Hostname,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|网络连接|资源的网络连接状态（预览）|计数|总计|API 管理服务中依赖资源类型的网络连接状态|位置，ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|计数|计数|传入的 http 请求总数。|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|计数|平均值|http 请求延迟。|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|系统 CPU 使用率百分比|百分比|平均值|整个系统的最近 CPU 使用率|AppName,Pod|
|AppCpuUsagePercentage|应用 CPU 使用率百分比|百分比|平均值|应用 JVM CPU 使用率百分比|AppName,Pod|
|AppMemoryCommitted|分配的应用内存|字节|平均值|分配给 JVM 的内存（字节）|AppName,Pod|
|AppMemoryUsed|已使用的应用内存|字节|平均值|已使用的应用内存（字节）|AppName,Pod|
|AppMemoryMax|最大应用内存|字节|最大值|可用于内存管理的最大内存量（以字节为单位）|AppName,Pod|
|MaxOldGenMemoryPoolBytes|最大可用的旧代数据大小|字节|平均值|旧代内存池的最大大小|AppName,Pod|
|OldGenMemoryPoolBytes|旧代数据大小|字节|平均值|完全 GC 后的旧代内存池大小|AppName,Pod|
|OldGenPromotedBytes|提升为旧代数据大小|字节|最大值|GC 之前、GC 之后，旧代内存池大小正增加的计数|AppName,Pod|
|YoungGenPromotedBytes|提升为新代数据大小|字节|最大值|运行一个 GC 之后、运行下一个 GC 之前，新代内存池大小增加的递增量|AppName,Pod|
|GCPauseTotalCount|GC 暂停计数|计数|总计|GC 暂停计数|AppName,Pod|
|GCPauseTotalTime|GC 暂停总时间|毫秒|总计|GC 暂停总时间|AppName,Pod|
|TomcatSentBytes|Tomcat 发送的总字节数|字节|总计|Tomcat 发送的总字节数|AppName,Pod|
|TomcatReceivedBytes|Tomcat 收到的总字节数|字节|总计|Tomcat 收到的总字节数|AppName,Pod|
|TomcatRequestTotalTime|Tomcat 请求总时间|毫秒|总计|Tomcat 请求总时间|AppName,Pod|
|TomcatRequestTotalCount|Tomcat 请求总数|计数|总计|Tomcat 请求总数|AppName,Pod|
|TomcatResponseAvgTime|Tomcat 请求平均时间|毫秒|平均值|Tomcat 请求平均时间|AppName,Pod|
|TomcatRequestMaxTime|Tomcat 请求最大时间|毫秒|最大值|Tomcat 请求最大时间|AppName,Pod|
|TomcatErrorCount|Tomcat 全局错误|计数|总计|Tomcat 全局错误|AppName,Pod|
|TomcatSessionActiveMaxCount|Tomcat 会话最大活动计数|计数|总计|Tomcat 会话最大活动计数|AppName,Pod|
|TomcatSessionAliveMaxTime|Tomcat 会话最大活动时间|毫秒|最大值|Tomcat 会话最大活动时间|AppName,Pod|
|TomcatSessionActiveCurrentCount|Tomcat 会话活动计数|计数|总计|Tomcat 会话活动计数|AppName,Pod|
|TomcatSessionCreatedCount|Tomcat 创建的会话数|计数|总计|Tomcat 创建的会话数|AppName,Pod|
|TomcatSessionExpiredCount|Tomcat 过期的会话数|计数|总计|Tomcat 过期的会话数|AppName,Pod|
|TomcatSessionRejectedCount|Tomcat 拒绝的会话数|计数|总计|Tomcat 拒绝的会话数|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalJob|作业总数|计数|总计|作业总数|Runbook,Status|
|TotalUpdateDeploymentRuns|更新部署运行总数|计数|总计|软件更新部署运行总数|SoftwareUpdateConfigurationName,Status|
|TotalUpdateDeploymentMachineRuns|更新部署计算机运行总数|计数|总计|软件更新部署运行中的软件更新部署计算机运行总数|SoftwareUpdateConfigurationName,Status,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CoreCount|专用核心计数|计数|总计|批处理帐户中的专用核心总数|无|
|TotalNodeCount|专用节点计数|计数|总计|批处理帐户中的专用节点总数|无|
|LowPriorityCoreCount|低优先级核心计数|计数|总计|批处理帐户中的低优先级核心总数|无|
|TotalLowPriorityNodeCount|低优先级节点计数|计数|总计|批处理帐户中的低优先级节点总数|无|
|CreatingNodeCount|正在创建的节点计数|计数|总计|正在创建的节点数目|无|
|StartingNodeCount|正在启动的节点计数|计数|总计|正在启动的节点数目|无|
|WaitingForStartTaskNodeCount|正在等待启动任务的节点计数|计数|总计|正在等待启动任务完成的节点数目|无|
|StartTaskFailedNodeCount|启动任务失败的节点计数|计数|总计|启动任务失败的节点数目|无|
|IdleNodeCount|空闲节点计数|计数|总计|空闲节点数目|无|
|OfflineNodeCount|脱机节点计数|计数|总计|脱机节点数目|无|
|RebootingNodeCount|正在重新启动的节点计数|计数|总计|正在重新启动的节点数目|无|
|ReimagingNodeCount|正在重置映像的节点计数|计数|总计|正在重置映像的节点数目|无|
|RunningNodeCount|正在运行的节点计数|计数|总计|正在运行的节点数目|无|
|LeavingPoolNodeCount|正在退出池的节点计数|计数|总计|正在退出池的节点数目|无|
|UnusableNodeCount|不可用的节点计数|计数|总计|不可用的节点数目|无|
|PreemptedNodeCount|已占用节点计数|计数|总计|已占用节点数|无|
|TaskStartEvent|任务启动事件数|计数|总计|已启动的任务总数|poolId，jobId|
|TaskCompleteEvent|任务完成事件数|计数|总计|已完成的任务总数|poolId，jobId|
|TaskFailEvent|任务失败事件数|计数|总计|处于失败状态的已完成任务总数|poolId，jobId|
|PoolCreateEvent|池创建事件数|计数|总计|已创建的池总数|poolId|
|PoolResizeStartEvent|池调整大小启动事件数|计数|总计|已启动的池调整大小活动总数|poolId|
|PoolResizeCompleteEvent|池调整大小完成事件数|计数|总计|已完成的池调整大小活动总数|poolId|
|PoolDeleteStartEvent|池删除启动事件数|计数|总计|已启动的池删除活动总数|poolId|
|PoolDeleteCompleteEvent|池删除完成事件数|计数|总计|已完成的池删除活动总数|poolId|
|JobDeleteCompleteEvent|作业删除完成事件数|计数|总计|已成功删除的作业总数。|jobId|
|JobDeleteStartEvent|作业删除启动事件数|计数|总计|已请求删除的作业总数。|jobId|
|JobDisableCompleteEvent|作业禁用完成事件数|计数|总计|已成功禁用的作业总数。|jobId|
|JobDisableStartEvent|作业禁用启动事件数|计数|总计|已请求禁用的作业总数。|jobId|
|JobStartEvent|作业启动事件数|计数|总计|已成功启动的作业总数。|jobId|
|JobTerminateCompleteEvent|作业终止完成事件数|计数|总计|已成功终止的作业总数。|jobId|
|JobTerminateStartEvent|作业终止启动事件数|计数|总计|已请求终止的作业总数。|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|提交的作业|提交的作业|计数|总计|提交的作业数|Scenario,ClusterName|
|已完成的作业|已完成的作业|计数|总计|已完成的作业数|Scenario,ClusterName,ResultType|
|节点总数|节点总数|计数|平均值|节点总数|Scenario,ClusterName|
|活动节点数|活动节点数|计数|平均值|正在运行的节点数目|Scenario,ClusterName|
|空闲节点数|空闲节点数|计数|平均值|空闲节点数目|Scenario,ClusterName|
|不可用的节点数|不可用的节点数|计数|平均值|不可用的节点数目|Scenario,ClusterName|
|占用的节点数|占用的节点数|计数|平均值|已占用节点数|Scenario,ClusterName|
|正在退出的节点数|正在退出的节点数|计数|平均值|正在退出的节点数|Scenario,ClusterName|
|核心总数|核心总数|计数|平均值|核心总数|Scenario,ClusterName|
|活动核心数|活动核心数|计数|平均值|活动核心数|Scenario,ClusterName|
|空闲核心数|空闲核心数|计数|平均值|空闲核心数|Scenario,ClusterName|
|不可用的核心数|不可用的核心数|计数|平均值|不可用的核心数|Scenario,ClusterName|
|占用的核心数|占用的核心数|计数|平均值|占用的核心数|Scenario,ClusterName|
|正在退出的核心数|正在退出的核心数|计数|平均值|正在退出的核心数|Scenario,ClusterName|
|配额利用率百分比|配额利用率百分比|计数|平均值|已利用的配额百分比|Scenario,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU 使用率百分比|百分比|最大值|CPU 使用率百分比|节点|
|MemoryUsage|内存用量|字节|平均值|内存用量|节点|
|MemoryLimit|内存限制|字节|平均值|内存限制|节点|
|MemoryUsagePercentageInDouble|内存使用率百分比|百分比|平均值|内存使用率百分比|节点|
|StorageUsage|存储使用率|字节|平均值|存储使用率|节点|
|IOReadBytes|IO 读取字节数|字节|总计|IO 读取字节数|节点|
|IOWriteBytes|IO 写入字节数|字节|总计|IO 写入字节数|节点|
|ConnectionAccepted|已接受的连接数|计数|总计|已接受的连接数|节点|
|ConnectionHandled|已处理的连接数|计数|总计|已处理的连接数|节点|
|ConnectionActive|活动连接|计数|平均值|活动连接|节点|
|RequestHandled|已处理的请求数|计数|总计|已处理的请求数|节点|
|ProcessedBlocks|已处理的块数|计数|总计|已处理的块数|节点|
|ProcessedTransactions|已处理的事务数|计数|总计|已处理的事务数|节点|
|PendingTransactions|挂起的事务数|计数|平均值|挂起的事务数|节点|
|QueuedTransactions|已排队的事务数|计数|平均值|已排队的事务数|节点|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|connectedclients|连接的客户端数|计数|最大值||ShardId|
|totalcommandsprocessed|总操作数|计数|总计||ShardId|
|cachehits|缓存命中数|计数|总计||ShardId|
|cachemisses|缓存未命中数|计数|总计||ShardId|
|cachemissrate|缓存未命中率|百分比|cachemissrate||ShardId|
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
|cacheLatency|缓存延迟毫秒数（预览）|计数|平均值||ShardId|
|错误|错误|计数|最大值||ShardId,ErrorType|
|connectedclients0|连接的客户端数(分片 0)|计数|最大值||无|
|totalcommandsprocessed0|总操作数(分片 0)|计数|总计||无|
|cachehits0|缓存命中数(分片 0)|计数|总计||无|
|cachemisses0|缓存未命中数(分片 0)|计数|总计||无|
|getcommands0|Get 数(分片 0)|计数|总计||无|
|setcommands0|Set 数(分片 0)|计数|总计||无|
|operationsPerSecond0|每秒操作数（分片 0）|计数|最大值||无|
|evictedkeys0|逐出的密钥数(分片 0)|计数|总计||无|
|totalkeys0|总密钥数（分片 0）|计数|最大值||无|
|expiredkeys0|过期的密钥数(分片 0)|计数|总计||无|
|usedmemory0|已用内存量(分片 0)|字节|最大值||无|
|usedmemoryRss0|已用内存 RSS (分片 0)|字节|最大值||无|
|serverLoad0|服务器负载(分片 0)|百分比|最大值||无|
|cacheWrite0|缓存写入量(分片 0)|每秒字节数|最大值||无|
|cacheRead0|缓存读取量(分片 0)|每秒字节数|最大值||无|
|percentProcessorTime0|CPU (分片 0)|百分比|最大值||无|
|connectedclients1|连接的客户端数(分片 1)|计数|最大值||无|
|totalcommandsprocessed1|总操作数(分片 1)|计数|总计||无|
|cachehits1|缓存命中数(分片 1)|计数|总计||无|
|cachemisses1|缓存未命中数(分片 1)|计数|总计||无|
|getcommands1|Get 数(分片 1)|计数|总计||无|
|setcommands1|Set 数(分片 1)|计数|总计||无|
|operationsPerSecond1|每秒操作数（分片 1）|计数|最大值||无|
|evictedkeys1|逐出的密钥数(分片 1)|计数|总计||无|
|totalkeys1|总密钥数（分片 1）|计数|最大值||无|
|expiredkeys1|过期的密钥数(分片 1)|计数|总计||无|
|usedmemory1|已用内存量(分片 1)|字节|最大值||无|
|usedmemoryRss1|已用内存 RSS (分片 1)|字节|最大值||无|
|serverLoad1|服务器负载(分片 1)|百分比|最大值||无|
|cacheWrite1|缓存写入量(分片 1)|每秒字节数|最大值||无|
|cacheRead1|缓存读取量(分片 1)|每秒字节数|最大值||无|
|percentProcessorTime1|CPU (分片 1)|百分比|最大值||无|
|connectedclients2|连接的客户端数(分片 2)|计数|最大值||无|
|totalcommandsprocessed2|总操作数(分片 2)|计数|总计||无|
|cachehits2|缓存命中数(分片 2)|计数|总计||无|
|cachemisses2|缓存未命中数(分片 2)|计数|总计||无|
|getcommands2|Get 数(分片 2)|计数|总计||无|
|setcommands2|Set 数(分片 2)|计数|总计||无|
|operationsPerSecond2|每秒操作数（分片 2）|计数|最大值||无|
|evictedkeys2|逐出的密钥数(分片 2)|计数|总计||无|
|totalkeys2|总密钥数（分片 2）|计数|最大值||无|
|expiredkeys2|过期的密钥数(分片 2)|计数|总计||无|
|usedmemory2|已用内存量(分片 2)|字节|最大值||无|
|usedmemoryRss2|已用内存 RSS (分片 2)|字节|最大值||无|
|serverLoad2|服务器负载(分片 2)|百分比|最大值||无|
|cacheWrite2|缓存写入量(分片 2)|每秒字节数|最大值||无|
|cacheRead2|缓存读取量(分片 2)|每秒字节数|最大值||无|
|percentProcessorTime2|CPU (分片 2)|百分比|最大值||无|
|connectedclients3|连接的客户端数(分片 3)|计数|最大值||无|
|totalcommandsprocessed3|总操作数(分片 3)|计数|总计||无|
|cachehits3|缓存命中数(分片 3)|计数|总计||无|
|cachemisses3|缓存未命中数(分片 3)|计数|总计||无|
|getcommands3|Get 数(分片 3)|计数|总计||无|
|setcommands3|Set 数(分片 3)|计数|总计||无|
|operationsPerSecond3|每秒操作数（分片 3）|计数|最大值||无|
|evictedkeys3|逐出的密钥数(分片 3)|计数|总计||无|
|totalkeys3|总密钥数（分片 3）|计数|最大值||无|
|expiredkeys3|过期的密钥数(分片 3)|计数|总计||无|
|usedmemory3|已用内存量(分片 3)|字节|最大值||无|
|usedmemoryRss3|已用内存 RSS (分片 3)|字节|最大值||无|
|serverLoad3|服务器负载(分片 3)|百分比|最大值||无|
|cacheWrite3|缓存写入量(分片 3)|每秒字节数|最大值||无|
|cacheRead3|缓存读取量(分片 3)|每秒字节数|最大值||无|
|percentProcessorTime3|CPU (分片 3)|百分比|最大值||无|
|connectedclients4|连接的客户端数(分片 4)|计数|最大值||无|
|totalcommandsprocessed4|总操作数(分片 4)|计数|总计||无|
|cachehits4|缓存命中数(分片 4)|计数|总计||无|
|cachemisses4|缓存未命中数(分片 4)|计数|总计||无|
|getcommands4|Get 数(分片 4)|计数|总计||无|
|setcommands4|Set 数(分片 4)|计数|总计||无|
|operationsPerSecond4|每秒操作数（分片 4）|计数|最大值||无|
|evictedkeys4|逐出的密钥数(分片 4)|计数|总计||无|
|totalkeys4|总密钥数（分片 4）|计数|最大值||无|
|expiredkeys4|过期的密钥数(分片 4)|计数|总计||无|
|usedmemory4|已用内存量(分片 4)|字节|最大值||无|
|usedmemoryRss4|已用内存 RSS (分片 4)|字节|最大值||无|
|serverLoad4|服务器负载(分片 4)|百分比|最大值||无|
|cacheWrite4|缓存写入量(分片 4)|每秒字节数|最大值||无|
|cacheRead4|缓存读取量(分片 4)|每秒字节数|最大值||无|
|percentProcessorTime4|CPU (分片 4)|百分比|最大值||无|
|connectedclients5|连接的客户端数(分片 5)|计数|最大值||无|
|totalcommandsprocessed5|总操作数(分片 5)|计数|总计||无|
|cachehits5|缓存命中数(分片 5)|计数|总计||无|
|cachemisses5|缓存未命中数(分片 5)|计数|总计||无|
|getcommands5|Get 数(分片 5)|计数|总计||无|
|setcommands5|Set 数(分片 5)|计数|总计||无|
|operationsPerSecond5|每秒操作数（分片 5）|计数|最大值||无|
|evictedkeys5|逐出的密钥数(分片 5)|计数|总计||无|
|totalkeys5|总密钥数（分片 5）|计数|最大值||无|
|expiredkeys5|过期的密钥数(分片 5)|计数|总计||无|
|usedmemory5|已用内存量(分片 5)|字节|最大值||无|
|usedmemoryRss5|已用内存 RSS (分片 5)|字节|最大值||无|
|serverLoad5|服务器负载(分片 5)|百分比|最大值||无|
|cacheWrite5|缓存写入量(分片 5)|每秒字节数|最大值||无|
|cacheRead5|缓存读取量(分片 5)|每秒字节数|最大值||无|
|percentProcessorTime5|CPU (分片 5)|百分比|最大值||无|
|connectedclients6|连接的客户端数(分片 6)|计数|最大值||无|
|totalcommandsprocessed6|总操作数(分片 6)|计数|总计||无|
|cachehits6|缓存命中数(分片 6)|计数|总计||无|
|cachemisses6|缓存未命中数(分片 6)|计数|总计||无|
|getcommands6|Get 数(分片 6)|计数|总计||无|
|setcommands6|Set 数(分片 6)|计数|总计||无|
|operationsPerSecond6|每秒操作数（分片 6）|计数|最大值||无|
|evictedkeys6|逐出的密钥数(分片 6)|计数|总计||无|
|totalkeys6|总密钥数（分片 6）|计数|最大值||无|
|expiredkeys6|过期的密钥数(分片 6)|计数|总计||无|
|usedmemory6|已用内存量(分片 6)|字节|最大值||无|
|usedmemoryRss6|已用内存 RSS (分片 6)|字节|最大值||无|
|serverLoad6|服务器负载(分片 6)|百分比|最大值||无|
|cacheWrite6|缓存写入量(分片 6)|每秒字节数|最大值||无|
|cacheRead6|缓存读取量(分片 6)|每秒字节数|最大值||无|
|percentProcessorTime6|CPU (分片 6)|百分比|最大值||无|
|connectedclients7|连接的客户端数(分片 7)|计数|最大值||无|
|totalcommandsprocessed7|总操作数(分片 7)|计数|总计||无|
|cachehits7|缓存命中数(分片 7)|计数|总计||无|
|cachemisses7|缓存未命中数(分片 7)|计数|总计||无|
|getcommands7|Get 数(分片 7)|计数|总计||无|
|setcommands7|Set 数(分片 7)|计数|总计||无|
|operationsPerSecond7|每秒操作数（分片 7）|计数|最大值||无|
|evictedkeys7|逐出的密钥数(分片 7)|计数|总计||无|
|totalkeys7|总密钥数（分片 7）|计数|最大值||无|
|expiredkeys7|过期的密钥数(分片 7)|计数|总计||无|
|usedmemory7|已用内存量(分片 7)|字节|最大值||无|
|usedmemoryRss7|已用内存 RSS (分片 7)|字节|最大值||无|
|serverLoad7|服务器负载(分片 7)|百分比|最大值||无|
|cacheWrite7|缓存写入量(分片 7)|每秒字节数|最大值||无|
|cacheRead7|缓存读取量(分片 7)|每秒字节数|最大值||无|
|percentProcessorTime7|CPU (分片 7)|百分比|最大值||无|
|connectedclients8|连接的客户端数(分片 8)|计数|最大值||无|
|totalcommandsprocessed8|总操作数(分片 8)|计数|总计||无|
|cachehits8|缓存命中数(分片 8)|计数|总计||无|
|cachemisses8|缓存未命中数(分片 8)|计数|总计||无|
|getcommands8|Get 数(分片 8)|计数|总计||无|
|setcommands8|Set 数(分片 8)|计数|总计||无|
|operationsPerSecond8|每秒操作数（分片 8）|计数|最大值||无|
|evictedkeys8|逐出的密钥数(分片 8)|计数|总计||无|
|totalkeys8|总密钥数（分片 8）|计数|最大值||无|
|expiredkeys8|过期的密钥数(分片 8)|计数|总计||无|
|usedmemory8|已用内存量(分片 8)|字节|最大值||无|
|usedmemoryRss8|已用内存 RSS (分片 8)|字节|最大值||无|
|serverLoad8|服务器负载(分片 8)|百分比|最大值||无|
|cacheWrite8|缓存写入量(分片 8)|每秒字节数|最大值||无|
|cacheRead8|缓存读取量(分片 8)|每秒字节数|最大值||无|
|percentProcessorTime8|CPU (分片 8)|百分比|最大值||无|
|connectedclients9|连接的客户端数(分片 9)|计数|最大值||无|
|totalcommandsprocessed9|总操作数(分片 9)|计数|总计||无|
|cachehits9|缓存命中数(分片 9)|计数|总计||无|
|cachemisses9|缓存未命中数(分片 9)|计数|总计||无|
|getcommands9|Get 数(分片 9)|计数|总计||无|
|setcommands9|Set 数(分片 9)|计数|总计||无|
|operationsPerSecond9|每秒操作数（分片 9）|计数|最大值||无|
|evictedkeys9|逐出的密钥数(分片 9)|计数|总计||无|
|totalkeys9|总密钥数（分片 9）|计数|最大值||无|
|expiredkeys9|过期的密钥数(分片 9)|计数|总计||无|
|usedmemory9|已用内存量(分片 9)|字节|最大值||无|
|usedmemoryRss9|已用内存 RSS (分片 9)|字节|最大值||无|
|serverLoad9|服务器负载(分片 9)|百分比|最大值||无|
|cacheWrite9|缓存写入量(分片 9)|每秒字节数|最大值||无|
|cacheRead9|缓存读取量(分片 9)|每秒字节数|最大值||无|
|percentProcessorTime9|CPU (分片 9)|百分比|最大值||无|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web 应用程序防火墙请求计数|计数|总计|Web 应用程序防火墙所处理的客户端请求数|PolicyName,RuleName,Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|无|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|无|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|无|
|Disk Read Bytes/Sec|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|无|
|Disk Write Bytes/Sec|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|无|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|无|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|无|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比。|RoleInstanceId|
|网络传入|网络传入|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）。|RoleInstanceId|
|网络传出|网络传出|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）。|RoleInstanceId|
|Disk Read Bytes/Sec|磁盘读取|每秒字节数|平均值|监视期间从磁盘读取的平均字节数。|RoleInstanceId|
|Disk Write Bytes/Sec|磁盘写入|每秒字节数|平均值|监视期间向磁盘写入的平均字节数。|RoleInstanceId|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS。|RoleInstanceId|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS。|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|UsedCapacity|已用容量|字节|平均值|帐户使用的容量|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|字节|平均值|存储帐户的 Blob 服务使用的存储量（以字节为单位）。|BlobType,Tier|
|BlobCount|Blob 计数|计数|平均值|存储帐户的 Blob 服务中的 Blob 数。|BlobType,Tier|
|ContainerCount|Blob 容器计数|计数|平均值|存储帐户的 Blob 服务中的容器数。|无|
|IndexCapacity|索引容量|字节|平均值|ADLS Gen2 (层次结构)索引所使用的存储量(按字节计)。|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TableCapacity|表容量|字节|平均值|存储帐户的表服务使用的存储量（以字节为单位）。|无|
|TableCount|表计数|计数|平均值|存储帐户的表服务中的表数。|无|
|TableEntityCount|表实体计数|计数|平均值|存储帐户的表服务中的表实体数。|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|FileCapacity|文件容量|字节|平均值|存储帐户的文件服务使用的存储量（以字节为单位）。|FileShare|
|FileCount|文件计数|计数|平均值|存储帐户的文件服务中的文件数。|FileShare|
|FileShareCount|文件共享计数|计数|平均值|存储帐户的文件服务中的文件共享数。|无|
|FileShareSnapshotCount|文件共享快照计数|计数|平均值|存储帐户的文件服务中的共享上存在的快照数。|FileShare|
|FileShareSnapshotSize|文件共享快照大小|字节|平均值|存储帐户的文件服务中的快照使用的存储量（以字节为单位）。|FileShare|
|FileShareQuota|文件共享配额大小|字节|平均值|Azure 文件服务可使用的存储量上限（以字节为单位）。|FileShare|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication,FileShare|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication,FileShare|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication,FileShare|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication,FileShare|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication,FileShare|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QueueCapacity|队列容量|字节|平均值|存储帐户的队列服务使用的存储量（以字节为单位）。|无|
|QueueCount|队列计数|计数|平均值|存储帐户的队列服务中的队列数。|无|
|QueueMessageCount|队列消息计数|计数|平均值|存储帐户的队列服务中的队列消息的大致数目。|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的延迟（以毫秒为单位）。 此值不包括 SuccessE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalCalls|总调用数|计数|总计|调用总数。|ApiName,OperationName,Region|
|SuccessfulCalls|成功调用数|计数|总计|成功调用数。|ApiName,OperationName,Region|
|TotalErrors|错误总数|计数|总计|引发错误响应（HTTP 响应代码 4xx 或 5xx）的调用总数。|ApiName,OperationName,Region|
|BlockedCalls|阻止的调用数|计数|总计|超过速率或配额限制的调用数。|ApiName,OperationName,Region|
|ServerErrors|服务器错误数|计数|总计|引发服务内部错误（HTTP 响应代码 5xx）的调用数。|ApiName,OperationName,Region|
|ClientErrors|客户端错误数|计数|总计|引发客户端错误（HTTP 响应代码 4xx）的调用数。|ApiName,OperationName,Region|
|DataIn|数据输入|字节|总计|传入数据的大小（字节）。|ApiName,OperationName,Region|
|DataOut|数据输出|字节|总计|传出数据的大小（字节）。|ApiName,OperationName,Region|
|延迟|延迟|毫秒|平均值|延迟（毫秒）。|ApiName,OperationName,Region|
|TotalTokenCalls|令牌调用总数|计数|总计|令牌调用的总数。|ApiName,OperationName,Region|
|CharactersTranslated|转换的字符|计数|总计|传入的文本请求中的字符总数。|ApiName,OperationName,Region|
|CharactersTrained|训练的字符数|计数|总计|训练的字符总数。|ApiName,OperationName,Region|
|SpeechSessionDuration|语音会话持续时间|秒|总计|语音会话的总持续时间（以秒计）。|ApiName,OperationName,Region|
|TotalTransactions|总事务|计数|总计|事务总数。|无|
|ProcessedImages|已处理图像|计数|总计| 用于处理图像的事务数。|ApiName、功能区、频道、区域|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无|
|网络传入|可计费网络（已弃用）|字节|总计|虚拟机在所有网络接口上收到的可计费字节数（传入流量）（已弃用）|无|
|网络传出|可计费网络流出量（已弃用）|字节|总计|通过虚拟机传出的所有网络接口上的可计费字节数（传出流量）（已弃用）|无|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|无|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节数|无|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无|
|每磁盘读取字节数/秒|数据磁盘读取字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|SlotId|
|每磁盘写入字节数/秒|数据磁盘写入字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|SlotId|
|每磁盘读取操作数/秒|数据磁盘读取操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|SlotId|
|每磁盘写入操作数/秒|数据磁盘写入操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|SlotId|
|每磁盘 QD|Data Disk QD [（已弃用）](portal-disk-metrics-deprecation.md)] （portal-disk-metrics-deprecation.md）|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|OS 每磁盘读取字节数/秒|OS 磁盘读取字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无|
|OS 每磁盘写入字节数/秒|OS 磁盘写入字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无|
|OS 每磁盘读取操作数/秒|OS 磁盘读取操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无|
|OS 每磁盘写入操作数/秒|OS 磁盘写入操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无|
|OS 每磁盘 QD|OS Disk QD [（已弃用）](portal-disk-metrics-deprecation.md)|计数|平均值|OS 磁盘队列深度(或队列长度)|无|
|数据磁盘读取字节数/秒|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|LUN|
|数据磁盘写入字节数/秒|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|LUN|
|数据磁盘读取操作数/秒|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|LUN|
|数据磁盘写入操作数/秒|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|LUN|
|数据磁盘队列深度|数据磁盘队列深度（预览版）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN|
|OS 磁盘读取字节数/秒|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无|
|OS 磁盘写入字节数/秒|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无|
|OS 磁盘读取操作次数/秒|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无|
|OS 磁盘写入操作次数/秒|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无|
|OS 磁盘队列深度|OS 磁盘队列深度（预览版）|计数|平均值|OS 磁盘队列深度(或队列长度)|无|
|入站流数|入站流数|计数|平均值|入站流数是入站方向的当前流（传入 VM 的流量）的数目|无|
|出站流数|出站流数|计数|平均值|出站流数是出站方向的当前流（传出 VM 的流量）的数目|无|
|入站流最大创建速率|入站流最大创建速率|每秒计数|平均值|入站流（传入 VM 的流量）的最大创建速率|无|
|出站流最大创建速率|出站流最大创建速率|每秒计数|平均值|出站流（传出 VM 的流量）的最大创建速率|无|
|高级数据磁盘缓存读取命中|高级数据磁盘缓存读取命中（预览版）|百分比|平均值|高级数据磁盘缓存读取命中|LUN|
|高级数据磁盘缓存读取未命中|高级数据磁盘缓存读取未命中（预览版）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN|
|高级 OS 磁盘缓存读取命中|高级 OS 磁盘缓存读取命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取命中|无|
|高级 OS 磁盘缓存读取未命中|高级 OS 磁盘缓存读取未命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取未命中|无|
|网络传入流量总计|网络传入流量总计|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无|
|网络传出流量总计|网络传出流量总计|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|VMName|
|网络传入|可计费网络（已弃用）|字节|总计|虚拟机在所有网络接口上收到的可计费字节数（传入流量）（已弃用）|VMName|
|网络传出|可计费网络流出量（已弃用）|字节|总计|通过虚拟机传出的所有网络接口上的可计费字节数（传出流量）（已弃用）|VMName|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|VMName|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节数|VMName|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|VMName|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|VMName|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无|
|每磁盘读取字节数/秒|数据磁盘读取字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|SlotId|
|每磁盘写入字节数/秒|数据磁盘写入字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|SlotId|
|每磁盘读取操作数/秒|数据磁盘读取操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|SlotId|
|每磁盘写入操作数/秒|数据磁盘写入操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|SlotId|
|每磁盘 QD|数据磁盘 QD [（已弃用）](portal-disk-metrics-deprecation.md)|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|OS 每磁盘读取字节数/秒|OS 磁盘读取字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无|
|OS 每磁盘写入字节数/秒|OS 磁盘写入字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无|
|OS 每磁盘读取操作数/秒|OS 磁盘读取操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无|
|OS 每磁盘写入操作数/秒|OS 磁盘写入操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无|
|OS 每磁盘 QD|OS Disk QD [（已弃用）](portal-disk-metrics-deprecation.md)|计数|平均值|OS 磁盘队列深度(或队列长度)|无|
|数据磁盘读取字节数/秒|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|LUN,VMName|
|数据磁盘写入字节数/秒|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|LUN,VMName|
|数据磁盘读取操作数/秒|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|LUN,VMName|
|数据磁盘写入操作数/秒|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|LUN,VMName|
|数据磁盘队列深度|数据磁盘队列深度（预览版）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN,VMName|
|OS 磁盘读取字节数/秒|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|VMName|
|OS 磁盘写入字节数/秒|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|VMName|
|OS 磁盘读取操作次数/秒|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|VMName|
|OS 磁盘写入操作次数/秒|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|VMName|
|OS 磁盘队列深度|OS 磁盘队列深度（预览版）|计数|平均值|OS 磁盘队列深度(或队列长度)|VMName|
|入站流数|入站流数|计数|平均值|入站流数是入站方向的当前流（传入 VM 的流量）的数目|VMName|
|出站流数|出站流数|计数|平均值|出站流数是出站方向的当前流（传出 VM 的流量）的数目|VMName|
|入站流最大创建速率|入站流最大创建速率|每秒计数|平均值|入站流（传入 VM 的流量）的最大创建速率|VMName|
|出站流最大创建速率|出站流最大创建速率|每秒计数|平均值|出站流（传出 VM 的流量）的最大创建速率|VMName|
|高级数据磁盘缓存读取命中|高级数据磁盘缓存读取命中（预览版）|百分比|平均值|高级数据磁盘缓存读取命中|LUN,VMName|
|高级数据磁盘缓存读取未命中|高级数据磁盘缓存读取未命中（预览版）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN,VMName|
|高级 OS 磁盘缓存读取命中|高级 OS 磁盘缓存读取命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取命中|VMName|
|高级 OS 磁盘缓存读取未命中|高级 OS 磁盘缓存读取未命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取未命中|VMName|
|网络传入流量总计|网络传入流量总计|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|VMName|
|网络传出流量总计|网络传出流量总计|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CPU 百分比|CPU 百分比|百分比|平均值|当前虚拟机正在使用的已分配计算单元百分比|无|
|网络传入|可计费网络（已弃用）|字节|总计|虚拟机在所有网络接口上收到的可计费字节数（传入流量）（已弃用）|无|
|网络传出|可计费网络流出量（已弃用）|字节|总计|通过虚拟机传出的所有网络接口上的可计费字节数（传出流量）（已弃用）|无|
|磁盘读取字节数|磁盘读取字节数|字节|总计|监视期间从磁盘读取的字节数|无|
|磁盘写入字节数|磁盘写入字节数|字节|总计|监视期间向磁盘写入的字节数|无|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|磁盘读取 IOPS|无|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|磁盘写入 IOPS|无|
|剩余 CPU 信用额度|剩余 CPU 信用额度|计数|平均值|可用来集中使用的总信用点数|无|
|已用 CPU 信用额度|已用 CPU 信用额度|计数|平均值|虚拟机使用的总信用点数|无|
|每磁盘读取字节数/秒|数据磁盘读取字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|SlotId|
|每磁盘写入字节数/秒|数据磁盘写入字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|SlotId|
|每磁盘读取操作数/秒|数据磁盘读取操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|SlotId|
|每磁盘写入操作数/秒|数据磁盘写入操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|SlotId|
|每磁盘 QD|数据磁盘 QD [（已弃用）](portal-disk-metrics-deprecation.md)|计数|平均值|数据磁盘队列深度(或队列长度)|SlotId|
|OS 每磁盘读取字节数/秒|OS 磁盘读取字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无|
|OS 每磁盘写入字节数/秒|OS 磁盘写入字节数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无|
|OS 每磁盘读取操作数/秒|OS 磁盘读取操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无|
|OS 每磁盘写入操作数/秒|OS 磁盘写入操作数/秒[（不推荐使用）](portal-disk-metrics-deprecation.md)|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无|
|OS 每磁盘 QD|OS Disk QD [（已弃用）](portal-disk-metrics-deprecation.md)|计数|平均值|OS 磁盘队列深度(或队列长度)|无|
|数据磁盘读取字节数/秒|数据磁盘读取字节数/秒（预览版）|每秒计数|平均值|监视期间每秒从单个磁盘读取的字节数|LUN|
|数据磁盘写入字节数/秒|数据磁盘写入字节数/秒（预览版）|每秒计数|平均值|监视期间每秒写入到单个磁盘的字节数|LUN|
|数据磁盘读取操作数/秒|数据磁盘读取操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的读取 IOPS|LUN|
|数据磁盘写入操作数/秒|数据磁盘写入操作数/秒（预览版）|每秒计数|平均值|监视期间从单个磁盘执行的写入 IOPS|LUN|
|数据磁盘队列深度|数据磁盘队列深度（预览版）|计数|平均值|数据磁盘队列深度(或队列长度)|LUN|
|OS 磁盘读取字节数/秒|OS 磁盘读取字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒从单个磁盘读取的字节数|无|
|OS 磁盘写入字节数/秒|OS 磁盘写入字节数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间每秒写入到单个磁盘的字节数|无|
|OS 磁盘读取操作次数/秒|OS 磁盘读取操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的读取 IOPS|无|
|OS 磁盘写入操作次数/秒|OS 磁盘写入操作数/秒（预览版）|每秒计数|平均值|OS 磁盘监视期间从单个磁盘执行的写入 IOPS|无|
|OS 磁盘队列深度|OS 磁盘队列深度（预览版）|计数|平均值|OS 磁盘队列深度(或队列长度)|无|
|入站流数|入站流数|计数|平均值|入站流数是入站方向的当前流（传入 VM 的流量）的数目|无|
|出站流数|出站流数|计数|平均值|出站流数是出站方向的当前流（传出 VM 的流量）的数目|无|
|入站流最大创建速率|入站流最大创建速率|每秒计数|平均值|入站流（传入 VM 的流量）的最大创建速率|无|
|出站流最大创建速率|出站流最大创建速率|每秒计数|平均值|出站流（传出 VM 的流量）的最大创建速率|无|
|高级数据磁盘缓存读取命中|高级数据磁盘缓存读取命中（预览版）|百分比|平均值|高级数据磁盘缓存读取命中|LUN|
|高级数据磁盘缓存读取未命中|高级数据磁盘缓存读取未命中（预览版）|百分比|平均值|高级数据磁盘缓存读取未命中|LUN|
|高级 OS 磁盘缓存读取命中|高级 OS 磁盘缓存读取命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取命中|无|
|高级 OS 磁盘缓存读取未命中|高级 OS 磁盘缓存读取未命中（预览版）|百分比|平均值|高级 OS 磁盘缓存读取未命中|无|
|网络传入流量总计|网络传入流量总计|字节|总计|虚拟机在所有网络接口上收到的字节数（传入流量）|无|
|网络传出流量总计|网络传出流量总计|字节|总计|虚拟机在所有网络接口上发出的字节数（传出流量）|无|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|计数|平均值|所有核心的 CPU 使用率（以 millicore 为单位）。|containerName|
|MemoryUsage|内存用量|字节|平均值|总内存使用量（以字节为单位）。|containerName|
|NetworkBytesReceivedPerSecond|每秒接收到的网络字节数|字节|平均值|每秒接收到的网络字节数。|无|
|NetworkBytesTransmittedPerSecond|每秒传输的网络字节数|字节|平均值|每秒传输的网络字节数。|无|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalPullCount|提取总数|计数|平均值|映像提取总次数|无|
|SuccessfulPullCount|成功提取计数|计数|平均值|成功的映像提取次数|无|
|TotalPushCount|推送总数|计数|平均值|映像推送总次数|无|
|SuccessfulPushCount|成功推送计数|计数|平均值|成功的映像推送次数|无|
|RunDuration|运行持续时间|毫秒|总计|运行持续时间，以毫秒为单位|无|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|托管群集中可用 CPU 内核的总数|计数|平均值|托管群集中可用 CPU 内核的总数|无|
|kube_node_status_allocatable_memory_bytes|托管群集中可用内存的总量|字节|平均值|托管群集中可用内存的总量|无|
|kube_pod_status_ready|就绪状态下的 Pod 数|计数|平均值|就绪状态下的 Pod 数|namespace,pod|
|kube_node_status_condition|各种节点条件的状态|计数|平均值|各种节点条件的状态|condition,status,status2,node|
|kube_pod_status_phase|依据阶段的 Pod 数|计数|平均值|依据阶段的 Pod 数|phase,namespace,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SuccessfullRequests|成功的请求数|计数|总计|自定义提供程序发出的成功请求|HttpMethod,CallPath,StatusCode|
|FailedRequests|失败的请求数|计数|总计|获取自定义资源提供程序的可用日志|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|NICReadThroughput|读取吞吐量（网络）|每秒字节数|平均值|报告期间，针对网关中的所有卷，设备网络接口的读取吞吐量。|InstanceName|
|NICWriteThroughput|写入吞吐量（网络）|每秒字节数|平均值|报告期间，针对网关中的所有卷，设备网络接口的写入吞吐量。|InstanceName|
|CloudReadThroughputPerShare|云下载吞吐量（共享）|每秒字节数|平均值|报告期间从共享到 Azure 的下载吞吐量。|共享|
|CloudUploadThroughputPerShare|云上传吞吐量（共享）|每秒字节数|平均值|报告期间从共享到 Azure 的上传吞吐量。|共享|
|BytesUploadedToCloudPerShare|已上传的云字节数（共享）|字节|平均值|报告期间从共享上传到 Azure 的总字节数。|共享|
|TotalCapacity|总容量|字节|平均值|总容量|无|
|AvailableCapacity|可用容量|字节|平均值|报告期间可用容量(以字节为单位)。|无|
|CloudUploadThroughput|云上传吞吐量|每秒字节数|平均值|报告期间到 Azure 的云上传吞吐量。|无|
|CloudReadThroughput|云下载吞吐量|每秒字节数|平均值|报告期间到 Azure 的云下载吞吐量。|无|
|BytesUploadedToCloud|已上传的云字节数（设备）|字节|平均值|报告期间从设备上传到 Azure 的总字节数。|无|
|HyperVVirtualProcessorUtilization|Edge 计算 - CPU 百分比|百分比|平均值|CPU 使用量的百分比|InstanceName|
|HyperVMemoryUtilization|Edge 计算 - 内存使用|百分比|平均值|正在使用的 RAM 量|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AssetDistributionByClassification|按分类分类的资产分布|计数|总计|指示已分配特定分类的资产的数量，即，使用该标签进行分类。|分类，源|
|AssetDistributionByStorageType|按存储类型的资产分布|计数|总计|指示具有特定存储类型的资产的数量。|StorageType|
|NumberOfAssetsWithClassifications|至少具有一个分类的资产数量|计数|平均值|指示至少具有一个标记分类的资产的数量。|无|
|ScanCancelled|扫描已取消|计数|总计|指示取消的扫描数。|无|
|ScanCompleted|扫描已完成|计数|总计|指示已成功完成的扫描数。|无|
|ScanFailed|扫描失败|计数|总计|指示扫描失败的次数。|无|
|ScanTimeTaken|扫描花费的时间|秒|总计|指示扫描总时间（以秒为单位）。|无|
|CatalogActiveUsers|每日活动用户|计数|总计|每日活动用户数|无|
|CatalogUsage|按操作的用量分布|计数|总计|指示用户对目录所做的操作的数量，即访问、搜索、词汇表。|操作|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|FailedRuns|失败的运行次数|计数|总计||pipelineName,activityName|
|SuccessfulRuns|成功的运行次数|计数|总计||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PipelineFailedRuns|失败的管道运行数指标|计数|总计||FailureType,Name|
|PipelineSucceededRuns|成功的管道运行数指标|计数|总计||FailureType,Name|
|PipelineCancelledRuns|已取消的管道运行指标数|计数|总计||FailureType,Name|
|ActivityFailedRuns|失败的活动运行数指标|计数|总计||ActivityType,PipelineName,FailureType,Name|
|ActivitySucceededRuns|成功的活动运行数指标|计数|总计||ActivityType,PipelineName,FailureType,Name|
|ActivityCancelledRuns|已取消的活动运行指标数|计数|总计||ActivityType,PipelineName,FailureType,Name|
|TriggerFailedRuns|失败的触发器运行数指标|计数|总计||Name,FailureType|
|TriggerSucceededRuns|成功的触发器运行数指标|计数|总计||Name,FailureType|
|TriggerCancelledRuns|已取消的触发器运行指标数|计数|总计||Name,FailureType|
|IntegrationRuntimeCpuPercentage|集成运行时 CPU 利用率|百分比|平均值||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAvailableMemory|集成运行时可用内存|字节|平均值||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|集成运行时队列持续时间|秒|平均值||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|集成运行时队列长度|计数|平均值||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|集成运行时可用节点计数|计数|平均值||IntegrationRuntimeName|
|MaxAllowedResourceCount|允许的最大实体计数|计数|最大值||无|
|MaxAllowedFactorySizeInGbUnits|允许的最大工厂大小（以 GB 为单位）|计数|最大值||无|
|ResourceCount|实体总数|计数|最大值||无|
|FactorySizeInGbUnits|总工厂大小（以 GB 为单位）|计数|最大值||无|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|JobEndedSuccess|成功作业数|计数|总计|成功作业计数。|无|
|JobEndedFailure|失败作业数|计数|总计|失败作业计数。|无|
|JobEndedCancelled|取消的作业数|计数|总计|取消的作业计数。|无|
|JobAUEndedSuccess|成功 AU 时间|秒|总计|成功作业的总 AU 时间。|无|
|JobAUEndedFailure|失败的 AU 时间|秒|总计|失败作业的总 AU 时间。|无|
|JobAUEndedCancelled|已取消的 AU 时间|秒|总计|取消的作业的总 AU 时间。|无|
|JobStage|阶段中的作业|计数|总计|每个阶段中的作业数。|无|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TotalStorage|总存储|字节|最大值|帐户中存储的数据总量。|无|
|DataWritten|写入的数据量|字节|总计|写入帐户的数据总量。|无|
|DataRead|读取的数据量|字节|总计|从帐户中读取的数据总量。|无|
|WriteRequests|写入请求数|计数|总计|帐户的数据写入请求计数。|无|
|ReadRequests|读取请求数|计数|总计|帐户的数据读取请求计数。|无|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ShareCount|已发送共享|计数|最大值|帐户中已发送共享的数目|ShareName|
|ShareSubscriptionCount|已接收共享|计数|最大值|帐户中接收的共享数|ShareSubscriptionName|
|SucceededShareSynchronizations|已发送共享成功快照|计数|计数|帐户中已发送共享成功快照数|无|
|FailedShareSynchronizations|已发送共享失败快照|计数|计数|帐户中已发送共享失败快照数|无|
|SucceededShareSubscriptionSynchronizations|已收到共享成功快照|计数|计数|帐户中已收到共享成功快照数|无|
|FailedShareSubscriptionSynchronizations|已接收共享失败快照|计数|计数|帐户中收到的共享失败快照数|无|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无|
|storage_limit|存储限制|字节|最大值|存储限制|无|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无|
|serverlog_storage_usage|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无|
|serverlog_storage_limit|服务器存储空间上限|字节|平均值|服务器存储空间上限|无|
|active_connections|活动连接|计数|平均值|活动连接|无|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无|
|seconds_behind_master|复制延迟（秒）|计数|最大值|复制延迟（秒）|无|
|backup_storage_used|已用的备份存储|字节|平均值|已用的备份存储|无|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无|
|storage_limit|存储限制|字节|最大值|存储限制|无|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无|
|serverlog_storage_usage|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无|
|serverlog_storage_limit|服务器存储空间上限|字节|最大值|服务器存储空间上限|无|
|active_connections|活动连接|计数|平均值|活动连接|无|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无|
|seconds_behind_master|复制延迟（秒）|计数|最大值|复制延迟（秒）|无|
|backup_storage_used|已用的备份存储|字节|平均值|已用的备份存储|无|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无|
|io_consumption_percent|IO 百分比|百分比|平均值|IO 百分比|无|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无|
|storage_limit|存储限制|字节|最大值|存储限制|无|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|平均值|服务器日志存储空间百分比|无|
|serverlog_storage_usage|服务器日志已用的存储量|字节|平均值|服务器日志已用的存储量|无|
|serverlog_storage_limit|服务器存储空间上限|字节|最大值|服务器存储空间上限|无|
|active_connections|活动连接|计数|平均值|活动连接|无|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无|
|backup_storage_used|已用的备份存储|字节|平均值|已用的备份存储|无|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无|
|pg_replica_log_delay_in_seconds|副本滞后时间|秒|最大值|复制延迟（秒）|无|
|pg_replica_log_delay_in_bytes|副本的最大滞后时间|字节|最大值|滞后时间最长的副本的滞后量（字节）|无|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无|
|iops|IOPS|计数|平均值|每秒 IO 操作次数|无|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无|
|active_connections|活动连接|计数|平均值|活动连接|无|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无|


## <a name="microsoftdbforpostgresqlsingleservers"></a>DBforPostgreSQL/singleservers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|memory_percent|内存百分比|百分比|平均值|内存百分比|无|
|iops|IOPS|计数|平均值|每秒 IO 操作次数|无|
|storage_percent|存储空间百分比|百分比|平均值|存储空间百分比|无|
|storage_used|已用的存储量|字节|平均值|已用的存储量|无|
|active_connections|活动连接|计数|平均值|活动连接|无|
|network_bytes_egress|网络传出|字节|总计|跨活动连接数的网络传出|无|
|network_bytes_ingress|网络传入|字节|总计|跨活动连接数的网络传入|无|
|connections_failed|失败的连接数|计数|总计|失败的连接数|无|
|connections_succeeded|成功的连接|计数|总计|成功的连接|无|
|maximum_used_transactionIDs|最大已用事务 Id|计数|平均值|最大已用事务 Id|无|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遥测消息发送尝试次数|计数|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无|
|d2c.telemetry.ingress.success|已发送的遥测消息数|计数|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无|
|c2d.commands.egress.complete.success|已完成的 C2D 消息传递次数|计数|总计|设备成功完成的云到设备消息传递次数|无|
|c2d.commands.egress.abandon.success|丢弃的 C2D 消息数|计数|总计|设备丢弃的云到设备消息数|无|
|c2d.commands.egress.reject.success|拒绝的 C2D 消息数|计数|总计|设备拒绝的云到设备消息数|无|
|C2DMessagesExpired|过期的 C2D 消息数（预览版）|计数|总计|过期的云到设备消息数|无|
|devices.totalDevices|（已弃用）设备总数|计数|总计|已注册到 IoT 中心的设备数目|无|
|devices.connectedDevices.allProtocol|（已弃用）连接设备数 |计数|总计|已连接到 IoT 中心的设备数目|无|
|d2c.telemetry.egress.success|路由：已发送的遥测消息数|计数|总计|已使用 IoT 中心路由将消息成功发送到所有终结点的次数。 如果消息路由到多个终结点，每次成功发送后，此值将逐一增加。 如果消息多次路由到同一终结点，每次成功发送后，此值将逐一增加。|无|
|d2c.telemetry.egress.dropped|路由：已删除的遥测消息数 |计数|总计|由于死终结点，IoT 中心路由删除消息的次数。 此值不会计算已发送到回退路由的消息，因为已删除的消息未发送到该位置。|无|
|d2c.telemetry.egress.orphaned|路由：已孤立的遥测消息数 |计数|总计|由于消息不匹配任何传递规则（包括回退规则），IoT 中心路由孤立消息的次数。 |无|
|d2c.telemetry.egress.invalid|路由：不兼容的遥测消息数|计数|总计|由于与终结点不兼容，IoT 中心路由未能发送消息的次数。 此值不包括重试次数。|无|
|d2c.telemetry.egress.fallback|路由：发送到回退的消息数|计数|总计|IoT 中心路由将消息发送到与回退路由相关联的终结点的次数。|无|
|d2c.endpoints.egress.eventHubs|路由：已发送到事件中心的消息数|计数|总计|IoT 中心路由成功将消息发送到事件中心终结点的次数。|无|
|d2c.endpoints.latency.eventHubs|路由：事件中心的消息延迟|毫秒|平均值|消息进入 IoT 中心与消息进入事件中心终结点之间的平均延迟时间（毫秒）。|无|
|d2c.endpoints.egress.serviceBusQueues|路由：已发送到服务总线队列的消息数|计数|总计|IoT 中心路由成功将消息发送到服务总线队列终结点的次数。|无|
|d2c.endpoints.latency.serviceBusQueues|路由：服务总线队列的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线队列终结点之间的平均延迟时间（毫秒）。|无|
|d2c.endpoints.egress.serviceBusTopics|路由：发送到服务总线主题的消息数|计数|总计|IoT 中心路由将消息成功发送到服务总线主题终结点的次数。|无|
|d2c.endpoints.latency.serviceBusTopics|路由：服务总线主题的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线主题终结点之间的平均延迟时间（毫秒）。|无|
|d2c.endpoints.egress.builtIn.events|路由：发送到消息/事件的消息数|计数|总计|IoT 中心路由成功将消息发送到内置终结点（消息/事件）的次数。|无|
|d2c.endpoints.latency.builtIn.events|路由：消息/事件的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入内置终结点（消息/事件）之间的平均延迟时间（毫秒）。|无|
|d2c.endpoints.egress.storage|路由：发送到存储器的消息数|计数|总计|IoT 中心路由成功将消息发送到存储器终结点的次数。|无|
|d2c.endpoints.latency.storage|路由：存储器的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入存储器终结点之间的平均延迟时间（毫秒）。|无|
|d2c.endpoints.egress.storage.bytes|路由：发送到存储器的数据数|字节|总计|IoT 中心路由发送到存储器终结点的数据量（字节）。|无|
|d2c.endpoints.egress.storage.blobs|路由：发送到存储器的 blob 数|计数|总计|IoT 中心路由将 blob 发送到存储器终结点的次数。|无|
|EventGridDeliveries|事件网格传送（预览版）|计数|总计|发布到事件网格的 IoT 中心事件的数量。 使用 Result 维度表示成功和失败请求的数量。 EventType 维度显示事件的类型 (https://aka.ms/ioteventgrid)。|ResourceId,Result,EventType|
|EventGridLatency|事件网格延迟（预览）|毫秒|平均值|从生成 IoT 中心事件到将事件发布到事件网格的平均延迟（毫秒）。 此数值是所有事件类型的平均。 若要查看特定事件类型的延迟，请使用 EventType 维度。|ResourceId,EventType|
|RoutingDeliveries|路由传递（预览）|毫秒|总计|IoT 中心尝试使用路由将消息传送到所有终结点的次数。 若要查看成功或失败的尝试次数，请使用 "结果" 维度。 若要查看失败的原因，例如无效、已删除或孤立，请使用 FailureReasonCategory 维度。 你还可以使用 "终结点" 和 "EndpointType" 维度来了解传递到不同终结点的消息数。 对于每次传递尝试，指标值将增加1，包括如果消息传递到多个终结点，或者将消息多次传递到同一终结点，则为。|ResourceId、EndpointType、终结点 Id、FailureReasonCategory、Result、RoutingSource|
|RoutingDeliveryLatency|路由传送延迟（预览版）|毫秒|平均值|消息进入到 IoT 中心和遥测消息进入终结点之间的平均延迟（毫秒）。 您可以使用终结点和 EndpointType 维度来了解不同终结点的延迟。|ResourceId、EndpointType、终结点、RoutingSource|
|d2c.twin.read.success|设备的成功克隆读取数|计数|总计|由设备发起的所有成功的克隆读取的计数。|无|
|d2c.twin.read.failure|设备的失败克隆读取数|计数|总计|由设备发起的所有失败的克隆读取的计数。|无|
|d2c.twin.read.size|设备的克隆读取的响应大小|字节|平均值|由设备发起的所有成功的克隆读取的平均大小、最小大小和最大大小。|无|
|d2c.twin.update.success|设备的成功克隆更新数|计数|总计|由设备发起的所有成功的克隆更新的计数。|无|
|d2c.twin.update.failure|设备的失败克隆更新数|计数|总计|由设备发起的所有失败的克隆更新的计数。|无|
|d2c.twin.update.size|设备的克隆更新的大小|字节|平均值|由设备发起的所有成功的克隆更新的平均大小、最小大小和最大大小。|无|
|c2d.methods.success|成功的直接方法调用数|计数|总计|所有成功的直接方法调用的计数。|无|
|c2d.methods.failure|失败的直接方法调用数|计数|总计|所有失败的直接方法调用的计数。|无|
|c2d.methods.requestSize|直接方法调用的请求大小|字节|平均值|所有成功的直接方法请求的平均大小、最小大小和最大大小。|无|
|c2d.methods.responseSize|直接方法调用的响应大小|字节|平均值|所有成功的直接方法响应的平均大小、最小大小和最大大小。|无|
|c2d.twin.read.success|后端的成功克隆读取数|计数|总计|由后端发起的所有成功的克隆读取的计数。|无|
|c2d.twin.read.failure|后端的失败克隆读取数|计数|总计|由后端发起的所有失败的克隆读取的计数。|无|
|c2d.twin.read.size|后端的克隆读取的响应大小|字节|平均值|由后端发起的所有成功的克隆读取的平均大小、最小大小和最大大小。|无|
|c2d.twin.update.success|后端的成功克隆更新数|计数|总计|由后端发起的所有成功的克隆更新的计数。|无|
|c2d.twin.update.failure|后端的失败克隆更新数|计数|总计|由后端发起的所有失败的克隆更新的计数。|无|
|c2d.twin.update.size|后端的克隆更新的大小|字节|平均值|由后端发起的所有成功的克隆更新的平均大小、最小大小和最大大小。|无|
|twinQueries.success|成功的克隆查询|计数|总计|所有成功的克隆查询的计数。|无|
|twinQueries.failure|失败的克隆查询|计数|总计|所有失败的克隆查询的计数。|无|
|twinQueries.resultSize|克隆查询结果大小|字节|平均值|所有成功的克隆查询的结果大小的平均值、最小值和最大值。|无|
|jobs.createTwinUpdateJob.success|克隆更新作业创建成功数|计数|总计|克隆更新作业创建成功的所有次数。|无|
|jobs.createTwinUpdateJob.failure|克隆更新作业创建失败数|计数|总计|克隆更新作业创建失败的所有次数。|无|
|jobs.createDirectMethodJob.success|方法调用作业的创建成功数|计数|总计|直接方法调用作业创建成功的所有次数。|无|
|jobs.createDirectMethodJob.failure|方法调用作业的创建失败数|计数|总计|直接方法调用作业创建失败的所有次数。|无|
|jobs.listJobs.success|对列出作业的成功调用数|计数|总计|对列出作业的所有成功调用的计数。|无|
|jobs.listJobs.failure|对列出作业的失败调用数|计数|总计|对列出作业的所有失败调用的计数。|无|
|jobs.cancelJob.success|成功的作业取消数|计数|总计|用来取消作业的调用成功的次数。|无|
|jobs.cancelJob.failure|失败的作业取消数|计数|总计|用来取消作业的调用失败的次数。|无|
|jobs.queryJobs.success|成功的作业查询数|计数|总计|对查询作业的所有成功调用的计数。|无|
|jobs.queryJobs.failure|失败的作业查询数|计数|总计|对查询作业的所有失败调用的计数。|无|
|jobs.completed|已完成的作业|计数|总计|所有已完成的作业的计数。|无|
|jobs.failed|失败的作业数|计数|总计|所有失败的作业的计数。|无|
|d2c.telemetry.ingress.sendThrottle|限制错误数|计数|总计|由于设备吞吐量限制而导致的限制错误数|无|
|dailyMessageQuotaUsed|已使用的消息总数|计数|平均值|今天使用的消息总数|无|
|deviceDataUsage|设备数据用量总计|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|deviceDataUsageV2|设备数据使用总量（预览）|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|totalDeviceCount|设备总数（预览）|计数|平均值|已注册到 IoT 中心的设备数目|无|
|connectedDeviceCount|已连接设备数（预览）|计数|平均值|已连接到 IoT 中心的设备数目|无|
|配置|配置指标|计数|总计|配置操作的指标|无|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RegistrationAttempts|注册尝试次数|计数|总计|已尝试的设备注册次数|ProvisioningServiceName,IotHubName,Status|
|DeviceAssignments|已分配设备|计数|总计|已分配给 IoT 中心的设备数|ProvisioningServiceName,IotHubName|
|AttestationAttempts|证明尝试次数|计数|总计|已尝试的设备证明次数|ProvisioningServiceName,Status,Protocol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AddRegion|添加的区域数|计数|计数|添加的区域数|区域|
|AvailableStorage|可用存储|字节|总计|按 5 分钟粒度报告的可用存储总量|CollectionName,DatabaseName,Region|
|CassandraConnectionClosures|Cassandra 连接关闭数|计数|总计|已关闭的 Cassandra 连接数，按 1 分钟粒度报告|APIType,Region,ClosureReason|
|CassandraKeyspaceDelete|已删除 Cassandra 密钥空间|计数|计数|已删除 Cassandra 密钥空间|Context.resourcename、/Apikind/、ApiKindResourceType、OperationType|
|CassandraKeyspaceThroughputUpdate|已更新 Cassandra 密钥空间吞吐量|计数|计数|已更新 Cassandra 密钥空间吞吐量|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra 密钥空间更新|计数|计数|Cassandra 密钥空间更新|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|CassandraRequestCharges|Cassandra 请求费用|计数|总计|发出的 Cassandra 请求所消耗的 RU 数|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType|
|CassandraRequests|Cassandra 请求数|计数|计数|发出的 Cassandra 请求数|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType,ErrorCode|
|CassandraTableDelete|已删除 Cassandra 表|计数|计数|已删除 Cassandra 表|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、OperationType|
|CassandraTableThroughputUpdate|已更新 Cassandra 表吞吐量|计数|计数|已更新 Cassandra 表吞吐量|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|CassandraTableUpdate|已更新 Cassandra 表|计数|计数|已更新 Cassandra 表|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|CreateAccount|创建的帐户数|计数|计数|创建的帐户数|无|
|DataUsage|数据使用情况|字节|总计|按 5 分钟粒度报告的总数据用量|CollectionName,DatabaseName,Region|
|DeleteAccount|删除的帐户数|计数|计数|删除的帐户数|无|
|DocumentCount|文档计数|计数|总计|按 5 分钟粒度报告的文档总数|CollectionName,DatabaseName,Region|
|DocumentQuota|文档配额|字节|总计|按 5 分钟粒度报告的存储配额总量|CollectionName,DatabaseName,Region|
|GremlinDatabaseDelete|Gremlin 数据库已删除|计数|计数|Gremlin 数据库已删除|Context.resourcename、/Apikind/、ApiKindResourceType、OperationType|
|GremlinDatabaseThroughputUpdate|已更新 Gremlin 数据库吞吐量|计数|计数|已更新 Gremlin 数据库吞吐量|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|GremlinDatabaseUpdate|已更新 Gremlin 数据库|计数|计数|已更新 Gremlin 数据库|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|GremlinGraphDelete|已删除 Gremlin 关系图|计数|计数|已删除 Gremlin 关系图|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、OperationType|
|GremlinGraphThroughputUpdate|已更新 Gremlin Graph 吞吐量|计数|计数|已更新 Gremlin Graph 吞吐量|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|GremlinGraphUpdate|已更新 Gremlin 图|计数|计数|已更新 Gremlin 图|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|IndexUsage|索引使用情况|字节|总计|按 5 分钟粒度报告的总索引用量|CollectionName,DatabaseName,Region|
|MetadataRequests|元数据请求|计数|计数|元数据请求的计数。 Cosmos DB 为每个帐户维护系统元数据集合，允许你免费枚举集合、数据库及其配置等等。|DatabaseName,CollectionName,Region,StatusCode,Role|
|MongoCollectionDelete|Mongo 集合已删除|计数|计数|Mongo 集合已删除|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、OperationType|
|MongoCollectionThroughputUpdate|已更新 Mongo 集合吞吐量|计数|计数|已更新 Mongo 集合吞吐量|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|MongoCollectionUpdate|Mongo 集合已更新|计数|计数|Mongo 集合已更新|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|MongoDBDatabaseUpdate|已更新 Mongo 数据库|计数|计数|已更新 Mongo 数据库|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|MongoDatabaseDelete|Mongo 数据库已删除|计数|计数|Mongo 数据库已删除|Context.resourcename、/Apikind/、ApiKindResourceType、OperationType|
|MongoDatabaseThroughputUpdate|已更新 Mongo 数据库吞吐量|计数|计数|已更新 Mongo 数据库吞吐量|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|MongoRequestCharge|Mongo 请求费用|计数|总计|Mongo 已消耗的请求单位|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequests|Mongo 请求|计数|计数|已发出的 Mongo 请求数|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequestsCount|Mongo 请求速率|每秒计数|平均值|每秒 Mongo 请求计数|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsDelete|Mongo 删除请求速率|每秒计数|平均值|每秒 Mongo 删除请求数|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsInsert|Mongo 插入请求速率|每秒计数|平均值|每秒 Mongo 插入计数|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsQuery|Mongo 查询请求速率|每秒计数|平均值|每秒 Mongo 查询请求数|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsUpdate|Mongo 更新请求速率|每秒计数|平均值|每秒 Mongo 更新请求数|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|NormalizedRUConsumption|规范化 RU 消耗量|百分比|最大值|每分钟最大 RU 消耗百分比|CollectionName,DatabaseName,Region|
|ProvisionedThroughput|预配的吞吐量|计数|最大值|预配的吞吐量|DatabaseName,CollectionName|
|RegionFailover|故障转移的区域数|计数|计数|故障转移的区域数|无|
|RemoveRegion|删除的区域数|计数|计数|删除的区域数|区域|
|ReplicationLatency|P99 复制延迟|毫秒|平均值|启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟|SourceRegion,TargetRegion|
|ServerSideLatency|服务器端延迟|毫秒|平均值|服务器端延迟|DatabaseName、CollectionName、Region、ConnectionMode、OperationType、PublicAPIType|
|ServiceAvailability|服务可用性|百分比|平均值|一小时、一天或一月粒度的帐户请求可用性|无|
|SqlContainerDelete|Sql 容器已删除|计数|计数|Sql 容器已删除|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、OperationType|
|SqlContainerThroughputUpdate|已更新 Sql 容器吞吐量|计数|计数|已更新 Sql 容器吞吐量|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|SqlContainerUpdate|已更新 Sql 容器|计数|计数|已更新 Sql 容器|Context.resourcename、ChildResourceName、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|SqlDatabaseDelete|Sql 数据库已删除|计数|计数|Sql 数据库已删除|Context.resourcename、/Apikind/、ApiKindResourceType、OperationType|
|SqlDatabaseThroughputUpdate|Sql 数据库吞吐量已更新|计数|计数|Sql 数据库吞吐量已更新|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|SqlDatabaseUpdate|Sql 数据库已更新|计数|计数|Sql 数据库已更新|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|TableTableDelete|已删除 AzureTable 表|计数|计数|已删除 AzureTable 表|Context.resourcename、/Apikind/、ApiKindResourceType、OperationType|
|TableTableThroughputUpdate|已更新 AzureTable 表吞吐量|计数|计数|已更新 AzureTable 表吞吐量|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|TableTableUpdate|已更新 AzureTable 表|计数|计数|已更新 AzureTable 表|Context.resourcename、/Apikind/、ApiKindResourceType、IsThroughputRequest|
|TotalRequestUnits|总请求单位数|计数|总计|已消耗的请求单位|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|TotalRequests|请求总数|计数|计数|已发出的请求数|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|UpdateAccountKeys|更新的帐户密钥数|计数|计数|更新的帐户密钥数|KeyType|
|UpdateAccountNetworkSettings|更新的帐户网络设置数|计数|计数|更新的帐户网络设置数|无|
|UpdateAccountReplicationSettings|更新的帐户复制设置数|计数|计数|更新的帐户复制设置数|无|
|UpdateDiagnosticsSettings|帐户诊断设置已更新|计数|计数|帐户诊断设置已更新|DiagnosticSettingsName、ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TransactionCount|事务计数|计数|计数|事务总数|TransactionCount|
|SuccessCount|成功计数|计数|计数|成功的事务数|SuccessCount|
|FailureCount|失败计数|计数|计数|失败的事务数|FailureCount|
|SuccessLatency|成功延迟|毫秒|平均值|成功事务的延迟|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|主题|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|Topic,ErrorType,Error|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|Topic,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType,Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason,EventSubscriptionName|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType,Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason,EventSubscriptionName|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|MatchedEventCount|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|无|
|DeliveryAttemptFailCount|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error,ErrorType|
|DeliverySuccessCount|发送的事件数|计数|总计|发送到此事件订阅的事件总数|无|
|DestinationProcessingDurationInMs|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|无|
|DroppedEventCount|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason|
|DeadLetteredCount|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PublishSuccessCount|发布的事件数|计数|总计|发布到此主题的事件总数|无|
|PublishFailCount|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType,Error|
|UnmatchedEventCount|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无|
|PublishSuccessLatencyInMs|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的请求数|计数|总计|Microsoft.EventHub 成功请求数。|EntityName,OperationResult|
|ServerErrors|服务器错误数。|计数|总计|Microsoft.EventHub 的服务器错误数。|EntityName,OperationResult|
|UserErrors|用户错误数。|计数|总计|Microsoft.EventHub 用户错误数。|EntityName,OperationResult|
|QuotaExceededErrors|超过限额错误。|计数|总计|Microsoft.EventHub 的超过限额错误数。|EntityName,OperationResult|
|ThrottledRequests|限制的请求数。|计数|总计|Microsoft.EventHub 限制的请求数。|EntityName,OperationResult|
|IncomingRequests|传入请求数|计数|总计|Microsoft.EventHub 传入的请求数。|EntityName|
|IncomingMessages|传入消息数|计数|总计|Microsoft.EventHub 传入的消息数。|EntityName|
|OutgoingMessages|传出消息数|计数|总计|Microsoft.EventHub 传出的消息数。|EntityName|
|IncomingBytes|传入字节数。|字节|总计|Microsoft.EventHub 传入的字节数。|EntityName|
|OutgoingBytes|传出字节数。|字节|总计|Microsoft.EventHub 传出的字节数。|EntityName|
|ActiveConnections|ActiveConnections|计数|平均值|Microsoft.EventHub 的活动连接总数。|无|
|ConnectionsOpened|打开的连接数。|计数|平均值|Microsoft.EventHub 打开的连接数。|EntityName|
|ConnectionsClosed|已关闭的连接数。|计数|平均值|Microsoft.EventHub 已关闭的连接数。|EntityName|
|CaptureBacklog|捕获积压工作(backlog)。|计数|总计|捕获有关 Microsoft.EventHub 的积压工作(backlog)。|EntityName|
|CapturedMessages|已捕获的消息数。|计数|总计|Microsoft.EventHub 已捕获的消息数。|EntityName|
|CapturedBytes|已捕获的字节数。|字节|总计|Microsoft.EventHub 已捕获的字节数。|EntityName|
|大小|大小|字节|平均值|EventHub 的大小（以字节为单位）。|EntityName|
|INREQS|传入的请求数（已弃用）|计数|总计|命名空间的传入发送请求总数（已弃用）|无|
|SUCCREQ|成功的请求数（已弃用）|计数|总计|命名空间的成功请求总数（已弃用）|无|
|FAILREQ|失败的请求数（已弃用）|计数|总计|命名空间的失败请求总数（已弃用）|无|
|SVRBSY|服务器繁忙错误数（已弃用）|计数|总计|命名空间的服务器繁忙错误总数（已弃用）|无|
|INTERR|内部服务器错误数（已弃用）|计数|总计|命名空间的内部服务器错误总数（已弃用）|无|
|MISCERR|其他错误（已弃用）|计数|总计|命名空间的失败请求总数（已弃用）|无|
|INMSGS|传入的消息数（已过时）（已弃用）|计数|总计|命名空间的传入消息总数。 此指标已弃用。 请改用传入消息指标（已弃用）|无|
|EHINMSGS|传入消息（已弃用）|计数|总计|命名空间的传入消息总数（已弃用）|无|
|OUTMSGS|传出的消息数（已过时）（已弃用）|计数|总计|命名空间的传出消息总数。 此指标已弃用。 请改用传出消息指标（已弃用）|无|
|EHOUTMSGS|传出消息（已弃用）|计数|总计|命名空间的传出消息总数（已弃用）|无|
|EHINMBS|传入字节数（已过时）（已弃用）|字节|总计|命名空间的事件中心传入消息吞吐量。 此指标已弃用。 请改用传入字节指标（已弃用）|无|
|EHINBYTES|传入字节（已弃用）|字节|总计|命名空间的事件中心传入消息吞吐量（已弃用）|无|
|EHOUTMBS|传出字节数（已过时）（已弃用）|字节|总计|命名空间的事件中心传出消息吞吐量。 此指标已弃用。 请改用传出字节指标（已弃用）|无|
|EHOUTBYTES|传出字节（已弃用）|字节|总计|命名空间的事件中心传出消息吞吐量（已弃用）|无|
|EHABL|存档积压工作消息数（已弃用）|计数|总计|命名空间积压工作中的事件中心存档消息数（已弃用）|无|
|EHAMSGS|存档消息数（已弃用）|计数|总计|命名空间中的事件中心存档消息数（已弃用）|无|
|EHAMBS|存档消息吞吐量（已弃用）|字节|总计|命名空间中的事件中心存档消息吞吐量（已弃用）|无|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的请求数|计数|总计|Microsoft.EventHub 成功请求数。|OperationResult|
|ServerErrors|服务器错误数。|计数|总计|Microsoft.EventHub 的服务器错误数。|OperationResult|
|UserErrors|用户错误数。|计数|总计|Microsoft.EventHub 用户错误数。|OperationResult|
|QuotaExceededErrors|超过限额错误。|计数|总计|Microsoft.EventHub 的超过限额错误数。|OperationResult|
|ThrottledRequests|限制的请求数。|计数|总计|Microsoft.EventHub 限制的请求数。|OperationResult|
|IncomingRequests|传入请求数|计数|总计|Microsoft.EventHub 传入的请求数。|无|
|IncomingMessages|传入消息数|计数|总计|Microsoft.EventHub 传入的消息数。|无|
|OutgoingMessages|传出消息数|计数|总计|Microsoft.EventHub 传出的消息数。|无|
|IncomingBytes|传入字节数。|字节|总计|Microsoft.EventHub 传入的字节数。|无|
|OutgoingBytes|传出字节数。|字节|总计|Microsoft.EventHub 传出的字节数。|无|
|ActiveConnections|ActiveConnections|计数|平均值|Microsoft.EventHub 的活动连接总数。|无|
|ConnectionsOpened|打开的连接数。|计数|平均值|Microsoft.EventHub 打开的连接数。|无|
|ConnectionsClosed|已关闭的连接数。|计数|平均值|Microsoft.EventHub 已关闭的连接数。|无|
|CaptureBacklog|捕获积压工作(backlog)。|计数|总计|捕获有关 Microsoft.EventHub 的积压工作(backlog)。|无|
|CapturedMessages|已捕获的消息数。|计数|总计|Microsoft.EventHub 已捕获的消息数。|无|
|CapturedBytes|已捕获的字节数。|字节|总计|Microsoft.EventHub 已捕获的字节数。|无|
|CPU|CPU|百分比|最大值|事件中心群集的 CPU 使用率（百分比）|角色|
|AvailableMemory|可用内存|百分比|最大值|事件中心群集的可用内存，以总内存量的百分比表示。|角色|
|大小|EventHub 的大小（以字节为单位）。|字节|平均值|EventHub 的大小（以字节为单位）。|角色|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|GatewayRequests|网关请求数|计数|总计|网关请求数|HttpStatus|
|CategorizedGatewayRequests|已分类的网关请求数|计数|总计|按类别（1xx/2xx/3xx/4xx/5xx）统计的网关请求数|HttpStatus|
|NumActiveWorkers|活动辅助角色数目|计数|最大值|活动辅助角色数目|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ObservedMetricValue|观察到的指标值|计数|平均值|执行自动缩放时计算的值|MetricTriggerSource|
|MetricThreshold|指标阈值|计数|平均值|自动缩放运行时已配置的自动缩放阈值。|MetricTriggerRule|
|ObservedCapacity|观察到的容量|计数|平均值|自动缩放执行时报告的容量。|无|
|ScaleActionsInitiated|启动的缩放操作|计数|总计|缩放操作的方向。|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|可用性|百分比|平均值|已成功完成的可用性测试百分比|availabilityResult/name,availabilityResult/location|
|availabilityResults/count|可用性测试|计数|计数|可用性测试计数|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|availabilityResults/duration|可用性测试持续时间|毫秒|平均值|可用性测试持续时间|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|browserTimings/networkDuration|页面加载网络连接时间|毫秒|平均值|用户请求和网络连接之间的时间。 包括 DNS 查找和传输连接。|无|
|browserTimings/processingDuration|客户端处理时间|毫秒|平均值|从接收文档的最后一个字节到 DOM 加载完之间的时间。 可能仍在处理异步请求。|无|
|browserTimings/receiveDuration|接收响应时间|毫秒|平均值|第一个和最后一个字节之间的时间，或直至断开连接的时间。|无|
|browserTimings/sendDuration|发送请求时间|毫秒|平均值|网络连接和接收第一个字节之间的时间。|无|
|browserTimings/totalDuration|浏览器页面加载时间|毫秒|平均值|从用户请求一直到 DOM、样式表、脚本和映像加载完之间的时间。|无|
|dependencies/count|依赖项调用|计数|计数|应用程序对外部资源所进行的调用计数。|依赖项/类型、依赖项/performanceBucket、依赖项/成功、依赖项/目标、依赖项/resultCode、操作/合成、cloud/roleInstance、云/对象类型|
|dependencies/duration|依赖项持续时间|毫秒|平均值|应用程序对外部资源所进行的调用持续时间。|依赖项/类型、依赖项/performanceBucket、依赖项/成功、依赖项/目标、依赖项/resultCode、操作/合成、cloud/roleInstance、云/对象类型|
|dependencies/failed|依赖项调用失败次数|计数|计数|应用程序对外部资源所进行的依赖项调用失败的计数。|依赖项/类型、依赖项/performanceBucket、依赖项/成功、依赖项/目标、依赖项/resultCode、操作/合成、cloud/roleInstance、云/对象类型|
|pageViews/count|页面查看次数|计数|计数|页面视图计数。|operation/synthetic,cloud/roleName|
|pageViews/duration|页面视图加载时间|毫秒|平均值|页面视图加载时间|operation/synthetic,cloud/roleName|
|performanceCounters/requestExecutionTime|HTTP 请求执行时间|毫秒|平均值|最近的请求执行时间。|cloud/roleInstance|
|performanceCounters/requestsInQueue|应用程序队列中的 HTTP 请求|计数|平均值|应用程序请求队列的长度。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 请求速率|每秒计数|平均值|每秒从 ASP.NET 发出的应用程序所有请求的速率。|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|异常率|每秒计数|平均值|报告给窗口的已处理和未处理的异常的计数，这些异常包括 .NET 异常和转换为 .NET 异常的非托管异常。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|进程 IO 率|每秒字节数|平均值|每秒读取和写入文件、网络和设备的总字节数。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|进程 CPU|百分比|平均值|所有进程线程使用处理器执行指令所用的运行时间的百分比。 介于 0 到 100 之间。 此指标仅表示 w3wp 进程的性能。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|处理器时间|百分比|平均值|处理器在非空闲线程上所花费的时间百分比。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|可用内存|字节|平均值|可立刻供进程或系统使用的物理内存。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|进程专用字节|字节|平均值|以独占方式分配给受监视应用程序进程的内存。|cloud/roleInstance|
|requests/duration|服务器响应时间|毫秒|平均值|从接收 HTTP 请求到完成响应发送之间的时间。|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/count|服务器请求数|计数|计数|已完成的 HTTP 请求计数。|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/failed|失败的请求|计数|计数|标记为失败的 HTTP 请求的计数。 在大多数情况下这些请求的响应代码 >= 400 且不等于 401。|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|requests/rate|服务器请求速率|每秒计数|平均值|每秒服务器请求速率|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|exceptions/count|例外|计数|计数|所有未捕获异常的已合并计数。|cloud/roleName,cloud/roleInstance,client/type|
|exceptions/browser|浏览器异常|计数|计数|浏览器中所引发未捕获异常的计数。|client/isServer,cloud/roleName|
|exceptions/server|服务器异常|计数|计数|服务器应用程序中引发的未捕获的异常计数。|client/isServer,cloud/roleName,cloud/roleInstance|
|traces/count|跟踪|计数|计数|跟踪文档计数|trace/severityLevel,operation/synthetic,cloud/roleName,cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>IoTCentral/IoTApps

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|connectedDeviceCount|连接的设备总数|计数|平均值|连接到 IoT Central 的设备数|无|
|c2d。成功|成功从 IoT Central 读取设备属性|计数|总计|从 IoT Central 启动的所有成功的属性读取的计数|无|
|c2d。读取失败|从 IoT Central 读取失败的设备属性|计数|总计|从 IoT Central 启动的所有失败的属性读取的计数|无|
|d2c。成功|设备属性从设备读取成功|计数|总计|从设备启动的所有成功的属性读取的计数|无|
|d2c。读取失败|设备属性读取失败|计数|总计|从设备启动的所有失败的属性读取的计数|无|
|c2d。成功|IoT Central 中的设备属性更新成功|计数|总计|从 IoT Central 启动的所有成功属性更新的计数|无|
|c2d。失败|IoT Central 中的设备属性更新失败|计数|总计|从 IoT Central 启动的所有失败属性更新的计数|无|
|d2c。成功|设备的成功更新设备属性|计数|总计|从设备启动的所有成功属性更新的计数|无|
|d2c。失败|设备的设备属性更新失败|计数|总计|从设备启动的所有失败属性更新的计数|无|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ServiceApiHit|服务 API 命中总计|计数|计数|服务 API 命中总数|ActivityType,ActivityName|
|ServiceApiLatency|总体服务 API 延迟|毫秒|平均值|服务 API 请求的总体延迟|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|ServiceApiResult|服务 API 结果总计|计数|计数|服务 API 结果总数|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|SaturationShoebox|保管库整体饱和度|百分比|平均值|已使用的保管库容量|ActivityType,ActivityName,TransactionType|
|可用性|保管库整体可用性|百分比|平均值|保管库请求可用性|ActivityType,ActivityName,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CacheUtilization|缓存利用率|百分比|平均值|群集范围内的使用率级别|无|
|QueryDuration|查询持续时间|毫秒|平均值|查询的持续时间（秒）|QueryStatus|
|IngestionUtilization|引入利用率|百分比|平均值|群集中已使用引入槽的比率|无|
|KeepAlive|保持活动状态|计数|平均值|完整性检查表示群集响应查询|无|
|IngestionVolumeInMB|引入量 (MB)|计数|总计|已引入群集的数据总量 (MB)|数据库|
|IngestionLatencyInSeconds|引入延迟（秒）|秒|平均值|从源（例如消息位于事件中心）到群集的引入时间（秒）|无|
|EventsProcessedForEventHubs|处理的事件数（适用于事件中心/IoT 中心）|计数|总计|从事件中心/IoT 中心引入时，由群集处理的事件数|EventStatus|
|IngestionResult|引入结果|计数|计数|引入操作的数量|IngestionResultDetails|
|CPU|CPU|百分比|平均值|CPU 使用率级别|无|
|ContinuousExportNumOfRecordsExported|连续导出 – 导出的记录数|计数|总计|导出的记录数，在导出操作期间对每个写入的存储项目激发|ContinuousExportName，数据库|
|ExportUtilization|导出利用率|百分比|最大值|导出利用率|无|
|ContinuousExportPendingCount|连续导出挂起计数|计数|最大值|做好执行准备的挂起的连续导出作业数|无|
|ContinuousExportMaxLatenessMinutes|连续导出最大迟到|计数|最大值|群集中的连续导出作业报告的迟到（分钟）|无|
|ContinuousExportResult|连续导出结果|计数|计数|指示连续导出是成功还是失败|ContinuousExportName,Result,Database|
|StreamingIngestDuration|流引入持续时间|毫秒|平均值|流引入持续时间（毫秒）|无|
|StreamingIngestDataRate|流引入数据速率|计数|平均值|流引入数据速率（MB/秒）|无|
|SteamingIngestRequestRate|流引入请求速率|计数|RateRequestsPerSecond|流引入请求速率（每秒请求数）|无|
|StreamingIngestResults|流引入结果|计数|平均值|流引入结果|结果|
|TotalNumberOfConcurrentQueries|并发查询总数|计数|总计|并发查询总数|无|
|TotalNumberOfThrottledQueries|受限制的查询总数|计数|总计|受限制的查询总数|无|
|TotalNumberOfThrottledCommands|受限制的命令总数|计数|总计|受限制的命令总数|CommandType|
|TotalNumberOfExtents|盘区总数|计数|总计|数据盘区总数|无|
|InstanceCount|实例计数|计数|平均值|实例总数|无|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RunsStarted|已启动的运行数|计数|总计|已启动的工作流运行数目。|无|
|RunsCompleted|已完成的运行数|计数|总计|已完成的工作流运行数目。|无|
|RunsSucceeded|成功的运行数|计数|总计|成功的工作流运行数目。|无|
|RunsFailed|失败的运行数|计数|总计|失败的工作流运行数目。|无|
|RunsCancelled|取消的运行数|计数|总计|已取消的工作流运行数目。|无|
|RunLatency|运行延迟|秒|平均值|已完成的工作流运行的延迟。|无|
|RunSuccessLatency|运行成功延迟|秒|平均值|已成功的工作流运行的延迟。|无|
|RunThrottledEvents|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无|
|RunStartThrottledEvents|运行启动限制事件|计数|总计|工作流运行启动限制事件数。|无|
|RunFailurePercentage|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无|
|ActionsStarted|启动的操作数 |计数|总计|已启动的工作流操作数目。|无|
|ActionsCompleted|完成的操作数 |计数|总计|已完成的工作流操作数目。|无|
|ActionsSucceeded|成功的操作数 |计数|总计|成功的工作流操作数目。|无|
|ActionsFailed|失败的操作数 |计数|总计|失败的工作流操作数目。|无|
|ActionsSkipped|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无|
|ActionLatency|操作延迟 |秒|平均值|已完成的工作流操作的延迟。|无|
|ActionSuccessLatency|操作成功延迟 |秒|平均值|已成功的工作流操作的延迟。|无|
|ActionThrottledEvents|操作限制事件数|计数|总计|工作流操作限制事件数目。|无|
|TriggersStarted|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无|
|TriggersCompleted|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无|
|TriggersSucceeded|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无|
|TriggersFailed|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无|
|TriggersSkipped|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无|
|TriggersFired|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无|
|TriggerLatency|触发器延迟 |秒|平均值|已完成的工作流触发器的延迟。|无|
|TriggerFireLatency|触发器激发延迟 |秒|平均值|已激发的工作流触发器的延迟。|无|
|TriggerSuccessLatency|触发器成功延迟 |秒|平均值|已成功的工作流触发器的延迟。|无|
|TriggerThrottledEvents|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无|
|BillableActionExecutions|计费的操作执行数|计数|总计|计费的工作流操作执行数目。|无|
|BillableTriggerExecutions|计费的触发器执行数|计数|总计|计费的工作流触发器执行数目。|无|
|TotalBillableExecutions|计费的执行总数|计数|总计|计费的工作流执行数目。|无|
|BillingUsageNativeOperation|本机操作执行的计费使用情况|计数|总计|已计费的本机操作执行次数。|无|
|BillingUsageStandardConnector|标准连接器执行的计费使用情况|计数|总计|已计费的标准连接器执行次数。|无|
|BillingUsageStorageConsumption|存储使用执行的计费使用情况|计数|总计|已计费的存储使用执行次数。|无|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RunsStarted|已启动的运行数|计数|总计|已启动的工作流运行数目。|无|
|RunsCompleted|已完成的运行数|计数|总计|已完成的工作流运行数目。|无|
|RunsSucceeded|成功的运行数|计数|总计|成功的工作流运行数目。|无|
|RunsFailed|失败的运行数|计数|总计|失败的工作流运行数目。|无|
|RunsCancelled|取消的运行数|计数|总计|已取消的工作流运行数目。|无|
|RunLatency|运行延迟|秒|平均值|已完成的工作流运行的延迟。|无|
|RunSuccessLatency|运行成功延迟|秒|平均值|已成功的工作流运行的延迟。|无|
|RunThrottledEvents|运行限制事件数|计数|总计|工作流操作或触发器限制事件数目。|无|
|RunStartThrottledEvents|运行启动限制事件|计数|总计|工作流运行启动限制事件数。|无|
|RunFailurePercentage|运行失败百分比|百分比|总计|失败的工作流运行百分比。|无|
|ActionsStarted|启动的操作数 |计数|总计|已启动的工作流操作数目。|无|
|ActionsCompleted|完成的操作数 |计数|总计|已完成的工作流操作数目。|无|
|ActionsSucceeded|成功的操作数 |计数|总计|成功的工作流操作数目。|无|
|ActionsFailed|失败的操作数 |计数|总计|失败的工作流操作数目。|无|
|ActionsSkipped|跳过的操作数 |计数|总计|已跳过的工作流操作数目。|无|
|ActionLatency|操作延迟 |秒|平均值|已完成的工作流操作的延迟。|无|
|ActionSuccessLatency|操作成功延迟 |秒|平均值|已成功的工作流操作的延迟。|无|
|ActionThrottledEvents|操作限制事件数|计数|总计|工作流操作限制事件数目。|无|
|TriggersStarted|启动的触发器数 |计数|总计|已启动的工作流触发器数目。|无|
|TriggersCompleted|完成的触发器数 |计数|总计|已完成的工作流触发器数目。|无|
|TriggersSucceeded|成功的触发器数 |计数|总计|成功的工作流触发器数目。|无|
|TriggersFailed|失败的触发器数 |计数|总计|失败的工作流触发器数目。|无|
|TriggersSkipped|跳过的触发器数|计数|总计|已跳过的工作流触发器数目。|无|
|TriggersFired|激发的触发器数 |计数|总计|已激发的工作流触发器数目。|无|
|TriggerLatency|触发器延迟 |秒|平均值|已完成的工作流触发器的延迟。|无|
|TriggerFireLatency|触发器激发延迟 |秒|平均值|已激发的工作流触发器的延迟。|无|
|TriggerSuccessLatency|触发器成功延迟 |秒|平均值|已成功的工作流触发器的延迟。|无|
|TriggerThrottledEvents|触发器限制事件数|计数|总计|工作流触发器限制事件数目。|无|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|集成服务环境的工作流处理器使用情况|百分比|平均值|集成服务环境的工作流处理器使用情况。|无|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|集成服务环境的工作流内存使用情况|百分比|平均值|集成服务环境的工作流内存使用情况。|无|
|IntegrationServiceEnvironmentConnectorProcessorUsage|集成服务环境的连接器处理器使用情况|百分比|平均值|集成服务环境的连接器处理器使用情况。|无|
|IntegrationServiceEnvironmentConnectorMemoryUsage|集成服务环境的连接器内存使用情况|百分比|平均值|集成服务环境的连接器内存使用情况。|无|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|已取消运行|已取消运行|计数|总计|为此工作区取消的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|取消请求的运行|取消请求的运行|计数|总计|为此工作区请求取消的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|已完成的运行数|已完成的运行数|计数|总计|已成功为此工作区完成的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|失败的运行次数|失败的运行次数|计数|总计|此工作区失败的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|正在完成运行|正在完成运行|计数|总计|为此工作区进入的运行已完成状态的数目|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|未响应运行|未响应运行|计数|总计|此工作区的运行数未响应|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|未开始运行|未开始运行|计数|总计|此工作区未启动的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|正在准备运行|正在准备运行|计数|总计|正在为此工作区准备的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|预配运行|预配运行|计数|总计|为此工作区预配的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|排队运行|排队运行|计数|总计|为此工作区排队的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|已启动的运行数|已启动的运行数|计数|总计|为此工作区启动的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|正在启动运行|正在启动运行|计数|总计|为此工作区启动的运行数|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|错误|错误|计数|总计|此工作区中的运行错误数|方案|
|警告|警告|计数|总计|此工作区中的运行警告数|方案|
|成功的模型注册数|成功的模型注册数|计数|总计|此工作区中成功的模型注册数|方案|
|失败的模型注册数|失败的模型注册数|计数|总计|此工作区中失败的模型注册数|Scenario,StatusCode|
|已启动的模型部署数|已启动的模型部署数|计数|总计|此工作区中已启动的模型部署数|方案|
|成功的模型部署数|成功的模型部署数|计数|总计|此工作区中成功的模型部署数|方案|
|失败的模型部署数|失败的模型部署数|计数|总计|此工作区中失败的模型部署数|Scenario,StatusCode|
|节点总数|节点总数|计数|平均值|节点总数。 此总数包括一些活动节点、空闲节点、不可用的节点、占用的节点和正在退出的节点|Scenario,ClusterName|
|活动节点数|活动节点数|计数|平均值|活动节点数。 这些节点是正在运行作业的节点。|Scenario,ClusterName|
|空闲节点数|空闲节点数|计数|平均值|空闲节点数。 空闲节点是指未运行任何作业，但可以接受新作业（如果可用）的节点。|Scenario,ClusterName|
|不可用的节点数|不可用的节点数|计数|平均值|不可用的节点数。 不可用的节点是由于存在某种不可解决的问题而无法正常运行的节点。 Azure 将回收这些节点。|Scenario,ClusterName|
|占用的节点数|占用的节点数|计数|平均值|占用的节点数。 这些节点是已从可用节点池中取出的低优先级节点。|Scenario,ClusterName|
|正在退出的节点数|正在退出的节点数|计数|平均值|正在退出的节点数。 正在退出的节点是指刚刚完成了作业处理，将进入空闲状态的节点。|Scenario,ClusterName|
|核心总数|核心总数|计数|平均值|核心总数|Scenario,ClusterName|
|活动核心数|活动核心数|计数|平均值|活动核心数|Scenario,ClusterName|
|空闲核心数|空闲核心数|计数|平均值|空闲核心数|Scenario,ClusterName|
|不可用的核心数|不可用的核心数|计数|平均值|不可用的核心数|Scenario,ClusterName|
|占用的核心数|占用的核心数|计数|平均值|占用的核心数|Scenario,ClusterName|
|正在退出的核心数|正在退出的核心数|计数|平均值|正在退出的核心数|Scenario,ClusterName|
|配额利用率百分比|配额利用率百分比|计数|平均值|已利用的配额百分比|Scenario,ClusterName,VmFamilyName,VmPriority|
|CpuUtilization|CpuUtilization|计数|平均值|CPU（预览）|Scenario,runId,NodeId,CreatedTime|
|GpuUtilization|GpuUtilization|计数|平均值|GPU（预览版）|Scenario,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|使用情况|使用情况|计数|计数|API 调用计数|ApiCategory,ApiName,ResultType,ResponseCode|
|可用性|可用性|百分比|平均值|API 可用性|ApiCategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。|OutputFormat|
|SuccessE2ELatency|成功端到端延迟|毫秒|平均值|成功请求的平均延迟（以毫秒为单位）。|OutputFormat|
|请求|请求|计数|总计|对流式处理终结点的请求。|OutputFormat,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AssetQuota|资产配额|计数|平均值|当前媒体服务帐户允许的资产数量|无|
|AssetCount|资产计数|计数|平均值|已在当前媒体服务帐户中创建的资产数量|无|
|AssetQuotaUsedPercentage|资产配额已用百分比|百分比|平均值|当前媒体服务帐户中的资产已用百分比|无|
|ContentKeyPolicyQuota|内容密钥策略配额|计数|平均值|当前媒体服务帐户允许的内容密钥策略数量|无|
|ContentKeyPolicyCount|内容密钥策略计数|计数|平均值|当前媒体服务帐户中已创建的内容密钥策略数量|无|
|ContentKeyPolicyQuotaUsedPercentage|内容密钥策略配额已用百分比|百分比|平均值|当前媒体服务帐户中的内容密钥策略已用百分比|无|
|StreamingPolicyQuota|流式处理策略配额|计数|平均值|当前媒体服务帐户允许的流式处理策略数量|无|
|StreamingPolicyCount|流式处理策略计数|计数|平均值|当前媒体服务帐户中已创建的流式处理策略数量|无|
|StreamingPolicyQuotaUsedPercentage|流式处理策略配额已用百分比|百分比|平均值|当前媒体服务帐户中的流式处理策略已用百分比|无|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>MixedReality/remoteRenderingAccounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AssetsConverted|已转换资产|计数|总计|已转换资产的总数|AppId、ResourceId、SDKVersion|
|ActiveRenderingSessions|活动呈现会话|计数|总计|活动呈现会话总数|AppId、ResourceId、SessionType、SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AverageReadLatency|平均读取延迟|毫秒|平均值|每个操作的平均读取延迟（毫秒）|无|
|AverageWriteLatency|平均写入延迟|毫秒|平均值|每个操作的平均写入延迟（毫秒）|无|
|VolumeLogicalSize|卷消耗大小|字节|平均值|卷的逻辑大小（所用字节数）|无|
|VolumeSnapshotSize|卷快照大小|字节|平均值|卷中所有快照的大小|无|
|ReadIops|读取 IOPS|每秒计数|平均值|每秒读取输入/输出操作数|无|
|WriteIops|写入 IOPS|每秒计数|平均值|每秒写入输入/输出操作数|无|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|分配到卷大小的池|字节|平均值|分配池已使用的大小|无|
|VolumePoolTotalLogicalSize|池消耗大小|字节|平均值|属于该池的所有卷的总逻辑大小|无|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BytesSentRate|发送的字节数|字节|总计|网络接口发送的字节数|无|
|BytesReceivedRate|接收的字节数|字节|总计|网络接口接收的字节数|无|
|PacketsSentRate|发送的数据包数|计数|总计|网络接口发送的数据包数|无|
|PacketsReceivedRate|已接收的数据包数|计数|总计|网络接口接收的数据包数|无|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|VipAvailability|数据路径可用性|计数|平均值|每个持续时间的负载均衡器数据路径的平均可用性|FrontendIPAddress,FrontendPort|
|DipAvailability|运行状况探测状态|计数|平均值|每个持续时间的负载均衡器运行状况探测的平均状态|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|字节计数|计数|总计|时间段内传输的字节总数|FrontendIPAddress,FrontendPort,Direction|
|PacketCount|数据包计数|计数|总计|时间段内传输的数据包总数|FrontendIPAddress,FrontendPort,Direction|
|SYNCount|SYN 计数|计数|总计|时间段内传输的 SYN 数据包总数|FrontendIPAddress,FrontendPort,Direction|
|SnatConnectionCount|SNAT 连接计数|计数|总计|时间段内创建的新 SNAT 连接的总数|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|已分配的 SNAT 端口数（预览）|计数|总计|时间段内已分配的 SNAT 端口总数|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UsedSnatPorts|已使用的 SNAT 端口数（预览）|计数|总计|时间段内 SNAT 端口的总数|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QueryVolume|查询量|计数|总计|为 DNS 区域提供服务的查询数|无|
|RecordSetCount|记录集计数|计数|最大值|DNS 区域中的记录集数|无|
|RecordSetCapacityUtilization|记录集容量使用率|百分比|最大值|DNS 区域利用的记录集容量的百分比|无|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PacketsInDDoS|入站数据包 DDoS|每秒计数|最大值|入站数据包 DDoS|无|
|PacketsDroppedDDoS|丢弃的入站数据包 DDoS|每秒计数|最大值|丢弃的入站数据包 DDoS|无|
|PacketsForwardedDDoS|转发的入站数据包 DDoS|每秒计数|最大值|转发的入站数据包 DDoS|无|
|TCPPacketsInDDoS|入站 TCP 数据包 DDoS|每秒计数|最大值|入站 TCP 数据包 DDoS|无|
|TCPPacketsDroppedDDoS|丢弃的入站 TCP 数据包 DDoS|每秒计数|最大值|丢弃的入站 TCP 数据包 DDoS|无|
|TCPPacketsForwardedDDoS|转发的入站 TCP 数据包 DDoS|每秒计数|最大值|转发的入站 TCP 数据包 DDoS|无|
|UDPPacketsInDDoS|入站 UDP 数据包 DDoS|每秒计数|最大值|入站 UDP 数据包 DDoS|无|
|UDPPacketsDroppedDDoS|丢弃的入站 UDP 数据包 DDoS|每秒计数|最大值|丢弃的入站 UDP 数据包 DDoS|无|
|UDPPacketsForwardedDDoS|转发的入站 UDP 数据包 DDoS|每秒计数|最大值|转发的入站 UDP 数据包 DDoS|无|
|BytesInDDoS|入站字节 DDoS|每秒字节数|最大值|入站字节 DDoS|无|
|BytesDroppedDDoS|丢弃的入站字节 DDoS|每秒字节数|最大值|丢弃的入站字节 DDoS|无|
|BytesForwardedDDoS|转发的入站字节 DDoS|每秒字节数|最大值|转发的入站字节 DDoS|无|
|TCPBytesInDDoS|入站 TCP 字节 DDoS|每秒字节数|最大值|入站 TCP 字节 DDoS|无|
|TCPBytesDroppedDDoS|丢弃的入站 TCP 字节 DDoS|每秒字节数|最大值|丢弃的入站 TCP 字节 DDoS|无|
|TCPBytesForwardedDDoS|转发的入站 TCP 字节 DDoS|每秒字节数|最大值|转发的入站 TCP 字节 DDoS|无|
|UDPBytesInDDoS|入站 UDP 字节 DDoS|每秒字节数|最大值|入站 UDP 字节 DDoS|无|
|UDPBytesDroppedDDoS|丢弃的入站 UDP 字节 DDoS|每秒字节数|最大值|丢弃的入站 UDP 字节 DDoS|无|
|UDPBytesForwardedDDoS|转发的入站 UDP 字节 DDoS|每秒字节数|最大值|转发的入站 UDP 字节 DDoS|无|
|IfUnderDDoSAttack|是否遭到 DDoS 攻击|计数|最大值|是否遭到 DDoS 攻击|无|
|DDoSTriggerTCPPackets|触发 DDoS 缓解的入站 TCP 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 TCP 数据包|无|
|DDoSTriggerUDPPackets|触发 DDoS 缓解的入站 UDP 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 UDP 数据包|无|
|DDoSTriggerSYNPackets|触发 DDoS 缓解的入站 SYN 数据包|每秒计数|最大值|触发 DDoS 缓解的入站 SYN 数据包|无|
|VipAvailability|数据路径可用性|计数|平均值|每个持续时间的 IP 地址的平均可用性|端口|
|ByteCount|字节计数|计数|总计|时间段内传输的字节总数|Port,Direction|
|PacketCount|数据包计数|计数|总计|时间段内传输的数据包总数|Port,Direction|
|SynCount|SYN 计数|计数|总计|时间段内传输的 SYN 数据包总数|Port,Direction|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Ping 某个 VM 所需的往返时间|毫秒|平均值|向目标 VM 发送 Ping 所需的往返时间|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|VM Ping 失败次数|百分比|平均值|失败的 Ping 次数占发送到目标 VM 的 Ping 总数的百分比|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ApplicationRuleHit|应用程序规则命中次数|计数|总计|应用程序规则的命中次数|Status,Reason,Protocol|
|NetworkRuleHit|网络规则命中次数|计数|总计|网络规则的命中次数|Status,Reason,Protocol|
|FirewallHealth|防火墙运行状况|百分比|平均值|防火墙运行状况|Status,Reason|
|DataProcessed|已处理的数据|字节|总计|防火墙处理的数据总量|无|
|SNATPortUtilization|SNAT 端口利用率|百分比|平均值|SNAT 端口利用率|无|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|吞吐量|吞吐量|每秒字节数|平均值|应用程序网关每秒提供的字节数|无|
|UnhealthyHostCount|不正常的主机计数|计数|平均值|不正常的后端主机数|BackendSettingsPool|
|HealthyHostCount|正常的主机计数|计数|平均值|正常的后端主机数|BackendSettingsPool|
|TotalRequests|请求总数|计数|总计|应用程序网关已提供服务的成功请求计数|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|每个正常主机每分钟的请求数|计数|平均值|池中每个正常后端主机每分钟的平均请求计数|BackendSettingsPool|
|FailedRequests|失败的请求数|计数|总计|应用程序网关已提供服务的失败请求计数|BackendSettingsPool|
|ResponseStatus|响应状态|计数|总计|应用程序网关返回的 Http 响应状态|HttpStatusGroup|
|CurrentConnections|当前连接|计数|总计|使用应用程序网关建立的当前连接计数|无|
|NewConnectionsPerSecond|每秒新连接数|每秒计数|平均值|与应用程序网关建立的每秒新连接数|无|
|CpuUtilization|CPU 使用率|百分比|平均值|应用程序网关的当前 CPU 利用率|无|
|CapacityUnits|当前容量单位数|计数|平均值|已消耗的请求单位数|无|
|FixedBillableCapacityUnits|固定的可计费容量单位|计数|平均值|要收取的最小容量单位|无|
|EstimatedBilledCapacityUnits|估计的计费容量单位|计数|平均值|要支付的估计容量单位|无|
|ComputeUnits|当前计算单位数|计数|平均值|消耗的计算单位数|无|
|BackendResponseStatus|后端响应状态|计数|总计|后端成员生成的 HTTP 响应代码数。 这不包括应用程序网关生成的任何响应代码。|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|TlsProtocol|客户端 TLS 协议|计数|总计|与应用程序网关建立了连接的客户端发起的 TLS 和非 TLS 请求数。 若要查看 TLS 协议分布，请按“TLS 协议”维度进行筛选。|Listener,TlsProtocol|
|BytesSent|发送的字节数|字节|总计|应用程序网关向客户端发送的字节总数|侦听器|
|BytesReceived|接收的字节数|字节|总计|应用程序网关从客户端收到的字节总数|侦听器|
|ClientRtt|客户端 RTT|毫秒|平均值|客户端与应用程序网关之间的平均往返时间。 此指标指示建立连接和返回确认所用的时间|侦听器|
|ApplicationGatewayTotalTime|应用程序网关总时间|毫秒|平均值|处理请求和发送响应平均花费的时间。 此时间按特定的平均时间间隔（从应用程序网关接收第一个 HTTP 请求字节到完成响应发送操作所需的时间）来计算。 必须注意，这通常包括应用程序网关处理时间、请求和响应数据包遍历网络的时间，以及后端服务器做出响应所花费的时间。|侦听器|
|BackendConnectTime|后端连接时间|毫秒|平均值|与后端服务器建立连接所花费的时间|Listener,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|后端第一个字节响应时间|毫秒|平均值|从开始与后端服务器建立连接，到收到响应标头的第一个字节的间隔时间，大致相当于后端服务器的处理时间|Listener,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|后端最后一个字节响应时间|毫秒|平均值|从开始与后端服务器建立连接，到收到响应正文的最后一个字节的间隔时间|Listener,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Web 应用程序防火墙 v1 规则分发总数|计数|总计|Web 应用程序防火墙 v1 传入流量的规则分配总数|RuleGroup,RuleId|
|BlockedCount|Web 应用程序防火墙 v1 阻止的请求规则分发|计数|总计|Web 应用程序防火墙 v1 阻止的请求规则分发|RuleGroup,RuleId|
|BlockedReqCount|Web 应用程序防火墙 v1 阻止的请求计数|计数|总计|Web 应用程序防火墙 v1 阻止的请求计数|无|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AverageBandwidth|网关 S2S 带宽|每秒字节数|平均值|网关站点到站点的平均带宽（字节/秒）|无|
|P2SBandwidth|网关 P2S 带宽|每秒字节数|平均值|网关点到站点的平均带宽（字节/秒）|无|
|P2SConnectionCount|P2S 连接计数|计数|最大值|网关的点到站点连接计数|协议|
|TunnelAverageBandwidth|隧道带宽|每秒字节数|平均值|隧道带宽平均值（以字节/秒为单位）|ConnectionName,RemoteIP|
|TunnelEgressBytes|隧道流出字节|字节|总计|隧道的传出字节数|ConnectionName,RemoteIP|
|TunnelIngressBytes|隧道流入字节|字节|总计|隧道的传入字节数|ConnectionName,RemoteIP|
|TunnelEgressPackets|隧道流出数据包|计数|总计|隧道的传出数据包计数|ConnectionName,RemoteIP|
|TunnelIngressPackets|隧道流入数据包|计数|总计|隧道的传入数据包计数|ConnectionName,RemoteIP|
|TunnelEgressPacketDropTSMismatch|隧道流出 TS 不匹配数据包丢弃|计数|总计|来自隧道的不匹配流量选择器的传出数据包丢弃|ConnectionName,RemoteIP|
|TunnelIngressPacketDropTSMismatch|隧道流入 TS 不匹配数据包丢弃|计数|总计|来自隧道的不匹配流量选择器的传入数据包丢弃|ConnectionName,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|计数|平均值|Rx 光能级（以 dBm 为单位）|Link,Lane|
|TxLightLevel|TxLightLevel|计数|平均值|Tx 光能级（以 dBm 为单位）|Link,Lane|
|AdminState|AdminState|计数|平均值|端口的管理状态|链接|
|LineProtocol|LineProtocol|计数|平均值|端口的线路协议状态|链接|
|PortBitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|链接|
|PortBitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|链接|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|PeeredCircuitSKey|
|BgpAvailability|BGP 可用性|百分比|平均值|从 MSEE 到所有对等方的 BGP 可用性。|PeeringType,Peer|
|ArpAvailability|Arp 可用性|百分比|平均值|从 MSEE 到所有对等方的 ARP 可用性。|PeeringType,Peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|每秒计数|平均值|每秒丢弃的数据流入位数|无|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|每秒计数|平均值|每秒丢弃的数据流出位数|无|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|无|
|BitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|无|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|每秒计数|平均值|每秒流入 Azure 的位数|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|每秒计数|平均值|每秒流出 Azure 的位数|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QpsByEndpoint|按终结点返回的查询|计数|总计|给定时间范围内返回流量管理器终结点的次数|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|按终结点显示的终结点状态|计数|最大值|如果终结点的探测状态为“已启用”，则值为 1；否则，值为 0。|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ProbesFailedPercent|失败的探测百分比|百分比|平均值|失败的连接监视探测百分比|无|
|AverageRoundtripMs|平均往返时间（毫秒）|毫秒|平均值|源和目标之间发送的连接监视探测的平均网络往返时间（毫秒）|无|
|ChecksFailedPercent|检查失败百分比（预览版）|百分比|平均值|连接监视检查失败百分比|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|往返时间（毫秒）（预览版）|毫秒|平均值|连接监视检查的往返时间（以毫秒为单位）|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RequestCount|请求计数|计数|总计|HTTP/S 代理提供客户端请求数|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|RequestSize|请求大小|字节|总计|以请求的形式从客户端发送到 HTTP/S 代理的字节数|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|ResponseSize|响应大小|字节|总计|以响应的形式从 HTTP/S 代理发送到客户端的字节数|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|计费响应大小|字节|总计|作为响应从 HTTP/S 代理发送到客户端的计费字节数（每个请求最小 2KB）。|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|后端请求计数|计数|总计|从 HTTP/S 代理发送到后端的请求数|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestLatency|后端请求延迟|毫秒|平均值|自请求由 HTTP/S 代理发送到后端直至 HTTP/S 代理从后端收到最后一个响应字节为止，所计算的时间|后端|
|TotalLatency|总延迟|毫秒|平均值|自请求由 HTTP/S 代理接收后到客户端确认来自 HTTP/S 代理的最后一个响应字节止，所计算的时间|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|后端运行状况百分比|百分比|平均值|从 HTTP/S 代理到后端，成功运行状况探测的百分比|Backend,BackendPool|
|WebApplicationFirewallRequestCount|Web 应用程序防火墙请求计数|计数|总计|Web 应用程序防火墙所处理的客户端请求数|PolicyName,RuleName,Action|


## <a name="microsoftnetworkprivatednszones"></a>PrivateDnsZones/网络

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QueryVolume|查询量|计数|总计|为专用 DNS 区域提供的查询数|无|
|RecordSetCount|记录集计数|计数|最大值|专用 DNS 区域中的记录集数|无|
|RecordSetCapacityUtilization|记录集容量使用率|百分比|最大值|专用 DNS 区域使用的记录集容量百分比|无|
|VirtualNetworkLinkCount|虚拟网络链接计数|计数|最大值|链接到专用 DNS 区域的虚拟网络数|无|
|VirtualNetworkLinkCapacityUtilization|虚拟网络链路容量利用率|百分比|最大值|专用 DNS 区域使用的虚拟网络链接容量的百分比|无|
|VirtualNetworkWithRegistrationLinkCount|虚拟网络注册链接计数|计数|最大值|已启用自动注册的专用 DNS 区域链接到的虚拟网络数|无|
|VirtualNetworkWithRegistrationCapacityUtilization|虚拟网络注册链接容量利用率|百分比|最大值|专用 DNS 区域使用自动注册容量的虚拟网络链接百分比|无|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|registration.all|注册操作|计数|总计|所有成功的注册操作（创建、更新、查询和删除）的计数。 |无|
|registration.create|注册创建操作数|计数|总计|所有成功的注册创建操作的计数。|无|
|registration.update|注册更新操作数|计数|总计|所有成功的注册更新操作的计数。|无|
|registration.get|注册读取操作数|计数|总计|所有成功的注册查询操作的计数。|无|
|registration.delete|注册删除操作数|计数|总计|所有成功的注册删除操作的计数。|无|
|incoming|传入消息数|计数|总计|所有成功的发送 API 调用的计数。 |无|
|incoming.scheduled|已发送的已安排推送通知数|计数|总计|已取消的已安排推送通知数|无|
|incoming.scheduled.cancel|已取消的已安排推送通知数|计数|总计|已取消的已安排推送通知数|无|
|scheduled.pending|挂起的已安排通知数|计数|总计|挂起的已安排通知数|无|
|installation.all|安装管理操作数|计数|总计|安装管理操作数|无|
|installation.get|获取安装操作数|计数|总计|获取安装操作数|无|
|installation.upsert|创建或更新安装操作数|计数|总计|创建或更新安装操作数|无|
|installation.patch|修补安装操作数|计数|总计|修补安装操作数|无|
|installation.delete|删除安装操作数|计数|总计|删除安装操作数|无|
|outgoing.allpns.success|成功的通知数|计数|总计|所有成功的通知的计数。|无|
|outgoing.allpns.invalidpayload|有效负载错误数|计数|总计|因为 PNS 返回了“有效负载不正确”错误而失败的推送的计数。|无|
|outgoing.allpns.pnserror|外部通知系统错误数|计数|总计|因为与 PNS 通信时遇到问题（不包括身份验证问题）而失败的推送的计数。|无|
|outgoing.allpns.channelerror|通道错误数|计数|总计|因为通道无效、没有与正确的应用相关联、受限制或已过期而失败的推送的计数。|无|
|outgoing.allpns.badorexpiredchannel|坏通道或已过期通道错误数|计数|总计|因为注册中的通道/令牌/registrationId 已过期或无效而失败的推送的计数。|无|
|outgoing.wns.success|WNS 成功的通知数|计数|总计|所有成功的通知的计数。|无|
|outgoing.wns.invalidcredentials|WNS 授权错误数（凭据无效）|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。 （Windows Live 不能识别凭据）。|无|
|outgoing.wns.badchannel|WNS 坏通道错误|计数|总计|因为注册中的 ChannelURI 不可识别（WNS 状态：404 找不到）而失败的推送的计数。|无|
|outgoing.wns.expiredchannel|WNS 已过期通道错误|计数|总计|因为 ChannelURI 已过期（WNS 状态：410 不存在）而失败的推送的计数。|无|
|outgoing.wns.throttled|WNS 受限的通知数|计数|总计|因为 WNS 限制了此应用（WNS 状态：406 不可接受）而失败的推送的计数。|无|
|outgoing.wns.tokenproviderunreachable|WNS 授权错误数（无法访问）|计数|总计|无法访问 Windows Live。|无|
|outgoing.wns.invalidtoken|WNS 授权错误数（令牌无效）|计数|总计|提供给 WNS 的令牌无效（WNS 状态：401 未经授权）。|无|
|outgoing.wns.wrongtoken|WNS 授权错误数（令牌错误）|计数|总计|提供给 WNS 的令牌有效，但它是用于另一应用程序的（WNS 状态：403 禁止访问）。 如果注册中的 ChannelURI 与另一应用相关联，则可能会发生此情况。 请检查客户端应用是否与其凭据位于通知中心内的同一应用相关联。|无|
|outgoing.wns.invalidnotificationformat|WNS 无效的通知格式|计数|总计|通知格式无效（WNS 状态：400）。 请注意，WNS 并不会拒绝所有无效的有效负载。|无|
|outgoing.wns.invalidnotificationsize|WNS 无效通知大小错误|计数|总计|通知有效负载太大（WNS 状态：413）。|无|
|outgoing.wns.channelthrottled|WNS 通道受限|计数|总计|通知因为注册中的 ChannelURI 受限而被丢弃（WNS 响应标头：X-WNS-NotificationStatus:channelThrottled）。|无|
|outgoing.wns.channeldisconnected|WNS 通道断开连接|计数|总计|通知因为注册中的 ChannelURI 受限而被丢弃（WNS 响应标头：X-WNS-DeviceConnectionStatus: disconnected）。|无|
|outgoing.wns.dropped|WNS 丢弃的通知数|计数|总计|通知因为注册中的 ChannelURI 受限而被丢弃（X-WNS-NotificationStatus 为 dropped，但 X-WNS-DeviceConnectionStatus 不是 disconnected）。|无|
|outgoing.wns.pnserror|WNS 错误数|计数|总计|与 WNS 通信时发生错误，因而未传递通知。|无|
|outgoing.wns.authenticationerror|WNS 身份验证错误数|计数|总计|与 Windows Live 通信时因凭据无效或令牌错误而发生错误，因为未传递通知。|无|
|outgoing.apns.success|APNS 成功的通知数|计数|总计|所有成功的通知的计数。|无|
|outgoing.apns.invalidcredentials|APNS 授权错误数|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无|
|outgoing.apns.badchannel|APNS 坏通道错误|计数|总计|因令牌无效而失败的推送的计数（APNS 状态代码：8）。|无|
|outgoing.apns.expiredchannel|APNS 已过期通道错误|计数|总计|由 APNS 反馈通道致其无效的令牌的计数。|无|
|outgoing.apns.invalidnotificationsize|APNS 无效通知大小错误|计数|总计|因有效负载太大而失败的推送的计数（APNS 状态代码：7）。|无|
|outgoing.apns.pnserror|APNS 错误数|计数|总计|因为与 APNS 通信时发生错误而失败的推送的计数。|无|
|outgoing.gcm.success|GCM 成功的通知数|计数|总计|所有成功的通知的计数。|无|
|outgoing.gcm.invalidcredentials|GCM 授权错误数（凭据无效）|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无|
|outgoing.gcm.badchannel|GCM 坏通道错误|计数|总计|因为注册中的 registrationId 不可识别而失败的推送的计数（GCM 结果：无效的注册）。|无|
|outgoing.gcm.expiredchannel|GCM 已过期通道错误|计数|总计|因为注册中的 registrationId 已过期而失败的推送的计数（GCM 结果：NotRegistered）。|无|
|outgoing.gcm.throttled|GCM 受限的通知数|计数|总计|因为 GCM 限制了此应用而失败的推送的计数（GCM 状态代码：501-599 或结果：不可用）。|无|
|outgoing.gcm.invalidnotificationformat|GCM 无效的通知格式|计数|总计|因为有效负载的格式不正确而失败的推送的计数（GCM 结果：InvalidDataKey 或 InvalidTtl）。|无|
|outgoing.gcm.invalidnotificationsize|GCM 无效通知大小错误|计数|总计|因有效负载太大而失败的推送的计数（GCM 结果：MessageTooBig）。|无|
|outgoing.gcm.wrongchannel|GCM 通道不正确错误|计数|总计|因为注册中的 registrationId 没有关联到当前应用而失败的推送的计数（GCM 结果：InvalidPackageName）。|无|
|outgoing.gcm.pnserror|GCM 错误数|计数|总计|因为与 GCM 通信时发生错误而失败的推送的计数。|无|
|outgoing.gcm.authenticationerror|GCM 身份验证错误数|计数|总计|因为 PNS 未接受所提供的凭据、凭据被阻止或者未在应用中正确配置 SenderId 而失败的推送的计数（GCM 结果：MismatchedSenderId）。|无|
|outgoing.mpns.success|MPNS 成功的通知数|计数|总计|所有成功的通知的计数。|无|
|outgoing.mpns.invalidcredentials|MPNS 无效的凭据|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无|
|outgoing.mpns.badchannel|MPNS 坏通道错误|计数|总计|因为注册中的 ChannelURI 不可识别（MPNS 状态：404 找不到）而失败的推送的计数。|无|
|outgoing.mpns.throttled|MPNS 受限的通知数|计数|总计|因为 MPNS 限制了此应用（MPNS 状态：406 不可接受）而失败的推送的计数。|无|
|outgoing.mpns.invalidnotificationformat|MPNS 无效的通知格式|计数|总计|因通知的有效负载太大而失败的推送的计数。|无|
|outgoing.mpns.channeldisconnected|MPNS 通道断开连接|计数|总计|因为注册中的 ChannelURI 断开连接（MPNS 状态：412 找不到）而失败的推送的计数。|无|
|outgoing.mpns.dropped|MPNS 丢弃的通知数|计数|总计|被 MPNS 丢弃的推送的计数（MPNS 响应标头：X-NotificationStatus：QueueFull 或 Suppressed）。|无|
|outgoing.mpns.pnserror|MPNS 错误数|计数|总计|因为与 MPNS 通信时发生错误而失败的推送的计数。|无|
|outgoing.mpns.authenticationerror|MPNS 身份验证错误数|计数|总计|因为 PNS 未接受所提供的凭据或者凭据被阻止而失败的推送的计数。|无|
|notificationhub.pushes|所有传出通知|计数|总计|通知中心的所有传出通知|无|
|incoming.all.requests|所有传入请求数|计数|总计|通知中心的传入的请求数总计|无|
|incoming.all.failedrequests|所有传入的失败请求数|计数|总计|通知中心的传入的失败请求数总计|无|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|Average_% Free Inodes|可用 Inode 百分比|计数|平均值|Average_% Free Inodes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Free Space|可用空间百分比|计数|平均值|Average_% Free Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Inodes|已用 Inode 百分比|计数|平均值|Average_% Used Inodes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Space|已用空间百分比|计数|平均值|Average_% Used Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Read Bytes/sec|磁盘读取字节数/秒|计数|平均值|Average_Disk Read Bytes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Reads/sec|磁盘读取数/秒|计数|平均值|Average_Disk Reads/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Transfers/sec|磁盘传输数/秒|计数|平均值|Average_Disk Transfers/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Write Bytes/sec|磁盘写入字节数/秒|计数|平均值|Average_Disk Write Bytes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Writes/sec|磁盘写入数/秒|计数|平均值|Average_Disk Writes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Megabytes|可用 MB 数|计数|平均值|Average_Free Megabytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Logical Disk Bytes/sec|逻辑磁盘字节数/秒|计数|平均值|Average_Logical Disk Bytes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Available Memory|可用内存百分比|计数|平均值|Average_% Available Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Available Swap Space|可用交换空间百分比|计数|平均值|Average_% Available Swap Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Memory|已用内存百分比|计数|平均值|Average_% Used Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Swap Space|已用交换空间百分比|计数|平均值|Average_% Used Swap Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Memory|可用内存 MB 数|计数|平均值|Average_Available MBytes Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Swap|可用交换空间 MB 数|计数|平均值|Average_Available MBytes Swap|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Reads/sec|页面读取数/秒|计数|平均值|Average_Page Reads/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Writes/sec|页面写入数/秒|计数|平均值|Average_Page Writes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pages/sec|页面数/秒|计数|平均值|Average_Pages/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used MBytes Swap Space|已用交换空间 MB 数|计数|平均值|Average_Used MBytes Swap Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used Memory MBytes|已用内存 MB 数|计数|平均值|Average_Used Memory MBytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Transmitted|已传输的字节数总计|计数|平均值|Average_Total Bytes Transmitted|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Received|已接收的字节数总计|计数|平均值|Average_Total Bytes Received|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes|字节数总计|计数|平均值|Average_Total Bytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Packets Transmitted|已传输的包数总计|计数|平均值|Average_Total Packets Transmitted|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Packets Received|已接收的包数总计|计数|平均值|Average_Total Packets Received|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Rx Errors|Rx 错误数总计|计数|平均值|Average_Total Rx Errors|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Tx Errors|Tx 错误数总计|计数|平均值|Average_Total Tx Errors|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Collisions|冲突数总计|计数|平均值|Average_Total Collisions|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. 磁盘秒数/读取|Avg. Disk sec/Read|计数|平均值|Average_Avg. 磁盘秒数/读取|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. 磁盘秒数/传输|Avg. Disk sec/Transfer|计数|平均值|Average_Avg. 磁盘秒数/传输|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. 磁盘秒数/写入|Avg. Disk sec/Write|计数|平均值|Average_Avg. 磁盘秒数/写入|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Physical Disk Bytes/sec|物理磁盘字节数/秒|计数|平均值|Average_Physical Disk Bytes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct Privileged Time|特权时间百分比|计数|平均值|Average_Pct Privileged Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct User Time|用户时间百分比|计数|平均值|Average_Pct User Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used Memory kBytes|已用内存 KB 数|计数|平均值|Average_Used Memory kBytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Virtual Shared Memory|虚拟共享内存|计数|平均值|Average_Virtual Shared Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% DPC Time|DPC 时间百分比|计数|平均值|Average_% DPC Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Idle Time|空闲时间百分比|计数|平均值|Average_% Idle Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Interrupt Time|中断时间百分比|计数|平均值|Average_% Interrupt Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% IO Wait Time|IO 等待时间百分比|计数|平均值|Average_% IO Wait Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Nice Time|良好时间百分比|计数|平均值|Average_% Nice Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Privileged Time|特权时间百分比|计数|平均值|Average_% Privileged Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Processor Time|处理器时间百分比|计数|平均值|Average_% Processor Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% User Time|用户时间百分比|计数|平均值|Average_% User Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Physical Memory|可用物理内存|计数|平均值|Average_Free Physical Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Space in Paging Files|分页文件中的可用空间|计数|平均值|Average_Free Space in Paging Files|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Virtual Memory|可用虚拟内存|计数|平均值|Average_Free Virtual Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Processes|进程|计数|平均值|Average_Processes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Size Stored In Paging Files|分页文件中存储的大小|计数|平均值|Average_Size Stored In Paging Files|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Uptime|运行时间|计数|平均值|Average_Uptime|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Users|用户|计数|平均值|Average_Users|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|计数|平均值|Average_Current Disk Queue Length|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes|可用兆字节数|计数|平均值|Average_Available MBytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Committed Bytes In Use|提交的在用字节数百分比|计数|平均值|Average_% Committed Bytes In Use|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Received/sec|收到的字节数/秒|计数|平均值|Average_Bytes Received/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Sent/sec|发送的字节数/秒|计数|平均值|Average_Bytes Sent/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Total/sec|字节总数/秒|计数|平均值|Average_Bytes Total/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Processor Queue Length|处理器队列长度|计数|平均值|Average_Processor Queue Length|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|检测信号|检测信号|计数|总计|检测信号|Computer,OSType,Version,SourceComputerId|
|更新|更新|计数|平均值|更新|Computer,Product,Classification,UpdateState,Optional,Approved|
|事件|事件|计数|平均值|事件|Source,EventLog,Computer,EventCategory,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|PrefixLatency|前缀延迟|毫秒|平均值|中间前缀延迟|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SessionAvailabilityV4|会话可用性 V4|百分比|平均值|V4 会话的可用性|ConnectionId|
|SessionAvailabilityV6|会话可用性 V6|百分比|平均值|V6 会话的可用性|ConnectionId|
|IngressTrafficRate|入口流量速率|BitsPerSecond|平均值|入口流量速率（位/秒）|ConnectionId|
|EgressTrafficRate|出口流量速率|BitsPerSecond|平均值|出口流量速率（位/秒）|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QueryDuration|查询持续时间|毫秒|平均值|上一个间隔的 DAX 查询持续时间|无维度|
|QueryPoolJobQueueLength|线程: 查询池作业队列长度|计数|平均值|查询线程池队列中的作业数。|无维度|
|qpu_high_utilization_metric|QPU 高利用率|计数|总计|最后一分钟内 QPU 高利用率，1 为高 QPU 利用率，反之为 0|无维度|
|memory_metric|内存|字节|平均值|内存。 A1 的范围为 0-3 GB，A2 为 0-5 GB，A3 为 0-10 GB，A4 为 0-25 GB，A5 为 0-50 GB，A6 为 0-100 GB|无维度|
|memory_thrashing_metric|内存抖动|百分比|平均值|平均内存抖动。|无维度|


## <a name="microsoftprojectbabylonaccounts"></a>ProjectBabylon/帐户

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AssetDistributionByClassification|按分类分类的资产分布|计数|总计|指示已分配特定分类的资产的数量，即，使用该标签进行分类。|分类、源、ResourceId|
|AssetDistributionByStorageType|按存储类型的资产分布|计数|总计|指示具有特定存储类型的资产的数量。|StorageType，ResourceId|
|CatalogActiveUsers|每日活动用户|计数|总计|每日活动用户数|ResourceId|
|CatalogUsage|按操作的用量分布|计数|总计|指示用户对目录所做的操作的数量，即访问、搜索、词汇表。|操作，ResourceId|
|NumberOfAssetsWithClassifications|至少具有一个分类的资产数量|计数|平均值|指示至少具有一个标记分类的资产的数量。|ResourceId|
|ScanCancelled|扫描已取消|计数|总计|指示取消的扫描数。|ResourceId|
|ScanCompleted|扫描已完成|计数|总计|指示已成功完成的扫描数。|ResourceId|
|ScanFailed|扫描失败|计数|总计|指示扫描失败的次数。|ResourceId|
|ScanTimeTaken|扫描花费的时间|秒|总计|指示扫描总时间（以秒为单位）。|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|计数|总计|Microsoft.Relay 的侦听程序成功连接数。|EntityName,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|计数|总计|关于 Microsoft.Relay 的侦听程序连接的客户端错误。|EntityName,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|计数|总计|关于 Microsoft.Relay 的侦听程序连接的服务器错误。|EntityName,OperationResult|
|SenderConnections-Success|SenderConnections-Success|计数|总计|Microsoft.Relay 的发送方成功连接数。|EntityName,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|计数|总计|关于 Microsoft.Relay 的发送方连接的客户端错误。|EntityName,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|计数|总计|关于 Microsoft.Relay 的发送方连接的服务器错误。|EntityName,OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|计数|总计|Microsoft.Relay 的侦听程序连接总数。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|计数|总计|Microsoft.Relay 的发送方连接请求总数。|EntityName|
|ActiveConnections|ActiveConnections|计数|总计|Microsoft.Relay 的活动连接总数。|EntityName|
|ActiveListeners|ActiveListeners|计数|总计|Microsoft.Relay 的活动侦听程序总数。|EntityName|
|BytesTransferred|BytesTransferred|计数|总计|Microsoft.Relay 的已传输字节总数。|EntityName|
|ListenerDisconnects|ListenerDisconnects|计数|总计|Microsoft.Relay 的侦听程序断开连接总数。|EntityName|
|SenderDisconnects|SenderDisconnects|计数|总计|Microsoft.Relay 的发送方断开连接总数。|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SearchLatency|搜索延迟|秒|平均值|搜索服务的平均搜索延迟|无|
|SearchQueriesPerSecond|每秒搜索查询数|每秒计数|平均值|搜索服务的每秒搜索查询数|无|
|ThrottledSearchQueriesPercentage|限制的搜索查询百分比|百分比|平均值|为搜索服务限制的搜索查询百分比|无|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的请求数|计数|总计|命名空间的成功请求总数|EntityName,OperationResult|
|ServerErrors|服务器错误数。|计数|总计|Microsoft.ServiceBus 的服务器错误数。|EntityName,OperationResult|
|UserErrors|用户错误数。|计数|总计|Microsoft.ServiceBus 的用户错误数。|EntityName,OperationResult|
|ThrottledRequests|限制的请求数。|计数|总计|Microsoft.ServiceBus 限制的请求数。|EntityName,OperationResult|
|IncomingRequests|传入请求数|计数|总计|Microsoft.ServiceBus 传入的请求数。|EntityName|
|IncomingMessages|传入消息数|计数|总计|Microsoft.ServiceBus 传入的消息数。|EntityName|
|OutgoingMessages|传出消息数|计数|总计|Microsoft.ServiceBus 传出的消息数。|EntityName|
|ActiveConnections|ActiveConnections|计数|总计|Microsoft.ServiceBus 的活动连接总数。|无|
|ConnectionsOpened|打开的连接数。|计数|平均值|Microsoft.ServiceBus 打开的连接数。|EntityName|
|ConnectionsClosed|已关闭的连接数。|计数|平均值|Microsoft.ServiceBus 已关闭的连接数。|EntityName|
|大小|大小|字节|平均值|队列/主题的大小（以字节为单位）。|EntityName|
|消息|队列/主题中的消息计数。|计数|平均值|队列/主题中的消息计数。|EntityName|
|ActiveMessages|队列/主题中的活动消息计数。|计数|平均值|队列/主题中的活动消息计数。|EntityName|
|DeadletteredMessages|队列/主题中的死信消息计数。|计数|平均值|队列/主题中的死信消息计数。|EntityName|
|ScheduledMessages|队列/主题中的计划消息计数。|计数|平均值|队列/主题中的计划消息计数。|EntityName|
|NamespaceCpuUsage|CPU|百分比|最大值|服务总线高级命名空间 CPU 使用率指标。|副本|
|NamespaceMemoryUsage|内存用量|百分比|最大值|服务总线高级命名空间内存使用率指标。|副本|
|CPUXNS|CPU（已弃用）|百分比|最大值|服务总线高级命名空间 CPU 使用率指标。 此指标已弃用。 请改用 CPU 指标 (NamespaceCpuUsage)。|副本|
|WSXNS|内存使用率（已弃用）|百分比|最大值|服务总线高级命名空间内存使用率指标。 此指标已弃用。 请改用内存使用率 (NamespaceMemoryUsage) 指标。|副本|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|计数|平均值|分配给此容器的 CPU（毫核心）|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|字节|平均值|分配给此容器的内存 (MB)|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ActualCpu|ActualCpu|计数|平均值|实际 CPU 用量（毫核心）|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ActualMemory|ActualMemory|字节|平均值|实际内存用量 (MB)|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|CpuUtilization|CpuUtilization|百分比|平均值|此容器的 CPU 利用率，以 AllocatedCpu 的百分比表示|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|百分比|平均值|此容器的 CPU 利用率，以 AllocatedCpu 的百分比表示|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|计数|平均值|Service Fabric 网格应用程序的状态|ApplicationName,Status|
|ServiceStatus|ServiceStatus|计数|平均值|Service Fabric 网格应用程序中某个服务的运行状态|ApplicationName,Status,ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|计数|平均值|Service Fabric 网格应用程序中某个服务副本的运行状态|ApplicationName,Status,ServiceName,ServiceReplicaName|
|ContainerStatus|ContainerStatus|计数|平均值|Service Fabric 网格应用程序中的容器状态|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName,Status|
|RestartCount|RestartCount|计数|平均值|Service Fabric 网格应用程序中某个容器的重启计数|ApplicationName,Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ConnectionCount|连接计数|计数|最大值|用户连接量。|终结点|
|MessageCount|消息计数|计数|总计|消息总量。|无|
|InboundTraffic|入站流量|字节|总计|服务的入站流量|无|
|OutboundTraffic|出站流量|字节|总计|服务的出站流量|无|
|UserErrors|用户错误数|百分比|最大值|用户错误数的百分比|无|
|SystemErrors|系统错误数|百分比|最大值|系统错误数的百分比|无|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无|
|log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比。 不适用于数据仓库。|无|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比。 适用于基于 DTU 的数据库。|无|
|存储|已用数据空间|字节|最大值|已用数据空间。 不适用于数据仓库。|无|
|connection_successful|成功的连接数|计数|总计|成功的连接数|无|
|connection_failed|失败的连接数|计数|总计|失败的连接数|无|
|blocked_by_firewall|被防火墙阻止|计数|总计|被防火墙阻止|无|
|deadlock|死锁数|计数|总计|死锁数。 不适用于数据仓库。|无|
|storage_percent|已用数据空间百分比|百分比|最大值|已用数据空间百分比。 不适用于数据仓库或超大规模数据库。|无|
|xtp_storage_percent|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比。 不适用于数据仓库。|无|
|workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比。 不适用于数据仓库。|无|
|sessions_percent|会话百分比|百分比|平均值|会话百分比。 不适用于数据仓库。|无|
|dtu_limit|DTU 限制|计数|平均值|DTU 限制。 适用于基于 DTU 的数据库。|无|
|dtu_used|已用的 DTU|计数|平均值|已用 DTU。 适用于基于 DTU 的数据库。|无|
|cpu_limit|CPU 限制|计数|平均值|CPU 限制。 适用于基于 vCore 的数据库。|无|
|cpu_used|已用 CPU|计数|平均值|已用 CPU。 适用于基于 vCore 的数据库。|无|
|dwu_limit|DWU 限制|计数|最大值|DWU 限制。 仅适用于数据仓库。|无|
|dwu_consumption_percent|DWU 百分比|百分比|最大值|DWU 百分比。 仅适用于数据仓库。|无|
|dwu_used|已用的 DWU|计数|最大值|已用 DWU。 仅适用于数据仓库。|无|
|cache_hit_percent|缓存命中百分比|百分比|最大值|缓存命中百分比。 仅适用于数据仓库。|无|
|cache_used_percent|缓存使用百分比|百分比|最大值|缓存使用百分比。 仅适用于数据仓库。|无|
|sqlserver_process_core_percent<sup>1</sup> |SQL Server 进程核心百分比|百分比|最大值|SQL Server 进程的 CPU 使用率百分比（由操作系统度量）。|无|
|sqlserver_process_memory_percent<sup>1</sup> |SQL Server 进程内存百分比|百分比|最大值|SQL Server 进程的内存使用率百分比（由操作系统度量）。|无|
|tempdb_data_size<sup>2</sup> |Tempdb 数据文件大小 (KB)|计数|最大值|Tempdb 数据文件大小 (KB)。|无|
|tempdb_log_size<sup>2</sup> |Tempdb 日志文件大小 (KB)|计数|最大值|Tempdb 日志文件大小 (KB)。|无|
|tempdb_log_used_percent<sup>2</sup> |Tempdb 日志已用百分比|百分比|最大值|Tempdb 日志已用百分比。|无|
|local_tempdb_usage_percent|本地 tempdb 百分比|百分比|平均值|本地 tempdb 百分比。 仅适用于数据仓库。|无|
|app_cpu_billed|计费的应用 CPU|计数|总计|计费的应用 CPU。 适用于无服务器数据库。|无|
|app_cpu_percent|应用 CPU 百分比|百分比|平均值|应用 CPU 百分比。 适用于无服务器数据库。|无|
|app_memory_percent|应用内存百分比|百分比|平均值|应用内存百分比。 适用于无服务器数据库。|无|
|allocated_data_storage|已分配的数据空间|字节|平均值|已分配的数据存储。 不适用于数据仓库。|无|
|memory_usage_percent|内存百分比|百分比|最大值|内存百分比。 仅适用于数据仓库。|无|
|dw_backup_size_gb|数据存储大小|计数|总计|数据存储大小由你的数据和事务日志的大小组成。 此指标将计入帐单的 "存储" 部分。 仅适用于数据仓库。|无|
|dw_snapshot_size_gb|快照存储大小|计数|总计|快照存储大小是由快照捕获的增量更改的大小，以创建用户定义的还原点和自动还原点。 此指标将计入帐单的 "存储" 部分。 仅适用于数据仓库。|无|
|dw_geosnapshot_size_gb|灾难恢复存储大小|计数|总计|灾难恢复存储大小在帐单中反映为 "灾难恢复存储"。 仅适用于数据仓库。|无|
|wlg_allocation_relative_to_system_percent|按系统百分比列出的工作负荷组分配|百分比|最大值|每个工作负荷组相对于整个系统的资源分配百分比。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|按上限资源百分比分配的工作负荷组|百分比|最大值|每个工作负荷组相对于指定 cap 资源的资源分配百分比。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|工作负荷组活动查询|计数|总计|工作负荷组内的活动查询。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|工作负荷组排队查询|计数|总计|工作负荷组内排队的查询。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|active_queries|活动查询|计数|总计|跨所有工作负荷组的活动查询。 仅适用于数据仓库。|无|
|queued_queries|排队的查询|计数|总计|跨所有工作负荷组的排队查询。 仅适用于数据仓库。|无|
|wlg_active_queries_timeouts|工作负荷组查询超时|计数|总计|工作负荷组的查询已超时。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|有效最小资源百分比|百分比|最大值|为工作负荷组预留和隔离的资源的最小百分比，考虑最小服务级别。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|有效上限资源百分比|百分比|最大值|对工作负荷组所允许的资源百分比进行硬性限制，考虑为其他工作负荷组分配的有效的最小资源百分比。 仅适用于数据仓库。|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|完整备份存储大小|字节|最大值|累积的完整备份存储大小。 适用于基于 vCore 的数据库。 不适用于超大规模数据库。|无|
|diff_backup_size_bytes|差异备份存储大小|字节|最大值|累积的差异备份存储大小。 适用于基于 vCore 的数据库。 不适用于超大规模数据库。|无|
|log_backup_size_bytes|日志备份存储大小|字节|最大值|累积的日志备份存储大小。 适用于基于 vCore 和超大规模的数据库。|无|
|snapshot_backup_size_bytes|快照备份存储大小|字节|最大值|累积快照备份存储大小。 适用于超大规模数据库。|无|
|base_blob_size_bytes|基本 blob 存储大小|字节|最大值|基本 blob 存储大小。 适用于超大规模数据库。|无|

<sup>1</sup> 此指标可用于这样的数据库：使用具有 2 vCore 或更多 vCore 的 vCore 购买模型，或使用具有 200 DTU 或更多 DTU 的基于 DTU 的购买模型。 

<sup>2</sup> 此指标可用于这样的数据库：使用具有 2 vCore 或更多 vCore 的 vCore 购买模型，或使用具有 200 DTU 或更多 DTU 的基于 DTU 的购买模型。 此指标目前不可用于超大规模数据库或数据仓库。

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|无|
|database_cpu_percent|CPU 百分比|百分比|平均值|CPU 百分比|DatabaseResourceId|
|physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|无|
|database_physical_data_read_percent|数据 IO 百分比|百分比|平均值|数据 IO 百分比|DatabaseResourceId|
|log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比|无|
|database_log_write_percent|日志 IO 百分比|百分比|平均值|日志 IO 百分比|DatabaseResourceId|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比。 适用于基于 DTU 的弹性池。|无|
|database_dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId|
|storage_percent|已用数据空间百分比|百分比|平均值|已用数据空间百分比|无|
|workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比|无|
|database_workers_percent|辅助角色百分比|百分比|平均值|辅助角色百分比|DatabaseResourceId|
|sessions_percent|会话百分比|百分比|平均值|会话百分比|无|
|database_sessions_percent|会话百分比|百分比|平均值|会话百分比|DatabaseResourceId|
|eDTU_limit|eDTU 限制|计数|平均值|eDTU 限制。 适用于基于 DTU 的弹性池。|无|
|storage_limit|数据最大大小|字节|平均值|数据最大大小|无|
|eDTU_used|已用的 eDTU|计数|平均值|已用 eDTU。 适用于基于 DTU 的弹性池。|无|
|database_eDTU_used|已用的 eDTU|计数|平均值|已用的 eDTU|DatabaseResourceId|
|storage_used|已用数据空间|字节|平均值|已用数据空间|无|
|database_storage_used|已用数据空间|字节|平均值|已用数据空间|DatabaseResourceId|
|xtp_storage_percent|内存中 OLTP 存储百分比|百分比|平均值|内存中 OLTP 存储百分比|无|
|cpu_limit|CPU 限制|计数|平均值|CPU 限制。 适用于基于 vCore 的弹性池。|无|
|database_cpu_limit|CPU 限制|计数|平均值|CPU 限制|DatabaseResourceId|
|cpu_used|已用 CPU|计数|平均值|已用 CPU。 适用于基于 vCore 的弹性池。|无|
|database_cpu_used|已用 CPU|计数|平均值|已用 CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server 进程核心百分比|百分比|最大值|SQL Server 进程的 CPU 使用率百分比（由操作系统度量）。 适用于弹性池。 |无|
|sqlserver_process_memory_percent<sup>1</sup>|SQL Server 进程内存百分比|百分比|最大值|SQL Server 进程的内存使用率百分比（由操作系统度量）。 适用于弹性池。 |无|
|tempdb_data_size<sup>2</sup>|Tempdb 数据文件大小 (KB)|计数|最大值|Tempdb 数据文件大小 (KB)。|无|
|tempdb_log_size<sup>2</sup>|Tempdb 日志文件大小 (KB)|计数|最大值|Tempdb 日志文件大小 (KB)。 |无|
|tempdb_log_used_percent<sup>2</sup>|Tempdb 日志已用百分比|百分比|最大值|Tempdb 日志已用百分比。|无|
|allocated_data_storage|已分配的数据空间|字节|平均值|已分配的数据空间|无|
|database_allocated_data_storage|已分配的数据空间|字节|平均值|已分配的数据空间|DatabaseResourceId|
|allocated_data_storage_percent|分配的数据空间百分比|百分比|最大值|分配的数据空间百分比|无|

<sup>1</sup> 此指标可用于这样的数据库：使用具有 2 vCore 或更多 vCore 的 vCore 购买模型，或使用具有 200 DTU 或更多 DTU 的基于 DTU 的购买模型。 

<sup>2</sup> 此指标可用于这样的数据库：使用具有 2 vCore 或更多 vCore 的 vCore 购买模型，或使用具有 200 DTU 或更多 DTU 的基于 DTU 的购买模型。 此指标当前不适用于超大规模数据库。


## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 百分比|百分比|平均值|DTU 百分比|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|已用数据空间|字节|平均值|已用数据空间|ElasticPoolResourceId|
|database_storage_used|已用数据空间|字节|平均值|已用数据空间|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|已用的 DTU|计数|平均值|已用的 DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|virtual_core_count|虚拟核心计数|计数|平均值|虚拟核心计数|无|
|avg_cpu_percent|CPU 平均百分比|百分比|平均值|CPU 平均百分比|无|
|reserved_storage_mb|预留的存储空间|计数|平均值|预留的存储空间|无|
|storage_space_used_mb|已使用的存储空间|计数|平均值|已使用的存储空间|无|
|io_requests|IO 请求计数|计数|平均值|IO 请求计数|无|
|io_bytes_read|已读取的 IO 字节数|字节|平均值|已读取的 IO 字节数|无|
|io_bytes_written|已写入的 IO 字节数|字节|平均值|已写入的 IO 字节数|无|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|UsedCapacity|已用容量|字节|平均值|帐户使用的容量|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|字节|平均值|存储帐户的 Blob 服务使用的存储量（以字节为单位）。|BlobType,Tier|
|BlobCount|Blob 计数|计数|平均值|存储帐户的 Blob 服务中的 Blob 数。|BlobType,Tier|
|ContainerCount|Blob 容器计数|计数|平均值|存储帐户的 Blob 服务中的容器数。|无|
|IndexCapacity|索引容量|字节|平均值|ADLS Gen2 (层次结构)索引所使用的存储量(按字节计)。|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|TableCapacity|表容量|字节|平均值|存储帐户的表服务使用的存储量（以字节为单位）。|无|
|TableCount|表计数|计数|平均值|存储帐户的表服务中的表数。|无|
|TableEntityCount|表实体计数|计数|平均值|存储帐户的表服务中的表实体数。|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|FileCapacity|文件容量|字节|平均值|存储帐户的文件服务使用的存储量（以字节为单位）。|FileShare|
|FileCount|文件计数|计数|平均值|存储帐户的文件服务中的文件数。|FileShare|
|FileShareCount|文件共享计数|计数|平均值|存储帐户的文件服务中的文件共享数。|无|
|FileShareSnapshotCount|文件共享快照计数。|计数|平均值|存储帐户的文件服务中的共享上存在的快照数。|FileShare|
|FileShareSnapshotSize|文件共享快照大小|字节|平均值|存储帐户的文件服务中的快照使用的存储量（以字节为单位）。|FileShare|
|FileShareQuota|文件共享配额大小|字节|平均值|Azure 文件服务可使用的存储量上限（以字节为单位）。|FileShare|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication,FileShare|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication,FileShare|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication,FileShare|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication,FileShare|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication,FileShare|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|QueueCapacity|队列容量|字节|平均值|存储帐户的队列服务使用的存储量（以字节为单位）。|无|
|QueueCount|队列计数|计数|平均值|存储帐户的队列服务中的队列数。|无|
|QueueMessageCount|队列消息计数|计数|平均值|存储帐户的队列服务中的队列消息的大致数目。|无|
|事务|事务|计数|总计|向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。|ResponseType,GeoType,ApiName,Authentication|
|流入量|流入量|字节|总计|流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。|GeoType,ApiName,Authentication|
|流出量|流出量|字节|总计|流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。|GeoType,ApiName,Authentication|
|SuccessServerLatency|成功服务器延迟|毫秒|平均值|由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。|GeoType,ApiName,Authentication|
|SuccessE2ELatency|成功 E2E 延迟|毫秒|平均值|向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。|GeoType,ApiName,Authentication|
|可用性|可用性|百分比|平均值|存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。|GeoType,ApiName,Authentication|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ClientIOPS|总客户端 IOPS|计数|平均值|由缓存处理的客户端文件操作的速率。|无|
|ClientLatency|平均客户端延迟|毫秒|平均值|存储缓存中客户端文件操作的平均延迟。|无|
|ClientReadIOPS|客户端读取 IOPS|每秒计数|平均值|每秒的客户端读取操作数。|无|
|ClientReadThroughput|平均缓存读取吞吐量|每秒字节数|平均值|客户端读取数据传输速率。|无|
|ClientWriteIOPS|客户端写入 IOPS|每秒计数|平均值|每秒的客户端写入操作数。|无|
|ClientWriteThroughput|平均缓存写入吞吐量|每秒字节数|平均值|客户端写入数据传输速率。|无|
|ClientMetadataReadIOPS|客户端元数据读取 IOPS|每秒计数|平均值|发送到缓存的客户端文件操作（不包括不会修改持久状态的数据读取）的速率。|无|
|ClientMetadataWriteIOPS|客户端元数据写入 IOPS|每秒计数|平均值|发送到缓存的客户端文件操作（不包括修改持久状态的数据写入）的速率。|无|
|ClientLockIOPS|客户端锁定 IOPS|每秒计数|平均值|每秒的客户端文件锁定操作数。|无|
|StorageTargetHealth|存储目标运行状况|计数|平均值|缓存和存储目标之间的连接测试的布尔结果。|无|
|运行时间|运行时间|计数|平均值|缓存和监视系统之间的连接测试的布尔结果。|无|
|StorageTargetIOPS|总 StorageTarget IOPS|计数|平均值|缓存发送到特定 StorageTarget 的所有文件操作的速率。|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget 写入 IOPS|计数|平均值|缓存发送到特定 StorageTarget 的文件写入操作的速率。|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget 异步写入吞吐量|每秒字节数|平均值|缓存以异步方式向特定 StorageTarget 写入数据的速率。 这些写入是不会导致客户端阻塞的机会性写入。|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget 同步写入吞吐量|每秒字节数|平均值|缓存以同步方式向特定 StorageTarget 写入数据的速率。 这些写入是导致客户端阻塞的写入操作。|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget 总写入吞吐量|每秒字节数|平均值|缓存将数据写入特定 StorageTarget 的总速率。|StorageTarget|
|StorageTargetLatency|StorageTarget 延迟|毫秒|平均值|缓存发送到特定 StorageTarget 的所有文件操作的平均往返延迟。|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget 元数据读取 IOPS|每秒计数|平均值|缓存发送到特定 StorageTarget 且不修改永久性状态的文件操作（不包括读取操作）的速率。|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget 元数据写入 IOPS|每秒计数|平均值|缓存发送到特定 StorageTarget 且不修改永久性状态的文件操作（不包括写入操作）的速率。|StorageTarget|
|StorageTargetReadIOPS|StorageTarget 读取 IOPS|每秒计数|平均值|缓存发送到特定 StorageTarget 的文件读取操作的速率。|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget 提前读取吞吐量|每秒字节数|平均值|缓存投机地从 StorageTarget 中读取数据的速率。|StorageTarget|
|StorageTargetFillThroughput|StorageTarget 填充吞吐量|每秒字节数|平均值|缓存从 StorageTarget 读取数据以处理缓存失误的速率。|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget 总读取吞吐量|每秒字节数|平均值|缓存从特定 StorageTarget 中读取数据的总速率。|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ServerSyncSessionResult|同步会话结果|计数|平均值|每当服务器终结点在云终结点中成功完成同步会话时，记录值 1 的指标|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|已同步的文件数|计数|总计|已同步的文件计数|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|未同步的文件|计数|总计|无法同步的文件计数|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|同步的字节数|字节|总计|为同步会话传输的总文件大小|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|服务器联机状态|计数|最大值|每当已注册的服务器在云终结点中成功记录检测信号时，记录值 1 的指标|ServerName|
|StorageSyncRecallIOTotalSizeBytes|云分层回调|字节|总计|服务器重新调用的数据总大小|ServerName|
|StorageSyncRecalledTotalNetworkBytes|云分层重调大小|字节|总计|回调的数据的大小|SyncGroupName,ServerName|
|StorageSyncRecallThroughputBytesPerSecond|云分层重调吞吐量|每秒字节数|平均值|数据重调吞吐量大小|SyncGroupName,ServerName|
|StorageSyncRecalledNetworkBytesByApplication|应用程序的云分层重调大小|字节|总计|应用程序重调的数据大小|SyncGroupName,ServerName,ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|已同步的文件数|计数|总计|已同步的文件计数|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|未同步的文件|计数|总计|无法同步的文件计数|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|同步的字节数|字节|总计|为同步会话传输的总文件大小|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|已同步的文件数|计数|总计|已同步的文件计数|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|未同步的文件|计数|总计|无法同步的文件计数|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|同步的字节数|字节|总计|为同步会话传输的总文件大小|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ServerHeartbeat|服务器联机状态|计数|最大值|每当已注册的服务器在云终结点中成功记录检测信号时，记录值 1 的指标|ServerResourceId,ServerName|
|ServerRecallIOTotalSizeBytes|云分层回调|字节|总计|服务器重新调用的数据总大小|ServerResourceId,ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|ResourceUtilization|流单元利用率 %|百分比|最大值|流单元利用率 %|LogicalName,PartitionId|
|InputEvents|输入事件数|计数|总计|输入事件数|LogicalName,PartitionId|
|InputEventBytes|输入事件字节数|字节|总计|输入事件字节数|LogicalName,PartitionId|
|LateInputEvents|延迟输入事件数|计数|总计|延迟输入事件数|LogicalName,PartitionId|
|OutputEvents|输出事件数|计数|总计|输出事件数|LogicalName,PartitionId|
|ConversionErrors|数据转换错误数|计数|总计|数据转换错误数|LogicalName,PartitionId|
|错误|运行时错误|计数|总计|运行时错误|LogicalName,PartitionId|
|DroppedOrAdjustedEvents|失序事件数|计数|总计|失序事件数|LogicalName,PartitionId|
|AMLCalloutRequests|函数请求数|计数|总计|函数请求数|LogicalName,PartitionId|
|AMLCalloutFailedRequests|失败的函数请求数|计数|总计|失败的函数请求数|LogicalName,PartitionId|
|AMLCalloutInputEvents|函数事件数|计数|总计|函数事件数|LogicalName,PartitionId|
|DeserializationError|输入反序列化错误|计数|总计|输入反序列化错误|LogicalName,PartitionId|
|EarlyInputEvents|早期输入事件数|计数|总计|早期输入事件数|LogicalName,PartitionId|
|OutputWatermarkDelaySeconds|水印延迟|秒|最大值|水印延迟|LogicalName,PartitionId|
|InputEventsSourcesBacklogged|积压的输入事件数|计数|最大值|积压的输入事件数|LogicalName,PartitionId|
|InputEventsSourcesPerSecond|收到的输入源数|计数|总计|收到的输入源数|LogicalName,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|管道运行已结束|计数|总计|成功、失败或取消的业务流程管道运行计数|Result、FailureType、管道|
|OrchestrationActivityRunsEnded|已结束活动运行|计数|总计|已成功、失败或取消的业务流程活动的计数|Result，FailureType，Activity，ActivityType，管道|
|OrchestrationTriggersEnded|触发器结束|计数|总计|成功、失败或取消的业务流程触发器的计数|Result、FailureType、Trigger|
|SQLOnDemandLoginAttempts|登录尝试|计数|总计|成功或失败的登录尝试计数|结果|
|SQLOnDemandQueriesEnded|查询结束|计数|总计|已成功、失败或已取消的查询计数|结果|
|SQLOnDemandQueryProcessedBytes|已处理的数据|字节|总计|查询处理的数据量|无|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Synapse/工作区/bigDataPools

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|SparkJobsEnded|结束应用程序|计数|总计|已结束的应用程序计数|JobType、Jobresult.txt|
|CoresCapacity|核心容量|计数|最大值|核心容量|无|
|MemoryCapacityGB|内存容量（GB）|计数|最大值|内存容量（GB）|无|

## <a name="microsoftsynapseworkspacessqlpools"></a>Synapse/工作区/sqlPools

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|DWULimit|DWU 限制|计数|最大值|SQL 池的服务级别目标|无|
|DWUUsed|已用的 DWU|计数|最大值|表示在 SQL 池中使用的高级表示形式。 按 DWU 限制 * DWU 百分比度量|无|
|DWUUsedPercent|DWU 已用百分比|百分比|最大值|表示在 SQL 池中使用的高级表示形式。 使用 CPU 百分比和数据 IO 百分比之间的最大值进行度量|无|
|ConnectionsBlockedByFirewall|防火墙阻止的连接|计数|总计|防火墙规则阻止的连接数。 重新访问 SQL 池的访问控制策略，并监视这些连接（如果计数较高）|无|
|AdaptiveCacheHitPercent|自适应缓存命中百分比|百分比|最大值|度量工作负荷使用自适应缓存的程度。 使用此指标和缓存命中率百分比度量来确定是要扩展以增加容量还是重新运行工作负荷以冻结缓存|无|
|AdaptiveCacheUsedPercent|自适应缓存已用百分比|百分比|最大值|度量工作负荷使用自适应缓存的程度。 使用此指标和缓存已用百分比度量来确定是要扩展以增加容量还是重新运行工作负荷以冻结缓存|无|
|LocalTempDBUsedPercent|本地 tempdb 已用百分比|百分比|最大值|跨所有计算节点的本地 tempdb 利用率-每五分钟发出一次值|无|
|MemoryUsedPercent|已用内存百分比|百分比|最大值|SQL 池中所有节点的内存使用率|无|
|连接|连接|计数|总计|SQL 池的总登录数计数|结果|
|WLGActiveQueries|工作负荷组活动查询|计数|总计|工作负荷组内的活动查询。 使用未筛选的此指标并撤消显示在系统上运行的所有活动查询|IsUserDefined、WorkloadGroup|
|WLGActiveQueriesTimeouts|工作负荷组查询超时|计数|总计|查询已超时的工作负荷组。此指标报告的查询超时仅在开始执行查询后（不包括由于锁定或资源等待而导致的等待时间）|IsUserDefined、WorkloadGroup|
|WLGAllocationBySystemPercent|按系统百分比列出的工作负荷组分配|百分比|最大值|资源相对于整个系统的百分比分配|IsUserDefined、WorkloadGroup|
|WLGAllocationByMaxResourcePercent|按最大资源百分比列出的工作负荷组分配|百分比|最大值|显示相对于每个工作负荷组的有效上限资源百分比的资源分配百分比。 此指标提供工作负荷组的有效利用率|IsUserDefined、WorkloadGroup|
|WLGEffectiveCapResourcePercent|有效上限资源百分比|百分比|最大值|工作负荷组的有效上限资源百分比。 如果有其他工作负荷组 min_percentage_resource > 0，effective_cap_percentage_resource 按比例降低|IsUserDefined、WorkloadGroup|
|wlg_effective_min_resource_percent|有效最小资源百分比|百分比|最小值|考虑服务级别和工作负荷组设置所允许的有效的最小资源百分比设置。 可在较低的服务级别调整有效 min_percentage_resource|IsUserDefined、WorkloadGroup|
|WLGQueuedQueries|工作负荷组排队查询|计数|总计|达到最大并发限制后排队的请求累计计数|IsUserDefined、WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|IngressReceivedMessages|入口收到的消息数|计数|总计|从所有事件中心或 IoT 中心事件源读取的消息的计数|无|
|IngressReceivedInvalidMessages|入口收到的无效消息数|计数|总计|从所有事件中心或 IoT 中心事件源读取的无效消息的计数|无|
|IngressReceivedBytes|入口收到的字节数|字节|总计|从所有事件源读取的字节数的计数|无|
|IngressStoredBytes|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|无|
|IngressStoredEvents|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|无|
|IngressReceivedMessagesTimeLag|入口收到的消息数时间延迟|秒|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|无|
|IngressReceivedMessagesCountLag|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|无|
|WarmStorageMaxProperties|暖存储最大属性数|计数|最大值|S1/S2 SKU 环境允许使用的最大属性数，以及 PAYG SKU 暖存储允许的最大属性数|无|
|WarmStorageUsedProperties|暖存储已用属性数 |计数|最大值|S1/S2 SKU 环境已使用的属性数，以及 PAYG SKU 暖存储已使用的属性数|无|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|IngressReceivedMessages|入口收到的消息数|计数|总计|从事件源中读取的消息的计数|无|
|IngressReceivedInvalidMessages|入口收到的无效消息数|计数|总计|从事件源中读取的无效消息的计数|无|
|IngressReceivedBytes|入口收到的字节数|字节|总计|从事件源读取的字节数的计数|无|
|IngressStoredBytes|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|无|
|IngressStoredEvents|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|无|
|IngressReceivedMessagesTimeLag|入口收到的消息数时间延迟|秒|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|无|
|IngressReceivedMessagesCountLag|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|无|
|WarmStorageMaxProperties|暖存储最大属性数|计数|最大值|S1/S2 SKU 环境允许使用的最大属性数，以及 PAYG SKU 暖存储允许的最大属性数|无|
|WarmStorageUsedProperties|暖存储已用属性数 |计数|最大值|S1/S2 SKU 环境已使用的属性数，以及 PAYG SKU 暖存储已使用的属性数|无|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|每秒字节数|平均值|由于读取操作超过采样期限而限制的平均磁盘吞吐量。|无|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|每秒字节数|平均值|由于写入操作超过采样期限而限制的平均磁盘吞吐量。|无|
|磁盘读取字节数|磁盘读取字节数|字节|总计|由于读取操作超过采样期限而限制的总磁盘吞吐量。|无|
|磁盘写入字节数|磁盘写入字节数|字节|总计|由于写入操作超过采样期限而限制的总磁盘吞吐量。|无|
|DiskReadOperations|磁盘读取操作次数|计数|总计|上一个采样周期的 IO 读取操作次数。 请注意，这些操作的大小可能是可变的。|无|
|DiskWriteOperations|磁盘写入操作次数|计数|总计|上一个采样周期的 IO 写入操作次数。 请注意，这些操作的大小可能是可变的。|无|
|磁盘读取操作次数/秒|磁盘读取操作次数/秒|每秒计数|平均值|上一个采样周期的平均 IO 读取操作次数。 请注意，这些操作的大小可能是可变的。|无|
|磁盘写入操作次数/秒|磁盘写入操作次数/秒|每秒计数|平均值|上一个采样周期的平均 IO 写入操作次数。 请注意，这些操作的大小可能是可变的。|无|
|DiskReadLatency|磁盘读取延迟|毫秒|平均值|总读取延迟。 设备和内核读取延迟之和。|无|
|DiskWriteLatency|磁盘写入延迟|毫秒|平均值|总写入延迟。 设备和内核写入延迟之和。|无|
|NetworkInBytesPerSecond|网络输入字节数/秒|每秒字节数|平均值|收到的流量的平均网络吞吐量。|无|
|NetworkOutBytesPerSecond|网络输出字节数/秒|每秒字节数|平均值|传输的流量的平均网络吞吐量。|无|
|网络传入|网络传入|字节|总计|收到的流量的总网络吞吐量。|无|
|网络传出|网络传出|字节|总计|传输的流量的总网络吞吐量。|无|
|MemoryUsed|已用内存量|字节|平均值|VM 已使用的计算机内存量。|无|
|MemoryGranted|授予的内存量|字节|平均值|由主机授予 VM 的内存量。 在至少使用主机一次之前，不会向主机授予内存；如果 VMkernel 需要内存，则授予的内存可能会换出或消逝。|无|
|MemoryActive|活动的内存量|字节|平均值|VM 在过去的小段时间范围内使用的内存量。 这是 VM 当前所需的“真正”内存量。 此外，未使用的内存可能会换出或消逝，而不会影响来宾的性能。|无|
|CPU 百分比|CPU 百分比|百分比|平均值|CPU 利用率。 此值按 100% 的量（表示系统上的所有处理器核心）报告。 例如，使用四核系统的 50% CPU 的双向 VM 将完全占用两个核心。|无|
|PercentageCpuReady|已就绪 CPU 百分比|毫秒|总计|就绪时间是指在过去的更新间隔内，等待 CPU 可供使用所花费的时间。|无|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|
|DiskQueueLength|磁盘队列长度|计数|平均值|磁盘队列长度|实例|
|HttpQueueLength|Http 队列长度|计数|平均值|Http 队列长度|实例|
|BytesReceived|数据输入|字节|总计|数据输入|实例|
|BytesSent|数据输出|字节|总计|数据输出|实例|
|TcpSynSent|发送的 TCP Syn|计数|平均值|发送的 TCP Syn|实例|
|TcpSynReceived|收到的 TCP Syn|计数|平均值|收到的 TCP Syn|实例|
|TcpEstablished|已建立的 TCP|计数|平均值|已建立的 TCP|实例|
|TcpFinWait1|TCP Fin 等待 1|计数|平均值|TCP Fin 等待 1|实例|
|TcpFinWait2|TCP Fin 等待 2|计数|平均值|TCP Fin 等待 2|实例|
|TcpClosing|TCP 正在关闭|计数|平均值|TCP 正在关闭|实例|
|TcpCloseWait|TCP 关闭等待|计数|平均值|TCP 关闭等待|实例|
|TcpLastAck|TCP 上一次的 Ack|计数|平均值|TCP 上一次的 Ack|实例|
|TcpTimeWait|TCP 时间等待|计数|平均值|TCP 时间等待|实例|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites（不包括 Functions） 

> [!NOTE]
> **文件系统使用情况**是一个向全球推出的新指标，除非我们已将你加入专用预览版的允许列表，否则你不会获得数据。

> [!IMPORTANT]
> 将弃用**平均响应时间**，以避免与指标聚合混淆。 使用**响应时间**作为替代。

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuTime|CPU 时间|秒|总计|CPU 时间|实例|
|请求|请求|计数|总计|Requests|实例|
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
|HttpResponseTime|响应时间|秒|总计|响应时间|实例|
|AverageResponseTime|平均响应时间（不推荐使用）|秒|平均值|平均响应时间|实例|
|AppConnections|连接|计数|平均值|连接|实例|
|控点|句柄计数|计数|平均值|句柄计数|实例|
|线程|线程计数|计数|平均值|线程计数|实例|
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
|HealthCheckStatus|运行状况检查状态|计数|平均值|运行状况检查状态|实例|
|FileSystemUsage|文件系统使用情况|字节|平均值|文件系统使用情况|无|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

> [!NOTE]
> **文件系统使用情况**是一个向全球推出的新指标，除非我们已将你加入专用预览版的允许列表，否则你不会获得数据。

|指标|指标显示名称|单位|聚合类型|说明|维度|
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
|HealthCheckStatus|运行状况检查状态|计数|平均值|运行状况检查状态|实例|
|FileSystemUsage|文件系统使用情况|字节|平均值|文件系统使用情况|无|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|CpuTime|CPU 时间|秒|总计|CPU 时间|实例|
|请求|请求|计数|总计|Requests|实例|
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
|HttpResponseTime|响应时间|秒|平均值|响应时间|实例|
|FunctionExecutionUnits|函数执行单位数|计数|总计|函数执行单位数|实例|
|FunctionExecutionCount|函数执行计数|计数|总计|函数执行计数|实例|
|AppConnections|连接|计数|平均值|连接|实例|
|控点|句柄计数|计数|平均值|句柄计数|实例|
|线程|线程计数|计数|平均值|线程计数|实例|
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
|HealthCheckStatus|运行状况检查状态|计数|平均值|运行状况检查状态|实例|
|FileSystemUsage|文件系统使用情况|字节|平均值|文件系统使用情况|无|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|请求|请求|计数|总计|Requests|实例|
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
|DiskQueueLength|磁盘队列长度|计数|平均值|磁盘队列长度|实例|
|HttpQueueLength|Http 队列长度|计数|平均值|Http 队列长度|实例|
|ActiveRequests|活动请求数|计数|总计|活动请求数|实例|
|TotalFrontEnds|前端总数|计数|平均值|前端总数|无|
|SmallAppServicePlanInstances|小型应用服务计划工作线程数|计数|平均值|小型应用服务计划工作线程数|无|
|MediumAppServicePlanInstances|中型应用服务计划工作线程数|计数|平均值|中型应用服务计划工作线程数|无|
|LargeAppServicePlanInstances|大型应用服务计划工作线程数|计数|平均值|大型应用服务计划工作线程数|无|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|指标|指标显示名称|单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|WorkersTotal|工作线程总数|计数|平均值|工作线程总数|无|
|WorkersAvailable|可用工作线程数|计数|平均值|可用工作线程数|无|
|WorkersUsed|使用的工作线程数|计数|平均值|使用的工作线程数|无|
|CpuPercentage|CPU 百分比|百分比|平均值|CPU 百分比|实例|
|MemoryPercentage|内存百分比|百分比|平均值|内存百分比|实例|
## <a name="next-steps"></a>后续步骤
* [了解 Azure Monitor 中的指标](data-platform.md)
* [针对指标创建警报](alerts-overview.md)
* [将指标导出到存储、事件中心或 Log Analytics](platform-logs-overview.md)

