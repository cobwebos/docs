---
title: Azure 备份的 azure Monitor 日志数据模型
description: 本文讨论 Azure Monitor 日志 Azure 备份的数据的数据模型详细的信息。
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 801516ae2cfad891098c16f8cd6e9a4c7f157a93
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342014"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure 备份数据的 Log Analytics 数据模型

使用 Log Analytics 数据模型从 Log Analytics 创建自定义警报。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>使用 Azure 备份数据模型

可以使用数据模型随附的以下字段，根据要求创建视觉对象、自定义查询和仪表板。

### <a name="alert"></a>警报

此表提供警报相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| AlertUniqueId_s |Text |生成的警报的唯一标识符 |
| AlertType_s |Text |警报的类型，例如 Backup |
| AlertStatus_s |Text |警报的状态，例如 Active |
| AlertOccurrenceDateTime_s |Date/Time |警报的创建日期和时间 |
| AlertSeverity_s |Text |警报的严重性，例如 Critical |
|AlertTimeToResolveInMinutes_s    | Number        |若要解决警报所用的时间。 活动警报的空白。         |
|AlertConsolidationStatus_s   |Text         |确定如果警报或不是合并的警报         |
|CountOfAlertsConsolidated_s     |Number         |如果是合并的警报合并的警报数          |
|AlertRaisedOn_s     |Text         |默认情况下，警报的实体类型         |
|AlertCode_s     |Text         |代码来唯一标识警报类型         |
|RecommendedAction_s   |Text         |建议要解决该警报的操作         |
| EventName_s |Text |事件的名称。 始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |与警报关联的备份项的唯一标识符 |
| SchemaVersion_s |Text |当前版本的架构，例如**V2** |
| State_s |Text |警报对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |执行备份的提供程序类型，例如此警报所属的 IaaSVM、FileFolder |
| OperationName |Text |当前操作的名称，例如 Alert |
| 类别 |Text |诊断数据推送到 Azure Monitor 日志类别。 始终为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedContainerUniqueId_s |Text |受保护的服务器与警报 (在 V1 中的是 ProtectedServerUniqueId_s) 相关联的唯一标识符|
| VaultUniqueId_s |Text |与警报关联的受保护保管库的唯一标识符 |
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |与收集的数据相关的资源的唯一标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |

### <a name="backupitem"></a>BackupItem

此表提供备份项相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |事件的名称。 始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |备份项的唯一标识符 |
| BackupItemId_s |Text |（此字段是仅为 v1 架构） 的备份项的标识符 |
| BackupItemName_s |Text |备份项的名称 |
| BackupItemFriendlyName_s |Text |备份项的友好名称 |
| BackupItemType_s |文本 |备份项的类型，例如 VM、FileFolder |
| BackupItemProtectionState_s |Text |备份项的保护状态 |
| BackupItemAppVersion_s |Text |备份项的应用程序版本 |
| ProtectionState_s |Text |备份项的当前保护状态，例如 Protected、ProtectionStopped |
| ProtectionGroupName_s |Text | 保护组备份项的名称中受保护，SC DPM 和 MABS，如果适用|
| SecondaryBackupProtectionState_s |Text |是否为备份项启用辅助保护|
| SchemaVersion_s |Text |架构，例如，版本**V2** |
| State_s |Text |备份项对象的状态，例如 Active、Deleted |
| BackupManagementType_s |Text |执行备份的提供程序类型，例如此备份项所属的 IaaSVM、FileFolder |
| OperationName |Text |操作的名称，例如 BackupItem |
| 类别 |Text |诊断数据推送到 Azure Monitor 日志类别。 始终为 AzureBackupReport |
| Resource |Text |正在收集其数据的资源，例如“恢复服务保管库名称” |
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |正在收集其数据的资源 ID，例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型，例如 Vaults |

### <a name="backupitemassociation"></a>BackupItemAssociation

此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此字段表示此事件的名称，始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |备份项的唯一 ID |
| SchemaVersion_s |Text |此字段表示架构的当前版本，它是**V2** |
| State_s |Text |备份项关联对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| BackupItemSourceSize_s |Text | 备份项的前端大小 |
| BackupManagementServerUniqueId_s |Text | 如果适用，通过保护字段用于唯一标识备份管理服务器备份项 |
| 类别 |Text |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| OperationName |Text |此字段表示当前操作的名称 - BackupItemAssociation |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedContainerUniqueId_s |Text |与备份项 (在 V1 中的是 ProtectedServerUniqueId_s) 关联的受保护服务器的唯一标识符 |
| VaultUniqueId_s |Text |包含备份项的保管库的唯一标识符 |
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceProvider |Text |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型，例如 Vaults |

