---
title: 可通过诊断设置导出 Azure Monitor 平台指标
description: 可在 Azure Monitor 中为每种资源类型使用的指标的列表。
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 5d38786d3c7b852d3a9b65cd366eed68ebbb01e3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152946"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>可通过诊断设置导出 Azure Monitor 平台指标

默认情况下，Azure Monitor 提供[平台指标](data-platform-metrics.md)，不进行任何配置。 它提供多种方式来与平台指标交互，包括在门户中绘制图表、通过 REST API 访问它们或使用 PowerShell 或 CLI 查询它们。 请参阅[指标-支持](metrics-supported.md)Azure Monitor 的合并指标管道中当前可用的平台指标的完整列表。 若要查询和访问这些指标，请使用[2018-01-01 api 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)。 其他指标可在门户或旧版 API 中使用。

可以通过以下两种方式之一将平台指标从 Azure monitor 管道导出到其他位置。
1. 使用[诊断设置](diagnostic-settings.md)发送到 Log Analytics、事件中心或 Azure 存储。
2. 使用[指标 REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

由于 Azure Monitor 后端中的复杂性，并非所有指标都可使用诊断设置进行导出。 下表列出了使用诊断设置可以和不能导出的情况。

是否可通过诊断设置导出？ | ResourceType | 度量值 | MetricDisplayName | 单位 | AggregationType
|----|-----|------|----|----|-----|
是 | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | 内存: 清理器当前价格 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | 内存: 不可收缩的清理器内存 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | 内存: 可收缩的清理器内存 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | 线程: 命令池繁忙线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | 线程: 命令池空闲线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | 命令池作业队列长度 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | CurrentConnections | 连接: 当前连接数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | CurrentUserSessions | 当前用户会话数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | 线程: 长分析繁忙线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | 线程: 长分析空闲线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | 线程: 长分析作业队列长度 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | M 引擎内存 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | M 引擎专用字节数 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | M 引擎 QPU | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | M 引擎虚拟字节数 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | memory_metric | 内存 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | memory_thrashing_metric | 内存抖动 | 百分比 | 平均值
是 | Microsoft.AnalysisServices/servers | MemoryLimitHard | 内存: 内存硬性限制 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | MemoryLimitHigh | 内存: 内存上限 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | MemoryLimitLow | 内存: 内存下限 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | 内存: 内存 VertiPaq 限制 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | MemoryUsage | 内存: 内存使用量 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | private_bytes_metric | 专用字节 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | 线程: 处理池繁忙 I/O 作业线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | 线程: 处理池繁忙非 I/O 线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | 线程: 处理池空闲 I/O 作业线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | 线程: 处理池空闲非 I/O 线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | 线程: 处理池 I/O 作业队列长度 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | 处理池作业队列长度 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | qpu_metric | QPU | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | 查询池繁忙线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | 线程: 查询池空闲线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | 线程: 查询池作业队列长度 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | 配额 | 内存: 配额 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | QuotaBlocked | 内存: 阻止的配额 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | 处理: 每秒转换的行数 | 每秒计数 | 平均值
是 | Microsoft.AnalysisServices/servers | RowsReadPerSec | 处理: 每秒读取的行数 | 每秒计数 | 平均值
是 | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | 处理: 每秒写入的行数 | 每秒计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | 线程: 短分析繁忙线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | 线程: 短分析空闲线程数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | 线程: 短分析作业队列长度 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | 每秒成功连接数 | 每秒计数 | 平均值
是 | Microsoft.AnalysisServices/servers | TotalConnectionFailures | 连接失败总数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | TotalConnectionRequests | 连接请求总数 | 计数 | 平均值
是 | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | 内存: VertiPaq 未分页 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | VertiPaqPaged | 内存: VertiPaq 已分页 | 字节 | 平均值
是 | Microsoft.AnalysisServices/servers | virtual_bytes_metric | 虚拟字节 | 字节 | 平均值
是 | Microsoft.ApiManagement/service | BackendDuration | 后端请求的持续时间 | 毫秒 | 平均值
是 | Microsoft.ApiManagement/service | 容量 | 容量 | 百分比 | 平均值
是 | Microsoft.ApiManagement/service | 持续时间 | 网关请求的总持续时间 | 毫秒 | 平均值
是 | Microsoft.ApiManagement/service | EventHubDroppedEvents | 已丢弃 EventHub 事件 | 计数 | 总计
是 | Microsoft.ApiManagement/service | EventHubRejectedEvents | 拒绝 EventHub 事件 | 计数 | 总计
是 | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | 成功的 EventHub 事件 | 计数 | 总计
是 | Microsoft.ApiManagement/service | EventHubThrottledEvents | 限制 EventHub 事件 | 计数 | 总计
是 | Microsoft.ApiManagement/service | EventHubTimedoutEvents | 已超时 EventHub 事件 | 计数 | 总计
是 | Microsoft.ApiManagement/service | EventHubTotalBytesSent | EventHub 事件的大小 | 字节 | 总计
是 | Microsoft.ApiManagement/service | EventHubTotalEvents | EventHub 事件总数 | 计数 | 总计
是 | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | 故障 EventHub 事件 | 计数 | 总计
是 | Microsoft.ApiManagement/service | FailedRequests | 失败的网关请求数（已弃用） | 计数 | 总计
是 | Microsoft.ApiManagement/service | OtherRequests | 其他网关请求（不推荐使用） | 计数 | 总计
是 | Microsoft.ApiManagement/service | 请求 | 请求 | 计数 | 总计
是 | Microsoft.ApiManagement/service | SuccessfulRequests | 成功的网关请求数（已弃用） | 计数 | 总计
是 | Microsoft.ApiManagement/service | TotalRequests | 网关请求总数（不推荐使用） | 计数 | 总计
是 | Microsoft.ApiManagement/service | UnauthorizedRequests | 未经授权的网关请求（不推荐使用） | 计数 | 总计
是 | AppPlatform/春季 | AppCpuUsagePercentage | 应用 CPU 使用率百分比 | 百分比 | 平均值
是 | AppPlatform/春季 | AppMemoryCommitted | 已分配应用内存 | 字节 | 平均值
是 | AppPlatform/春季 | AppMemoryMax | 最大应用内存 | 字节 | 最大值
是 | AppPlatform/春季 | AppMemoryUsed | 使用的应用内存 | 字节 | 平均值
是 | AppPlatform/春季 | GCPauseTotalCount | GC 暂停计数 | 计数 | 总计
是 | AppPlatform/春季 | GCPauseTotalTime | GC 暂停总时间 | 毫秒 | 总计
是 | AppPlatform/春季 | MaxOldGenMemoryPoolBytes | 最大可用旧生成数据大小 | 字节 | 平均值
是 | AppPlatform/春季 | OldGenMemoryPoolBytes | 旧生成数据大小 | 字节 | 平均值
是 | AppPlatform/春季 | OldGenPromotedBytes | 提升为旧的生成数据大小 | 字节 | 最大值
是 | AppPlatform/春季 | SystemCpuUsagePercentage | 系统 CPU 使用率百分比 | 百分比 | 平均值
是 | AppPlatform/春季 | TomcatErrorCount | Tomcat 全局错误 | 计数 | 总计
是 | AppPlatform/春季 | TomcatReceivedBytes | Tomcat 收到的总字节数 | 字节 | 总计
是 | AppPlatform/春季 | TomcatRequestMaxTime | Tomcat 请求最大时间 | 毫秒 | 最大值
是 | AppPlatform/春季 | TomcatRequestTotalCount | Tomcat 请求总数 | 计数 | 总计
是 | AppPlatform/春季 | TomcatRequestTotalTime | Tomcat 请求总时间 | 毫秒 | 总计
是 | AppPlatform/春季 | TomcatResponseAvgTime | Tomcat 请求平均时间 | 毫秒 | 平均值
是 | AppPlatform/春季 | TomcatSentBytes | Tomcat 发送的总字节数 | 字节 | 总计
是 | AppPlatform/春季 | TomcatSessionActiveCurrentCount | Tomcat 会话活动计数 | 计数 | 总计
是 | AppPlatform/春季 | TomcatSessionActiveMaxCount | Tomcat 会话最大活动计数 | 计数 | 总计
是 | AppPlatform/春季 | TomcatSessionAliveMaxTime | Tomcat 会话最大活动时间 | 毫秒 | 最大值
是 | AppPlatform/春季 | TomcatSessionCreatedCount | Tomcat 会话创建计数 | 计数 | 总计
是 | AppPlatform/春季 | TomcatSessionExpiredCount | Tomcat 会话过期计数 | 计数 | 总计
是 | AppPlatform/春季 | TomcatSessionRejectedCount | Tomcat 会话已拒绝计数 | 计数 | 总计
是 | AppPlatform/春季 | YoungGenPromotedBytes | 提升为年轻生成数据大小 | 字节 | 最大值
是 | Microsoft.Automation/automationAccounts | TotalJob | 作业总数 | 计数 | 总计
是 | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | 运行更新部署计算机总数 | 计数 | 总计
是 | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | 更新部署运行总数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | CoreCount | 专用核心计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | CreatingNodeCount | 正在创建的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | IdleNodeCount | 空闲节点计数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | 作业删除完成事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | 作业删除启动事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | 作业禁用完成事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobDisableStartEvent | 作业禁用启动事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobStartEvent | 作业启动事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | 作业终止完成事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | 作业终止启动事件数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | 正在退出池的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | 低优先级核心计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | OfflineNodeCount | 脱机节点计数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | PoolCreateEvent | 池创建事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | 池删除完成事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | 池删除启动事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | 池调整大小完成事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | 池调整大小启动事件数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | PreemptedNodeCount | 已占用节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | RebootingNodeCount | 正在重新启动的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | ReimagingNodeCount | 正在重置映像的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | RunningNodeCount | 正在运行的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | StartingNodeCount | 正在启动的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | 启动任务失败的节点计数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | TaskCompleteEvent | 任务完成事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | TaskFailEvent | 任务失败事件数 | 计数 | 总计
是 | Microsoft.Batch/batchAccounts | TaskStartEvent | 任务启动事件数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | 低优先级节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | TotalNodeCount | 专用节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | UnusableNodeCount | 不可用的节点计数 | 计数 | 总计
否 | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | 正在等待启动任务的节点计数 | 计数 | 总计
是 | BatchAI/工作区 | 活动核心 | 活动核心 | 计数 | 平均值
是 | BatchAI/工作区 | 活动节点 | 活动节点 | 计数 | 平均值
是 | BatchAI/工作区 | 空闲内核数 | 空闲内核数 | 计数 | 平均值
是 | BatchAI/工作区 | 空闲节点 | 空闲节点 | 计数 | 平均值
是 | BatchAI/工作区 | 作业已完成 | 作业已完成 | 计数 | 总计
是 | BatchAI/工作区 | 已提交作业 | 已提交作业 | 计数 | 总计
是 | BatchAI/工作区 | 正在离开内核 | 正在离开内核 | 计数 | 平均值
是 | BatchAI/工作区 | 退出节点 | 退出节点 | 计数 | 平均值
是 | BatchAI/工作区 | 抢先核心 | 抢先核心 | 计数 | 平均值
是 | BatchAI/工作区 | 抢占节点 | 抢占节点 | 计数 | 平均值
是 | BatchAI/工作区 | 配额使用率百分比 | 配额使用率百分比 | 计数 | 平均值
是 | BatchAI/工作区 | 内核总数 | 内核总数 | 计数 | 平均值
是 | BatchAI/工作区 | 节点总数 | 节点总数 | 计数 | 平均值
是 | BatchAI/工作区 | 不可用内核 | 不可用内核 | 计数 | 平均值
是 | BatchAI/工作区 | 不可用节点 | 不可用节点 | 计数 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | ConnectionAccepted | 接受的连接 | 计数 | 总计
是 | Microsoft.Blockchain/blockchainMembers | ConnectionActive | 活动连接数 | 计数 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | ConnectionHandled | 处理的连接 | 计数 | 总计
是 | Microsoft.Blockchain/blockchainMembers | CpuUsagePercentageInDouble | CPU 使用率百分比 | 百分比 | 最大值
是 | Microsoft.Blockchain/blockchainMembers | IOReadBytes | IO 读取字节数 | 字节 | 总计
是 | Microsoft.Blockchain/blockchainMembers | IOWriteBytes | IO 写入字节数 | 字节 | 总计
是 | Microsoft.Blockchain/blockchainMembers | MemoryLimit | 内存限制 | 字节 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | MemoryUsage | 内存用量 | 字节 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | 内存使用率百分比 | 百分比 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | PendingTransactions | 挂起的事务 | 计数 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | ProcessedBlocks | 处理的块 | 计数 | 总计
是 | Microsoft.Blockchain/blockchainMembers | ProcessedTransactions | 处理的事务 | 计数 | 总计
是 | Microsoft.Blockchain/blockchainMembers | QueuedTransactions | 排队事务 | 计数 | 平均值
是 | Microsoft.Blockchain/blockchainMembers | RequestHandled | 处理的请求 | 计数 | 总计
是 | Microsoft.Blockchain/blockchainMembers | StorageUsage | 存储使用情况 | 字节 | 平均值
是 | Microsoft.Cache/redis | cachehits | 缓存命中数 | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits0 | 缓存命中数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits1 | 缓存命中数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits2 | 缓存命中数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits3 | 缓存命中数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits4 | 缓存命中数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits5 | 缓存命中数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits6 | 缓存命中数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits7 | 缓存命中数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits8 | 缓存命中数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | cachehits9 | 缓存命中数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | cacheLatency | 缓存延迟毫秒数（预览） | 计数 | 平均值
是 | Microsoft.Cache/redis | cachemisses | 缓存未命中数 | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses0 | 缓存未命中数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses1 | 缓存未命中数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses2 | 缓存未命中数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses3 | 缓存未命中数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses4 | 缓存未命中数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses5 | 缓存未命中数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses6 | 缓存未命中数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses7 | 缓存未命中数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses8 | 缓存未命中数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | cachemisses9 | 缓存未命中数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | cacheRead | 缓存读取量 | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead0 | 缓存读取量(分片 0) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead1 | 缓存读取量(分片 1) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead2 | 缓存读取量(分片 2) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead3 | 缓存读取量(分片 3) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead4 | 缓存读取量(分片 4) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead5 | 缓存读取量(分片 5) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead6 | 缓存读取量(分片 6) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead7 | 缓存读取量(分片 7) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead8 | 缓存读取量(分片 8) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheRead9 | 缓存读取量(分片 9) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite | 缓存写入量 | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite0 | 缓存写入量(分片 0) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite1 | 缓存写入量(分片 1) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite2 | 缓存写入量(分片 2) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite3 | 缓存写入量(分片 3) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite4 | 缓存写入量(分片 4) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite5 | 缓存写入量(分片 5) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite6 | 缓存写入量(分片 6) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite7 | 缓存写入量(分片 7) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite8 | 缓存写入量(分片 8) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | cacheWrite9 | 缓存写入量(分片 9) | 每秒字节数 | 最大值
是 | Microsoft.Cache/redis | connectedclients | 连接的客户端数 | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients0 | 连接的客户端数(分片 0) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients1 | 连接的客户端数(分片 1) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients2 | 连接的客户端数(分片 2) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients3 | 连接的客户端数(分片 3) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients4 | 连接的客户端数(分片 4) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients5 | 连接的客户端数(分片 5) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients6 | 连接的客户端数(分片 6) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients7 | 连接的客户端数(分片 7) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients8 | 连接的客户端数(分片 8) | 计数 | 最大值
是 | Microsoft.Cache/redis | connectedclients9 | 连接的客户端数(分片 9) | 计数 | 最大值
是 | Microsoft.Cache/redis | 错误 | 错误 | 计数 | 最大值
是 | Microsoft.Cache/redis | evictedkeys | 逐出的密钥数 | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys0 | 逐出的密钥数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys1 | 逐出的密钥数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys2 | 逐出的密钥数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys3 | 逐出的密钥数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys4 | 逐出的密钥数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys5 | 逐出的密钥数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys6 | 逐出的密钥数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys7 | 逐出的密钥数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys8 | 逐出的密钥数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | evictedkeys9 | 逐出的密钥数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys | 过期的密钥数 | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys0 | 过期的密钥数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys1 | 过期的密钥数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys2 | 过期的密钥数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys3 | 过期的密钥数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys4 | 过期的密钥数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys5 | 过期的密钥数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys6 | 过期的密钥数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys7 | 过期的密钥数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys8 | 过期的密钥数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | expiredkeys9 | 过期的密钥数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands | 获取数 | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands0 | Get 数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands1 | Get 数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands2 | Get 数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands3 | Get 数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands4 | Get 数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands5 | Get 数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands6 | Get 数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands7 | Get 数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands8 | Get 数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | getcommands9 | Get 数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | operationsPerSecond | 每秒操作数 | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond0 | 每秒操作数（分片 0） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond1 | 每秒操作数（分片 1） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond2 | 每秒操作数（分片 2） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond3 | 每秒操作数（分片 3） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond4 | 每秒操作数（分片 4） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond5 | 每秒操作数（分片 5） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond6 | 每秒操作数（分片 6） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond7 | 每秒操作数（分片 7） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond8 | 每秒操作数（分片 8） | 计数 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond9 | 每秒操作数（分片 9） | 计数 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime | CPU | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime0 | CPU (分片 0) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime1 | CPU (分片 1) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime2 | CPU (分片 2) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime3 | CPU (分片 3) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime4 | CPU (分片 4) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime5 | CPU (分片 5) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime6 | CPU (分片 6) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime7 | CPU (分片 7) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime8 | CPU (分片 8) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime9 | CPU (分片 9) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad | 服务器负载 | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad0 | 服务器负载(分片 0) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad1 | 服务器负载(分片 1) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad2 | 服务器负载(分片 2) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad3 | 服务器负载(分片 3) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad4 | 服务器负载(分片 4) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad5 | 服务器负载(分片 5) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad6 | 服务器负载(分片 6) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad7 | 服务器负载(分片 7) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad8 | 服务器负载(分片 8) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad9 | 服务器负载(分片 9) | 百分比 | 最大值
是 | Microsoft.Cache/redis | setcommands | 集 (Sets) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands0 | Set 数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands1 | Set 数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands2 | Set 数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands3 | Set 数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands4 | Set 数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands5 | Set 数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands6 | Set 数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands7 | Set 数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands8 | Set 数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | setcommands9 | Set 数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed | 总操作数 | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed0 | 总操作数(分片 0) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed1 | 总操作数(分片 1) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed2 | 总操作数(分片 2) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed3 | 总操作数(分片 3) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed4 | 总操作数(分片 4) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed5 | 总操作数(分片 5) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed6 | 总操作数(分片 6) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed7 | 总操作数(分片 7) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed8 | 总操作数(分片 8) | 计数 | 总计
是 | Microsoft.Cache/redis | totalcommandsprocessed9 | 总操作数(分片 9) | 计数 | 总计
是 | Microsoft.Cache/redis | totalkeys | 总密钥数 | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys0 | 总密钥数（分片 0） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys1 | 总密钥数（分片 1） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys2 | 总密钥数（分片 2） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys3 | 总密钥数（分片 3） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys4 | 总密钥数（分片 4） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys5 | 总密钥数（分片 5） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys6 | 总密钥数（分片 6） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys7 | 总密钥数（分片 7） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys8 | 总密钥数（分片 8） | 计数 | 最大值
是 | Microsoft.Cache/redis | totalkeys9 | 总密钥数（分片 9） | 计数 | 最大值
是 | Microsoft.Cache/redis | usedmemory | 已用内存 | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory0 | 已用内存量(分片 0) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory1 | 已用内存量(分片 1) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory2 | 已用内存量(分片 2) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory3 | 已用内存量(分片 3) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory4 | 已用内存量(分片 4) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory5 | 已用内存量(分片 5) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory6 | 已用内存量(分片 6) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory7 | 已用内存量(分片 7) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory8 | 已用内存量(分片 8) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemory9 | 已用内存量(分片 9) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemorypercentage | 已用内存百分比 | 百分比 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss | 已用内存 RSS | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss0 | 已用内存 RSS (分片 0) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss1 | 已用内存 RSS (分片 1) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss2 | 已用内存 RSS (分片 2) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss3 | 已用内存 RSS (分片 3) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss4 | 已用内存 RSS (分片 4) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss5 | 已用内存 RSS (分片 5) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss6 | 已用内存 RSS (分片 6) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss7 | 已用内存 RSS (分片 7) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss8 | 已用内存 RSS (分片 8) | 字节 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss9 | 已用内存 RSS (分片 9) | 字节 | 最大值
否 | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Read Bytes/Sec | 磁盘读取 | 每秒字节数 | 平均值
是 | Microsoft.ClassicCompute/domainNames/slots/roles | 磁盘读取操作次数/秒 | 磁盘读取操作次数/秒 | 每秒计数 | 平均值
否 | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Write Bytes/Sec | 磁盘写入 | 每秒字节数 | 平均值
是 | Microsoft.ClassicCompute/domainNames/slots/roles | 磁盘写入操作次数/秒 | 磁盘写入操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.ClassicCompute/domainNames/slots/roles | 网络传入 | 网络传入 | 字节 | 总计
是 | Microsoft.ClassicCompute/domainNames/slots/roles | 网络传出 | 网络传出 | 字节 | 总计
是 | Microsoft.ClassicCompute/domainNames/slots/roles | CPU 百分比 | CPU 百分比 | 百分比 | 平均值
否 | Microsoft.ClassicCompute/virtualMachines | Disk Read Bytes/Sec | 磁盘读取 | 每秒字节数 | 平均值
是 | Microsoft.ClassicCompute/virtualMachines | 磁盘读取操作次数/秒 | 磁盘读取操作次数/秒 | 每秒计数 | 平均值
否 | Microsoft.ClassicCompute/virtualMachines | Disk Write Bytes/Sec | 磁盘写入 | 每秒字节数 | 平均值
是 | Microsoft.ClassicCompute/virtualMachines | 磁盘写入操作次数/秒 | 磁盘写入操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.ClassicCompute/virtualMachines | 网络传入 | 网络传入 | 字节 | 总计
是 | Microsoft.ClassicCompute/virtualMachines | 网络传出 | 网络传出 | 字节 | 总计
是 | Microsoft.ClassicCompute/virtualMachines | CPU 百分比 | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.ClassicStorage/storageAccounts | 可用性 | 可用性 | 百分比 | 平均值
是 | Microsoft.ClassicStorage/storageAccounts | 流出量 | 流出量 | 字节 | 总计
是 | Microsoft.ClassicStorage/storageAccounts | 入口 | 入口 | 字节 | 总计
是 | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | Microsoft.ClassicStorage/storageAccounts | 事务 | 事务 | 计数 | 总计
否 | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | 已用容量 | 字节 | 平均值
是 | ClassicStorage/storageAccounts/blobServices | 可用性 | 可用性 | 百分比 | 平均值
否 | ClassicStorage/storageAccounts/blobServices | BlobCapacity | Blob 容量 | 字节 | 平均值
否 | ClassicStorage/storageAccounts/blobServices | BlobCount | Blob 计数 | 计数 | 平均值
是 | ClassicStorage/storageAccounts/blobServices | ContainerCount | Blob 容器计数 | 计数 | 平均值
是 | ClassicStorage/storageAccounts/blobServices | 流出量 | 流出量 | 字节 | 总计
否 | ClassicStorage/storageAccounts/blobServices | IndexCapacity | 索引容量 | 字节 | 平均值
是 | ClassicStorage/storageAccounts/blobServices | 入口 | 入口 | 字节 | 总计
是 | ClassicStorage/storageAccounts/blobServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/blobServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/blobServices | 事务 | 事务 | 计数 | 总计
是 | ClassicStorage/storageAccounts/fileServices | 可用性 | 可用性 | 百分比 | 平均值
是 | ClassicStorage/storageAccounts/fileServices | 流出量 | 流出量 | 字节 | 总计
否 | ClassicStorage/storageAccounts/fileServices | FileCapacity | 文件容量 | 字节 | 平均值
否 | ClassicStorage/storageAccounts/fileServices | FileCount | 文件计数 | 计数 | 平均值
否 | ClassicStorage/storageAccounts/fileServices | FileShareCount | 文件共享计数 | 计数 | 平均值
否 | ClassicStorage/storageAccounts/fileServices | FileShareQuota | 文件共享配额大小 | 字节 | 平均值
否 | ClassicStorage/storageAccounts/fileServices | FileShareSnapshotCount | 文件共享快照计数 | 计数 | 平均值
否 | ClassicStorage/storageAccounts/fileServices | FileShareSnapshotSize | 文件共享快照大小 | 字节 | 平均值
是 | ClassicStorage/storageAccounts/fileServices | 入口 | 入口 | 字节 | 总计
是 | ClassicStorage/storageAccounts/fileServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/fileServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/fileServices | 事务 | 事务 | 计数 | 总计
是 | ClassicStorage/storageAccounts/queueServices | 可用性 | 可用性 | 百分比 | 平均值
是 | ClassicStorage/storageAccounts/queueServices | 流出量 | 流出量 | 字节 | 总计
是 | ClassicStorage/storageAccounts/queueServices | 入口 | 入口 | 字节 | 总计
是 | ClassicStorage/storageAccounts/queueServices | QueueCapacity | 队列容量 | 字节 | 平均值
是 | ClassicStorage/storageAccounts/queueServices | QueueCount | 队列计数 | 计数 | 平均值
是 | ClassicStorage/storageAccounts/queueServices | QueueMessageCount | 队列消息计数 | 计数 | 平均值
是 | ClassicStorage/storageAccounts/queueServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/queueServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/queueServices | 事务 | 事务 | 计数 | 总计
是 | ClassicStorage/storageAccounts/tableServices | 可用性 | 可用性 | 百分比 | 平均值
是 | ClassicStorage/storageAccounts/tableServices | 流出量 | 流出量 | 字节 | 总计
是 | ClassicStorage/storageAccounts/tableServices | 入口 | 入口 | 字节 | 总计
是 | ClassicStorage/storageAccounts/tableServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/tableServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | ClassicStorage/storageAccounts/tableServices | TableCapacity | 表容量 | 字节 | 平均值
是 | ClassicStorage/storageAccounts/tableServices | TableCount | 表计数 | 计数 | 平均值
是 | ClassicStorage/storageAccounts/tableServices | TableEntityCount | 表实体计数 | 计数 | 平均值
是 | ClassicStorage/storageAccounts/tableServices | 事务 | 事务 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | BlockedCalls | 阻止的调用数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | CharactersTrained | 训练的字符 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | CharactersTranslated | 转换的字符 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | ClientErrors | 客户端错误数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | DataIn | 数据输入 | 字节 | 总计
是 | Microsoft.CognitiveServices/accounts | DataOut | 数据输出 | 字节 | 总计
是 | Microsoft.CognitiveServices/accounts | 延迟 | 延迟 | 毫秒 | 平均值
是 | Microsoft.CognitiveServices/accounts | ServerErrors | 服务器错误数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | 语音会话持续时间 | 秒数 | 总计
是 | Microsoft.CognitiveServices/accounts | SuccessfulCalls | 成功调用数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | TotalCalls | 总调用数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | TotalErrors | 错误总数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | TotalTokenCalls | 令牌调用总数 | 计数 | 总计
是 | Microsoft.CognitiveServices/accounts | TotalTransactions | 总事务 | 计数 | 总计
是 | Microsoft.Compute/virtualMachines | 已用 CPU 信用额度 | 已用 CPU 信用额度 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 剩余 CPU 信用额度 | 剩余 CPU 信用额度 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 数据磁盘队列深度 | 数据磁盘队列深度（预览） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 数据磁盘读取字节数/秒 | 数据磁盘读取字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 数据磁盘读取操作数/秒 | 数据磁盘读取操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 数据磁盘写入字节数/秒 | 数据磁盘写入字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 数据磁盘写入操作数/秒 | 数据磁盘写入操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 磁盘读取字节数 | 磁盘读取字节数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachines | 磁盘读取操作次数/秒 | 磁盘读取操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 磁盘写入字节数 | 磁盘写入字节数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachines | 磁盘写入操作次数/秒 | 磁盘写入操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 入站流 | 入站流 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 入站流最大创建速率 | 入站流最大创建速率（预览） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 网络传入 | 收费网络（不推荐使用） | 字节 | 总计
是 | Microsoft.Compute/virtualMachines | 网络总数 | 网络总数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachines | 网络传出 | 网络出量（不推荐使用） | 字节 | 总计
是 | Microsoft.Compute/virtualMachines | 网络传出总数 | 网络传出总数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachines | OS 磁盘队列深度 | OS 磁盘队列深度（预览） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 磁盘读取字节数/秒 | OS 磁盘读取字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 磁盘读取操作数/秒 | OS 磁盘读取操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 磁盘写入字节数/秒 | OS 磁盘写入字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 磁盘写入操作数/秒 | OS 磁盘写入操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 每磁盘 QD | OS Disk QD （已弃用） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 每磁盘读取字节数/秒 | OS 磁盘读取字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 每磁盘读取操作数/秒 | OS 磁盘读取操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 每磁盘写入字节数/秒 | OS 磁盘写入字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | OS 每磁盘写入操作数/秒 | OS 磁盘写入操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 出站流 | 出站流 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 出站流的最大创建速率 | 出站流的最大创建速率（预览） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 每磁盘 QD | 数据磁盘 QD （已弃用） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 每磁盘读取字节数/秒 | 数据磁盘读取字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 每磁盘读取操作数/秒 | 数据磁盘读取操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 每磁盘写入字节数/秒 | 数据磁盘写入字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | 每磁盘写入操作数/秒 | 数据磁盘写入操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachines | CPU 百分比 | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachines | 高级数据磁盘缓存读取命中 | 高级数据磁盘缓存读取命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachines | 高级数据磁盘缓存读取未命中 | 高级数据磁盘缓存读取未命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachines | 高级 OS 磁盘缓存读取命中 | 高级 OS 磁盘缓存读取命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachines | 高级 OS 磁盘缓存读取未命中 | 高级 OS 磁盘缓存读取未命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 已用 CPU 信用额度 | 已用 CPU 信用额度 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 剩余 CPU 信用额度 | 剩余 CPU 信用额度 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 数据磁盘队列深度 | 数据磁盘队列深度（预览） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 数据磁盘读取字节数/秒 | 数据磁盘读取字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 数据磁盘读取操作数/秒 | 数据磁盘读取操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 数据磁盘写入字节数/秒 | 数据磁盘写入字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 数据磁盘写入操作数/秒 | 数据磁盘写入操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 磁盘读取字节数 | 磁盘读取字节数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets | 磁盘读取操作次数/秒 | 磁盘读取操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 磁盘写入字节数 | 磁盘写入字节数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets | 磁盘写入操作次数/秒 | 磁盘写入操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 入站流 | 入站流 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 入站流最大创建速率 | 入站流最大创建速率（预览） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 网络传入 | 收费网络（不推荐使用） | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets | 网络总数 | 网络总数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets | 网络传出 | 网络出量（不推荐使用） | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets | 网络传出总数 | 网络传出总数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁盘队列深度 | OS 磁盘队列深度（预览） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁盘读取字节数/秒 | OS 磁盘读取字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁盘读取操作数/秒 | OS 磁盘读取操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁盘写入字节数/秒 | OS 磁盘写入字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁盘写入操作数/秒 | OS 磁盘写入操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每磁盘 QD | OS Disk QD （已弃用） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每磁盘读取字节数/秒 | OS 磁盘读取字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每磁盘读取操作数/秒 | OS 磁盘读取操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每磁盘写入字节数/秒 | OS 磁盘写入字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每磁盘写入操作数/秒 | OS 磁盘写入操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 出站流 | 出站流 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 出站流的最大创建速率 | 出站流的最大创建速率（预览） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 每磁盘 QD | 数据磁盘 QD （已弃用） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 每磁盘读取字节数/秒 | 数据磁盘读取字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 每磁盘读取操作数/秒 | 数据磁盘读取操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 每磁盘写入字节数/秒 | 数据磁盘写入字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 每磁盘写入操作数/秒 | 数据磁盘写入操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | CPU 百分比 | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 高级数据磁盘缓存读取命中 | 高级数据磁盘缓存读取命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 高级数据磁盘缓存读取未命中 | 高级数据磁盘缓存读取未命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 高级 OS 磁盘缓存读取命中 | 高级 OS 磁盘缓存读取命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets | 高级 OS 磁盘缓存读取未命中 | 高级 OS 磁盘缓存读取未命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 已用 CPU 信用额度 | 已用 CPU 信用额度 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 剩余 CPU 信用额度 | 剩余 CPU 信用额度 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 数据磁盘队列深度 | 数据磁盘队列深度（预览） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 数据磁盘读取字节数/秒 | 数据磁盘读取字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 数据磁盘读取操作数/秒 | 数据磁盘读取操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 数据磁盘写入字节数/秒 | 数据磁盘写入字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 数据磁盘写入操作数/秒 | 数据磁盘写入操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 磁盘读取字节数 | 磁盘读取字节数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 磁盘读取操作次数/秒 | 磁盘读取操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 磁盘写入字节数 | 磁盘写入字节数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 磁盘写入操作次数/秒 | 磁盘写入操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 入站流 | 入站流 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 入站流最大创建速率 | 入站流最大创建速率（预览） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 网络传入 | 收费网络（不推荐使用） | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 网络总数 | 网络总数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 网络传出 | 网络出量（不推荐使用） | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 网络传出总数 | 网络传出总数 | 字节 | 总计
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁盘队列深度 | OS 磁盘队列深度（预览） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁盘读取字节数/秒 | OS 磁盘读取字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁盘读取操作数/秒 | OS 磁盘读取操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁盘写入字节数/秒 | OS 磁盘写入字节数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁盘写入操作数/秒 | OS 磁盘写入操作数/秒（预览版） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每磁盘 QD | OS Disk QD （已弃用） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每磁盘读取字节数/秒 | OS 磁盘读取字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每磁盘读取操作数/秒 | OS 磁盘读取操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每磁盘写入字节数/秒 | OS 磁盘写入字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每磁盘写入操作数/秒 | OS 磁盘写入操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 出站流 | 出站流 | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 出站流的最大创建速率 | 出站流的最大创建速率（预览） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每磁盘 QD | 数据磁盘 QD （已弃用） | 计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每磁盘读取字节数/秒 | 数据磁盘读取字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每磁盘读取操作数/秒 | 数据磁盘读取操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每磁盘写入字节数/秒 | 数据磁盘写入字节数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每磁盘写入操作数/秒 | 数据磁盘写入操作数/秒（不推荐使用） | 每秒计数 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | CPU 百分比 | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 高级数据磁盘缓存读取命中 | 高级数据磁盘缓存读取命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 高级数据磁盘缓存读取未命中 | 高级数据磁盘缓存读取未命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 高级 OS 磁盘缓存读取命中 | 高级 OS 磁盘缓存读取命中（预览） | 百分比 | 平均值
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 高级 OS 磁盘缓存读取未命中 | 高级 OS 磁盘缓存读取未命中（预览） | 百分比 | 平均值
是 | Microsoft.ContainerInstance/containerGroups | CpuUsage | CPU 使用率 | 计数 | 平均值
是 | Microsoft.ContainerInstance/containerGroups | MemoryUsage | 内存用量 | 字节 | 平均值
是 | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | 每秒接收到的网络字节数 | 字节 | 平均值
是 | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | 每秒传输的网络字节数 | 字节 | 平均值
是 | Microsoft.ContainerRegistry/registries | RunDuration | 运行持续时间 | 毫秒 | 总计
是 | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | 成功的请求计数 | 计数 | 平均值
是 | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | 推送计数成功 | 计数 | 平均值
是 | Microsoft.ContainerRegistry/registries | TotalPullCount | 请求总数 | 计数 | 平均值
是 | Microsoft.ContainerRegistry/registries | TotalPushCount | 推送计数总计 | 计数 | 平均值
否 | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | 托管群集中可用 CPU 内核的总数 | 计数 | 平均值
否 | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | 托管群集中可用内存的总量 | 字节 | 平均值
否 | Microsoft.ContainerService/managedClusters | kube_node_status_condition | 各种节点条件的状态 | 计数 | 平均值
否 | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | 依据阶段的 Pod 数 | 计数 | 平均值
否 | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | 就绪状态下的 Pod 数 | 计数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | AvailableCapacity | 可用容量 | 字节 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | 已上传云字节（设备） | 字节 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | 已上传云字节数（共享） | 字节 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | 云下载吞吐量 | 每秒字节数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | 云下载吞吐量（共享） | 每秒字节数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | 云上传吞吐量 | 每秒字节数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | 云上传吞吐量（共享） | 每秒字节数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | 边缘计算-内存使用情况 | 百分比 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | 边缘计算-CPU 百分比 | 百分比 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | 读取吞吐量（网络） | 每秒字节数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | 写入吞吐量（网络） | 每秒字节数 | 平均值
是 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | 总容量 | 字节 | 平均值
是 | Microsoft.DataFactory/datafactories | FailedRuns | 失败的运行次数 | 计数 | 总计
是 | Microsoft.DataFactory/datafactories | SuccessfulRuns | 成功的运行次数 | 计数 | 总计
是 | Microsoft.DataFactory/factories | ActivityCancelledRuns | 已取消的活动运行指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | ActivityFailedRuns | 失败的活动运行数指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | ActivitySucceededRuns | 成功的活动运行数指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | FactorySizeInGbUnits | 总工厂大小（GB 单位） | 计数 | 最大值
是 | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | 集成运行时可用内存 | 字节 | 平均值
是 | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | 集成运行时队列持续时间 | 秒数 | 平均值
是 | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | 集成运行时 CPU 利用率 | 百分比 | 平均值
是 | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | 集成运行时队列长度 | 计数 | 平均值
是 | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | 允许的最大工厂大小（GB 单位） | 计数 | 最大值
是 | Microsoft.DataFactory/factories | MaxAllowedResourceCount | 允许的最大实体计数 | 计数 | 最大值
是 | Microsoft.DataFactory/factories | PipelineCancelledRuns | 已取消的管道运行指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | PipelineFailedRuns | 失败的管道运行数指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | PipelineSucceededRuns | 成功的管道运行数指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | ResourceCount | 实体总数 | 计数 | 最大值
是 | Microsoft.DataFactory/factories | TriggerCancelledRuns | 已取消的触发器运行指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | TriggerFailedRuns | 失败的触发器运行数指标 | 计数 | 总计
是 | Microsoft.DataFactory/factories | TriggerSucceededRuns | 成功的触发器运行数指标 | 计数 | 总计
是 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | 已取消的 AU 时间 | 秒数 | 总计
是 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | 失败的 AU 时间 | 秒数 | 总计
是 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | 成功 AU 时间 | 秒数 | 总计
是 | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | 取消的作业数 | 计数 | 总计
是 | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | 失败作业数 | 计数 | 总计
是 | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | 成功作业数 | 计数 | 总计
是 | Microsoft.DataLakeStore/accounts | DataRead | 读取的数据量 | 字节 | 总计
是 | Microsoft.DataLakeStore/accounts | DataWritten | 写入的数据量 | 字节 | 总计
是 | Microsoft.DataLakeStore/accounts | ReadRequests | 读取请求数 | 计数 | 总计
是 | Microsoft.DataLakeStore/accounts | TotalStorage | 总存储 | 字节 | 最大值
是 | Microsoft.DataLakeStore/accounts | WriteRequests | 写入请求数 | 计数 | 总计
是 | Microsoft.DBforMariaDB/servers | active_connections | 活动连接数 | 计数 | 平均值
是 | Microsoft.DBforMariaDB/servers | backup_storage_used | 使用的备份存储 | 字节 | 平均值
是 | Microsoft.DBforMariaDB/servers | connections_failed | 失败的连接数 | 计数 | 总计
是 | Microsoft.DBforMariaDB/servers | cpu_percent | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.DBforMariaDB/servers | io_consumption_percent | IO 百分比 | 百分比 | 平均值
是 | Microsoft.DBforMariaDB/servers | memory_percent | 内存百分比 | 百分比 | 平均值
是 | Microsoft.DBforMariaDB/servers | network_bytes_egress | 网络传出 | 字节 | 总计
是 | Microsoft.DBforMariaDB/servers | network_bytes_ingress | 网络传入 | 字节 | 总计
是 | Microsoft.DBforMariaDB/servers | seconds_behind_master | 复制延迟（秒） | 计数 | 最大值
是 | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | 服务器存储空间上限 | 字节 | 平均值
是 | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | 服务器日志存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | 服务器日志已用的存储量 | 字节 | 平均值
是 | Microsoft.DBforMariaDB/servers | storage_limit | 存储限制 | 字节 | 最大值
是 | Microsoft.DBforMariaDB/servers | storage_percent | 存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforMariaDB/servers | storage_used | 已用存储 | 字节 | 平均值
是 | Microsoft.DBforMySQL/servers | active_connections | 活动连接数 | 计数 | 平均值
是 | Microsoft.DBforMySQL/servers | backup_storage_used | 使用的备份存储 | 字节 | 平均值
是 | Microsoft.DBforMySQL/servers | connections_failed | 失败的连接数 | 计数 | 总计
是 | Microsoft.DBforMySQL/servers | cpu_percent | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.DBforMySQL/servers | io_consumption_percent | IO 百分比 | 百分比 | 平均值
是 | Microsoft.DBforMySQL/servers | memory_percent | 内存百分比 | 百分比 | 平均值
是 | Microsoft.DBforMySQL/servers | network_bytes_egress | 网络传出 | 字节 | 总计
是 | Microsoft.DBforMySQL/servers | network_bytes_ingress | 网络传入 | 字节 | 总计
是 | Microsoft.DBforMySQL/servers | seconds_behind_master | 复制延迟（秒） | 计数 | 最大值
是 | Microsoft.DBforMySQL/servers | serverlog_storage_limit | 服务器存储空间上限 | 字节 | 最大值
是 | Microsoft.DBforMySQL/servers | serverlog_storage_percent | 服务器日志存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforMySQL/servers | serverlog_storage_usage | 服务器日志已用的存储量 | 字节 | 平均值
是 | Microsoft.DBforMySQL/servers | storage_limit | 存储限制 | 字节 | 最大值
是 | Microsoft.DBforMySQL/servers | storage_percent | 存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforMySQL/servers | storage_used | 已用存储 | 字节 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | active_connections | 活动连接数 | 计数 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | backup_storage_used | 使用的备份存储 | 字节 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | connections_failed | 失败的连接数 | 计数 | 总计
是 | Microsoft.DBforPostgreSQL/servers | cpu_percent | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | IO 百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | memory_percent | 内存百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | 网络传出 | 字节 | 总计
是 | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | 网络传入 | 字节 | 总计
是 | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | 副本的最大滞后时间 | 字节 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | 副本滞后时间 | 秒数 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | 服务器存储空间上限 | 字节 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | 服务器日志存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | 服务器日志已用的存储量 | 字节 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | storage_limit | 存储限制 | 字节 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | storage_percent | 存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/servers | storage_used | 已用存储 | 字节 | 平均值
是 | Microsoft.DBforPostgreSQL/serversv2 | active_connections | 活动连接数 | 计数 | 平均值
是 | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/serversv2 | iops | IOPS | 计数 | 平均值
是 | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | 内存百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | 网络传出 | 字节 | 总计
是 | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | 网络传入 | 字节 | 总计
是 | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | 存储空间百分比 | 百分比 | 平均值
是 | Microsoft.DBforPostgreSQL/serversv2 | storage_used | 已用存储 | 字节 | 平均值
是 | Microsoft. 设备/帐户 | digitaltwins | 数字孪生节点遥测占位符 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | C2D 消息已放弃 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | 已完成 C2D 消息传递 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | 已拒绝 C2D 消息 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.methods.failure | 失败的直接方法调用数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.methods.requestSize | 直接方法调用的请求大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | c2d.methods.responseSize | 直接方法调用的响应大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | c2d.methods.success | 成功的直接方法调用数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.twin.read.failure | 后端的失败克隆读取数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.twin.read.size | 后端的克隆读取的响应大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | c2d.twin.read.success | 后端的成功克隆读取数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.twin.update.failure | 后端的失败克隆更新数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | c2d.twin.update.size | 后端的克隆更新的大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | c2d.twin.update.success | 后端的成功克隆更新数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D 消息已过期（预览版） | 计数 | 总计
是 | Microsoft.Devices/IotHubs | 配置 | 配置指标 | 计数 | 总计
否 | Microsoft.Devices/IotHubs | connectedDeviceCount | 已连接设备数（预览） | 计数 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | 路由：发送到消息/事件的消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | 路由：已发送到事件中心的消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | 路由：已发送到服务总线队列的消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | 路由：发送到服务总线主题的消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | 路由：发送到存储器的消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | 路由：发送到存储器的 blob 数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | 路由：发送到存储器的数据数 | 字节 | 总计
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | 路由：消息/事件的消息延迟 | 毫秒 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | 路由：事件中心的消息延迟 | 毫秒 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | 路由：服务总线队列的消息延迟 | 毫秒 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | 路由：服务总线主题的消息延迟 | 毫秒 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | 路由：存储器的消息延迟 | 毫秒 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | 路由：已删除的遥测消息数  | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | 路由：发送到回退的消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | 路由：不兼容的遥测消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | 路由：已孤立的遥测消息数  | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | 路由：已发送的遥测消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | 遥测消息发送尝试次数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | 限制错误数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | 已发送的遥测消息数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.twin.read.failure | 设备的失败克隆读取数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.twin.read.size | 设备的克隆读取的响应大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.twin.read.success | 设备的成功克隆读取数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.twin.update.failure | 设备的失败克隆更新数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | d2c.twin.update.size | 设备的克隆更新的大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | d2c.twin.update.success | 设备的成功克隆更新数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | 已使用的消息总数 | 计数 | 平均值
是 | Microsoft.Devices/IotHubs | deviceDataUsage | 设备数据总使用量 | 字节 | 总计
是 | Microsoft.Devices/IotHubs | deviceDataUsageV2 | 设备数据使用总量（预览） | 字节 | 总计
是 | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | （已弃用）连接设备数  | 计数 | 总计
是 | Microsoft.Devices/IotHubs | devices.totalDevices | （已弃用）设备总数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | EventGridDeliveries | 事件网格传递（预览） | 计数 | 总计
是 | Microsoft.Devices/IotHubs | EventGridLatency | 事件网格延迟（预览版） | 毫秒 | 平均值
是 | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | 失败的作业取消数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.cancelJob.success | 成功的作业取消数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.completed | 已完成的作业 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | 方法调用作业的创建失败数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | 方法调用作业的创建成功数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | 克隆更新作业创建失败数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | 克隆更新作业创建成功数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.failed | 失败的作业数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.listJobs.failure | 对列出作业的失败调用数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.listJobs.success | 对列出作业的成功调用数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | 失败的作业查询数 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | jobs.queryJobs.success | 成功的作业查询数 | 计数 | 总计
否 | Microsoft.Devices/IotHubs | totalDeviceCount | 设备总数（预览） | 计数 | 平均值
是 | Microsoft.Devices/IotHubs | twinQueries.failure | 失败的克隆查询 | 计数 | 总计
是 | Microsoft.Devices/IotHubs | twinQueries.resultSize | 克隆查询结果大小 | 字节 | 平均值
是 | Microsoft.Devices/IotHubs | twinQueries.success | 成功的克隆查询 | 计数 | 总计
是 | Microsoft.Devices/provisioningServices | AttestationAttempts | 证明尝试次数 | 计数 | 总计
是 | Microsoft.Devices/provisioningServices | DeviceAssignments | 已分配设备 | 计数 | 总计
是 | Microsoft.Devices/provisioningServices | RegistrationAttempts | 注册尝试次数 | 计数 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | 可用存储 | 字节 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Cassandra 连接关闭数 | 计数 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Cassandra 请求费用 | 计数 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Cassandra 请求数 | 计数 | 计数
否 | Microsoft.DocumentDB/databaseAccounts | DataUsage | 数据使用情况 | 字节 | 总计
是 | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | 计数 | 计数
否 | Microsoft.DocumentDB/databaseAccounts | DocumentCount | 文档计数 | 计数 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | 文档配额 | 字节 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | IndexUsage | 索引使用情况 | 字节 | 总计
否 | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | 元数据请求 | 计数 | 计数
是 | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Mongo 请求费用 | 计数 | 总计
是 | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Mongo 请求 | 计数 | 计数
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Mongo 请求速率 | 每秒计数 | 平均值
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Mongo 删除请求速率 | 每秒计数 | 平均值
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Mongo 插入请求速率 | 每秒计数 | 平均值
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Mongo 查询请求速率 | 每秒计数 | 平均值
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Mongo 更新请求速率 | 每秒计数 | 平均值
否 | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | 预配的吞吐量 | 计数 | 最大值
是 | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | P99 复制延迟 | 毫秒 | 平均值
否 | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | 服务可用性 | 百分比 | 平均值
是 | Microsoft.DocumentDB/databaseAccounts | TotalRequests | 请求总数 | 计数 | 计数
是 | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | 总请求单位数 | 计数 | 总计
否 | EnterpriseKnowledgeGraph/服务 | FailureCount | 失败计数 | 计数 | 计数
否 | EnterpriseKnowledgeGraph/服务 | 成功计数 | 成功计数 | 计数 | 计数
否 | EnterpriseKnowledgeGraph/服务 | SuccessLatency | 成功延迟 | 毫秒 | 平均值
否 | EnterpriseKnowledgeGraph/服务 | TransactionCount | 事务计数 | 计数 | 计数
是 | Microsoft.EventGrid/domains | DeadLetteredCount | 死信事件数 | 计数 | 总计
否 | Microsoft.EventGrid/domains | DeliveryAttemptFailCount | 发送失败的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/domains | DeliverySuccessCount | 发送的事件数 | 计数 | 总计
否 | Microsoft.EventGrid/domains | DestinationProcessingDurationInMs | 目标处理持续时间 | 毫秒 | 平均值
是 | Microsoft.EventGrid/domains | DroppedEventCount | 删除的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/domains | MatchedEventCount | 匹配的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/domains | PublishFailCount | 发布失败事件 | 计数 | 总计
是 | Microsoft.EventGrid/domains | PublishSuccessCount | 发布的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/domains | PublishSuccessLatencyInMs | 发布成功延迟 | 计数 | 总计
是 | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | 死信事件数 | 计数 | 总计
否 | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | 发送失败的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | 发送的事件数 | 计数 | 总计
否 | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | 目标处理持续时间 | 毫秒 | 平均值
是 | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | 删除的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | 匹配的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/extensionTopics | PublishFailCount | 发布失败事件 | 计数 | 总计
是 | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | 发布的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | 发布成功延迟 | 计数 | 总计
是 | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | 不匹配的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/topics | PublishFailCount | 发布失败事件 | 计数 | 总计
是 | Microsoft.EventGrid/topics | PublishSuccessCount | 发布的事件数 | 计数 | 总计
是 | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | 发布成功延迟 | 计数 | 总计
是 | Microsoft.EventGrid/topics | UnmatchedEventCount | 不匹配的事件数 | 计数 | 总计
否 | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | 计数 | 平均值
否 | Microsoft.EventHub/clusters | AvailableMemory | 可用内存 | 百分比 | 最大值
否 | Microsoft.EventHub/clusters | CaptureBacklog | 捕获积压工作(backlog)。 | 计数 | 总计
否 | Microsoft.EventHub/clusters | CapturedBytes | 已捕获的字节数。 | 字节 | 总计
否 | Microsoft.EventHub/clusters | CapturedMessages | 已捕获的消息数。 | 计数 | 总计
否 | Microsoft.EventHub/clusters | ConnectionsClosed | 已关闭的连接数。 | 计数 | 平均值
否 | Microsoft.EventHub/clusters | ConnectionsOpened | 打开的连接数。 | 计数 | 平均值
否 | Microsoft.EventHub/clusters | CPU | CPU | 百分比 | 最大值
是 | Microsoft.EventHub/clusters | IncomingBytes | 传入字节数。 | 字节 | 总计
是 | Microsoft.EventHub/clusters | IncomingMessages | 传入消息数 | 计数 | 总计
是 | Microsoft.EventHub/clusters | IncomingRequests | 传入请求数 | 计数 | 总计
是 | Microsoft.EventHub/clusters | OutgoingBytes | 传出字节数。 | 字节 | 总计
是 | Microsoft.EventHub/clusters | OutgoingMessages | 传出消息数 | 计数 | 总计
否 | Microsoft.EventHub/clusters | QuotaExceededErrors | 超过限额错误。 | 计数 | 总计
否 | Microsoft.EventHub/clusters | ServerErrors | 服务器错误数。 | 计数 | 总计
否 | Microsoft.EventHub/clusters | SuccessfulRequests | 成功的请求数 | 计数 | 总计
否 | Microsoft.EventHub/clusters | ThrottledRequests | 限制的请求数。 | 计数 | 总计
否 | Microsoft.EventHub/clusters | UserErrors | 用户错误数。 | 计数 | 总计
否 | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | 计数 | 平均值
否 | Microsoft.EventHub/namespaces | CaptureBacklog | 捕获积压工作(backlog)。 | 计数 | 总计
否 | Microsoft.EventHub/namespaces | CapturedBytes | 已捕获的字节数。 | 字节 | 总计
否 | Microsoft.EventHub/namespaces | CapturedMessages | 已捕获的消息数。 | 计数 | 总计
否 | Microsoft.EventHub/namespaces | ConnectionsClosed | 已关闭的连接数。 | 计数 | 平均值
否 | Microsoft.EventHub/namespaces | ConnectionsOpened | 打开的连接数。 | 计数 | 平均值
是 | Microsoft.EventHub/namespaces | EHABL | 存档积压消息（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | EHAMBS | 存档消息吞吐量（不推荐使用） | 字节 | 总计
是 | Microsoft.EventHub/namespaces | EHAMSGS | 存档消息（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | EHINBYTES | 传入字节（已弃用） | 字节 | 总计
是 | Microsoft.EventHub/namespaces | EHINMBS | 传入字节（已过时）（不推荐使用） | 字节 | 总计
是 | Microsoft.EventHub/namespaces | EHINMSGS | 传入消息（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | EHOUTBYTES | 传出字节（已弃用） | 字节 | 总计
是 | Microsoft.EventHub/namespaces | EHOUTMBS | 传出字节数（已过时）（不推荐使用） | 字节 | 总计
是 | Microsoft.EventHub/namespaces | EHOUTMSGS | 传出消息（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | FAILREQ | 失败的请求数（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | IncomingBytes | 传入字节数。 | 字节 | 总计
是 | Microsoft.EventHub/namespaces | IncomingMessages | 传入消息数 | 计数 | 总计
是 | Microsoft.EventHub/namespaces | IncomingRequests | 传入请求数 | 计数 | 总计
是 | Microsoft.EventHub/namespaces | INMSGS | 传入消息（已过时）（不推荐使用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | INREQS | 传入请求（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | INTERR | 内部服务器错误（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | MISCERR | 其他错误（不推荐使用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | OutgoingBytes | 传出字节数。 | 字节 | 总计
是 | Microsoft.EventHub/namespaces | OutgoingMessages | 传出消息数 | 计数 | 总计
是 | Microsoft.EventHub/namespaces | OUTMSGS | 传出消息（已过时）（不推荐使用） | 计数 | 总计
否 | Microsoft.EventHub/namespaces | QuotaExceededErrors | 超过限额错误。 | 计数 | 总计
否 | Microsoft.EventHub/namespaces | ServerErrors | 服务器错误数。 | 计数 | 总计
否 | Microsoft.EventHub/namespaces | 大小 | 大小 | 字节 | 平均值
否 | Microsoft.EventHub/namespaces | SuccessfulRequests | 成功的请求数 | 计数 | 总计
是 | Microsoft.EventHub/namespaces | SUCCREQ | 成功的请求数（已弃用） | 计数 | 总计
是 | Microsoft.EventHub/namespaces | SVRBSY | 服务器忙错误（已弃用） | 计数 | 总计
否 | Microsoft.EventHub/namespaces | ThrottledRequests | 限制的请求数。 | 计数 | 总计
否 | Microsoft.EventHub/namespaces | UserErrors | 用户错误数。 | 计数 | 总计
是 | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | 已分类的网关请求数 | 计数 | 总计
是 | Microsoft.HDInsight/clusters | GatewayRequests | 网关请求数 | 计数 | 总计
是 | Microsoft.HDInsight/clusters | NumActiveWorkers | 活动工作线程数 | 计数 | 最大值
是 | Microsoft.HDInsight/clusters | ScalingRequests | 缩放请求 | 计数 | 最大值
是 | Microsoft.Insights/AutoscaleSettings | MetricThreshold | 指标阈值 | 计数 | 平均值
是 | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | 观察到的容量 | 计数 | 平均值
是 | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | 观察到的指标值 | 计数 | 平均值
是 | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | 启动的缩放操作 | 计数 | 总计
是 | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | 可用性 | 百分比 | 平均值
否 | Microsoft.Insights/Components | availabilityResults/count | 可用性测试 | 计数 | 计数
是 | Microsoft.Insights/Components | availabilityResults/duration | 可用性测试持续时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | browserTimings/networkDuration | 页面加载网络连接时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | browserTimings/processingDuration | 客户端处理时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | browserTimings/receiveDuration | 接收响应时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | browserTimings/sendDuration | 发送请求时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | browserTimings/totalDuration | 浏览器页面加载时间 | 毫秒 | 平均值
否 | Microsoft.Insights/Components | dependencies/count | 依赖项调用 | 计数 | 计数
是 | Microsoft.Insights/Components | dependencies/duration | 依赖项持续时间 | 毫秒 | 平均值
否 | Microsoft.Insights/Components | dependencies/failed | 依赖项调用失败 | 计数 | 计数
否 | Microsoft.Insights/Components | exceptions/browser | 浏览器异常 | 计数 | 计数
是 | Microsoft.Insights/Components | exceptions/count | 异常 | 计数 | 计数
否 | Microsoft.Insights/Components | exceptions/server | 服务器异常 | 计数 | 计数
是 | Microsoft.Insights/Components | pageViews/count | 页面视图 | 计数 | 计数
是 | Microsoft.Insights/Components | pageViews/duration | 页面视图加载时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | 异常率 | 每秒计数 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | 可用内存 | 字节 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | 进程 CPU | 百分比 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | 进程 IO 率 | 每秒字节数 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | 处理器时间 | 百分比 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | 进程专用字节 | 字节 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | HTTP 请求执行时间 | 毫秒 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/requestsInQueue | 应用程序队列中的 HTTP 请求 | 计数 | 平均值
是 | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | HTTP 请求速率 | 每秒计数 | 平均值
否 | Microsoft.Insights/Components | requests/count | 服务器请求数 | 计数 | 计数
是 | Microsoft.Insights/Components | requests/duration | 服务器响应时间 | 毫秒 | 平均值
否 | Microsoft.Insights/Components | requests/failed | 失败的请求 | 计数 | 计数
否 | Microsoft.Insights/Components | requests/rate | 服务器请求速率 | 每秒计数 | 平均值
是 | Microsoft.Insights/Components | traces/count | 跟踪 | 计数 | 计数
是 | Microsoft.KeyVault/vaults | ServiceApiHit | 服务 API 命中总计 | 计数 | 计数
是 | Microsoft.KeyVault/vaults | ServiceApiLatency | 总体服务 API 延迟 | 毫秒 | 平均值
是 | Microsoft.KeyVault/vaults | ServiceApiResult | 服务 API 结果总计 | 计数 | 计数
是 | Microsoft.Kusto/Clusters | CacheUtilization | 缓存利用率 | 百分比 | 平均值
是 | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | 连续导出最大迟到分钟数 | 计数 | 最大值
是 | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | 连续导出-导出记录的数目 | 计数 | 总计
是 | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | 连续导出挂起计数 | 计数 | 最大值
是 | Microsoft.Kusto/Clusters | ContinuousExportResult | 连续导出结果 | 计数 | 计数
是 | Microsoft.Kusto/Clusters | CPU | CPU | 百分比 | 平均值
是 | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | 处理的事件数（适用于事件/IoT 中心） | 计数 | 总计
是 | Microsoft.Kusto/Clusters | ExportUtilization | 导出利用率 | 百分比 | 最大值
是 | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | 引入延迟（秒） | 秒数 | 平均值
是 | Microsoft.Kusto/Clusters | IngestionResult | 引入结果 | 计数 | 计数
是 | Microsoft.Kusto/Clusters | IngestionUtilization | 引入利用率 | 百分比 | 平均值
是 | Microsoft.Kusto/Clusters | IngestionVolumeInMB | 引入卷（MB） | 计数 | 总计
是 | Microsoft.Kusto/Clusters | KeepAlive | 保持活动状态 | 计数 | 平均值
是 | Microsoft.Kusto/Clusters | QueryDuration | 查询持续时间 | 毫秒 | 平均值
是 | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | 流式处理引入请求速率 | 计数 | RateRequestsPerSecond
是 | Microsoft.Kusto/Clusters | StreamingIngestDataRate | 流引入数据速率 | 计数 | 平均值
是 | Microsoft.Kusto/Clusters | StreamingIngestDuration | 流摄取持续时间 | 毫秒 | 平均值
是 | Microsoft.Kusto/Clusters | StreamingIngestResults | 流式处理引入结果 | 计数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | 操作延迟  | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | 完成的操作数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | 失败的操作数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | 跳过的操作数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | 启动的操作数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | 成功的操作数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | 操作成功延迟  | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | 操作限制事件数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | 集成服务环境的连接器内存使用情况 | 百分比 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | 集成服务环境的连接器处理器使用情况 | 百分比 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | 集成服务环境的工作流内存使用情况 | 百分比 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | 集成服务环境的工作流处理器使用情况 | 百分比 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | 运行失败百分比 | 百分比 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunLatency | 运行延迟 | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | 已取消运行 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | 已完成的运行数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | 失败的运行数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | 已启动的运行数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | 成功的运行数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | 运行启动限制事件 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | 运行成功延迟 | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | 运行限制事件数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | 触发器激发延迟  | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | 触发器延迟  | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | 完成的触发器数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | 失败的触发器数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | 激发的触发器数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | 跳过的触发器数 | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | 启动的触发器数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | 成功的触发器数  | 计数 | 总计
是 | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | 触发器成功延迟  | 秒数 | 平均值
是 | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | 触发器限制事件数 | 计数 | 总计
是 | Microsoft.Logic/workflows | ActionLatency | 操作延迟  | 秒数 | 平均值
是 | Microsoft.Logic/workflows | ActionsCompleted | 完成的操作数  | 计数 | 总计
是 | Microsoft.Logic/workflows | ActionsFailed | 失败的操作数  | 计数 | 总计
是 | Microsoft.Logic/workflows | ActionsSkipped | 跳过的操作数  | 计数 | 总计
是 | Microsoft.Logic/workflows | ActionsStarted | 启动的操作数  | 计数 | 总计
是 | Microsoft.Logic/workflows | ActionsSucceeded | 成功的操作数  | 计数 | 总计
是 | Microsoft.Logic/workflows | ActionSuccessLatency | 操作成功延迟  | 秒数 | 平均值
是 | Microsoft.Logic/workflows | ActionThrottledEvents | 操作限制事件数 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillableActionExecutions | 计费的操作执行数 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillableTriggerExecutions | 计费的触发器执行数 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillingUsageNativeOperation | 本机操作执行的计费使用情况 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillingUsageNativeOperation | 本机操作执行的计费使用情况 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillingUsageStandardConnector | 标准连接器执行的计费使用情况 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillingUsageStandardConnector | 标准连接器执行的计费使用情况 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillingUsageStorageConsumption | 存储使用执行的计费使用情况 | 计数 | 总计
是 | Microsoft.Logic/workflows | BillingUsageStorageConsumption | 存储使用执行的计费使用情况 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunFailurePercentage | 运行失败百分比 | 百分比 | 总计
是 | Microsoft.Logic/workflows | RunLatency | 运行延迟 | 秒数 | 平均值
是 | Microsoft.Logic/workflows | RunsCancelled | 已取消运行 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunsCompleted | 已完成的运行数 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunsFailed | 失败的运行数 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunsStarted | 已启动的运行数 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunsSucceeded | 成功的运行数 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunStartThrottledEvents | 运行启动限制事件 | 计数 | 总计
是 | Microsoft.Logic/workflows | RunSuccessLatency | 运行成功延迟 | 秒数 | 平均值
是 | Microsoft.Logic/workflows | RunThrottledEvents | 运行限制事件数 | 计数 | 总计
是 | Microsoft.Logic/workflows | TotalBillableExecutions | 计费的执行总数 | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggerFireLatency | 触发器激发延迟  | 秒数 | 平均值
是 | Microsoft.Logic/workflows | TriggerLatency | 触发器延迟  | 秒数 | 平均值
是 | Microsoft.Logic/workflows | TriggersCompleted | 完成的触发器数  | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggersFailed | 失败的触发器数  | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggersFired | 激发的触发器数  | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggersSkipped | 跳过的触发器数 | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggersStarted | 启动的触发器数  | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggersSucceeded | 成功的触发器数  | 计数 | 总计
是 | Microsoft.Logic/workflows | TriggerSuccessLatency | 触发器成功延迟  | 秒数 | 平均值
是 | Microsoft.Logic/workflows | TriggerThrottledEvents | 触发器限制事件数 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 活动核心 | 活动核心 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 活动节点 | 活动节点 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 已完成运行 | 已完成运行 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 失败的运行次数 | 失败的运行次数 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 空闲内核数 | 空闲内核数 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 空闲节点 | 空闲节点 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 正在离开内核 | 正在离开内核 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 退出节点 | 退出节点 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 模型部署失败 | 模型部署失败 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 模型部署已启动 | 模型部署已启动 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 模型部署成功 | 模型部署成功 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 模型注册失败 | 模型注册失败 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 模型注册成功 | 模型注册成功 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 抢先核心 | 抢先核心 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 抢占节点 | 抢占节点 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 配额使用率百分比 | 配额使用率百分比 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 已开始运行 | 已开始运行 | 计数 | 总计
是 | Microsoft.MachineLearningServices/workspaces | 内核总数 | 内核总数 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 节点总数 | 节点总数 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 不可用内核 | 不可用内核 | 计数 | 平均值
是 | Microsoft.MachineLearningServices/workspaces | 不可用节点 | 不可用节点 | 计数 | 平均值
是 | Microsoft.Maps/accounts | 可用性 | 可用性 | 百分比 | 平均值
否 | Microsoft.Maps/accounts | 使用情况 | 使用情况 | 计数 | 计数
是 | Microsoft. Media/windowsazure.mediaservices | AssetCount | 资产计数 | 计数 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | AssetQuota | 资产配额 | 计数 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | AssetQuotaUsedPercentage | 资产配额已用百分比 | 百分比 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | ContentKeyPolicyCount | 内容密钥策略计数 | 计数 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | ContentKeyPolicyQuota | 内容密钥策略配额 | 计数 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | ContentKeyPolicyQuotaUsedPercentage | 内容密钥策略配额已用百分比 | 百分比 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | StreamingPolicyCount | 流式处理策略计数 | 计数 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | StreamingPolicyQuota | 流式处理策略配额 | 计数 | 平均值
是 | Microsoft. Media/windowsazure.mediaservices | StreamingPolicyQuotaUsedPercentage | 流式处理策略配额已用百分比 | 百分比 | 平均值
是 | Windowsazure.mediaservices/Streamingendpoint | 流出量 | 流出量 | 字节 | 总计
是 | Windowsazure.mediaservices/Streamingendpoint | 请求 | 请求 | 计数 | 总计
是 | Windowsazure.mediaservices/Streamingendpoint | SuccessE2ELatency | 成功的端到端延迟 | 毫秒 | 平均值
是 | Microservices4Spring/appClusters | GCPauseTotalCount | GC 暂停计数 | 计数 | 总计
是 | Microservices4Spring/appClusters | GCPauseTotalTime | GC 暂停总时间 | 毫秒 | 总计
是 | Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | 最大可用旧生成数据大小 | 字节 | 平均值
是 | Microservices4Spring/appClusters | OldGenMemoryPoolBytes | 旧生成数据大小 | 字节 | 平均值
是 | Microservices4Spring/appClusters | OldGenPromotedBytes | 提升为旧的生成数据大小 | 字节 | 最大值
是 | Microservices4Spring/appClusters | ServiceCpuUsagePercentage | 服务 CPU 使用率百分比 | 百分比 | 平均值
是 | Microservices4Spring/appClusters | ServiceMemoryCommitted | 分配的服务内存 | 字节 | 平均值
是 | Microservices4Spring/appClusters | ServiceMemoryMax | 服务内存最大值 | 字节 | 最大值
是 | Microservices4Spring/appClusters | ServiceMemoryUsed | 使用的服务内存 | 字节 | 平均值
是 | Microservices4Spring/appClusters | SystemCpuUsagePercentage | 系统 CPU 使用率百分比 | 百分比 | 平均值
是 | Microservices4Spring/appClusters | TomcatErrorCount | Tomcat 全局错误 | 计数 | 总计
是 | Microservices4Spring/appClusters | TomcatReceivedBytes | Tomcat 收到的总字节数 | 字节 | 总计
是 | Microservices4Spring/appClusters | TomcatRequestMaxTime | Tomcat 请求最大时间 | 毫秒 | 最大值
是 | Microservices4Spring/appClusters | TomcatRequestTotalCount | Tomcat 请求总数 | 计数 | 总计
是 | Microservices4Spring/appClusters | TomcatRequestTotalTime | Tomcat 请求总时间 | 毫秒 | 总计
是 | Microservices4Spring/appClusters | TomcatResponseAvgTime | Tomcat 请求平均时间 | 毫秒 | 平均值
是 | Microservices4Spring/appClusters | TomcatSentBytes | Tomcat 发送的总字节数 | 字节 | 总计
是 | Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Tomcat 会话活动计数 | 计数 | 总计
是 | Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Tomcat 会话最大活动计数 | 计数 | 总计
是 | Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tomcat 会话最大活动时间 | 毫秒 | 最大值
是 | Microservices4Spring/appClusters | TomcatSessionCreatedCount | Tomcat 会话创建计数 | 计数 | 总计
是 | Microservices4Spring/appClusters | TomcatSessionExpiredCount | Tomcat 会话过期计数 | 计数 | 总计
是 | Microservices4Spring/appClusters | TomcatSessionRejectedCount | Tomcat 会话已拒绝计数 | 计数 | 总计
是 | Microservices4Spring/appClusters | YoungGenPromotedBytes | 提升为年轻生成数据大小 | 字节 | 最大值
是 | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | 使用的分配卷池 | 字节 | 平均值
是 | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | 卷池的总逻辑大小 | 字节 | 平均值
是 | NetAppAccounts/capacityPools/卷 | AverageReadLatency | 平均读取延迟 | 毫秒 | 平均值
是 | NetAppAccounts/capacityPools/卷 | AverageWriteLatency | 平均写入延迟 | 毫秒 | 平均值
是 | NetAppAccounts/capacityPools/卷 | ReadIops | 读取 IOPS | 每秒计数 | 平均值
是 | NetAppAccounts/capacityPools/卷 | VolumeLogicalSize | 卷逻辑大小 | 字节 | 平均值
是 | NetAppAccounts/capacityPools/卷 | VolumeSnapshotSize | 卷快照大小 | 字节 | 平均值
是 | NetAppAccounts/capacityPools/卷 | WriteIops | 写入 IOPS | 每秒计数 | 平均值
否 | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | 应用程序网关总时间 | 毫秒 | 平均值
否 | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | 每分钟每个正常主机的请求数 | 计数 | 平均值
否 | Microsoft.Network/applicationGateways | BackendConnectTime | 后端连接时间 | 毫秒 | 平均值
否 | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | 后端首字节响应时间 | 毫秒 | 平均值
否 | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | 后端最后字节响应时间 | 毫秒 | 平均值
是 | Microsoft.Network/applicationGateways | BackendResponseStatus | 后端响应状态 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | BlockedCount | Web 应用程序防火墙阻止的请求规则分发 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | BlockedReqCount | Web 应用程序防火墙阻止的请求计数 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | BytesReceived | 接收的字节数 | 字节 | 总计
是 | Microsoft.Network/applicationGateways | BytesSent | 发送的字节数 | 字节 | 总计
否 | Microsoft.Network/applicationGateways | CapacityUnits | 当前容量单位 | 计数 | 平均值
否 | Microsoft.Network/applicationGateways | ClientRtt | 客户端 RTT | 毫秒 | 平均值
否 | Microsoft.Network/applicationGateways | ComputeUnits | 当前计算单元 | 计数 | 平均值
是 | Microsoft.Network/applicationGateways | CurrentConnections | 当前连接 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | FailedRequests | 失败的请求数 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | HealthyHostCount | 正常的主机计数 | 计数 | 平均值
是 | Microsoft.Network/applicationGateways | MatchedCount | Web 应用程序防火墙规则分发总数 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | ResponseStatus | 响应状态 | 计数 | 总计
否 | Microsoft.Network/applicationGateways | 吞吐量 | 吞吐量 | 每秒字节数 | 平均值
是 | Microsoft.Network/applicationGateways | TlsProtocol | 客户端 TLS 协议 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | TotalRequests | 请求总数 | 计数 | 总计
是 | Microsoft.Network/applicationGateways | UnhealthyHostCount | 不正常的主机计数 | 计数 | 平均值
是 | Microsoft.Network/azurefirewalls | ApplicationRuleHit | 应用程序规则命中计数 | 计数 | 总计
是 | Microsoft.Network/azurefirewalls | DataProcessed | 已处理的数据 | 字节 | 总计
是 | Microsoft.Network/azurefirewalls | FirewallHealth | 防火墙运行状况状态 | 百分比 | 平均值
是 | Microsoft.Network/azurefirewalls | NetworkRuleHit | 网络规则命中计数 | 计数 | 总计
是 | Microsoft.Network/azurefirewalls | SNATPortUtilization | SNAT 端口利用率 | 百分比 | 平均值
是 | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | 每秒计数 | 平均值
是 | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | 每秒计数 | 平均值
是 | Microsoft.Network/dnszones | QueryVolume | 查询量 | 计数 | 总计
否 | Microsoft.Network/dnszones | RecordSetCapacityUtilization | 记录集容量使用率 | 百分比 | 最大值
是 | Microsoft.Network/dnszones | RecordSetCount | 记录集计数 | 计数 | 最大值
是 | Microsoft.Network/expressRouteCircuits | ArpAvailability | Arp 可用性 | 百分比 | 平均值
是 | Microsoft.Network/expressRouteCircuits | BgpAvailability | Bgp 可用性 | 百分比 | 平均值
否 | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | 每秒计数 | 平均值
否 | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | 每秒计数 | 平均值
否 | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | 每秒计数 | 平均值
否 | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | 每秒计数 | 平均值
否 | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | 每秒计数 | 平均值
否 | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | 每秒计数 | 平均值
是 | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | 每秒计数 | 平均值
是 | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | 每秒计数 | 平均值
否 | ExpressRouteGateways/网络 | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | 每秒计数 | 平均值
否 | ExpressRouteGateways/网络 | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | 每秒计数 | 平均值
是 | ExpressRoutePorts/网络 | AdminState | AdminState | 计数 | 平均值
是 | ExpressRoutePorts/网络 | LineProtocol | LineProtocol | 计数 | 平均值
是 | ExpressRoutePorts/网络 | PortBitsInPerSecond | BitsInPerSecond | 每秒计数 | 平均值
是 | ExpressRoutePorts/网络 | PortBitsOutPerSecond | BitsOutPerSecond | 每秒计数 | 平均值
是 | ExpressRoutePorts/网络 | RxLightLevel | RxLightLevel | 计数 | 平均值
是 | ExpressRoutePorts/网络 | TxLightLevel | TxLightLevel | 计数 | 平均值
是 | Microsoft.Network/frontdoors | BackendHealthPercentage | 后端运行状况百分比 | 百分比 | 平均值
是 | Microsoft.Network/frontdoors | BackendRequestCount | 后端请求计数 | 计数 | 总计
是 | Microsoft.Network/frontdoors | BackendRequestLatency | 后端请求延迟 | 毫秒 | 平均值
是 | Microsoft.Network/frontdoors | BillableResponseSize | 计费响应大小 | 字节 | 总计
是 | Microsoft.Network/frontdoors | RequestCount | 请求计数 | 计数 | 总计
是 | Microsoft.Network/frontdoors | RequestSize | 请求大小 | 字节 | 总计
是 | Microsoft.Network/frontdoors | ResponseSize | 响应大小 | 字节 | 总计
是 | Microsoft.Network/frontdoors | TotalLatency | 总延迟 | 毫秒 | 平均值
是 | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | 计数 | 总计
否 | Microsoft.Network/loadBalancers | AllocatedSnatPorts | 已分配的 SNAT 端口数（预览） | 计数 | 总计
是 | Microsoft.Network/loadBalancers | ByteCount | 字节计数 | 计数 | 总计
是 | Microsoft.Network/loadBalancers | DipAvailability | 运行状况探测状态 | 计数 | 平均值
是 | Microsoft.Network/loadBalancers | PacketCount | 数据包计数 | 计数 | 总计
是 | Microsoft.Network/loadBalancers | SnatConnectionCount | SNAT 连接计数 | 计数 | 总计
是 | Microsoft.Network/loadBalancers | SYNCount | SYN 计数 | 计数 | 总计
否 | Microsoft.Network/loadBalancers | UsedSnatPorts | 已使用的 SNAT 端口数（预览） | 计数 | 总计
是 | Microsoft.Network/loadBalancers | VipAvailability | 数据路径可用性 | 计数 | 平均值
是 | Microsoft.Network/networkInterfaces | BytesReceivedRate | 接收的字节数 | 字节 | 总计
是 | Microsoft.Network/networkInterfaces | BytesSentRate | 发送的字节数 | 字节 | 总计
是 | Microsoft.Network/networkInterfaces | PacketsReceivedRate | 已接收的数据包数 | 计数 | 总计
是 | Microsoft.Network/networkInterfaces | PacketsSentRate | 发送的数据包数 | 计数 | 总计
是 | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | 平均往返时间（毫秒） | 毫秒 | 平均值
是 | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | 检查失败百分比（预览） | 百分比 | 平均值
是 | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | 失败的探测百分比 | 百分比 | 平均值
是 | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | 往返时间（毫秒）（预览版） | 毫秒 | 平均值
是 | Microsoft.Network/publicIPAddresses | ByteCount | 字节计数 | 计数 | 总计
是 | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | 丢弃的入站字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | 转发的入站字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | BytesInDDoS | 入站字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | 触发 DDoS 缓解的入站 SYN 数据包 | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | 触发 DDoS 缓解的入站 TCP 数据包 | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | 触发 DDoS 缓解的入站 UDP 数据包 | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | 是否遭到 DDoS 攻击 | 计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | PacketCount | 数据包计数 | 计数 | 总计
是 | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | 丢弃的入站数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | 转发的入站数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | PacketsInDDoS | 入站数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | SynCount | SYN 计数 | 计数 | 总计
是 | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | 丢弃的入站 TCP 字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | 转发的入站 TCP 字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | 入站 TCP 字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | 丢弃的入站 TCP 数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | 转发的入站 TCP 数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | 入站 TCP 数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | 丢弃的入站 UDP 字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | 转发的入站 UDP 字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | 入站 UDP 字节 DDoS | 每秒字节数 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | 丢弃的入站 UDP 数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | 转发的入站 UDP 数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | 入站 UDP 数据包 DDoS | 每秒计数 | 最大值
是 | Microsoft.Network/publicIPAddresses | VipAvailability | 数据路径可用性 | 计数 | 平均值
是 | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | 按终结点显示的终结点状态 | 计数 | 最大值
是 | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | 按终结点返回的查询 | 计数 | 总计
是 | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | 网关 S2S 带宽 | 每秒字节数 | 平均值
是 | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | 网关 P2S 带宽 | 每秒字节数 | 平均值
是 | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | P2S 连接计数 | 计数 | 最大值
是 | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | 隧道带宽 | 每秒字节数 | 平均值
是 | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | 隧道流出字节 | 字节 | 总计
是 | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | 隧道流出 TS 不匹配数据包丢弃 | 计数 | 总计
是 | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | 隧道流出数据包 | 计数 | 总计
是 | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | 隧道流入字节 | 字节 | 总计
是 | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | 隧道流入 TS 不匹配数据包丢弃 | 计数 | 总计
是 | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | 隧道流入数据包 | 计数 | 总计
是 | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Ping 到 VM 的往返时间 | 毫秒 | 平均值
是 | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | 向 VM 进行 Ping 操作失败 | 百分比 | 平均值
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming | 传入消息数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests | 所有传入的失败请求数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | 所有传入请求数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | 已发送的已安排推送通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | 已取消的已安排推送通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all | 安装管理操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | 删除安装操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | 获取安装操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | 修补安装操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | 创建或更新安装操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | 所有传出通知 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | 坏通道或已过期通道错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | 通道错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | 有效负载错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | 外部通知系统错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | 成功的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | APNS 坏通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | APNS 已过期通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | APNS 授权错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | APNS 无效通知大小错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | APNS 错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | APNS 成功的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | GCM 身份验证错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | GCM 坏通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | GCM 已过期通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | GCM 授权错误数（凭据无效） | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | GCM 无效的通知格式 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | GCM 无效通知大小错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | GCM 错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | GCM 成功的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | GCM 受限的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | GCM 通道不正确错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | MPNS 身份验证错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | MPNS 坏通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | MPNS 通道断开连接 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | MPNS 丢弃的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | MPNS 无效的凭据 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | MPNS 无效的通知格式 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | MPNS 错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | MPNS 成功的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | MPNS 受限的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | WNS 身份验证错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | WNS 坏通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | WNS 通道断开连接 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | WNS 通道受限 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | WNS 丢弃的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | WNS 已过期通道错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | WNS 授权错误数（凭据无效） | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | WNS 无效的通知格式 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | WNS 无效通知大小错误 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | WNS 授权错误数（令牌无效） | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | WNS 错误数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | WNS 成功的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled | WNS 受限的通知数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | WNS 授权错误数（无法访问） | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | WNS 授权错误数（令牌错误） | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all | 注册操作 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | 注册创建操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | 注册删除操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | 注册读取操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | 注册更新操作数 | 计数 | 总计
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending | 挂起的已安排通知数 | 计数 | 总计
是 | Microsoft.OperationalInsights/workspaces | Average_% Available Memory | 可用内存百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Available Swap Space | 可用交换空间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Committed Bytes In Use | 提交的在用字节数百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% DPC Time | DPC 时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Free Inodes | 可用 Inode 百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Free Space | 可用空间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Free Space | 可用空间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Idle Time | 空闲时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Interrupt Time | 中断时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% IO Wait Time | IO 等待时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Nice Time | 良好时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Privileged Time | 特权时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | 处理器时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | 处理器时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Inodes | 已用 Inode 百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Memory | 已用内存百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Space | 已用空间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Swap Space | 已用交换空间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_% User Time | 用户时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes | 可用兆字节数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Memory | 可用内存 MB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Swap | 可用交换空间 MB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. 磁盘秒数/读取 | 每次读取的平均磁盘扇区数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. 磁盘秒数/读取 | 每次读取的平均磁盘扇区数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. 磁盘秒数/传输 | Avg. Disk sec/Transfer | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. 磁盘秒数/写入 | 每次写入的平均磁盘扇区数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. 磁盘秒数/写入 | 每次写入的平均磁盘扇区数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Bytes Received/sec | 收到的字节数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Bytes Sent/sec | 发送的字节数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Bytes Total/sec | 字节总数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Current Disk Queue Length | Current Disk Queue Length | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Read Bytes/sec | 磁盘读取字节数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | 磁盘读取数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | 磁盘读取数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | 磁盘传输数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | 磁盘传输数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Write Bytes/sec | 磁盘写入字节数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | 磁盘写入数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | 磁盘写入数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | 可用 MB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | 可用 MB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Free Physical Memory | 可用物理内存 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Free Space in Paging Files | 分页文件中的可用空间 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Free Virtual Memory | 可用虚拟内存 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Logical Disk Bytes/sec | 逻辑磁盘字节数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Page Reads/sec | 页面读取数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Page Writes/sec | 页面写入数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Pages/sec | 页面数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Pct Privileged Time | 特权时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Pct User Time | 用户时间百分比 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Physical Disk Bytes/sec | 物理磁盘字节数/秒 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Processes | 流程 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Processor Queue Length | 处理器队列长度 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Size Stored In Paging Files | 分页文件中存储的大小 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes | 字节数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Received | 已接收的字节数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Transmitted | 已传输的字节数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Collisions | 冲突数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Packets Received | 已接收的包数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Packets Transmitted | 已传输的包数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Rx Errors | Rx 错误数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Total Tx Errors | Tx 错误数总计 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Uptime | 运行时间 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Used MBytes Swap Space | 已用交换空间 MB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Used Memory kBytes | 已用内存 KB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Used Memory MBytes | 已用内存 MB 数 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Users | 用户 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | Average_Virtual Shared Memory | 虚拟共享内存 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | 事件 | 事件 | 计数 | 平均值
是 | Microsoft.OperationalInsights/workspaces | 检测信号 | 检测信号 | 计数 | 总计
是 | Microsoft.OperationalInsights/workspaces | 更新 | 更新 | 计数 | 平均值
是 | Microsoft.PowerBIDedicated/capacities | memory_metric | 内存 | 字节 | 平均值
是 | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | 内存抖动（数据集） | 百分比 | 平均值
是 | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | QPU 高利用率 | 计数 | 总计
是 | Microsoft.PowerBIDedicated/capacities | QueryDuration | 查询持续时间（数据集） | 毫秒 | 平均值
是 | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | 查询池作业队列长度（数据集） | 计数 | 平均值
否 | Microsoft.Relay/namespaces | ActiveConnections | ActiveConnections | 计数 | 总计
否 | Microsoft.Relay/namespaces | ActiveListeners | ActiveListeners | 计数 | 总计
是 | Microsoft.Relay/namespaces | BytesTransferred | BytesTransferred | 计数 | 总计
否 | Microsoft.Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | 计数 | 总计
否 | Microsoft.Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | 计数 | 总计
否 | Microsoft.Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | 计数 | 总计
否 | Microsoft.Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | 计数 | 总计
否 | Microsoft.Relay/namespaces | ListenerDisconnects | ListenerDisconnects | 计数 | 总计
否 | Microsoft.Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | 计数 | 总计
否 | Microsoft.Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | 计数 | 总计
否 | Microsoft.Relay/namespaces | SenderConnections-Success | SenderConnections-Success | 计数 | 总计
否 | Microsoft.Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | 计数 | 总计
否 | Microsoft.Relay/namespaces | SenderDisconnects | SenderDisconnects | 计数 | 总计
是 | Microsoft.Search/searchServices | SearchLatency | 搜索延迟 | 秒数 | 平均值
是 | Microsoft.Search/searchServices | SearchQueriesPerSecond | 每秒搜索查询数 | 每秒计数 | 平均值
是 | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | 限制的搜索查询百分比 | 百分比 | 平均值
否 | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | ActiveMessages | 队列/主题中的活动消息计数。 | 计数 | 平均值
否 | Microsoft.ServiceBus/namespaces | ConnectionsClosed | 已关闭的连接数。 | 计数 | 平均值
否 | Microsoft.ServiceBus/namespaces | ConnectionsOpened | 打开的连接数。 | 计数 | 平均值
否 | Microsoft.ServiceBus/namespaces | CPUXNS | CPU （不推荐使用） | 百分比 | 最大值
否 | Microsoft.ServiceBus/namespaces | DeadletteredMessages | 队列/主题中的死信消息计数。 | 计数 | 平均值
是 | Microsoft.ServiceBus/namespaces | IncomingMessages | 传入消息数 | 计数 | 总计
是 | Microsoft.ServiceBus/namespaces | IncomingRequests | 传入请求数 | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | 消息数 | 队列/主题中的消息计数。 | 计数 | 平均值
否 | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | 百分比 | 最大值
否 | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | 内存用量 | 百分比 | 最大值
是 | Microsoft.ServiceBus/namespaces | OutgoingMessages | 传出消息数 | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | ScheduledMessages | 队列/主题中计划消息的计数。 | 计数 | 平均值
否 | Microsoft.ServiceBus/namespaces | ServerErrors | 服务器错误数。 | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | 大小 | 大小 | 字节 | 平均值
否 | Microsoft.ServiceBus/namespaces | SuccessfulRequests | 成功的请求数 | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | ThrottledRequests | 限制的请求数。 | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | UserErrors | 用户错误数。 | 计数 | 总计
否 | Microsoft.ServiceBus/namespaces | WSXNS | 内存使用情况（已弃用） | 百分比 | 最大值
否 | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | 计数 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | 字节 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | 计数 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | 字节 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | 计数 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | 计数 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | 百分比 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | 百分比 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | 计数 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | 计数 | 平均值
否 | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | 计数 | 平均值
是 | Microsoft.SignalRService/SignalR | ConnectionCount | 连接计数 | 计数 | 最大值
是 | Microsoft.SignalRService/SignalR | InboundTraffic | 入站流量 | 字节 | 总计
是 | Microsoft.SignalRService/SignalR | MessageCount | 消息计数 | 计数 | 总计
是 | Microsoft.SignalRService/SignalR | OutboundTraffic | 出站流量 | 字节 | 总计
是 | Microsoft.SignalRService/SignalR | SystemErrors | 系统错误数 | 百分比 | 最大值
是 | Microsoft.SignalRService/SignalR | UserErrors | 用户错误数 | 百分比 | 最大值
是 | Microsoft.Sql/managedInstances | avg_cpu_percent | CPU 平均百分比 | 百分比 | 平均值
是 | Microsoft.Sql/managedInstances | io_bytes_read | 已读取的 IO 字节数 | 字节 | 平均值
是 | Microsoft.Sql/managedInstances | io_bytes_written | 已写入的 IO 字节数 | 字节 | 平均值
是 | Microsoft.Sql/managedInstances | io_requests | IO 请求计数 | 计数 | 平均值
是 | Microsoft.Sql/managedInstances | reserved_storage_mb | 预留的存储空间 | 计数 | 平均值
是 | Microsoft.Sql/managedInstances | storage_space_used_mb | 已使用的存储空间 | 计数 | 平均值
是 | Microsoft.Sql/managedInstances | virtual_core_count | 虚拟核心计数 | 计数 | 平均值
否 | Microsoft.Sql/servers | database_dtu_consumption_percent | DTU 百分比 | 百分比 | 平均值
否 | Microsoft.Sql/servers | database_storage_used | 已用数据空间 | 字节 | 平均值
是 | Microsoft.Sql/servers | dtu_consumption_percent | DTU 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers | dtu_used | 已用的 DTU | 计数 | 平均值
是 | Microsoft.Sql/servers | storage_used | 已用数据空间 | 字节 | 平均值
是 | Microsoft.Sql/servers/databases | allocated_data_storage | 已分配数据空间 | 字节 | 平均值
是 | Microsoft.Sql/servers/databases | app_cpu_billed | 应用 CPU 计费 | 计数 | 总计
是 | Microsoft.Sql/servers/databases | app_cpu_percent | 应用 CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | app_memory_percent | 应用内存百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | blocked_by_firewall | 被防火墙阻止 | 计数 | 总计
是 | Microsoft.Sql/servers/databases | cache_hit_percent | 缓存命中百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | cache_used_percent | 缓存使用百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | connection_failed | 失败的连接数 | 计数 | 总计
是 | Microsoft.Sql/servers/databases | connection_successful | 成功的连接数 | 计数 | 总计
是 | Microsoft.Sql/servers/databases | cpu_limit | CPU 限制 | 计数 | 平均值
是 | Microsoft.Sql/servers/databases | cpu_percent | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | cpu_used | 使用的 CPU | 计数 | 平均值
是 | Microsoft.Sql/servers/databases | deadlock | 死锁数 | 计数 | 总计
是 | Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | dtu_limit | DTU 限制 | 计数 | 平均值
是 | Microsoft.Sql/servers/databases | dtu_used | 已用的 DTU | 计数 | 平均值
是 | Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU 百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | dwu_limit | DWU 限制 | 计数 | 最大值
是 | Microsoft.Sql/servers/databases | dwu_used | 已用的 DWU | 计数 | 最大值
是 | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | 本地 tempdb 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | log_write_percent | 日志 IO 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | memory_usage_percent | 内存百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | physical_data_read_percent | 数据 IO 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | sessions_percent | 会话百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | 存储 | 已用数据空间 | 字节 | 最大值
是 | Microsoft.Sql/servers/databases | storage_percent | 已用数据空间百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | tempdb_data_size | Tempdb 数据文件大小（Kb） | 计数 | 最大值
是 | Microsoft.Sql/servers/databases | tempdb_log_size | Tempdb 日志文件大小（Kb） | 计数 | 最大值
是 | Microsoft.Sql/servers/databases | tempdb_log_used_percent | 使用的 Tempdb 百分比日志 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | workers_percent | 辅助角色百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/databases | xtp_storage_percent | 内存中 OLTP 存储百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | allocated_data_storage | 已分配数据空间 | 字节 | 平均值
是 | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | 已分配的数据空间百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/elasticPools | cpu_limit | CPU 限制 | 计数 | 平均值
是 | Microsoft.Sql/servers/elasticPools | cpu_percent | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | cpu_used | 使用的 CPU | 计数 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | 已分配数据空间 | 字节 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_cpu_limit | CPU 限制 | 计数 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_cpu_percent | CPU 百分比 | 百分比 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_cpu_used | 使用的 CPU | 计数 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | DTU 百分比 | 百分比 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_eDTU_used | 已用的 eDTU | 计数 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_log_write_percent | 日志 IO 百分比 | 百分比 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | 数据 IO 百分比 | 百分比 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_sessions_percent | 会话百分比 | 百分比 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_storage_used | 已用数据空间 | 字节 | 平均值
否 | Microsoft.Sql/servers/elasticPools | database_workers_percent | 辅助角色百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | eDTU_limit | eDTU 限制 | 计数 | 平均值
是 | Microsoft.Sql/servers/elasticPools | eDTU_used | 已用的 eDTU | 计数 | 平均值
是 | Microsoft.Sql/servers/elasticPools | log_write_percent | 日志 IO 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | 数据 IO 百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | sessions_percent | 会话百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | storage_limit | 数据的最大大小 | 字节 | 平均值
是 | Microsoft.Sql/servers/elasticPools | storage_percent | 已用数据空间百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | storage_used | 已用数据空间 | 字节 | 平均值
是 | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Tempdb 数据文件大小（Kb） | 计数 | 最大值
是 | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Tempdb 日志文件大小（Kb） | 计数 | 最大值
是 | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | 使用的 Tempdb 百分比日志 | 百分比 | 最大值
是 | Microsoft.Sql/servers/elasticPools | workers_percent | 辅助角色百分比 | 百分比 | 平均值
是 | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | 内存中 OLTP 存储百分比 | 百分比 | 平均值
是 | Microsoft.Storage/storageAccounts | 可用性 | 可用性 | 百分比 | 平均值
是 | Microsoft.Storage/storageAccounts | 流出量 | 流出量 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts | 入口 | 入口 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts | 事务 | 事务 | 计数 | 总计
否 | Microsoft.Storage/storageAccounts | UsedCapacity | 已用容量 | 字节 | 平均值
是 | Microsoft.Storage/storageAccounts/blobServices | 可用性 | 可用性 | 百分比 | 平均值
否 | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Blob 容量 | 字节 | 平均值
否 | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Blob 计数 | 计数 | 平均值
是 | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Blob 容器计数 | 计数 | 平均值
是 | Microsoft.Storage/storageAccounts/blobServices | 流出量 | 流出量 | 字节 | 总计
否 | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | 索引容量 | 字节 | 平均值
是 | Microsoft.Storage/storageAccounts/blobServices | 入口 | 入口 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/blobServices | 事务 | 事务 | 计数 | 总计
是 | Microsoft.Storage/storageAccounts/fileServices | 可用性 | 可用性 | 百分比 | 平均值
是 | Microsoft.Storage/storageAccounts/fileServices | 流出量 | 流出量 | 字节 | 总计
否 | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | 文件容量 | 字节 | 平均值
否 | Microsoft.Storage/storageAccounts/fileServices | FileCount | 文件计数 | 计数 | 平均值
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | 文件共享计数 | 计数 | 平均值
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | 文件共享配额大小 | 字节 | 平均值
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | 文件共享快照计数 | 计数 | 平均值
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | 文件共享快照大小 | 字节 | 平均值
是 | Microsoft.Storage/storageAccounts/fileServices | 入口 | 入口 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/fileServices | 事务 | 事务 | 计数 | 总计
是 | Microsoft.Storage/storageAccounts/queueServices | 可用性 | 可用性 | 百分比 | 平均值
是 | Microsoft.Storage/storageAccounts/queueServices | 流出量 | 流出量 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts/queueServices | 入口 | 入口 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | 队列容量 | 字节 | 平均值
是 | Microsoft.Storage/storageAccounts/queueServices | QueueCount | 队列计数 | 计数 | 平均值
是 | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | 队列消息计数 | 计数 | 平均值
是 | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/queueServices | 事务 | 事务 | 计数 | 总计
是 | Microsoft.Storage/storageAccounts/tableServices | 可用性 | 可用性 | 百分比 | 平均值
是 | Microsoft.Storage/storageAccounts/tableServices | 流出量 | 流出量 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts/tableServices | 入口 | 入口 | 字节 | 总计
是 | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值
是 | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | 表容量 | 字节 | 平均值
是 | Microsoft.Storage/storageAccounts/tableServices | TableCount | 表计数 | 计数 | 平均值
是 | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | 表实体计数 | 计数 | 平均值
是 | Microsoft.Storage/storageAccounts/tableServices | 事务 | 事务 | 计数 | 总计
是 | StorageCache/缓存 | ClientIOPS | 客户端 IOPS 总数 | 计数 | 平均值
是 | StorageCache/缓存 | ClientLatency | 平均客户端延迟 | 毫秒 | 平均值
是 | StorageCache/缓存 | ClientLockIOPS | 客户端锁定 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | ClientMetadataReadIOPS | 客户端元数据读取 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | ClientMetadataWriteIOPS | 客户端元数据写入 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | ClientReadIOPS | 客户端读取 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | ClientReadThroughput | 平均缓存读取吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | ClientWriteIOPS | 客户端写入 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | ClientWriteThroughput | 平均缓存写入吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetAsyncWriteThroughput | StorageTarget 异步写入吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetFillThroughput | StorageTarget 填充吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetHealth | 存储目标运行状况 | 计数 | 平均值
是 | StorageCache/缓存 | StorageTargetIOPS | StorageTarget IOPS 总数 | 计数 | 平均值
是 | StorageCache/缓存 | StorageTargetLatency | StorageTarget 延迟 | 毫秒 | 平均值
是 | StorageCache/缓存 | StorageTargetMetadataReadIOPS | StorageTarget 元数据读取 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | StorageTargetMetadataWriteIOPS | StorageTarget 元数据写入 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | StorageTargetReadAheadThroughput | StorageTarget 读取提前吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetReadIOPS | StorageTarget 读取 IOPS | 每秒计数 | 平均值
是 | StorageCache/缓存 | StorageTargetSyncWriteThroughput | StorageTarget 同步写入吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetTotalReadThroughput | StorageTarget 总读取吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetTotalWriteThroughput | StorageTarget 总写入吞吐量 | 每秒字节数 | 平均值
是 | StorageCache/缓存 | StorageTargetWriteIOPS | StorageTarget 写入 IOPS | 计数 | 平均值
是 | StorageCache/缓存 | 运行时间 | 运行时间 | 计数 | 平均值
是 | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | 同步会话结果 | 计数 | 平均值
是 | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | 同步的字节数 | 字节 | 总计
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | 云分层通过应用程序撤回大小 | 字节 | 总计
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | 云分层撤回大小 | 字节 | 总计
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | 云分层回调 | 字节 | 总计
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | 云分层召回吞吐量 | 每秒字节数 | 平均值
是 | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | 服务器联机状态 | 计数 | 最大值
是 | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | 已同步文件 | 计数 | 总计
是 | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | 未同步的文件 | 计数 | 总计
是 | storagesync.sys/storageSyncServices/registeredServers | ServerHeartbeat | 服务器联机状态 | 计数 | 最大值
是 | storagesync.sys/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | 云分层回调 | 字节 | 总计
是 | storagesync.sys/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | 同步的字节数 | 字节 | 总计
是 | storagesync.sys/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | 已同步文件 | 计数 | 总计
是 | storagesync.sys/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | 未同步的文件 | 计数 | 总计
是 | storagesync.sys/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | 同步的字节数 | 字节 | 总计
是 | storagesync.sys/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | 已同步文件 | 计数 | 总计
是 | storagesync.sys/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | 未同步的文件 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | 失败的函数请求数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | 函数事件数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | 函数请求数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | 数据转换错误数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | 输入反序列化错误 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | 失序事件数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | 早期输入事件数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | 错误 | 运行时错误 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | 输入事件字节数 | 字节 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | InputEvents | 输入事件数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | 积压的输入事件数 | 计数 | 最大值
是 | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | 收到的输入源数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | 延迟输入事件数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | 输出事件数 | 计数 | 总计
是 | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | 水印延迟 | 秒数 | 最大值
是 | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | 流单元利用率 % | 百分比 | 最大值
是 | Microsoft.VMwareCloudSimple/virtualMachines | 磁盘读取字节数 | 磁盘读取字节数 | 字节 | 总计
是 | Microsoft.VMwareCloudSimple/virtualMachines | 磁盘读取操作次数/秒 | 磁盘读取操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | 磁盘写入字节数 | 磁盘写入字节数 | 字节 | 总计
是 | Microsoft.VMwareCloudSimple/virtualMachines | 磁盘写入操作次数/秒 | 磁盘写入操作次数/秒 | 每秒计数 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Disk Read Bytes/Sec | 每秒字节数 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadLatency | 磁盘读取延迟 | 毫秒 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadOperations | 磁盘读取操作 | 计数 | 总计
是 | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Disk Write Bytes/Sec | 每秒字节数 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteLatency | 磁盘写入延迟 | 毫秒 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteOperations | 磁盘写入操作 | 计数 | 总计
是 | Microsoft.VMwareCloudSimple/virtualMachines | MemoryActive | 活动内存 | 字节 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | MemoryGranted | 已授予内存 | 字节 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | MemoryUsed | 使用的内存 | 字节 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | 网络传入 | 网络传入 | 字节 | 总计
是 | Microsoft.VMwareCloudSimple/virtualMachines | 网络传出 | 网络传出 | 字节 | 总计
是 | Microsoft.VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | 网络字节数/秒 | 每秒字节数 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | 网络输出字节数/秒 | 每秒字节数 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | CPU 百分比 | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.VMwareCloudSimple/virtualMachines | PercentageCpuReady | CPU 可用百分比 | 毫秒 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | 活动请求数 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | 平均响应时间 | 秒数 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | 数据输入 | 字节 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | 数据输出 | 字节 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | 磁盘队列长度 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http 3xx | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | Http 401 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Http 服务器错误 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Http 队列长度 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | 大型应用服务计划工作线程数 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | 中型应用服务计划工作线程数 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | 内存百分比 | 百分比 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | 请求 | 请求 | 计数 | 总计
是 | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | 小型应用服务计划工作线程数 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | 前端总数 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | 内存百分比 | 百分比 | 平均值
是 | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | 可用工作线程数 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | 工作线程总数 | 计数 | 平均值
是 | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | 使用的工作线程数 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | BytesReceived | 数据输入 | 字节 | 总计
是 | Microsoft.Web/serverfarms | BytesSent | 数据输出 | 字节 | 总计
是 | Microsoft.Web/serverfarms | CpuPercentage | CPU 百分比 | 百分比 | 平均值
是 | Microsoft.Web/serverfarms | DiskQueueLength | 磁盘队列长度 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | HttpQueueLength | Http 队列长度 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | MemoryPercentage | 内存百分比 | 百分比 | 平均值
是 | Microsoft.Web/serverfarms | TcpCloseWait | TCP 关闭等待 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpClosing | TCP 关闭 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpEstablished | 已建立 TCP | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpFinWait1 | TCP Fin 等待1 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpFinWait2 | TCP Fin 等待2 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpLastAck | TCP 上次确认 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpSynReceived | 已收到 TCP Syn | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpSynSent | TCP Syn 已发送 | 计数 | 平均值
是 | Microsoft.Web/serverfarms | TcpTimeWait | TCP 时间等待 | 计数 | 平均值
是 | Microsoft.Web/sites | AppConnections | 连接 | 计数 | 平均值
是 | Microsoft.Web/sites | AverageMemoryWorkingSet | 平均内存工作集 | 字节 | 平均值
是 | Microsoft.Web/sites | AverageResponseTime | 平均响应时间 | 秒数 | 平均值
是 | Microsoft.Web/sites | BytesReceived | 数据输入 | 字节 | 总计
是 | Microsoft.Web/sites | BytesSent | 数据输出 | 字节 | 总计
是 | Microsoft.Web/sites | CpuTime | CPU 时间 | 秒数 | 总计
是 | Microsoft.Web/sites | CurrentAssemblies | 当前程序集 | 计数 | 平均值
是 | Microsoft.Web/sites | FunctionExecutionCount | 函数执行计数 | 计数 | 总计
是 | Microsoft.Web/sites | FunctionExecutionUnits | 函数执行单位数 | 计数 | 总计
是 | Microsoft.Web/sites | Gen0Collections | 第 0 代垃圾回收 | 计数 | 总计
是 | Microsoft.Web/sites | Gen1Collections | 第 1 代垃圾回收 | 计数 | 总计
是 | Microsoft.Web/sites | Gen2Collections | 第 2 代垃圾回收 | 计数 | 总计
是 | Microsoft.Web/sites | 控点 | 句柄计数 | 计数 | 平均值
是 | Microsoft.Web/sites | HealthCheckStatus | 运行状况检查状态 | 计数 | 平均值
是 | Microsoft.Web/sites | Http101 | Http 101 | 计数 | 总计
是 | Microsoft.Web/sites | Http2xx | Http 2xx | 计数 | 总计
是 | Microsoft.Web/sites | Http3xx | Http 3xx | 计数 | 总计
是 | Microsoft.Web/sites | Http401 | Http 401 | 计数 | 总计
是 | Microsoft.Web/sites | Http403 | Http 403 | 计数 | 总计
是 | Microsoft.Web/sites | Http404 | Http 404 | 计数 | 总计
是 | Microsoft.Web/sites | Http406 | Http 406 | 计数 | 总计
是 | Microsoft.Web/sites | Http4xx | Http 4xx | 计数 | 总计
是 | Microsoft.Web/sites | Http5xx | Http 服务器错误 | 计数 | 总计
是 | Microsoft.Web/sites | HttpResponseTime | 响应时间 | 秒数 | 平均值
是 | Microsoft.Web/sites | IoOtherBytesPerSecond | IO 每秒其他字节数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites | IoOtherOperationsPerSecond | IO 每秒其他操作数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites | IoReadBytesPerSecond | IO 每秒读取字节数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites | IoReadOperationsPerSecond | IO 每秒读取操作数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites | IoWriteBytesPerSecond | IO 每秒写入字节数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites | IoWriteOperationsPerSecond | IO 每秒写入操作数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites | MemoryWorkingSet | 内存工作集 | 字节 | 平均值
是 | Microsoft.Web/sites | PrivateBytes | 专用字节 | 字节 | 平均值
是 | Microsoft.Web/sites | 请求 | 请求 | 计数 | 总计
是 | Microsoft.Web/sites | RequestsInApplicationQueue | 应用程序队列中的请求数 | 计数 | 平均值
是 | Microsoft.Web/sites | 线程数 | 线程计数 | 计数 | 平均值
是 | Microsoft.Web/sites | TotalAppDomains | 应用程序域总数 | 计数 | 平均值
是 | Microsoft.Web/sites | TotalAppDomainsUnloaded | 卸载的应用程序域总数 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | AppConnections | 连接 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | 平均内存工作集 | 字节 | 平均值
是 | Microsoft.Web/sites/slots | AverageResponseTime | 平均响应时间 | 秒数 | 平均值
是 | Microsoft.Web/sites/slots | BytesReceived | 数据输入 | 字节 | 总计
是 | Microsoft.Web/sites/slots | BytesSent | 数据输出 | 字节 | 总计
是 | Microsoft.Web/sites/slots | CpuTime | CPU 时间 | 秒数 | 总计
是 | Microsoft.Web/sites/slots | CurrentAssemblies | 当前程序集 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | FunctionExecutionCount | 函数执行计数 | 计数 | 总计
是 | Microsoft.Web/sites/slots | FunctionExecutionUnits | 函数执行单位数 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Gen0Collections | 第 0 代垃圾回收 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Gen1Collections | 第 1 代垃圾回收 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Gen2Collections | 第 2 代垃圾回收 | 计数 | 总计
是 | Microsoft.Web/sites/slots | 控点 | 句柄计数 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | HealthCheckStatus | 运行状况检查状态 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | Http101 | Http 101 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http2xx | Http 2xx | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http3xx | Http 3xx | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http401 | Http 401 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http403 | Http 403 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http404 | Http 404 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http406 | Http 406 | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http4xx | Http 4xx | 计数 | 总计
是 | Microsoft.Web/sites/slots | Http5xx | Http 服务器错误 | 计数 | 总计
是 | Microsoft.Web/sites/slots | HttpResponseTime | 响应时间 | 秒数 | 平均值
是 | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | IO 每秒其他字节数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | IO 每秒其他操作数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites/slots | IoReadBytesPerSecond | IO 每秒读取字节数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | IO 每秒读取操作数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | IO 每秒写入字节数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | IO 每秒写入操作数 | 每秒字节数 | 总计
是 | Microsoft.Web/sites/slots | MemoryWorkingSet | 内存工作集 | 字节 | 平均值
是 | Microsoft.Web/sites/slots | PrivateBytes | 专用字节 | 字节 | 平均值
是 | Microsoft.Web/sites/slots | 请求 | 请求 | 计数 | 总计
是 | Microsoft.Web/sites/slots | RequestsInApplicationQueue | 应用程序队列中的请求数 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | 线程数 | 线程计数 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | TotalAppDomains | 应用程序域总数 | 计数 | 平均值
是 | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | 卸载的应用程序域总数 | 计数 | 平均值
