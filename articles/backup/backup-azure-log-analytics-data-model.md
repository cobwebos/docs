---
title: Azure 备份的 Azure Monitor 日志数据模型
description: 本文提供 Azure 备份数据的 Azure Monitor 日志数据模型详细信息。
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 6563eefffee0ed8d9ce94c3e0a1e24b0d32314f0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466155"
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
|AlertTimeToResolveInMinutes_s    | 数量        |解决某个警报所花费的时间。 对于活动警报为空白。         |
|AlertConsolidationStatus_s   |Text         |确定警报是否为合并的警报         |
|CountOfAlertsConsolidated_s     |数量         |合并的警报数目（如果为合并的警报）          |
|AlertRaisedOn_s     |Text         |对其引发警报的实体的类型         |
|AlertCode_s     |Text         |用于唯一标识警报类型的代码         |
|RecommendedAction_s   |Text         |建议用于解决警报的操作         |
| EventName_s |Text |活动名称。 始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |与警报关联的备份项的唯一标识符 |
| SchemaVersion_s |Text |架构的当前版本，例如 **V2** |
| State_s |Text |警报对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |执行备份的提供程序类型，例如此警报所属的 IaaSVM、FileFolder |
| OperationName |Text |当前操作的名称，例如 Alert |
| 类别 |Text |推送到 Azure Monitor 日志的诊断数据类别。 始终为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedContainerUniqueId_s |Text |与警报关联的受保护服务器的唯一标识符（在 V1 中为 ProtectedServerUniqueId_s）|
| VaultUniqueId_s |Text |与警报关联的受保护保管库的唯一标识符 |
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |与收集的数据相关的资源的唯一标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |

### <a name="backupitem"></a>BackupItem

此表提供备份项相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |活动名称。 始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |备份项的唯一标识符 |
| BackupItemId_s |Text |备份项的标识符（此字段仅适用于 v1 架构） |
| BackupItemName_s |Text |备份项的名称 |
| BackupItemFriendlyName_s |Text |备份项的友好名称 |
| BackupItemType_s |文本 |备份项的类型，例如 VM、FileFolder |
| BackupItemProtectionState_s |Text |备份项的保护状态 |
| BackupItemAppVersion_s |Text |备份项的应用程序版本 |
| ProtectionState_s |Text |备份项的当前保护状态，例如 Protected、ProtectionStopped |
| ProtectionGroupName_s |Text | SC DPM 和 MABS 的保护组名称，其中的备份项受保护（如果适用）|
| SecondaryBackupProtectionState_s |Text |是否为备份项启用了辅助保护|
| SchemaVersion_s |Text |架构的版本，例如 **V2** |
| State_s |Text |备份项对象的状态，例如 Active、Deleted |
| BackupManagementType_s |Text |执行备份的提供程序类型，例如此备份项所属的 IaaSVM、FileFolder |
| OperationName |Text |操作的名称，例如 BackupItem |
| 类别 |Text |推送到 Azure Monitor 日志的诊断数据类别。 始终为 AzureBackupReport |
| Resource |Text |正在收集其数据的资源，例如“恢复服务保管库名称” |
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |正在收集其数据的资源 ID，例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型，例如 Vaults |

### <a name="backupitemassociation"></a>BackupItemAssociation

此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此字段表示此事件的名称，始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |备份项的唯一 ID |
| SchemaVersion_s |Text |此字段表示架构的当前版本，值为 **V2** |
| State_s |Text |备份项关联对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| BackupItemSourceSize_s |Text | 备份项的前端大小 |
| BackupManagementServerUniqueId_s |Text | 用于唯一标识保护备份项所用的备份管理服务器的字段（如果适用） |
| 类别 |Text |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| OperationName |Text |此字段表示当前操作的名称 - BackupItemAssociation |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedContainerUniqueId_s |Text |与备份项关联的受保护服务器的唯一标识符（在 V1 中为 ProtectedServerUniqueId_s） |
| VaultUniqueId_s |Text |包含备份项的保管库的唯一标识符 |
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceProvider |Text |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型，例如 Vaults |

### <a name="backupmanagementserver"></a>BackupManagementServer

此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |备份管理服务器的名称        |
|AzureBackupAgentVersion_s     |Text         |备份管理服务器上的 Azure 备份代理版本          |
|BackupManagementServerVersion_s     |Text         |备份管理服务器的版本|
|BackupManagementServerOSVersion_s    |Text            |备份管理服务器的 OS 版本|
|BackupManagementServerType_s     |Text         |备份管理服务器的类型：MABS 或 SC DPM|
|BackupManagementServerUniqueId_s     |Text         |用于唯一标识备份管理服务器的字段       |
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的订阅标识符 |
| ResourceProvider |Text |正在收集其数据的资源提供程序，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型，例如 Vaults |

### <a name="job"></a>作业