### <a name="backupmanagementserver"></a>BackupManagementServer

此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |备份管理服务器的名称        |
|AzureBackupAgentVersion_s     |Text         |备份管理服务器上的 Azure 备份代理的版本          |
|BackupManagementServerVersion_s     |Text         |备份管理服务器的版本|
|BackupManagementServerOSVersion_s    |Text            |备份管理服务器的操作系统版本|
|BackupManagementServerType_s     |Text         |类型的备份管理服务器，作为 MABS，SC DPM|
|BackupManagementServerUniqueId_s     |Text         |字段用于唯一标识备份管理服务器       |
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceProvider |Text |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型，例如 Vaults |

### <a name="job"></a>作业

此表提供作业相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |事件的名称。 始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |备份项的唯一标识符 |
| SchemaVersion_s |Text |架构，例如，版本**V2** |
| State_s |Text |作业对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text |此字段表示当前操作的名称 - Job |
| 类别 |Text |此字段表示诊断数据推送到 Azure Monitor 日志类别，值为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |Text |与作业关联的受保护服务器的唯一标识符 |
| ProtectedContainerUniqueId_s |Text | 用于标识受保护的容器在运行作业的唯一 Id |
| VaultUniqueId_s |Text |受保护保管库的唯一标识符 |
| JobOperation_s |Text |为其运行作业的操作，例如备份、还原、配置备份 |
| JobStatus_s |Text |作业的状态，例如 Completed、Failed |
| JobFailureCode_s |Text |导致作业失败的故障代码字符串 |
| JobStartDateTime_s |Date/Time |开始运行作业的日期和时间 |
| BackupStorageDestination_s |Text |备份存储目标，例如 Cloud、Disk  |
| AdHocOrScheduledJob_s |Text | 字段来指定是否 Ad Hoc 或计划的作业 |
| JobDurationInSecs_s | Number |作业的总持续时间，以秒为单位 |
| DataTransferredInMB_s | Number |此作业传输的数据量，以 MB 为单位|
| JobUniqueId_g |Text |用于标识作业的唯一 ID |
| RecoveryJobDestination_s |Text | 目标的恢复作业，恢复数据 |
| RecoveryJobRPDateTime_s |DateTime | 日期，正在恢复的恢复点的创建的时间 |
| RecoveryJobRPLocation_s |Text | 存储要恢复的恢复点的位置|
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID”|
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |

### <a name="policy"></a>策略

此表提供策略相关字段的详细信息。

