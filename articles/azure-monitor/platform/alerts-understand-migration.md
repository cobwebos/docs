---
title: 了解 Azure Monitor 警报的自愿性迁移工具的工作原理
description: 了解警报迁移工具的工作原理和如何排查问题。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295533"
---
# <a name="understand-how-the-migration-tool-works"></a>了解迁移工具的工作原理

作为[之前公布](monitoring-classic-retirement.md)，在 Azure Monitor 经典警报由 2019 年 8 月 31 日停用 (已最初年 6 月 30，2019年)。 Azure 门户中为使用经典警报规则并想要自行触发迁移的客户提供了一个迁移工具。

本文介绍自愿性迁移工具的工作原理。 此外还介绍了一些常见问题的补救措施。

> [!NOTE]
> 由于推出的迁移工具中的延迟，经典警报迁移的停用日期已[延长到 2019 年 8 月 31 日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)从 2019 年 6 月 30 日的最初公布日期。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>将不会迁移的经典警报规则

> [!IMPORTANT]
> 活动日志警报 （包括服务运行状况警报） 和日志警报不会影响迁移。 迁移仅适用于所述的经典警报规则[此处](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)。

尽管该工具可以将几乎所有迁移[经典警报规则](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)，有一些例外情况。 使用工具 （或在自动迁移开始 2019 年 9 月），则不会迁移以下警报规则：

