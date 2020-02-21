---
title: Azure Monitor 日志数据模型
description: 本文介绍 Azure 备份数据的 Azure Monitor Log Analytics 数据模型详细信息。
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 121117d20f5b9eb6e53da1b8884557fcfb46ff76
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500999"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure 备份数据的 Log Analytics 数据模型

使用 Log Analytics 数据模型从 Log Analytics 创建自定义警报。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> 此数据模型引用向 Log Analytics （LA）发送诊断事件的 Azure 诊断模式。 若要了解新资源特定模式的数据模型，可以参阅以下文章： [Azure 备份诊断事件的数据模型](https://aka.ms/diagnosticsdatamodel)

## <a name="using-azure-backup-data-model"></a>使用 Azure 备份数据模型

可以使用数据模型随附的以下字段，根据要求创建视觉对象、自定义查询和仪表板。

### <a name="alert"></a>警报

此表提供警报相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| AlertUniqueId_s |文本 |生成的警报的唯一标识符 |
| AlertType_s |文本 |警报的类型，例如 Backup |
| AlertStatus_s |文本 |警报的状态，例如 Active |
| AlertOccurrenceDateTime_s |日期/时间 |警报的创建日期和时间 |
| AlertSeverity_s |文本 |警报的严重性，例如 Critical |
|AlertTimeToResolveInMinutes_s    | Number        |解决警报所需的时间。 对于活动警报为空。         |
|AlertConsolidationStatus_s   |文本         |确定警报是否为合并的警报         |
|CountOfAlertsConsolidated_s     |Number         |合并警报的警报的数目          |
|AlertRaisedOn_s     |文本         |引发警报的实体类型         |
|AlertCode_s     |文本         |用于唯一标识警报类型的代码         |
|RecommendedAction_s   |文本         |建议用于解决警报的操作         |
| EventName_s |文本 |事件的名称。 始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |文本 |与警报关联的备份项的唯一标识符 |
| SchemaVersion_s |文本 |架构的当前版本，例如**V2** |
| State_s |文本 |警报对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此警报所属的 IaaSVM、FileFolder |
| OperationName |文本 |当前操作的名称，例如 Alert |
| 类别 |文本 |推送到 Azure Monitor 日志的诊断数据的类别。 始终为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedContainerUniqueId_s |文本 |与警报关联的受保护服务器的唯一标识符（在 V1 中 ProtectedServerUniqueId_s）|
| VaultUniqueId_s |文本 |与警报关联的受保护保管库的唯一标识符 |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |与收集的数据相关的资源的唯一标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型。 例如 Vaults |

### <a name="backupitem"></a>BackupItem

此表提供备份项相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |事件的名称。 始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |文本 |备份项的唯一标识符 |
| BackupItemId_s |文本 |备份项的标识符（此字段仅适用于 v1 架构） |
| BackupItemName_s |文本 |备份项的名称 |
| BackupItemFriendlyName_s |文本 |备份项的友好名称 |
| BackupItemType_s |文本 |备份项的类型，例如 VM、FileFolder |
| BackupItemProtectionState_s |文本 |备份项的保护状态 |
| BackupItemAppVersion_s |文本 |备份项的应用程序版本 |
| ProtectionState_s |文本 |备份项的当前保护状态，例如 Protected、ProtectionStopped |
| ProtectionGroupName_s |文本 | 保护备份项的保护组的名称，适用于 SC DPM 和 MABS （如果适用）|
| SecondaryBackupProtectionState_s |文本 |是否为备份项启用辅助保护|
| SchemaVersion_s |文本 |架构的版本，例如**V2** |
| State_s |文本 |备份项对象的状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此备份项所属的 IaaSVM、FileFolder |
| OperationName |文本 |操作的名称，例如 BackupItem |
| 类别 |文本 |推送到 Azure Monitor 日志的诊断数据的类别。 始终为 AzureBackupReport |
| 资源 |文本 |正在收集其数据的资源，例如“恢复服务保管库名称” |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |正在收集的数据的资源 ID，例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型，例如 Vaults |

### <a name="backupitemassociation"></a>BackupItemAssociation

此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |文本 |备份项的唯一 ID |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，它是**V2** |
| State_s |文本 |备份项关联对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| BackupItemSourceSize_s |文本 | 备份项的前端大小 |
| BackupManagementServerUniqueId_s |文本 | 用于唯一标识备份项目受保护的备份管理服务器的字段（如果适用） |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| OperationName |文本 |此字段表示当前操作的名称 - BackupItemAssociation |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedContainerUniqueId_s |文本 |与备份项相关联的受保护服务器的唯一标识符（V1 中 ProtectedServerUniqueId_s） |
| VaultUniqueId_s |文本 |包含备份项的保管库的唯一标识符 |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型，例如 Vaults |

### <a name="backupmanagementserver"></a>BackupManagementServer

此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
|BackupManagementServerName_s     |文本         |备份管理服务器的名称        |
|AzureBackupAgentVersion_s     |文本         |备份管理服务器上的 Azure 备份代理版本          |
|BackupManagementServerVersion_s     |文本         |备份管理服务器的版本|
|BackupManagementServerOSVersion_s    |文本            |备份管理服务器的操作系统版本|
|BackupManagementServerType_s     |文本         |备份管理服务器的类型，as MABS，SC DPM|
|BackupManagementServerUniqueId_s     |文本         |用于唯一标识备份管理服务器的字段       |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型，例如 Vaults |

### <a name="job"></a>作业

此表提供作业相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |事件的名称。 始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |文本 |备份项的唯一标识符 |
| SchemaVersion_s |文本 |架构的版本，例如**V2** |
| State_s |文本 |作业对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - Job |
| 类别 |文本 |此字段表示推送到 Azure Monitor 日志的诊断数据类别，AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |与作业关联的受保护服务器的唯一标识符 |
| ProtectedContainerUniqueId_s |文本 | 用于标识运行作业的受保护容器的唯一 ID |
| VaultUniqueId_s |文本 |受保护保管库的唯一标识符 |
| JobOperation_s |文本 |为其运行作业的操作，例如备份、还原、配置备份 |
| JobStatus_s |文本 |作业的状态，例如 Completed、Failed |
| JobFailureCode_s |文本 |导致作业失败的故障代码字符串 |
| JobStartDateTime_s |日期/时间 |开始运行作业的日期和时间 |
| BackupStorageDestination_s |文本 |备份存储目标，例如 Cloud、Disk  |
| AdHocOrScheduledJob_s |文本 | 用于指定作业是即席还是计划的字段 |
| JobDurationInSecs_s | Number |作业的总持续时间，以秒为单位 |
| DataTransferredInMB_s | Number |此作业传输的数据量，以 MB 为单位|
| JobUniqueId_g |文本 |用于标识作业的唯一 ID |
| RecoveryJobDestination_s |文本 | 恢复作业的目标，其中的数据已恢复 |
| RecoveryJobRPDateTime_s |DateTime | 创建要恢复的恢复点的日期和时间 |
| RecoveryJobRPLocation_s |文本 | 要恢复的恢复点的存储位置|
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID|
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型。 例如 Vaults |

### <a name="policy"></a>策略

此表提供策略相关字段的详细信息。

| 字段 | 数据类型 | 适用版本 | 说明 |
| --- | --- | --- | --- |
| EventName_s |文本 ||此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 ||此字段表示架构的当前版本，它是**V2** |
| State_s |文本 ||策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 ||服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |文本 ||此字段表示当前操作的名称 - Policy |
| 类别 |文本 ||此字段表示推送到 Azure Monitor 日志的诊断数据类别，AzureBackupReport |
| 资源 |文本 ||这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |文本 ||用于标识策略的唯一 ID |
| PolicyName_s |文本 ||已定义的策略的名称 |
| BackupFrequency_s |文本 ||运行备份的频率，例如 daily、weekly |
| BackupTimes_s |文本 ||计划备份时的日期和时间 |
| BackupDaysOfTheWeek_s |文本 ||计划备份时的日期（星期几） |
| RetentionDuration_s |整数 ||所配置备份的保留持续时间 |
| DailyRetentionDuration_s |整数 ||所配置备份的总保留时间（按天算） |
| DailyRetentionTimes_s |文本 ||配置每日保留时的日期和时间 |
| WeeklyRetentionDuration_s |小数 ||所配置备份的每周保留总时间（按周算） |
| WeeklyRetentionTimes_s |文本 ||配置每周保留时的日期和时间 |
| WeeklyRetentionDaysOfTheWeek_s |文本 ||选择进行每周保留的日期（星期几） |
| MonthlyRetentionDuration_s |小数 ||所配置备份的总保留时间（按月算） |
| MonthlyRetentionTimes_s |文本 ||配置每月保留时的日期和时间 |
| MonthlyRetentionFormat_s |文本 ||每月保留的配置类型，例如基于日期的每日、基于周次的每周 |
| MonthlyRetentionDaysOfTheWeek_s |文本 ||选择进行每月保留的日期（星期几） |
| MonthlyRetentionWeeksOfTheMonth_s |文本 ||配置每月保留时的当月周次，例如 First、Last 等 |
| YearlyRetentionDuration_s |小数 ||所配置备份的总保留时间（按年算） |
| YearlyRetentionTimes_s |文本 ||配置每年保留时的日期和时间 |
| YearlyRetentionMonthsOfTheYear_s |文本 ||选择进行每年保留的月份 |
| YearlyRetentionFormat_s |文本 ||每年保留的配置类型，例如基于日期的每日、基于周次的每周 | |
| YearlyRetentionDaysOfTheMonth_s |文本 ||选择进行每年保留的日期 |
| SynchronisationFrequencyPerDay_s |整数 |v2|一天中为 SC DPM 和 MABS 同步文件备份的次数 |
| DiffBackupFormat_s |文本 |v2|Azure VM 备份中的 SQL 差异备份格式 |
| DiffBackupTime_s |时间 |v2|Azure VM 备份中的 SQL 差异备份时间|
| DiffBackupRetentionDuration_s |小数 |v2|Azure VM 备份中 SQL 差异备份的保留期|
| LogBackupFrequency_s |小数 |v2|SQL 日志备份的频率|
| LogBackupRetentionDuration_s |小数 |v2|Azure VM 备份中的 SQL 日志备份的保留期|
| DiffBackupDaysofTheWeek_s |文本 |v2|Azure VM 备份中的 SQL 差异备份星期几|
| SourceSystem |文本 ||当前数据的源系统 - Azure |
| ResourceId |文本 ||正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 ||正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |文本 ||正在收集其数据的资源类型。 例如 Vaults |

### <a name="policyassociation"></a>PolicyAssociation

此表提供有关策略与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 适用版本 | 说明 |
| --- | --- | --- | --- |
| EventName_s |文本 ||此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 ||此字段表示架构的当前版本，它是**V2** |
| State_s |文本 ||策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 ||服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |文本 ||此字段表示当前操作的名称 - PolicyAssociation |
| 类别 |文本 ||此字段表示推送到 Azure Monitor 日志的诊断数据类别，AzureBackupReport |
| 资源 |文本 ||这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |文本 ||用于标识策略的唯一 ID |
| VaultUniqueId_s |文本 ||此策略所属的保管库的唯一 ID |
| BackupManagementServerUniqueId_s |文本 |v2 |用于唯一标识备份项目受保护的备份管理服务器的字段（如果适用）        |
| SourceSystem |文本 ||当前数据的源系统 - Azure |
| ResourceId |文本 ||正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 ||正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |文本 ||正在收集其数据的资源类型。 例如 Vaults |

### <a name="protected-container"></a>受保护容器

此表提供有关受保护容器的基本字段。 （V1 中的 ProtectedServer）

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |文本 | 用于唯一标识受保护容器的字段 |
| ProtectedContainerOSType_s |文本 |受保护容器的操作系统类型 |
| ProtectedContainerOSVersion_s |文本 |受保护容器的操作系统版本 |
| AgentVersion_s |文本 |代理备份或保护代理的版本号（如果是 SC DPM 和 MABS） |
| BackupManagementType_s |文本 |用于执行备份的提供程序类型。 例如，IaaSVM、FileFolder |
| EntityState_s |文本 |受保护服务器对象的当前状态。 例如，Active、Deleted |
| ProtectedContainerFriendlyName_s |文本 |受保护的服务器的友好名称 |
| ProtectedContainerName_s |文本 |受保护容器的名称 |
| ProtectedContainerWorkloadType_s |文本 |备份的受保护容器的类型。 例如，IaaSVMContainer |
| ProtectedContainerLocation_s |文本 |受保护的容器是位于本地还是 Azure 中 |
| ProtectedContainerType_s |文本 |受保护的容器是服务器还是容器 |
| ProtectedContainerProtectionState_s’  |文本 |受保护容器的保护状态 |

### <a name="storage"></a>存储

此表提供存储相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| CloudStorageInBytes_s |小数 |备份所用的云备份存储，基于最新值进行计算（此字段仅适用于 v1 架构）|
| ProtectedInstances_s |小数 |用于计算账单中前端存储量的受保护实例的数目，基于最新值进行计算 |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，它是**V2** |
| State_s |文本 |存储对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - Storage |
| 类别 |文本 |此字段表示推送到 Azure Monitor 日志的诊断数据类别，AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |为其计算存储的受保护服务器的唯一 ID |
| VaultUniqueId_s |文本 |计算存储的保管库的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型。 例如 Vaults |
| StorageUniqueId_s |文本 |用于标识存储实体的唯一 ID |
| StorageType_s |文本 |存储类型，例如云、卷、磁盘 |
| StorageName_s |文本 |存储实体的名称，例如 E:\ |
| StorageTotalSizeInGBs_s |文本 |存储实体占用的存储的总大小（GB）|

### <a name="storageassociation"></a>StorageAssociation

此表提供与存储相关的基本字段，它们将存储连接到其他实体。

| 字段 | 数据类型 | 说明 |
| --- | --- |  --- |
| StorageUniqueId_s |文本 |用于标识存储实体的唯一 ID |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，它是**V2** |
| BackupItemUniqueId_s |文本 |用于标识与存储实体相关的备份项的唯一 ID |
| BackupManagementServerUniqueId_s |文本 |用于标识与存储实体相关的备份管理服务器的唯一 ID|
| VaultUniqueId_s |文本 |用于标识与存储实体相关的保管库的唯一 ID|
| StorageConsumedInMBs_s |Number|相应存储中的相应备份项使用的存储大小 |
| StorageAllocatedInMBs_s |Number |在磁盘类型的相应存储中，相应备份项分配的存储大小|

### <a name="vault"></a>保管库

此表提供保管库相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，它是**V2** |
| State_s |文本 |保管库对象的当前状态，例如 Active、Deleted |
| OperationName |文本 |此字段表示当前操作的名称 - Vault |
| 类别 |文本 |此字段表示推送到 Azure Monitor 日志的诊断数据类别，AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| VaultUniqueId_s |文本 |保管库的唯一 ID |
| VaultName_s |文本 |保管库的名称 |
| AzureDataCenter_s |文本 |保管库所在的数据中心 |
| StorageReplicationType_s |文本 |保管库存储复制的类型，例如 GeoRedundant |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| SubscriptionId |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceGroup |文本 |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |文本 |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |文本 |正在收集其数据的资源类型。 例如 Vaults |

### <a name="backup-management-server"></a>备份管理服务器

此表提供了有关备份管理服务器的基本字段。

|字段  |数据类型  | 说明  |
|---------|---------|----------|
|BackupManagementServerName_s     |文本         |备份管理服务器的名称        |
|AzureBackupAgentVersion_s     |文本         |备份管理服务器上的 Azure 备份代理版本          |
|BackupManagementServerVersion_s     |文本         |备份管理服务器的版本|
|BackupManagementServerOSVersion_s     |文本            |备份管理服务器的操作系统版本|
|BackupManagementServerType_s     |文本         |备份管理服务器的类型，as MABS，SC DPM|
|BackupManagementServerUniqueId_s     |文本         |用于唯一标识备份管理服务器的字段       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

此表指定与卷关联的工作负荷。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| StorageUniqueId_s |文本 |用于标识存储实体的唯一 ID |
| BackupItemType_s |文本 |此卷是首选存储的工作负荷|

### <a name="protectedinstance"></a>ProtectedInstance

此表提供了与实例相关的基本字段。

| 字段 | 数据类型 |适用版本 | 说明 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |文本 |v2|用于识别使用 DPM 备份的 Vm 的备份项的唯一 ID，MABS|
| ProtectedContainerUniqueId_s |文本 |v2|用于标识受保护容器的唯一 ID，该 ID 用于标识使用 DPM 备份的 Vm 以外的所有内容、MABS|
| ProtectedInstanceCount_s |文本 |v2|此日期时间关联的备份项或受保护的容器的受保护实例计数|

### <a name="recoverypoint"></a>RecoveryPoint

此表提供了与基本恢复点相关的字段。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| BackupItemUniqueId_s |文本 |用于识别使用 DPM 备份的 Vm 的备份项的唯一 ID，MABS|
| OldestRecoveryPointTime_s |文本 |备份项的最早恢复点的日期时间|
| OldestRecoveryPointLocation_s |文本 |备份项的最早恢复点的位置|
| LatestRecoveryPointTime_s |文本 |备份项的最新恢复点的日期时间|
| LatestRecoveryPointLocation_s |文本 |备份项的最新恢复点的位置|

## <a name="sample-kusto-queries"></a>示例 Kusto 查询

下面是几个示例，可帮助你针对位于 Azure 诊断表中的 Azure 备份数据编写查询：

- 所有成功的备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- 所有失败的备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- 所有成功的 Azure VM 备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- 所有成功的 SQL 日志备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- 所有成功的 Azure 备份代理作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>后续步骤

查看数据模型后，可以开始在 Azure Monitor 日志中[创建自定义查询](../azure-monitor/learn/tutorial-logs-dashboards.md)来构建自己的仪表板。