此表提供作业相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |活动名称。 始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |备份项的唯一标识符 |
| SchemaVersion_s |Text |架构的版本，例如 **V2** |
| State_s |Text |作业对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text |此字段表示当前操作的名称 - Job |
| 类别 |Text |此字段表示推送到 Azure Monitor 日志的诊断数据的类别，值为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |Text |与作业关联的受保护服务器的唯一标识符 |
| ProtectedContainerUniqueId_s |Text | 用于标识运行作业的受保护容器的唯一 ID |
| VaultUniqueId_s |Text |受保护保管库的唯一标识符 |
| JobOperation_s |Text |为其运行作业的操作，例如备份、还原、配置备份 |
| JobStatus_s |Text |作业的状态，例如 Completed、Failed |
| JobFailureCode_s |Text |导致作业失败的故障代码字符串 |
| JobStartDateTime_s |Date/Time |开始运行作业的日期和时间 |
| BackupStorageDestination_s |Text |备份存储目标，例如 Cloud、Disk  |
| AdHocOrScheduledJob_s |Text | 用于指定作业是临时作业还是计划作业的字段 |
| JobDurationInSecs_s | 数量 |作业的总持续时间，以秒为单位 |
| DataTransferredInMB_s | 数量 |此作业传输的数据量，以 MB 为单位|
| JobUniqueId_g |Text |用于标识作业的唯一 ID |
| RecoveryJobDestination_s |Text | 恢复作业的目标，数据将恢复到其中 |
| RecoveryJobRPDateTime_s |日期时间 | 要恢复的恢复点的创建日期和时间 |
| RecoveryJobRPLocation_s |Text | 要恢复的恢复点已还原到的位置|
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID”|
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |

### <a name="policy"></a>策略

此表提供策略相关字段的详细信息。

| 字段 | 数据类型 | 适用版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此字段表示架构的当前版本，值为 **V2** |
| State_s |Text ||策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text ||服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此字段表示当前操作的名称 - Policy |
| 类别 |Text ||此字段表示推送到 Azure Monitor 日志的诊断数据的类别，值为 AzureBackupReport |
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
| SynchronisationFrequencyPerDay_s |整数 |v2|一天中同步 SC DPM 和 MABS 文件备份的次数 |
| DiffBackupFormat_s |Text |v2|Azure VM 备份中 SQL 差异备份的格式 |
| DiffBackupTime_s |Time |v2|Azure VM 备份中 SQL 差异备份的时间|
| DiffBackupRetentionDuration_s |十进制数 |v2|Azure VM 备份中 SQL 差异备份的保留期|
| LogBackupFrequency_s |十进制数 |v2|SQL 日志备份的频率|
| LogBackupRetentionDuration_s |十进制数 |v2|Azure VM 备份中 SQL 日志备份的保留期|
| DiffBackupDaysofTheWeek_s |Text |v2|Azure VM 备份中 SQL 差异备份的星期|
| SourceSystem |Text ||当前数据的源系统 - Azure |
| resourceId |Text ||正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text ||正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||正在收集其数据的资源类型。 例如 Vaults |

### <a name="policyassociation"></a>PolicyAssociation

此表提供有关策略与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 适用版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此字段表示架构的当前版本，值为 **V2** |
| State_s |Text ||策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text ||服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此字段表示当前操作的名称 - PolicyAssociation |
| 类别 |Text ||此字段表示推送到 Azure Monitor 日志的诊断数据的类别，值为 AzureBackupReport |
| Resource |Text ||这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |Text ||用于标识策略的唯一 ID |
| VaultUniqueId_s |Text ||此策略所属的保管库的唯一 ID |
| BackupManagementServerUniqueId_s |Text |v2 |用于唯一标识保护备份项所用的备份管理服务器的字段（如果适用）        |
| SourceSystem |Text ||当前数据的源系统 - Azure |
| resourceId |Text ||正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text ||正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text ||正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||正在收集其数据的资源类型。 例如 Vaults |

### <a name="protected-container"></a>受保护容器

下表提供了有关受保护容器的基本字段。 （在 v1 中为 ProtectedServer）

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | 用于唯一标识受保护容器的字段 |
| ProtectedContainerOSType_s |Text |受保护容器的 OS 类型 |
| ProtectedContainerOSVersion_s |Text |受保护容器的 OS 版本 |
| AgentVersion_s |Text |代理备份或保护代理（如果使用 SC DPM 和 MABS）的版本号 |
| BackupManagementType_s |Text |用于执行备份的提供程序类型。 例如 IaaSVM、FileFolder |
| EntityState_s |Text |受保护服务器对象的当前状态。 例如 Active、Deleted |
| ProtectedContainerFriendlyName_s |Text |受保护的服务器的友好名称 |
| ProtectedContainerName_s |Text |受保护容器的名称 |
| ProtectedContainerWorkloadType_s |Text |备份的受保护容器类型 例如 IaaSVMContainer |
| ProtectedContainerLocation_s |Text |受保护容器是位于本地还是 Azure 中 |
| ProtectedContainerType_s |Text |受保护容器是服务器还是容器 |
| ProtectedContainerProtectionState_s’  |Text |受保护容器的保护状态 |

### <a name="storage"></a>存储