| 字段 | 数据类型 | 适用的版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此字段表示架构的当前版本，它是**V2** |
| State_s |Text ||策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text ||服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此字段表示当前操作的名称 - Policy |
| 类别 |Text ||此字段表示诊断数据推送到 Azure Monitor 日志类别，值为 AzureBackupReport |
| Resource |Text ||这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |Text ||用于标识策略的唯一 ID |
| PolicyName_s |Text ||已定义的策略的名称 |
| BackupFrequency_s |Text ||运行备份的频率，例如 daily、weekly |
| BackupTimes_s |Text ||计划备份时的日期和时间 |
| BackupDaysOfTheWeek_s |Text ||计划备份时的日期（星期几） |
| RetentionDuration_s |整数 ||所配置备份的保留持续时间 |
| DailyRetentionDuration_s |整数 ||所配置备份的总保留时间（按天算） |
| DailyRetentionTimes_s |Text ||配置每日保留时的日期和时间 |
| WeeklyRetentionDuration_s |十进制数 ||所配置备份的每周保留总时间（按周算） |
| WeeklyRetentionTimes_s |Text ||配置每周保留时的日期和时间 |
| WeeklyRetentionDaysOfTheWeek_s |Text ||选择进行每周保留的日期（星期几） |
| MonthlyRetentionDuration_s |十进制数 ||所配置备份的总保留时间（按月算） |
| MonthlyRetentionTimes_s |Text ||配置每月保留时的日期和时间 |
| MonthlyRetentionFormat_s |Text ||每月保留的配置类型，例如基于日期的每日、基于周次的每周 |
| MonthlyRetentionDaysOfTheWeek_s |Text ||选择进行每月保留的日期（星期几） |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||配置每月保留时的当月周次，例如 First、Last 等 |
| YearlyRetentionDuration_s |十进制数 ||所配置备份的总保留时间（按年算） |
| YearlyRetentionTimes_s |Text ||配置每年保留时的日期和时间 |
| YearlyRetentionMonthsOfTheYear_s |Text ||选择进行每年保留的月份 |
| YearlyRetentionFormat_s |Text ||每年保留的配置类型，例如基于日期的每日、基于周次的每周 | |
| YearlyRetentionDaysOfTheMonth_s |Text ||选择进行每年保留的日期 |
| SynchronisationFrequencyPerDay_s |整数 |v2|文件备份同步 SC DPM 和 MABS 一天中的次数 |
| DiffBackupFormat_s |Text |v2|在 Azure VM 备份的 SQL 格式对于差异备份 |
| DiffBackupTime_s |Time |v2|SQL Azure VM 备份中的差异备份的时间|
| DiffBackupRetentionDuration_s |十进制数 |v2|SQL Azure VM 备份中的差异备份的保留持续时间|
| LogBackupFrequency_s |十进制数 |v2|Sql 日志备份的频率|
| LogBackupRetentionDuration_s |十进制数 |v2|为 Azure VM 备份中的 SQL 日志备份的保留持续时间|
| DiffBackupDaysofTheWeek_s |Text |v2|SQL Azure VM 备份中的差异备份的星期日期|
| SourceSystem |Text ||当前数据的源系统 - Azure |
| ResourceId |Text ||正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text ||正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||正在收集其数据的资源类型。 例如 Vaults |

### <a name="policyassociation"></a>PolicyAssociation

此表提供有关策略与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 适用的版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此字段表示架构的当前版本，它是**V2** |
| State_s |Text ||策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text ||服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此字段表示当前操作的名称 - PolicyAssociation |
| 类别 |Text ||此字段表示诊断数据推送到 Azure Monitor 日志类别，值为 AzureBackupReport |
| Resource |Text ||这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |Text ||用于标识策略的唯一 ID |
| VaultUniqueId_s |Text ||此策略所属的保管库的唯一 ID |
| BackupManagementServerUniqueId_s |Text |v2 |如果适用，通过保护字段用于唯一标识备份管理服务器备份项        |
| SourceSystem |Text ||当前数据的源系统 - Azure |
| ResourceId |Text ||正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text ||正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||正在收集其数据的资源类型。 例如 Vaults |

### <a name="protected-container"></a>受保护的容器

此表提供了有关受保护的容器的基本字段。 （在 v1 中为 ProtectedServer）

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | 字段来唯一地标识保护容器 |
| ProtectedContainerOSType_s |Text |受保护的容器的 OS 类型 |
| ProtectedContainerOSVersion_s |Text |OS 版本的受保护的容器 |
| AgentVersion_s |Text |代理备份或保护代理 （对于 SC DPM 和 MABS） 的版本数 |
| BackupManagementType_s |Text |执行备份的提供程序类型。 对于 IaaSVM、 FileFolder |
| EntityState_s |Text |受保护的服务器对象的当前状态。 例如，Active、 Deleted |
| ProtectedContainerFriendlyName_s |Text |受保护的服务器的友好名称 |
| ProtectedContainerName_s |Text |受保护的容器的名称 |
| ProtectedContainerWorkloadType_s |Text |备份受保护的容器的类型。 例如，IaaSVMContainer |
| ProtectedContainerLocation_s |Text |受保护的容器是位于的本地还是在 Azure 中 |
| ProtectedContainerType_s |Text |受保护的容器是否是一个服务器或一个容器 |
| ProtectedContainerProtectionState_s’  |Text |受保护的容器的保护状态 |

### <a name="storage"></a>存储

此表提供存储相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| CloudStorageInBytes_s |十进制数 |云备份存储使用的备份，计算基于最新值 （此字段是仅为 v1 架构）|
| ProtectedInstances_s |十进制数 |用于计算账单中前端存储量的受保护实例的数目，基于最新值进行计算 |
| EventName_s |Text |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text |此字段表示架构的当前版本，它是**V2** |
| State_s |Text |存储对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text |此字段表示当前操作的名称 - Storage |
| 类别 |Text |此字段表示诊断数据推送到 Azure Monitor 日志类别，值为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |Text |为其计算存储的受保护服务器的唯一 ID |
| VaultUniqueId_s |Text |为其计算存储的保管库的唯一 ID |
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |
| StorageUniqueId_s |Text |用来标识存储实体的唯一 Id |
| StorageType_s |Text |存储，例如云、 卷、 磁盘类型 |
| StorageName_s |Text |存储实体，例如 E:\ 的名称 |
| StorageTotalSizeInGBs_s |Text |存储空间，以 gb 为单位，使用存储实体的总大小|

