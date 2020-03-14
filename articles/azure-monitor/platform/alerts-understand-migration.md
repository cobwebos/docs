---
title: 了解自愿迁移工具如何处理 Azure Monitor 警报
description: 了解警报迁移工具的工作原理，并解决问题。
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 8cc77d13567910797cd519ac193b848f3ea434da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274809"
---
# <a name="understand-how-the-migration-tool-works"></a>了解迁移工具的工作原理

如前所述[，Azure Monitor](monitoring-classic-retirement.md)中的经典警报在2019年8月31日停用（最初为6月30日，2019）。 向使用经典警报规则的客户和想要触发迁移的客户提供 Azure 门户迁移工具。

本文介绍自愿迁移工具的工作原理。 还介绍了一些常见问题的补救措施。

> [!NOTE]
> 由于迁移工具的推出延迟，经典警报的停用日期已于2019年6月 30 2019 日最初发布日期[延长到了年8月 31](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)日。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>不会迁移的经典警报规则

> [!IMPORTANT]
> 活动日志警报（包括服务运行状况警报）和日志警报不受迁移的影响。 迁移仅适用于[此处](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)所述的经典警报规则。

尽管该工具可以迁移几乎所有[经典警报规则](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)，但还是有一些例外情况。 以下警报规则不会使用此工具进行迁移（或从2019年9月开始自动迁移期间）：