此表提供存储相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| CloudStorageInBytes_s |十进制数 |备份所用的云备份存储量，基于最新值计算（此字段仅适用于 v1 架构）|
| ProtectedInstances_s |十进制数 |用于计算账单中前端存储量的受保护实例的数目，基于最新值进行计算 |
| EventName_s |Text |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text |此字段表示架构的当前版本，值为 **V2** |
| State_s |Text |存储对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |Text |服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| OperationName |Text |此字段表示当前操作的名称 - Storage |
| 类别 |Text |此字段表示推送到 Azure Monitor 日志的诊断数据的类别，值为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |Text |为其计算存储的受保护服务器的唯一 ID |
| VaultUniqueId_s |Text |为其计算存储的保管库的唯一 ID |
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |
| StorageUniqueId_s |Text |用于标识存储实体的唯一 ID |
| StorageType_s |Text |存储类型，例如 Cloud、Volume、Disk |
| StorageName_s |Text |存储实体的名称，例如 E:\ |
| StorageTotalSizeInGBs_s |Text |存储实体占用的存储总大小 (GB)|

### <a name="storageassociation"></a>StorageAssociation

下表提供了用于将存储连接到其他实体的存储相关基本字段。

| 字段 | 数据类型 | 描述 |
| --- | --- |  --- |
| StorageUniqueId_s |Text |用于标识存储实体的唯一 ID |
| SchemaVersion_s |Text |此字段表示架构的当前版本，值为 **V2** |
| BackupItemUniqueId_s |Text |用于标识与存储实体相关的备份项的唯一 ID |
| BackupManagementServerUniqueId_s |Text |用于标识与存储实体相关的备份管理服务器的唯一 ID|
| VaultUniqueId_s |Text |用于标识与存储实体相关的保管库的唯一 ID|
| StorageConsumedInMBs_s |数量|相应存储中相应备份项占用的存储大小 |
| StorageAllocatedInMBs_s |数量 |磁盘类型的相应存储中相应备份项分配的存储大小|

### <a name="vault"></a>保管库

此表提供保管库相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |Text |此字段表示架构的当前版本，值为 **V2** |
| State_s |Text |保管库对象的当前状态，例如 Active、Deleted |
| OperationName |Text |此字段表示当前操作的名称 - Vault |
| 类别 |Text |此字段表示推送到 Azure Monitor 日志的诊断数据的类别，值为 AzureBackupReport |
| Resource |Text |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| VaultUniqueId_s |Text |保管库的唯一 ID |
| VaultName_s |Text |保管库的名称 |
| AzureDataCenter_s |Text |保管库所在的数据中心 |
| StorageReplicationType_s |Text |保管库存储复制的类型，例如 GeoRedundant |
| SourceSystem |Text |当前数据的源系统 - Azure |
| resourceId |Text |正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| SubscriptionId |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| 资源组 |Text |正在收集其数据的资源（例如 恢复服务保管库）的资源组 |
| ResourceProvider |Text |正在收集其数据的资源提供程序。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集其数据的资源类型。 例如 Vaults |

### <a name="backup-management-server"></a>备份管理服务器

下表提供了有关备份管理服务器的基本字段。

|字段  |数据类型  | 描述  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |备份管理服务器的名称        |
|AzureBackupAgentVersion_s     |Text         |备份管理服务器上的 Azure 备份代理版本          |
|BackupManagmentServerVersion_s     |Text         |备份管理服务器的版本|
|BackupManagmentServerOSVersion_s     |Text            |备份管理服务器的 OS 版本|
|BackupManagementServerType_s     |Text         |备份管理服务器的类型：MABS 或 SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |用于唯一标识备份管理服务器的字段       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

下表指定了与卷关联的工作负荷。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| StorageUniqueId_s |Text |用于标识存储实体的唯一 ID |
| BackupItemType_s |Text |使用此卷作为首选存储的工作负荷|

### <a name="protectedinstance"></a>ProtectedInstance

下表提供了与受保护实例相关的基本字段。

| 字段 | 数据类型 |适用版本 | 描述 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|用于标识使用 DPM、MABS 备份的 VM 的备份项的唯一 ID|
| ProtectedContainerUniqueId_s |Text |v2|用于标识受保护容器（除使用 DPM、MABS 备份的 VM 以外的所有内容）的唯一 ID|
| ProtectedInstanceCount_s |Text |v2|关联的备份项或受保护容器在该日期时间的受保护实例计数|

### <a name="recoverypoint"></a>RecoveryPoint

下表提供了与恢复点相关的基本字段。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |用于标识使用 DPM、MABS 备份的 VM 的备份项的唯一 ID|
| OldestRecoveryPointTime_s |Text |备份项的最旧恢复点的日期时间|
| OldestRecoveryPointLocation_s |Text |备份项的最旧恢复点的位置|
| LatestRecoveryPointTime_s |Text |备份项的最新恢复点的日期时间|
| LatestRecoveryPointLocation_s |Text |备份项的最新恢复点的位置|

## <a name="next-steps"></a>后续步骤

查看数据模型后，可以开始在 Azure Monitor 日志中[创建自定义查询](../azure-monitor/learn/tutorial-logs-dashboards.md)，以构建自己的仪表板。