### <a name="storageassociation"></a>StorageAssociation

此表提供了与存储相关的基本字段连接到其他实体的存储。

| 字段 | 数据类型 | 描述 |
| --- | --- |  --- |
| StorageUniqueId_s |Text |用来标识存储实体的唯一 Id |
| SchemaVersion_s |Text |此字段表示架构的当前版本，它是**V2** |
| BackupItemUniqueId_s |Text |用来标识与存储实体相关的备份项的唯一 Id |
| BackupManagementServerUniqueId_s |Text |用来标识与存储实体相关的备份管理服务器的唯一 Id|
| VaultUniqueId_s |Text |用来标识此保管库与存储实体相关的唯一 Id|
| StorageConsumedInMBs_s |Number|使用相应的存储区中的相应备份项的存储空间的大小 |
| StorageAllocatedInMBs_s |Number |分配的磁盘类型对应的备份项在相应的存储区中存储的大小|

### <a name="vault"></a>保管库

此表提供保管库相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text |此字段表示架构的当前版本，它是**V2** |
| State_s |Text |保管库对象的当前状态，例如 Active、Deleted |
| OperationName |Text |此字段表示当前操作的名称 - Vault |
| 类别 |Text |此字段表示诊断数据推送到 Azure Monitor 日志类别，值为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| VaultUniqueId_s |Text |保管库的唯一 ID |
| VaultName_s |Text |保管库的名称 |
| AzureDataCenter_s |Text |保管库所在的数据中心 |
| StorageReplicationType_s |Text |保管库存储复制的类型，例如 GeoRedundant |
| SourceSystem |Text |当前数据的源系统 - Azure |
| ResourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| resourceGroup |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |

### <a name="backup-management-server"></a>备份管理服务器

此表提供了有关备份管理服务器的基本字段。

|字段  |数据类型  | 描述  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |备份管理服务器的名称        |
|AzureBackupAgentVersion_s     |Text         |备份管理服务器上的 Azure 备份代理的版本          |
|BackupManagmentServerVersion_s     |Text         |备份管理服务器的版本|
|BackupManagmentServerOSVersion_s     |Text            |备份管理服务器的操作系统版本|
|BackupManagementServerType_s     |Text         |类型的备份管理服务器，作为 MABS，SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |字段用于唯一标识备份管理服务器       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

此表指定卷是与相关联的工作负载。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| StorageUniqueId_s |Text |用来标识存储实体的唯一 Id |
| BackupItemType_s |Text |此卷是首选的存储工作负荷|

### <a name="protectedinstance"></a>ProtectedInstance

此表提供了基本的受保护的实例相关字段。

| 字段 | 数据类型 |适用的版本 | 描述 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|使用 DPM，MABS 唯一 Id 用于标识备份项的 Vm 备份|
| ProtectedContainerUniqueId_s |Text |v2|使用 DPM，MABS 用来标识受保护的容器，以便 Vm 以外的所有内容唯一 Id 备份|
| ProtectedInstanceCount_s |Text |v2|计数的受保护的实例相关联的备份项或该日期时间上的受保护的容器|

### <a name="recoverypoint"></a>RecoveryPoint

此表提供了基本的恢复点相关的字段。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |使用 DPM，MABS 唯一 Id 用于标识备份项的 Vm 备份|
| OldestRecoveryPointTime_s |Text |备份项的最早的恢复点日期时间|
| OldestRecoveryPointLocation_s |Text |备份项的最早的恢复点的位置|
| LatestRecoveryPointTime_s |Text |备份项的最新恢复点日期时间|
| LatestRecoveryPointLocation_s |Text |备份项的最新恢复点的位置|

## <a name="next-steps"></a>后续步骤

一旦您查看的数据模型，即可[创建自定义查询](../azure-monitor/learn/tutorial-logs-dashboards.md)Azure Monitor 日志来构建自己的仪表板中。