- 针对虚拟机来宾指标的经典警报规则（Windows 和 Linux）。 请参阅本文稍后所述的[有关在新指标警报中重新创建此类警报规则的指导](#guest-metrics-on-virtual-machines)。
- 针对经典存储指标的经典警报规则。 请参阅[有关监视经典存储帐户的指导](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 针对某些存储帐户指标的经典警报规则。 请参阅本文稍后所述的[详细信息](#storage-account-metrics)。
- 在一些 Cosmos DB 指标的经典警报规则。 将在未来更新中添加详细信息。

如果你的订阅包含任何此类经典规则，必须手动迁移。 由于我们无法提供自动迁移，任何此类现有经典指标警报会继续运行到 2020 年 6 月。 此缓冲期可让你从容地迁移到新警报。 但是，可以在 2019 年 8 月之后创建新的经典警报。

> [!NOTE]
> 除了上面列出的异常，如果经典警报规则无效即它们位于[已弃用指标](#classic-alert-rules-on-deprecated-metrics)或资源已被删除，它们不会迁移在自愿迁移过程。 自动迁移发生时，将删除任何此类无效经典警报规则。

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

经典警报规则上 AnonymousThrottlingError、 SASThrottlingError 和 ThrottlingError 必须将拆分为两个新警报，因为没有提供相同的功能没有合并的指标。 需要相应地调整阈值。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>不推荐使用的指标的经典警报规则

这些是以前支持，但最终不推荐使用的指标的经典警报规则。 极少比例的客户可能无效的经典警报规则对此类度量值。 这些警报规则的定义无效，因为它们不会迁移。

| 资源类型| 不推荐使用的度量值 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、 storage_limit、 storage_used、 限制、 dtu_consumption_percent、 storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何创建等效的新警报规则和操作组

迁移工具将经典警报规则转换为等效的新警报规则和操作组。 对于大多数的经典警报规则，等效的新警报规则都具有相同的属性的相同指标上如`windowSize`和`aggregationType`。 但是，有一些经典警报规则是在新系统中具有不同，则等效指标的指标。 除非另有指定下面的部分中，以下原则适用于经典警报的迁移：

- **频率**:定义一个经典或新的警报规则检查条件的频率。 `frequency`经典警报规则中不可以由用户进行配置，而且始终是 5 分钟，对于除是 1 分钟的 Application Insights 组件的所有资源类型。因此等效规则的频率也分别设置为 5 分钟和 1 分钟。
- **聚合类型**:定义度量值感兴趣的范围内的聚合方式。 `aggregationType`还之间是相同的经典警报和大多数度量值的新警报。 在某些情况下，因为该度量值是经典警报和等效的新警报之间的差异`aggregationType`或`primary Aggregation Type`为度量值定义。
- **单位**:在其创建警报的指标的属性。 某些等效指标具有不同的单位。 根据需要适当地调整阈值。 例如，如果原始的度量值单位为秒但等效的新度量单位为毫秒，原始阈值将乘以 1000，以确保相同的行为。
- **窗口大小**:定义内的指标数据进行聚合与阈值进行比较的窗口。 对于标准`windowSize`5mins 15mins、 30 分钟，然后，值 1 小时、 3 小时、 6 小时、 12 小时、 1 天，没有等效的新警报规则进行任何更改。 有关其他值，离`windowSize`选择要使用。 对于大多数客户而言，没有进行此更改会影响。 对于较少的客户，可能需要调整阈值，若要获取完全相同的行为。

在以下部分中，我们将详细介绍新系统中有不同，则等效的度量值的度量值。 未列出任何规格，它将保持不变的经典和新警报规则。 您可以找到新系统中支持的指标列表[此处](metrics-supported.md)。

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

对于存储 blob、 表、 文件和队列之类的帐户服务，以下度量值被映射到等效的度量值，如下所示：

| 在经典警报的指标 | 在新的警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 事务指标的维度"ResponseType"="AuthorizationError"和"身份验证"="Anonymous"| |
| AnonymousClientOtherError | 事务指标的维度"ResponseType"="ClientOtherError"和"身份验证"="Anonymous" | |
| AnonymousClientTimeOutError| 事务指标的维度"ResponseType"="ClientTimeOutError"和"身份验证"="Anonymous" | |
| AnonymousNetworkError | 事务指标的维度"ResponseType"="NetworkError"和"身份验证"="Anonymous" | |
| AnonymousServerOtherError | 事务指标的维度"ResponseType"="ServerOtherError"和"身份验证"="Anonymous" | |
| AnonymousServerTimeOutError | 事务指标的维度"ResponseType"="ServerTimeOutError"和"身份验证"="Anonymous" | |
| AnonymousSuccess | 事务指标的维度"ResponseType"="Success"和"身份验证"="Anonymous" | |
| AuthorizationError | 事务指标的维度"ResponseType"="AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 容量 | BlobCapacity | 使用`aggregationType`平均而不是上一次。 指标仅适用于 Blob 服务 |
| ClientOtherError | 事务指标的维度"ResponseType"="ClientOtherError"  | |
| ClientTimeoutError | 事务指标的维度"ResponseType"="ClientTimeOutError" | |
| ContainerCount | ContainerCount | 使用`aggregationType`平均而不是上一次。 指标仅适用于 Blob 服务 |
| NetworkError | 事务指标的维度"ResponseType"="NetworkError" | |
| ObjectCount | BlobCount| 使用`aggregationType`平均而不是上一次。 指标仅适用于 Blob 服务 |
| SASAuthorizationError | 事务指标的维度"ResponseType"="AuthorizationError"和"身份验证"="SAS" | |
| SASClientOtherError | 事务指标的维度"ResponseType"="ClientOtherError"和"身份验证"="SAS" | |
| SASClientTimeOutError | 事务指标的维度"ResponseType"="ClientTimeOutError"和"身份验证"="SAS" | |
| SASNetworkError | 事务指标的维度"ResponseType"="NetworkError"和"身份验证"="SAS" | |
| SASServerOtherError | 事务指标的维度"ResponseType"="ServerOtherError"和"身份验证"="SAS" | |
| SASServerTimeOutError | 事务指标的维度"ResponseType"="ServerTimeOutError"和"身份验证"="SAS" | |
| SASSuccess | 事务指标的维度"ResponseType"="Success"和"身份验证"="SAS" | |
| ServerOtherError | 事务指标的维度"ResponseType"="ServerOtherError" | |
| ServerTimeOutError | 事务指标的维度"ResponseType"="ServerTimeOutError"  | |
| Success | 事务指标的维度"ResponseType"="成功" | |
| TotalBillableRequests| 事务 | |
| TotalEgress | 流出量 | |
| TotalIngress | 流入量 | |
| TotalRequests | 事务 | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

对于 Application Insights 等效指标包括如下所示：

| 在经典警报的指标 | 在新的警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| 原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。  |
| basicExceptionBrowser.count | exceptions/browser|  使用`aggregationType`count 而不是 sum。 |
| basicExceptionServer.count | exceptions/server| 使用`aggregationType`count 而不是 sum。  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| 原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。 |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| 原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| 原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。  |
| clientPerformance.total.value | browserTimings/totalDuration| 原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| 阈值将需要适当地修改原始的度量值是跨所有核心以及新的度量值规范化为一个核心。 迁移工具不会更改的阈值。  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| 原始阈值乘以 1000年，经典指标的单位为以秒为单位，并且为新建一个以毫秒为单位。  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| 使用`aggregationType`count 而不是 sum。   |
| view.count | pageViews/count| 使用`aggregationType`count 而不是 sum。   |

### <a name="how-equivalent-action-groups-are-created"></a>如何创建等效操作组

经典警报规则有电子邮件、 webhook，逻辑应用和 runbook 操作绑定到警报规则本身。 新警报规则使用操作组可以跨多个警报规则重复使用。 迁移工具将创建相同的操作，而不考虑多少警报规则使用的操作的单个操作组。 操作组创建的迁移工具使用的命名格式 Migrated_AG *。

## <a name="rollout-phases"></a>推出阶段

迁移工具将分阶段推出到使用经典警报规则的客户。 当订阅准备好使用该工具迁移时，订阅所有者会收到一封电子邮件。

> [!NOTE]
> 由于正在分阶段推出该工具，您可能会看到，某些订阅不尚未准备好进行迁移的早期阶段。

当前，大部分订阅都标记为迁移做好准备。 以下资源类型有经典警报的订阅是仍未准备好进行迁移。

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

在订阅级别拥有“监视参与者”内置角色的任何用户都可以触发迁移。 拥有自定义角色和以下权限的用户也可以触发迁移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> 除了具有上述权限，此外应使用 Microsoft.AlertsManagement 资源提供程序注册你的订阅。 这是要成功迁移在 Application Insights 失败异常警报所必需的。 

## <a name="common-problems-and-remedies"></a>常见问题和补救措施

[触发迁移](alerts-using-migration-tool.md)后，提供的电子邮件地址会收到电子邮件，其中告知迁移已完成，或者需要你采取任何措施。 本部分描述了一些常见问题及其处理方法。

### <a name="validation-failed"></a>验证失败

由于最近对订阅中的经典警报规则做了某些更改，无法迁移该订阅。 此问题是暂时性的。 几天后当迁移状态恢复为“准备好迁移”时，可以重新开始迁移。 

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>策略或范围锁阻止我们迁移你的规则

在迁移过程中，将会创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，有某个策略或范围锁阻止我们创建资源。 根据具体的策略或范围锁，无法迁移某些或所有规则。 暂时删除该范围锁或策略并再次触发迁移即可解决此问题。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [准备迁移](alerts-prepare-migration.md)
