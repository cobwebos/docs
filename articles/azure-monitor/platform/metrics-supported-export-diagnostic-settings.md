---
title: 具有 NULL 和零值的指标导出行为
description: 讨论导出指标时的 NULL 与零值，以及指向不可导出指标的列表的指针。
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: ca6acb97e52123a0663d988b3f217d305bce2c4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131678"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>可通过诊断设置导出的 Azure Monitor 平台指标

Azure Monitor 默认提供[平台指标](data-platform-metrics.md)，无需配置。 它提供多种方式来与平台指标交互，包括在门户中制作指标图表、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 有关 Azure Monitor 的合并指标管道中当前可用的平台指标的完整列表，请参阅[支持的指标](metrics-supported.md)。 若要查询和访问这些指标，请使用 [2018-01-01 API 版本](/rest/api/monitor/metricdefinitions)。 其他指标可在门户或旧版 API 中使用。

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>无法通过诊断设置导出的指标

以前位于此位置的内容已移至 [Azure Monitor 指标的支持列表](metrics-supported.md#exporting-platform-metrics-to-other-locations)。

通过诊断设置导出指标时存在一些限制。 可使用 REST API 导出所有指标。 

## <a name="exported-zero-vs-null-values"></a>导出的零与 NULL 值 

指标在处理 0 或 NULL 值时具有不同的行为。  如果未获取任何数据，则某些指标（例如有关 http 故障的指标）报告 0。 如果未获取任何数据，则其他指标将存储 NULL，因为它可以指示资源处于离线状态。 以图表显示具有 NULL 值的这些指标时，你会看到[虚线](metrics-troubleshoot.md#chart-shows-dashed-line)所示的差异。 

平台指标可以通过诊断设置导出时，将与指标的行为匹配。 也就是说，资源不发送数据时，平台指标将导出 NULL。  仅当基础资源真正发出数据时，平台指标才会导出“0”。 

如果删除了资源组或特定的资源，来自受影响资源的指标数据将不再发送到诊断设置导出目标。 也就是说，它不再显示在事件中心、Azure 存储帐户和 Log Analytics 工作区中。

### <a name="metrics-that-used-to-export-zero-for-null"></a>过去为 NULL 导出零的指标

在 2020 年 6 月 1 日之前，以下指标曾在没有数据时发出“0”。 然后，可以将这些零导出到 Log Analytics 和 Azure 存储等下游系统。  此行为在实际的“0”（由资源发出）和解译的“0”(NULL) 之间引起了一些混淆，因此，已更改此行为以匹配基础指标的行为，如上一节中所述。 

更改发生在所有公有和私有云中。

此项更改未影响以下任何体验的行为： 
   - 通过诊断设置导出的平台资源日志
   - 指标资源管理器中的指标图表
   - 有关平台指标的警报
 
以下是行为已更改的指标的列表。 

| ResourceType                    | 指标               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  未经授权的网关请求数（已弃用）  | 
| Microsoft.ApiManagement/service | TotalRequests |  网关请求总数（已弃用）  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  成功的网关请求数（已弃用）  | 
| Microsoft.ApiManagement/service | 请求 |  请求  | 
| Microsoft.ApiManagement/service | OtherRequests |  其他网关请求数（已弃用）  | 
| Microsoft.ApiManagement/service | FailedRequests |  失败的网关请求数（已弃用）  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  失败的事件中心事件数  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  事件中心事件总数  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  事件中心事件大小  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  超时的事件中心事件数  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  限制的事件中心事件数  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  成功的事件中心事件数  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  拒绝的事件中心事件数  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  删除的事件中心事件数  | 
| Microsoft.ApiManagement/service | 持续时间 |  网关请求的总持续时间  | 
| Microsoft.ApiManagement/service | BackendDuration |  后端请求持续时间  | 
| Microsoft.DBforMariaDB/servers | storage_used |  已用的存储量  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  存储空间百分比  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  存储限制  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  服务器日志已用的存储量  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  服务器日志存储空间百分比  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  服务器存储空间上限  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  复制延迟（秒）  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  网络传入  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  网络传出  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  内存百分比  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  IO 百分比  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  CPU 百分比  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  失败的连接数  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  已用的备份存储  | 
| Microsoft.DBforMariaDB/servers | active_connections |  活动连接数  | 
| Microsoft.DBforMySQL/servers | storage_used |  已用的存储量  | 
| Microsoft.DBforMySQL/servers | storage_percent |  存储空间百分比  | 
| Microsoft.DBforMySQL/servers | storage_limit |  存储限制  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  服务器日志已用的存储量  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  服务器日志存储空间百分比  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  服务器存储空间上限  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  复制延迟（秒）  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  网络传入  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  网络传出  | 
| Microsoft.DBforMySQL/servers | memory_percent |  内存百分比  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  IO 百分比  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  CPU 百分比  | 
| Microsoft.DBforMySQL/servers | connections_failed |  失败的连接数  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  已用的备份存储  | 
| Microsoft.DBforMySQL/servers | active_connections |  活动连接数  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  数字孪生节点遥测占位符  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  成功的孪生查询数  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  孪生查询结果大小  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  失败的孪生查询数  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  成功的作业查询数  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  失败的作业查询数  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  对列出作业的成功调用数  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  对列出作业的失败调用数  | 
| Microsoft.Devices/IotHubs | jobs.failed |  失败的作业数  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  孪生更新作业创建成功数  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  孪生更新作业创建失败数  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  方法调用作业的创建成功数  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  方法调用作业的创建失败数  | 
| Microsoft.Devices/IotHubs | jobs.completed |  已完成的作业  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  成功的作业取消数  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  失败的作业取消数  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  事件网格延迟（预览）  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  事件网格传递次数（预览版）  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  设备总数（已弃用）  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  连接的设备数（已弃用）   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  设备数据用量总计（预览）  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  设备数据用量总计  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  已使用的消息总数  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  设备的成功孪生更新数  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  设备的孪生更新的大小  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  设备的失败孪生更新数  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  设备的成功孪生读取数  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  设备的孪生读取的响应大小  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  设备的失败孪生读取数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  发送的遥测消息数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  限制错误数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol |  遥测消息发送尝试次数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  路由：遥测消息传送次数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  路由：遥测消息孤立次数   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  路由：遥测消息不兼容  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  路由：消息传送到回退路由的次数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  路由：遥测消息删除次数   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  路由：存储的消息延迟  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  路由：服务总线主题的消息延迟  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  路由：服务总线队列的消息延迟  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  路由：事件中心的消息延迟  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events |  路由：消息/事件的消息延迟  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  路由：传送到存储的数据量  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  路由：将 Blob 传送到存储的次数  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  路由：消息传送到存储的次数  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  路由：消息传送到服务总线主题的次数  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  路由：消息传送到服务总线队列的次数  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  路由：消息传送到事件中心的次数  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  路由：消息传送到消息/事件的次数  | 
| Microsoft.Devices/IotHubs | 配置 |  配置指标  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  过期的 C2D 消息数（预览版）  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  后端的成功孪生更新数  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  后端的失败孪生更新大小  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  后端的失败孪生更新数  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  后端的成功孪生读取数  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  后端的孪生读取的响应大小  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  后端的失败孪生读取数  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  成功的直接方法调用数  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  直接方法调用的响应大小  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  直接方法调用的请求大小  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  失败的直接方法调用数  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  拒绝的 C2D 消息数  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  已完成的 C2D 消息传递次数  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  丢弃的 C2D 消息数  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  注册尝试次数  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  已分配设备  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  证明尝试次数  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  总请求单位数  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  请求总数  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo 请求  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo 请求费用  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  发布成功延迟  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  发布的事件数  | 
| Microsoft.EventGrid/domains | PublishFailCount |  发布失败的事件数  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  匹配的事件数  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  删除的事件数  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  发送的事件数  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  死信事件数  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  匹配的事件数  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  删除的事件数  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  发送的事件数  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  死信事件数  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  不匹配的事件数  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  发布成功延迟  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  发布的事件数  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  发布失败的事件数  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  不匹配的事件数  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  发布成功延迟  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  发布的事件数  | 
| Microsoft.EventGrid/topics | PublishFailCount |  发布失败的事件数  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  传出消息数  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  传出字节数。  | 
| Microsoft.EventHub/clusters | IncomingRequests |  传入请求数  | 
| Microsoft.EventHub/clusters | IncomingMessages |  传入消息数  | 
| Microsoft.EventHub/clusters | IncomingBytes |  传入字节数。  | 
| Microsoft.EventHub/namespaces | SVRBSY |  服务器繁忙错误数（已弃用）  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  成功的请求数（已弃用）  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  传出的消息数（已过时）（已弃用）  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  传出消息数  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  传出字节数。  | 
| Microsoft.EventHub/namespaces | MISCERR |  其他错误（已弃用）  | 
| Microsoft.EventHub/namespaces | INTERR |  内部服务器错误数（已弃用）  | 
| Microsoft.EventHub/namespaces | INREQS |  传入的请求数（已弃用）  | 
| Microsoft.EventHub/namespaces | INMSGS |  传入的消息数（已过时）（已弃用）  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  传入请求数  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  传入消息数  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  传入字节数。  | 
| Microsoft.EventHub/namespaces | FAILREQ |  失败的请求数（已弃用）  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  传出消息（已弃用）  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  传出字节数（已过时）（已弃用）  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  传出字节（已弃用）  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  传入消息（已弃用）  | 
| Microsoft.EventHub/namespaces | EHINMBS |  传入字节数（已过时）（已弃用）  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  传入字节（已弃用）  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  存档消息数（已弃用）  | 
| Microsoft.EventHub/namespaces | EHAMBS |  存档消息吞吐量（已弃用）  | 
| Microsoft.EventHub/namespaces | EHABL |  存档积压工作消息数（已弃用）  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  活动辅助角色数目  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  网关请求数  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  已分类的网关请求数  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  启动的缩放操作  | 
| Microsoft.Insights/Components | traces/count |  跟踪  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  HTTP 请求速率  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  应用程序队列中的 HTTP 请求  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  异常率  | 
| Microsoft.Insights/Components | pageViews/count |  页面视图  | 
| Microsoft.Insights/Components | exceptions/count |  异常  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  流引入结果  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  流引入持续时间  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  流引入数据速率  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  流引入请求速率  | 
| Microsoft.Kusto/Clusters | QueryDuration |  查询持续时间  | 
| Microsoft.Kusto/Clusters | KeepAlive |  保持活动状态  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  引入量 (MB)  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  引入利用率  | 
| Microsoft.Kusto/Clusters | IngestionResult |  引入结果  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  引入延迟（秒）  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  导出利用率  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  处理的事件数（适用于事件中心/IoT 中心）  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  连续导出结果  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  连续导出挂起计数  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  连续导出 – 导出的记录数  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  连续导出最大延迟分钟数  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  缓存利用率  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  触发器限制事件数  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  成功的触发器数   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  启动的触发器数   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  跳过的触发器数  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  激发的触发器数   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  失败的触发器数   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  完成的触发器数   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  运行限制事件数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  运行启动限制事件  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  成功的运行数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  已启动的运行数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  失败的运行数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  已完成的运行数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  已取消的运行数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  运行失败百分比  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  操作限制事件数  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  成功的操作数   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  启动的操作数   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  跳过的操作数   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  失败的操作数   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  完成的操作数   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  触发器限制事件数  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  成功的触发器数   | 
| Microsoft.Logic/workflows | TriggersStarted |  启动的触发器数   | 
| Microsoft.Logic/workflows | TriggersSkipped |  跳过的触发器数  | 
| Microsoft.Logic/workflows | TriggersFired |  激发的触发器数   | 
| Microsoft.Logic/workflows | TriggersFailed |  失败的触发器数   | 
| Microsoft.Logic/workflows | TriggersCompleted |  完成的触发器数   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  计费的执行总数  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  运行限制事件数  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  运行启动限制事件  | 
| Microsoft.Logic/workflows | RunsSucceeded |  成功的运行数  | 
| Microsoft.Logic/workflows | RunsStarted |  已启动的运行数  | 
| Microsoft.Logic/workflows | RunsFailed |  失败的运行数  | 
| Microsoft.Logic/workflows | RunsCompleted |  已完成的运行数  | 
| Microsoft.Logic/workflows | RunsCancelled |  已取消的运行数  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  运行失败百分比  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  存储使用执行的计费使用情况  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  存储使用执行的计费使用情况  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  标准连接器执行的计费使用情况  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  标准连接器执行的计费使用情况  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  本机操作执行的计费使用情况  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  本机操作执行的计费使用情况  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  计费的触发器执行数  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  计费的操作执行数  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  操作限制事件数  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  成功的操作数   | 
| Microsoft.Logic/workflows | ActionsStarted |  启动的操作数   | 
| Microsoft.Logic/workflows | ActionsSkipped |  跳过的操作数   | 
| Microsoft.Logic/workflows | ActionsFailed |  失败的操作数   | 
| Microsoft.Logic/workflows | ActionsCompleted |  完成的操作数   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Web 应用程序防火墙请求计数  | 
| Microsoft.Network/frontdoors | TotalLatency |  总延迟  | 
| Microsoft.Network/frontdoors | ResponseSize |  响应大小  | 
| Microsoft.Network/frontdoors | RequestSize |  请求大小  | 
| Microsoft.Network/frontdoors | RequestCount |  请求计数  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  计费响应大小  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  后端请求延迟  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  后端请求计数  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  后端运行状况百分比  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  按终结点返回的查询  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  挂起的已计划通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  注册更新操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  注册读取操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  注册删除操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  注册创建操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  注册操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  WNS 授权错误数（令牌错误）  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  WNS 授权错误数（无法访问）  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  WNS 受限的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  WNS 成功的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  WNS 错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  WNS 授权错误数（令牌无效）  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  WNS 无效通知大小错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  WNS 无效的通知格式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  WNS 授权错误数（凭据无效）  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  WNS 已过期通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  WNS 丢弃的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  WNS 通道受限  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  WNS 通道断开连接  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  WNS 坏通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  WNS 身份验证错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  MPNS 受限的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  MPNS 成功的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  MPNS 错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  MPNS 无效的通知格式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  MPNS 无效的凭据  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  MPNS 丢弃的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  MPNS 通道断开连接  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  MPNS 坏通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  MPNS 身份验证错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  GCM 通道不正确错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  GCM 受限的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  GCM 成功的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  GCM 错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  GCM 无效通知大小错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  GCM 无效的通知格式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  GCM 授权错误数（凭据无效）  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  GCM 已过期通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  GCM 坏通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  GCM 身份验证错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  APNS 成功的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  APNS 错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  APNS 无效通知大小错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  APNS 授权错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  APNS 已过期通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  APNS 坏通道错误  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  成功的通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  外部通知系统错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  有效负载错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  通道错误数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  所有传出通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  创建或更新安装操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  修补安装操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  获取安装操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  删除安装操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  安装管理操作数目  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  已取消的已安排推送通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  已发送的已安排推送通知数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  所有传入请求数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  所有传入的失败请求数  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  传入消息数  | 
| Microsoft.OperationalInsights/workspaces | 检测信号 |  检测信号  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  传出消息数  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  传入请求数  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  传入消息数  | 
| Microsoft.SignalRService/SignalR | UserErrors |  用户错误数  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  系统错误数  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  出站流量  | 
| Microsoft.SignalRService/SignalR | MessageCount |  消息计数  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  入站流量  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  连接计数  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  CPU 平均百分比  | 
| Microsoft.Sql/servers | dtu_used |  已用的 DTU  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  DTU 百分比  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  内存中 OLTP 存储百分比  | 
| Microsoft.Sql/servers/databases | workers_percent |  辅助角色百分比  | 
| Microsoft.Sql/servers/databases | sessions_percent |  会话百分比  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  数据 IO 百分比  | 
| Microsoft.Sql/servers/databases | log_write_percent |  日志 IO 百分比  | 
| Microsoft.Sql/servers/databases | dwu_used |  已用的 DWU  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  DWU 百分比  | 
| Microsoft.Sql/servers/databases | dtu_used |  已用的 DTU  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  DTU 百分比  | 
| Microsoft.Sql/servers/databases | deadlock |  死锁数  | 
| Microsoft.Sql/servers/databases | cpu_used |  已用 CPU  | 
| Microsoft.Sql/servers/databases | cpu_percent |  CPU 百分比  | 
| Microsoft.Sql/servers/databases | connection_successful |  成功的连接数  | 
| Microsoft.Sql/servers/databases | connection_failed |  失败的连接数  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  缓存命中百分比  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  被防火墙阻止  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  内存中 OLTP 存储百分比  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  辅助角色百分比  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  会话百分比  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  数据 IO 百分比  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  日志 IO 百分比  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  已用的 eDTU  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  DTU 百分比  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  CPU 百分比  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  前端总数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  小型应用服务计划工作线程数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | 请求 |  请求  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  内存百分比  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  中型应用服务计划工作线程数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  大型应用服务计划工作线程数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Http 队列长度  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  Http 服务器错误  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  磁盘队列长度  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU 百分比  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  数据输出  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  数据输入  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  平均响应时间  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  活动请求数  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  使用的工作线程数  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  工作线程总数  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  可用工作线程数  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  内存百分比  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  CPU 百分比  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  TCP 时间等待  | 
| Microsoft.Web/serverfarms | TcpSynSent |  发送的 TCP Syn  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  收到的 TCP Syn  | 
| Microsoft.Web/serverfarms | TcpLastAck |  TCP 上一次的 Ack  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  TCP Fin 等待 2  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  TCP Fin 等待 1  | 
| Microsoft.Web/serverfarms | TcpEstablished |  已建立的 TCP  | 
| Microsoft.Web/serverfarms | TcpClosing |  TCP 正在关闭  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  TCP 关闭等待  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  内存百分比  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Http 队列长度  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  磁盘队列长度  | 
| Microsoft.Web/serverfarms | CpuPercentage |  CPU 百分比  | 
| Microsoft.Web/serverfarms | BytesSent |  数据输出  | 
| Microsoft.Web/serverfarms | BytesReceived |  数据输入  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  卸载的应用程序域总数  | 
| Microsoft.Web/sites | TotalAppDomains |  应用程序域总数  | 
| Microsoft.Web/sites | 线程数 |  线程计数  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  应用程序队列中的请求数  | 
| Microsoft.Web/sites | 请求 |  请求  | 
| Microsoft.Web/sites | PrivateBytes |  专用字节数  | 
| Microsoft.Web/sites | MemoryWorkingSet |  内存工作集  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  IO 每秒写入操作数  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  IO 每秒写入字节数  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  IO 每秒读取操作数  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  IO 每秒读取字节数  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  IO 每秒其他操作数  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  IO 每秒其他字节数  | 
| Microsoft.Web/sites | HttpResponseTime |  响应时间  | 
| Microsoft.Web/sites | Http5xx |  Http 服务器错误  | 
| Microsoft.Web/sites | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites | Http406 |  Http 406  | 
| Microsoft.Web/sites | Http404 |  Http 404  | 
| Microsoft.Web/sites | Http403 |  Http 403  | 
| Microsoft.Web/sites | Http401 |  Http 401  | 
| Microsoft.Web/sites | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  运行状况检查状态  | 
| Microsoft.Web/sites | 句柄数 |  句柄计数  | 
| Microsoft.Web/sites | Gen2Collections |  第 2 代垃圾回收  | 
| Microsoft.Web/sites | Gen1Collections |  第 1 代垃圾回收  | 
| Microsoft.Web/sites | Gen0Collections |  第 0 代垃圾回收  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  函数执行单位数  | 
| Microsoft.Web/sites | FunctionExecutionCount |  函数执行计数  | 
| Microsoft.Web/sites | CurrentAssemblies |  当前程序集  | 
| Microsoft.Web/sites | CpuTime |  CPU 时间  | 
| Microsoft.Web/sites | BytesSent |  数据输出  | 
| Microsoft.Web/sites | BytesReceived |  数据输入  | 
| Microsoft.Web/sites | AverageResponseTime |  平均响应时间  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  平均内存工作集  | 
| Microsoft.Web/sites | AppConnections |  连接  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  卸载的应用程序域总数  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  应用程序域总数  | 
| Microsoft.Web/sites/slots | 线程数 |  线程计数  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  应用程序队列中的请求数  | 
| Microsoft.Web/sites/slots | 请求 |  请求  | 
| Microsoft.Web/sites/slots | PrivateBytes |  专用字节数  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  内存工作集  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  IO 每秒写入操作数  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  IO 每秒写入字节数  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  IO 每秒读取操作数  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  IO 每秒读取字节数  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  IO 每秒其他操作数  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  IO 每秒其他字节数  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  响应时间  | 
| Microsoft.Web/sites/slots | Http5xx |  Http 服务器错误  | 
| Microsoft.Web/sites/slots | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  Http 406  | 
| Microsoft.Web/sites/slots | Http404 |  Http 404  | 
| Microsoft.Web/sites/slots | Http403 |  Http 403  | 
| Microsoft.Web/sites/slots | Http401 |  Http 401  | 
| Microsoft.Web/sites/slots | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  运行状况检查状态  | 
| Microsoft.Web/sites/slots | 句柄数 |  句柄计数  | 
| Microsoft.Web/sites/slots | Gen2Collections |  第 2 代垃圾回收  | 
| Microsoft.Web/sites/slots | Gen1Collections |  第 1 代垃圾回收  | 
| Microsoft.Web/sites/slots | Gen0Collections |  第 0 代垃圾回收  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  函数执行单位数  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  函数执行计数  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  当前程序集  | 
| Microsoft.Web/sites/slots | CpuTime |  CPU 时间  | 
| Microsoft.Web/sites/slots | BytesSent |  数据输出  | 
| Microsoft.Web/sites/slots | BytesReceived |  数据输入  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  平均响应时间  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  平均内存工作集  | 
| Microsoft.Web/sites/slots | AppConnections |  连接  | 
| Microsoft.Sql/servers/databases | cpu_percent | CPU 百分比 | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | 数据 IO 百分比 | 
| Microsoft.Sql/servers/databases | log_write_percent | 日志 IO 百分比 | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU 百分比 | 
| Microsoft.Sql/servers/databases | connection_successful | 成功的连接数 | 
| Microsoft.Sql/servers/databases | connection_failed | 失败的连接数 | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | 被防火墙阻止 | 
| Microsoft.Sql/servers/databases | deadlock | 死锁数 | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | 内存中 OLTP 存储百分比 | 
| Microsoft.Sql/servers/databases | workers_percent | 辅助角色百分比 | 
| Microsoft.Sql/servers/databases | sessions_percent | 会话百分比 | 
| Microsoft.Sql/servers/databases | dtu_used | 已用的 DTU | 
| Microsoft.Sql/servers/databases | cpu_used | 已用 CPU | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU 百分比 | 
| Microsoft.Sql/servers/databases | dwu_used | 已用的 DWU | 
| Microsoft.Sql/servers/databases | cache_hit_percent | 缓存命中百分比 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | 按系统百分比列出的工作负荷组分配 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | 按最大资源百分比列出的工作负荷组分配 | 
| Microsoft.Sql/servers/databases | wlg_active_queries | 工作负荷组活动查询 | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | 工作负荷组排队查询 | 
| Microsoft.Sql/servers/databases | active_queries | 活动查询数 | 
| Microsoft.Sql/servers/databases | queued_queries | 排队的查询数 | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | 工作负荷组查询超时 | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | 排队的工作负载组查询超时 | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | 有效最小资源百分比 | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | 有效上限资源百分比 | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | CPU 百分比 | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | 数据 IO 百分比 | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | 日志 IO 百分比 | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU 百分比 | 
| Microsoft.Sql/servers/elasticPools | workers_percent | 辅助角色百分比 | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | 会话百分比 | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | 已用的 eDTU | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | 内存中 OLTP 存储百分比 | 
| Microsoft.Sql/servers | dtu_consumption_percent | DTU 百分比 | 
| Microsoft.Sql/servers | dtu_used | 已用的 DTU | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | CPU 平均百分比 | 

