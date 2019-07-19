---
title: 了解 Azure Monitor 警报的自愿性迁移工具的工作原理
description: 了解警报迁移工具的工作原理和如何排查问题。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: f981c14e26c51c427dab6b418cab8df46b1bb026
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302246"
---
# <a name="understand-how-the-migration-tool-works"></a>了解迁移工具的工作原理

根据[之前的公告](monitoring-classic-retirement.md)，Azure Monitor 中的经典警报即将在 2019 年 8 月 31 日（原定于 2019 年 6 月 30 日）停用。 Azure 门户中为使用经典警报规则并想要自行触发迁移的客户提供了一个迁移工具。

本文介绍自愿性迁移工具的工作原理。 此外还介绍了一些常见问题的补救措施。

> [!NOTE]
> 由于迁移工具的延迟推出，经典警报迁移的停用日期已从原来宣布的 2019 年 6 月 30 日[推迟至 2019 年 8 月 31 日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>不会迁移的经典警报规则

> [!IMPORTANT]
> 活动日志警报（包括服务运行状况警报）和日志警报不受迁移的影响。 迁移仅适用于[此处](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)所述的经典警报规则。

该工具几乎可以迁移所有的[经典警报规则](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)，不过也存在一些例外情况。 使用该工具（或者在 2019 年 9 月开始自动迁移期间）无法迁移以下警报规则：

- 针对虚拟机来宾指标的经典警报规则（Windows 和 Linux）。 请参阅本文稍后所述的[有关在新指标警报中重新创建此类警报规则的指导](#guest-metrics-on-virtual-machines)。
- 针对经典存储指标的经典警报规则。 请参阅[有关监视经典存储帐户的指导](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 针对某些存储帐户指标的经典警报规则。 请参阅本文稍后所述的[详细信息](#storage-account-metrics)。
- 针对某些 Cosmos DB 指标的经典警报规则。 请参阅本文稍后所述的[详细信息](#cosmos-db-metrics)。
- 针对所有经典虚拟机和云服务指标 (Microsoft.classiccompute/virtualMachines 和 Microsoft.classiccompute/domainNames/个角色) 的经典警报规则。 请参阅本文稍后所述的[详细信息](#classic-compute-metrics)。

如果你的订阅包含任何此类经典规则，必须手动迁移。 由于我们无法提供自动迁移，任何此类现有经典指标警报会继续运行到 2020 年 6 月。 此缓冲期可让你从容地迁移到新警报。 但是，在 2019 年 8 月之后，不再可以创建新的经典警报。

> [!NOTE]
> 除了上面所列的例外情况，如果经典警报规则无效，即，它们应用于[已弃用的指标](#classic-alert-rules-on-deprecated-metrics)或已删除的资源，则在自愿性迁移过程中，不会迁移这些规则。 自动迁移时，将删除任何此类无效经典警报规则。

### <a name="guest-metrics-on-virtual-machines"></a>虚拟机上的来宾指标

在针对来宾指标创建新的指标警报之前，必须先将来宾指标发送到 Azure Monitor 自定义指标存储。 遵照以下说明在诊断设置中启用 Azure Monitor 接收器：

- [为 Windows VM 启用来宾指标](collect-custom-metrics-guestos-resource-manager-vm.md)
- [为 Linux VM 启用来宾指标](collect-custom-metrics-linux-telegraf.md)

完成这些步骤后，可以针对来宾指标创建新的指标警报。 创建新的指标警报后，可以删除经典警报。

### <a name="storage-account-metrics"></a>存储帐户指标

可以迁移针对存储帐户的所有经典警报，但针对以下指标的警报除外：

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

必须基于[旧的和新的存储指标之间的映射](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)迁移针对 Percent 指标的经典警报规则。 需要相应地修改阈值，因为提供的新指标是绝对指标。

必须将针对 AnonymousThrottlingError、SASThrottlingError 和 ThrottlingError 的经典警报规则拆分为两个新警报，因为没有任何合并的指标可提供相同的功能。 需要相应地调整阈值。

### <a name="cosmos-db-metrics"></a>Cosmos DB 指标

可以迁移 Cosmos DB 指标的所有经典警报, 但这些指标的警报除外:

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

每秒平均请求数, 一致性级别, 每分钟使用的最大 RUPM 数, 最大每秒的每秒, 观察到的读取延迟, 观察到的写入延迟, 存储容量目前在[新系统](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)中不可用。

不会迁移 http 2xx、Http 3xx、Http 400、Http 401、内部服务器错误、服务可用性、受限请求和请求总数等请求指标的警报, 因为在经典指标和新指标之间对请求进行计数的方式不同。 需要手动重新创建这些警报, 并调整阈值。

Mongo 失败请求的警报必须拆分为多个警报, 因为没有提供相同功能的组合指标。 需要相应地调整阈值。

### <a name="classic-compute-metrics"></a>典型计算指标

对于经典计算指标的任何警报都不会使用迁移工具迁移, 因为新警报尚不支持经典计算资源。 将来会添加对这些资源类型的新警报的支持。 一旦可用, 客户必须根据2020年6月之前的经典警报规则重新创建新的等效警报规则。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>针对已弃用指标的经典警报规则

某些针对指标的经典警报规则在以前受支持，但最终将会弃用。 只有少量的客户可能针对此类指标应用无效的经典警报规则。 由于这些警报规则无效，因此不会迁移它们。

| 资源类型| 已弃用的指标 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent、compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent、compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、storage_limit、storage_used、throttling、dtu_consumption_percent、storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived、httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何创建等效的新警报规则和操作组

迁移工具可将经典警报规则转换为等效的新警报规则和操作组。 对于大多数经典警报规则，等效的新警报规则将应用于包含 `windowSize` 和 `aggregationType` 等相同属性的相同指标。 但是，某些经典警报规则应用于新系统中包含不同但等效的指标的指标。 除非以下部分另有说明，否则，以下原则适用于经典警报的迁移：

- **频率**：定义经典警报规则或新警报规则检查条件的频率。 经典警报规则中的 `frequency` 不可由用户配置，对于所有资源类型，其值始终为 5 分钟，不过，对于 Application Insights 组件，其值为 1 分钟。因此，等效规则的频率也分别设置为 5 分钟和 1 分钟。
- **聚合类型**：定义聚合所需时间范围内的指标的方式。 对于大多数指标而言，使用经典警报和新警报时，`aggregationType` 也是相同的。 在某些情况下，由于经典警报和新警报的指标不同，将使用为指标定义的等效 `aggregationType` 或 `primary Aggregation Type`。
- **单位**：针对其创建警报的指标的属性。 某些等效指标具有不同的单位。 可根据需要相应地调整阈值。 例如，如果原始指标以秒为单位，但等效的新指标以毫秒为单位，则原始阈值将乘以 1000，以确保相同的行为。
- **时间范围**：定义要聚合的、与阈值进行比较的指标数据的时间范围。 等效的新警报规则同样可以使用 5 分钟、15 分钟、30 分钟、1 小时、3 小时、 6 小时、12 小时、1 天等标准的 `windowSize` 值。 如果使用其他值，将选择使用最接近的 `windowSize`。 大多数客户不受此项更改的影响。 少量的客户可能需要调整阈值才能获得完全相同的行为。

以下部分详细介绍了新系统中包含不同但等效的指标的指标。 对于经典和新警报规则保持不变的任何指标未列出。 可在[此处](metrics-supported.md)找到新系统中支持的指标列表。

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

对于 Blob、表、文件和队列等存储帐户服务，以下指标将映射到等效的指标，如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 包含维度 "ResponseType"="AuthorizationError" 和 "Authentication"="Anonymous" 的事务指标| |
| AnonymousClientOtherError | 包含维度 "ResponseType"="ClientOtherError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousClientTimeOutError| 包含维度 "ResponseType"="ClientTimeOutError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousNetworkError | 包含维度 "ResponseType"="NetworkError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousServerOtherError | 包含维度 "ResponseType"="ServerOtherError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousServerTimeOutError | 包含维度 "ResponseType"="ServerTimeOutError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousSuccess | 包含维度 "ResponseType"="Success" 和 "Authentication"="Anonymous" 的事务指标 | |
| AuthorizationError | 包含维度 "ResponseType"="AuthorizationError" 的事务指标 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 容量 | BlobCapacity | 使用 `aggregationType` 值“average”而不是“average”。 指标仅适用于 Blob 服务 |
| ClientOtherError | 包含维度 "ResponseType"="ClientOtherError" 的事务指标  | |
| ClientTimeoutError | 包含维度 "ResponseType"="ClientTimeOutError" 的事务指标 | |
| ContainerCount | ContainerCount | 使用 `aggregationType` 值“average”而不是“average”。 指标仅适用于 Blob 服务 |
| NetworkError | 包含维度 "ResponseType"="NetworkError" 的事务指标 | |
| ObjectCount | BlobCount| 使用 `aggregationType` 值“average”而不是“average”。 指标仅适用于 Blob 服务 |
| SASAuthorizationError | 包含维度 "ResponseType"="AuthorizationError" 和 "Authentication"="SAS" 的事务指标 | |
| SASClientOtherError | 包含维度 "ResponseType"="ClientOtherError" 和 "Authentication"="SAS" 的事务指标 | |
| SASClientTimeOutError | 包含维度 "ResponseType"="ClientTimeOutError" 和 "Authentication"="SAS" 的事务指标 | |
| SASNetworkError | 包含维度 "ResponseType"="NetworkError" 和 "Authentication"="SAS" 的事务指标 | |
| SASServerOtherError | 包含维度 "ResponseType"="ServerOtherError" 和 "Authentication"="SAS" 的事务指标 | |
| SASServerTimeOutError | 包含维度 "ResponseType"="ServerTimeOutError" 和 "Authentication"="SAS" 的事务指标 | |
| SASSuccess | 包含维度 "ResponseType"="Success" 和 "Authentication"="SAS" 的事务指标 | |
| ServerOtherError | 包含维度 "ResponseType"="ServerOtherError" 的事务指标 | |
| ServerTimeOutError | 包含维度 "ResponseType"="ServerTimeOutError" 的事务指标  | |
| Success | 包含维度 "ResponseType"="Success" 的事务指标 | |
| TotalBillableRequests| 事务 | |
| TotalEgress | 流出量 | |
| TotalIngress | 流入量 | |
| TotalRequests | 事务 | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

对于 Application Insights，等效的指标如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| 将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。  |
| basicExceptionBrowser.count | exceptions/browser|  使用 `aggregationType` 值“count”而不是“sum”。 |
| basicExceptionServer.count | exceptions/server| 使用 `aggregationType` 值“count”而不是“sum”。  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| 将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。 |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| 将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| 将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。  |
| clientPerformance.total.value | browserTimings/totalDuration| 将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| 阈值需要经过适当的修改，因为原始指标针对所有核心，而新指标规范化到一个核心。 迁移工具不会更改阈值。  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| 将原始阈值乘以 1000，因为经典指标的单位为秒，而新指标的单位为毫秒。  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| 使用 `aggregationType` 值“count”而不是“sum”。   |
| view.count | pageViews/count| 使用 `aggregationType` 值“count”而不是“sum”。   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

对于 Cosmos DB, 等效指标如下所示:

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

### <a name="how-equivalent-action-groups-are-created"></a>如何创建等效的操作组

经典警报规则中的电子邮件、Webhook、逻辑应用和 Runbook 操作已绑定到警报规则本身。 新警报规则使用可在多个警报规则之间重用的操作组。 不管有多少警报规则正在使用操作，迁移工具都会为相同的操作创建一个操作组。 迁移工具创建的操作组使用命名格式“Migrated_AG*”。

> [!NOTE]
> 经典警报在用于通知经典管理员角色时, 根据经典管理员的区域设置发送本地化电子邮件。 新警报电子邮件是通过操作组发送的, 并且仅以英语表示。

## <a name="rollout-phases"></a>推出阶段

迁移工具将分阶段推出到使用经典警报规则的客户。 当订阅准备好使用该工具迁移时，订阅所有者会收到一封电子邮件。

> [!NOTE]
> 由于该工具是分阶段推出的，在早期阶段，你可能会发现某些订阅尚未做好迁移的准备。

目前，大部分订阅已标记为准备好迁移。 只有针对以下资源类型应用经典警报的订阅仍未做好迁移的准备。

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

在订阅级别拥有“监视参与者”内置角色的任何用户都可以触发迁移。 拥有自定义角色和以下权限的用户也可以触发迁移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 除了获取上述权限以外，还应额外地将订阅注册到 Microsoft.AlertsManagement 资源提供程序。 只有这样，才能成功迁移 Application Insights 中的“失败异常”警报。 

## <a name="common-problems-and-remedies"></a>常见问题和补救措施

[触发迁移](alerts-using-migration-tool.md)后，提供的电子邮件地址会收到电子邮件，其中告知迁移已完成，或者需要你采取任何措施。 本部分描述了一些常见问题及其处理方法。

### <a name="validation-failed"></a>验证失败

由于最近对订阅中的经典警报规则做了某些更改，无法迁移该订阅。 此问题是暂时性的。 几天后当迁移状态恢复为“准备好迁移”时，可以重新开始迁移。 

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>策略或范围锁阻止我们迁移你的规则

在迁移过程中，将会创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，有某个策略或范围锁阻止我们创建资源。 根据具体的策略或范围锁，无法迁移某些或所有规则。 暂时删除该范围锁或策略并再次触发迁移即可解决此问题。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [准备迁移](alerts-prepare-migration.md)