- 虚拟机来宾指标（Windows 和 Linux）上的经典警报规则。 请参阅本文后面的在[新的指标警报中重新创建此类警报规则的指南](#guest-metrics-on-virtual-machines)。
- 经典存储指标的经典警报规则。 请参阅[监视经典存储帐户的指南](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 针对某些存储帐户指标的经典警报规则。 请参阅本文后面的[详细信息](#storage-account-metrics)。
- 针对某些 Cosmos DB 指标的经典警报规则。 请参阅本文后面的[详细信息](#cosmos-db-metrics)。
- 针对所有经典虚拟机和云服务指标（Microsoft.classiccompute/virtualMachines 和 Microsoft.classiccompute/domainNames/个角色）的经典警报规则。 请参阅本文后面的[详细信息](#classic-compute-metrics)。

如果你的订阅有任何此类经典规则，则必须手动进行迁移。 因为我们无法提供自动迁移，所以，在2020年6月之前，这些类型的任何现有的经典指标警报都将继续工作。 此扩展提供了将时间移到新警报的时间。 还可以继续在2020年6月之前的列出异常上创建新的经典警报。 但对于其他所有内容，2019年8月后将不能创建新的经典警报。

> [!NOTE]
> 除了以上列出的异常，如果经典警报规则无效（即它们处于[弃用的指标](#classic-alert-rules-on-deprecated-metrics)或已删除的资源），则它们将不会迁移，并且在服务停用后将无法使用。

### <a name="guest-metrics-on-virtual-machines"></a>虚拟机上的来宾指标

必须先将来宾指标发送到 Azure Monitor 自定义度量值存储，然后才能创建新的指标警报。 按照以下说明在诊断设置中启用 Azure Monitor 接收器：

- [为 Windows Vm 启用来宾指标](collect-custom-metrics-guestos-resource-manager-vm.md)
- [为 Linux Vm 启用来宾指标](collect-custom-metrics-linux-telegraf.md)

完成这些步骤后，可以创建新的指标警报来处理来宾指标。 创建新的指标警报后，可以删除经典警报。

### <a name="storage-account-metrics"></a>存储帐户指标

可以迁移有关存储帐户的所有经典警报，但这些指标的警报除外：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

基于百分比度量值的经典警报规则必须基于[新旧存储指标之间的映射](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)进行迁移。 阈值将需要相应地进行修改，因为可用的新度量值是一个绝对指标。

AnonymousThrottlingError、SASThrottlingError 和 ThrottlingError 上的经典警报规则必须拆分为两个新警报，因为没有提供相同功能的组合指标。 需要适当调整阈值。

### <a name="cosmos-db-metrics"></a>Cosmos DB 指标

可以迁移 Cosmos DB 指标的所有经典警报，但这些指标的警报除外：

- 每秒平均请求数
- 一致性级别
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- 内部服务器错误
- 每分钟使用的最大 RUPM
- 最大每秒 ru 数
- Mongo Count 失败请求数
- Mongo 删除失败的请求
- Mongo 插入失败的请求
- Mongo 其他失败的请求
- 其他请求费用 Mongo
- Mongo 其他请求速率
- Mongo 查询失败的请求数
- Mongo 更新失败请求数
- 观察到的读取延迟
- 观察到的写入延迟
- 服务可用性
- 存储容量
- 限制的请求
- 请求总数

每秒平均请求数，一致性级别，每分钟使用的最大 RUPM 数，最大每秒的每秒，观察到的读取延迟，观察到的写入延迟，存储容量目前在[新系统](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)中不可用。

不会迁移 http 2xx、Http 3xx、Http 400、Http 401、内部服务器错误、服务可用性、受限请求和请求总数等请求指标的警报，因为在经典指标和新指标之间对请求进行计数的方式不同。 需要手动重新创建这些警报，并调整阈值。

Mongo 失败请求的警报必须拆分为多个警报，因为没有提供相同功能的组合指标。 需要适当调整阈值。

### <a name="classic-compute-metrics"></a>典型计算指标

对于经典计算指标的任何警报都不会使用迁移工具迁移，因为新警报尚不支持经典计算资源。 将来会添加对这些资源类型的新警报的支持。 一旦可用，客户必须根据2020年6月之前的经典警报规则重新创建新的等效警报规则。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>针对弃用指标的经典警报规则

这些是以前支持但最终不推荐使用的指标的经典警报规则。 一小部分客户可能对此类指标具有无效的经典警报规则。 由于这些警报规则无效，因此它们不会被迁移。

| 资源类型| 不推荐使用的指标 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent，compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent，compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、storage_limit、storage_used、限制、dtu_consumption_percent、storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived、httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何创建等效的新警报规则和操作组

迁移工具将经典警报规则转换为等效的新警报规则和操作组。 对于大多数经典警报规则，等效的新警报规则与 `windowSize` 和 `aggregationType`等相同的属性具有相同的指标。 但在新系统中，有一些典型的警报规则用于具有不同的等效指标的指标。 以下原则适用于经典警报的迁移，除非在下面的部分中指定：

- **Frequency**：定义经典或新警报规则检查条件的频率。 经典警报规则中的 `frequency` 不能由用户进行配置，对于所有资源类型（其最小为1分钟 Application Insights 组件除外）始终为5分钟。因此，等价规则的频率也分别设置为5分钟和1分钟。
- "**聚合类型**"：定义度量值在相关窗口中的聚合方式。 对于大多数指标，`aggregationType` 在经典警报和新警报之间也是相同的。 在某些情况下，由于经典警报与新警报的指标不同，因此使用的是等效 `aggregationType` 或为指标定义的 `primary Aggregation Type`。
- **单元**：在其上创建警报的指标的属性。 某些等效指标具有不同的单位。 根据需要调整阈值。 例如，如果原始度量值的单位为秒，但等效的新指标以毫秒为单位，则原始阈值将乘以1000，以确保相同的行为。
- **窗口大小**：定义汇总度量值数据以与阈值进行比较的窗口。 对于标准 `windowSize` 值，如5分钟、15mins、30mins、1hour、3hours、6小时、12小时、1天，则不会对等效的新警报规则进行任何更改。 对于其他值，选择最接近的 `windowSize`。 对于大多数客户，此更改不会带来任何影响。 对于少量的客户而言，可能需要调整阈值才能获得完全相同的行为。

在以下部分中，我们将详细说明在新系统中具有不同的等效指标的指标。 不会列出任何对于经典和新警报规则均相同的指标。 可在[此处](metrics-supported.md)找到新系统支持的指标列表。

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

对于存储帐户服务（如 blob、表、文件和队列），以下度量值映射到等效的指标，如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 维度 "ResponseType" = "Authorizationerror)" 和 "Authentication" = "Anonymous" 的事务指标| |
| AnonymousClientOtherError | 维度 "ResponseType" = "ClientOtherError" 和 "Authentication" = "Anonymous" 的事务指标 | |
| AnonymousClientTimeOutError| 维度 "ResponseType" = "ClientTimeOutError" 和 "Authentication" = "Anonymous" 的事务指标 | |
| AnonymousNetworkError | 维度 "ResponseType" = "NetworkError" 和 "Authentication" = "Anonymous" 的事务指标 | |
| AnonymousServerOtherError | 维度 "ResponseType" = "ServerOtherError" 和 "Authentication" = "Anonymous" 的事务指标 | |
| AnonymousServerTimeOutError | 维度 "ResponseType" = "ServerTimeOutError" 和 "Authentication" = "Anonymous" 的事务指标 | |
| AnonymousSuccess | 维度 "ResponseType" = "成功" 和 "身份验证" = "匿名" 的事务指标 | |
| AuthorizationError | 维度为 "ResponseType" 的事务度量值为 "Authorizationerror)" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 容量 | BlobCapacity | 使用 `aggregationType` "average" 而不是 "last"。 指标仅适用于 Blob 服务 |
| ClientOtherError | 维度为 "ResponseType" 的事务度量值为 "ClientOtherError"  | |
| ClientTimeoutError | 维度为 "ResponseType" 的事务度量值为 "ClientTimeOutError" | |
| ContainerCount | ContainerCount | 使用 `aggregationType` "average" 而不是 "last"。 指标仅适用于 Blob 服务 |
| NetworkError | 维度为 "ResponseType" 的事务度量值为 "NetworkError" | |
| ObjectCount | BlobCount| 使用 `aggregationType` "average" 而不是 "last"。 指标仅适用于 Blob 服务 |
| SASAuthorizationError | 维度 "ResponseType" = "Authorizationerror)" 和 "Authentication" = "SAS" 的事务指标 | |
| SASClientOtherError | 维度 "ResponseType" = "ClientOtherError" 和 "Authentication" = "SAS" 的事务指标 | |
| SASClientTimeOutError | 维度 "ResponseType" = "ClientTimeOutError" 和 "Authentication" = "SAS" 的事务指标 | |
| SASNetworkError | 维度 "ResponseType" = "NetworkError" 和 "Authentication" = "SAS" 的事务指标 | |
| SASServerOtherError | 维度 "ResponseType" = "ServerOtherError" 和 "Authentication" = "SAS" 的事务指标 | |
| SASServerTimeOutError | 维度 "ResponseType" = "ServerTimeOutError" 和 "Authentication" = "SAS" 的事务指标 | |
| SASSuccess | 维度 "ResponseType" = "成功" 和 "身份验证" = "SAS" 的事务指标 | |
| ServerOtherError | 维度为 "ResponseType" 的事务度量值为 "ServerOtherError" | |
| ServerTimeOutError | 维度为 "ResponseType" 的事务度量值为 "ServerTimeOutError"  | |
| Success | 维度为 "ResponseType" 的事务度量值为 "Success" | |
| TotalBillableRequests| 事务 | |
| TotalEgress | 流出量 | |
| TotalIngress | 流入量 | |
| TotalRequests | 事务 | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

对于 Application Insights，等效指标如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| 将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。  |
| basicExceptionBrowser.count | exceptions/browser|  使用 `aggregationType` "count" 而不是 "sum"。 |
| basicExceptionServer.count | exceptions/server| 使用 `aggregationType` "count" 而不是 "sum"。  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| 将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。 |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| 将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| 将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。  |
| clientPerformance.total.value | browserTimings/totalDuration| 将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| 需要对阈值进行适当的修改，因为原始指标跨所有内核，而新度量值规范化为一个核心。 迁移工具不更改阈值。  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| 将原始阈值乘以1000作为典型指标的单位，以秒为单位，而新的阈值则以毫秒为单位。  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| 使用 `aggregationType` "count" 而不是 "sum"。   |
| view.count | pageViews/count| 使用 `aggregationType` "count" 而不是 "sum"。   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

对于 Cosmos DB，等效指标如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| 数据大小 | DataUsage| |
| 文档计数 | DocumentCount||
| 索引大小 | IndexUsage||
| Mongo Count 请求费用| MongoRequestCharge 维度 "CommandName" = "count"||
| Mongo 计数请求速率 | MongoRequestsCount 维度 "CommandName" = "count"||
| Mongo 删除请求费用 | MongoRequestCharge 维度 "CommandName" = "delete"||
| Mongo 删除请求速率 | MongoRequestsCount 维度 "CommandName" = "delete"||
| Mongo 插入请求费用 | MongoRequestCharge 维度 "CommandName" = "insert"||
| Mongo 插入请求速率 | MongoRequestsCount 维度 "CommandName" = "insert"||
| Mongo 查询请求费用 | MongoRequestCharge 维度 "CommandName" = "find"||
| Mongo 查询请求速率 | MongoRequestsCount 维度 "CommandName" = "find"||
| Mongo 更新请求费用 | MongoRequestCharge 维度 "CommandName" = "update"||
| 服务不可用| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>如何创建等效操作组

经典警报规则具有与警报规则本身关联的电子邮件、webhook、逻辑应用和 runbook 操作。 新警报规则使用可跨多个警报规则重复使用的操作组。 迁移工具为相同操作创建单个操作组，而不考虑使用该操作的警报规则的数量。 迁移工具创建的操作组使用命名格式 "Migrated_AG *"。

> [!NOTE]
> 经典警报在用于通知经典管理员角色时，根据经典管理员的区域设置发送本地化电子邮件。 新警报电子邮件是通过操作组发送的，并且仅以英语表示。

## <a name="rollout-phases"></a>推出阶段

迁移工具正在逐步推出使用经典警报规则的客户。 订阅所有者将在订阅准备好使用该工具进行迁移时收到一封电子邮件。

> [!NOTE]
> 由于该工具正在分阶段推出，你可能会发现某些订阅尚未准备好在早期阶段中进行迁移。

大多数订阅当前已标记为准备好进行迁移。 只有具有以下资源类型的经典警报的订阅仍未准备好进行迁移。

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

在订阅级别具有监视参与者的内置角色的任何用户都可以触发迁移。 具有以下权限的自定义角色的用户还可以触发迁移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 除了具有上述权限外，还应向 AlertsManagement 资源提供程序注册订阅。 这是成功迁移 Application Insights 上的故障异常警报所必需的。 

## <a name="common-problems-and-remedies"></a>常见问题与补救措施

[触发迁移](alerts-using-migration-tool.md)后，你将在提供的地址收到电子邮件，通知你迁移已完成，或者你需要执行任何操作。 本部分介绍一些常见问题以及如何处理它们。

### <a name="validation-failed"></a>验证失败

由于订阅中的经典警报规则发生了一些最新更改，因此无法迁移订阅。 此问题是暂时性的。 你可以在几天后将迁移状态恢复**为迁移准备就绪**后重新启动迁移。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>范围锁阻止我们迁移规则

在迁移过程中，将创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，范围锁可防止我们创建或删除资源。 根据范围锁，无法迁移部分或全部规则。 若要解决此问题，可以删除[迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中列出的订阅、资源组或资源的作用域锁定，并再次触发迁移。 在迁移过程中，不能禁用作用域锁定，必须将其删除。 [详细了解如何管理范围锁](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>具有 "拒绝" 影响的策略阻止我们迁移你的规则

在迁移过程中，将创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，策略可以阻止我们创建资源。 根据策略，无法迁移部分或全部规则。 [迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中列出了阻止进程的策略。 通过以下方法之一解决此问题：

- 从策略分配中排除在迁移过程中的订阅或资源组。 [了解有关管理策略排除范围的详细信息](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)。
- 删除或更改 "audit" 或 "append" 的效果（例如，可以解决与缺少标记相关的问题）。 [了解有关管理策略的详细信息](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [准备迁移](alerts-prepare-migration.md)
