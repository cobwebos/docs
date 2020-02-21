---
title: Azure 备份诊断事件的数据模型
description: 此数据模型引用将诊断事件发送到 Log Analytics （LA）的资源特定模式。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d38c9dedba7111923fa4f823d348d0783ac36681
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500622"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure 备份诊断事件的数据模型

## <a name="coreazurebackup"></a>CoreAzureBackup

此表提供有关核心备份实体（如保管库和备份项）的信息。

| **字段**                         | **数据类型** | **说明**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | 文本          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID。 |
| OperationName                     | 文本          | 此字段表示当前操作的名称-BackupItem、BackupItemAssociation 或 ProtectedContainer。 |
| 类别                          | 文本          | 此字段表示推送到 Azure Monitor 日志的诊断数据的类别。 例如，CoreAzureBackup。 |
| AgentVersion                      | 文本          | 代理备份或保护代理的版本号（如果是 SC DPM 和 MABS） |
| AzureBackupAgentVersion           | 文本          | 备份管理服务器上的 Azure 备份代理版本 |
| AzureDataCenter                   | 文本          | 保管库所在的数据中心                       |
| BackupItemAppVersion              | 文本          | 备份项的应用程序版本                       |
| BackupItemFriendlyName            | 文本          | 备份项的友好名称                             |
| BackupItemName                    | 文本          | 备份项的名称                                      |
| BackupItemProtectionState         | 文本          | 备份项的保护状态                          |
| BackupItemFrontEndSize            | 文本          | 备份项的前端大小                            |
| BackupItemType                    | 文本          | 备份项的类型。 例如： VM、FileFolder             |
| BackupItemUniqueId                | 文本          | 备份项的唯一标识符                         |
| BackupManagementServerType        | 文本          | 备份管理服务器的类型，如 MABS、SC DPM     |
| BackupManagementServerUniqueId    | 文本          | 用于唯一标识备份管理服务器的字段      |
| BackupManagementType              | 文本          | 执行备份作业的服务器的提供程序类型。 例如，IaaSVM、FileFolder |
| BackupManagementServerName        | 文本          | 备份管理服务器的名称                         |
| BackupManagementServerOSVersion   | 文本          | 备份管理服务器的操作系统版本                   |
| BackupManagementServerVersion     | 文本          | 备份管理服务器的版本                      |
| LatestRecoveryPointLocation       | 文本          | 备份项的最新恢复点的位置    |
| LatestRecoveryPointTime           | DateTime      | 备份项的最新恢复点的日期时间   |
| OldestRecoveryPointLocation       | 文本          | 备份项的最早恢复点的位置    |
| OldestRecoveryPointTime           | DateTime      | 备份项的最新恢复点的日期时间   |
| PolicyUniqueId                    | 文本          | 用于标识策略的唯一 ID                             |
| ProtectedContainerFriendlyName    | 文本          | 受保护服务器的友好名称                        |
| ProtectedContainerLocation        | 文本          | 受保护的容器是位于本地还是 Azure 中 |
| ProtectedContainerName            | 文本          | 受保护容器的名称                            |
| ProtectedContainerOSType          | 文本          | 受保护容器的操作系统类型                          |
| ProtectedContainerOSVersion       | 文本          | 受保护容器的操作系统版本                        |
| ProtectedContainerProtectionState | 文本          | 受保护容器的保护状态                  |
| ProtectedContainerType            | 文本          | 受保护的容器是服务器还是容器  |
| ProtectedContainerUniqueId        | 文本          | 用于标识受保护容器的唯一 ID，该 ID 用于标识使用 DPM 备份的 Vm 以外的所有内容、MABS |
| ProtectedContainerWorkloadType    | 文本          | 备份的受保护容器的类型。 例如，IaaSVMContainer |
| ProtectionGroupName               | 文本          | 保护备份项的保护组的名称，适用于 SC DPM 和 MABS （如果适用） |
| ResourceGroupName                 | 文本          | 正在收集的数据的资源的资源组（例如，恢复服务保管库） |
| schemaVersion                     | 文本          | 此字段表示架构的当前版本，它是**V2** |
| SecondaryBackupProtectionState    | 文本          | 是否为备份项启用辅助保护  |
| 状态                             | 文本          | 备份项对象的状态。 例如，Active、Deleted |
| StorageReplicationType            | 文本          | 保管库的存储复制类型。 例如，GeoRedundant |
| SubscriptionId                    | 文本          | 收集其数据的资源（例如，恢复服务保管库）的订阅标识符 |
| VaultName                         | 文本          | 保管库的名称                                            |
| VaultTags                         | 文本          | 与保管库资源关联的标记                    |
| VaultUniqueId                     | 文本          | 保管库的唯一标识符                             |
| SourceSystem                      | 文本          | 当前数据的源系统-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

此表提供警报相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 与收集的数据相关的资源的唯一标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | 文本          | 当前操作的名称。 例如，Alert            |
| 类别                       | 文本          | 推送到 Azure Monitor 日志的诊断数据类别-AddonAzureBackupAlerts |
| AlertCode                      | 文本          | 用于唯一标识警报类型的代码                     |
| AlertConsolidationStatus       | 文本          | 确定警报是否为合并的警报         |
| AlertOccurrenceDateTime        | DateTime      | 创建警报的日期和时间                     |
| AlertRaisedOn                  | 文本          | 引发警报的实体类型                        |
| AlertSeverity                  | 文本          | 警报的严重性。 例如，关键                 |
| AlertStatus                    | 文本          | 警报的状态。 例如，Active                     |
| AlertTimeToResolveInMinutes    | Number        | 解决警报所需的时间。 对于活动警报为空。     |
| AlertType                      | 文本          | 警报的类型。 例如，备份                           |
| AlertUniqueId                  | 文本          | 生成的警报的唯一标识符                    |
| BackupItemUniqueId             | 文本          | 与警报关联的备份项的唯一标识符 |
| BackupManagementServerUniqueId | 文本          | 用于唯一标识备份项目受保护的备份管理服务器的字段（如果适用） |
| BackupManagementType           | 文本          | 执行备份作业的服务器的提供程序类型，例如，IaaSVM、FileFolder |
| CountOfAlertsConsolidated      | Number        | 合并警报的警报的数目  |
| ProtectedContainerUniqueId     | 文本          | 与警报关联的受保护服务器的唯一标识符 |
| RecommendedAction              | 文本          | 建议用于解决警报的操作                      |
| schemaVersion                  | 文本          | 架构的当前版本，例如**V2**            |
| 状态                          | 文本          | 警报对象的当前状态，例如 Active、Deleted |
| StorageUniqueId                | 文本          | 用于标识存储实体的唯一 ID                |
| VaultUniqueId                  | 文本          | 用于标识与警报相关的保管库的唯一 ID    |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

此表提供了与实例相关的基本字段。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 收集其数据的资源的唯一标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | 文本          | 操作的名称，例如 ProtectedInstance         |
| 类别                       | 文本          | 推送到 Azure Monitor 日志的诊断数据类别-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | 文本          | 备份项的唯一 ID                                 |
| BackupManagementServerUniqueId | 文本          | 用于唯一标识备份项目受保护的备份管理服务器的字段（如果适用） |
| BackupManagementType           | 文本          | 执行备份作业的服务器的提供程序类型，例如，IaaSVM、FileFolder |
| ProtectedContainerUniqueId     | 文本          | 用于标识运行作业的受保护容器的唯一 ID |
| ProtectedInstanceCount         | 文本          | 此日期时间关联的备份项或受保护的容器的受保护实例计数 |
| schemaVersion                  | 文本          | 架构的当前版本，例如**V2**            |
| 状态                          | 文本          | 备份项对象的状态，例如 "活动"、"已删除" |
| VaultUniqueId                  | 文本          | 与受保护实例关联的受保护保管库的唯一标识符 |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

此表提供作业相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | 文本          | 此字段表示当前操作的名称 - Job    |
| 类别                       | 文本          | 此字段表示推送到 Azure Monitor 日志的诊断数据类别-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | 文本          | 用于指定作业是即席还是计划的字段           |
| BackupItemUniqueId             | 文本          | 用于标识与存储实体相关的备份项的唯一 ID |
| BackupManagementServerUniqueId | 文本          | 用于标识与存储实体相关的备份管理服务器的唯一 ID |
| BackupManagementType           | 文本          | 用于执行备份的提供程序类型，例如，此警报所属的 IaaSVM、FileFolder |
| DataTransferredInMB            | Number        | 此作业传输的数据量，以 MB 为单位                          |
| JobDurationInSecs              | Number        | 作业的总持续时间，以秒为单位                                |
| JobFailureCode                 | 文本          | 导致作业失败的故障代码字符串    |
| JobOperation                   | 文本          | 为其运行作业的操作，例如备份、还原、配置备份 |
| JobOperationSubType            | 文本          | 作业操作的子类型。 例如，如果是日志备份作业，则为 "Log" |
| JobStartDateTime               | DateTime      | 开始运行作业的日期和时间                       |
| JobStatus                      | 文本          | 作业的状态，例如 Completed、Failed   |
| JobUniqueId                    | 文本          | 用于标识作业的唯一 ID                                |
| ProtectedContainerUniqueId     | 文本          | 与警报关联的受保护服务器的唯一标识符 |
| RecoveryJobDestination         | 文本          | 恢复作业的目标，其中的数据已恢复   |
| RecoveryJobRPDateTime          | DateTime      | 创建要恢复的恢复点的日期和时间 |
| RecoveryJobLocation            | 文本          | 要恢复的恢复点的存储位置 |
| RecoveryLocationType           | 文本          | 恢复位置的类型                                |
| schemaVersion                  | 文本          | 架构的当前版本，例如**V2**            |
| 状态                          | 文本          | 警报对象的当前状态，例如 Active、Deleted |
| VaultUniqueId                  | 文本          | 与警报关联的受保护保管库的唯一标识符 |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

此表提供策略相关字段的详细信息。

| **字段**                       | **数据类型**  | **说明**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | 文本           | 收集其数据的资源的唯一标识符。 例如，恢复服务保管库资源 ID |
| OperationName                   | 文本           | 操作的名称，例如 Policy 或 PolicyAssociation |
| 类别                        | 文本           | 推送到 Azure Monitor 日志的诊断数据类别-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | 文本           | 计划备份时的日期（星期几）            |
| BackupFrequency                 | 文本           | 运行备份的频率。 例如每日、每周 |
| BackupManagementType            | 文本           | 执行备份作业的服务器的提供程序类型。 例如，IaaSVM、FileFolder |
| BackupManagementServerUniqueId  | 文本           | 用于唯一标识备份项目受保护的备份管理服务器的字段（如果适用） |
| BackupTimes                     | 文本           | 计划备份时的日期和时间                     |
| DailyRetentionDuration          | 整数   | 所配置备份的总保留时间（按天算）      |
| DailyRetentionTimes             | 文本           | 配置每日保留时的日期和时间            |
| DiffBackupDaysOfTheWeek         | 文本           | Azure VM 备份中的 SQL 差异备份星期几 |
| DiffBackupFormat                | 文本           | Azure VM 备份中的 SQL 差异备份格式   |
| DiffBackupRetentionDuration     | 小数 | Azure VM 备份中 SQL 差异备份的保留期 |
| DiffBackupTime                  | 时间           | Azure VM 备份中的 SQL 差异备份时间     |
| LogBackupFrequency              | 小数 | SQL 日志备份的频率                            |
| LogBackupRetentionDuration      | 小数 | Azure VM 备份中的 SQL 日志备份的保留期 |
| MonthlyRetentionDaysOfTheMonth  | 文本           | 配置每月保留期的月中的周数。  例如，First、Last 等。 |
| MonthlyRetentionDaysOfTheWeek   | 文本           | 选择进行每月保留的日期（星期几）              |
| MonthlyRetentionDuration        | 文本           | 所配置备份的总保留时间（按月算）    |
| MonthlyRetentionFormat          | 文本           | 每月保留的配置类型。 例如，基于每天，每周基于周 |
| MonthlyRetentionTimes           | 文本           | 配置每月保留时的日期和时间           |
| MonthlyRetentionWeeksOfTheMonth | 文本           | 配置每月保留期的月中的周数。   例如，First、Last 等。 |
| PolicyName                      | 文本           | 已定义的策略的名称                                   |
| PolicyUniqueId                  | 文本           | 用于标识策略的唯一 ID                             |
| PolicyTimeZone                  | 文本           | 策略时间字段在日志中指定的时区 |
| RetentionDuration               | 文本           | 所配置备份的保留持续时间                    |
| RetentionType                   | 文本           | 保留类型                                            |
| schemaVersion                   | 文本           | 此字段表示架构的当前版本，它是**V2** |
| 状态                           | 文本           | 策略对象的当前状态。 例如，Active、Deleted |
| SynchronisationFrequencyPerDay  | 整数   | 一天中为 SC DPM 和 MABS 同步文件备份的次数 |
| VaultUniqueId                   | 文本           | 此策略所属的保管库的唯一 ID          |
| WeeklyRetentionDaysOfTheWeek    | 文本           | 选择进行每周保留的日期（星期几）               |
| WeeklyRetentionDuration         | 小数 | 已配置备份的每周保留持续时间（周） |
| WeeklyRetentionTimes            | 文本           | 配置每周保留时的日期和时间            |
| YearlyRetentionDaysOfTheMonth   | 文本           | 选择进行每年保留的日期             |
| YearlyRetentionDaysOfTheWeek    | 文本           | 选择进行每年保留的日期（星期几）               |
| YearlyRetentionDuration         | 小数 | 所配置备份的总保留时间（按年算）     |
| YearlyRetentionFormat           | 文本           | 每年保留的配置类型，例如基于日期的每日、基于周的每周 |
| YearlyRetentionMonthsOfTheYear  | 文本           | 选择进行每年保留的月份             |
| YearlyRetentionTimes            | 文本           | 配置每年保留时的日期和时间            |
| YearlyRetentionWeeksOfTheMonth  | 文本           | 选择进行每年保留的月中的周数             |
| SourceSystem                    | 文本           | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

此表提供存储相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | 文本          | 此字段表示当前操作的名称-存储或 StorageAssociation |
| 类别                       | 文本          | 此字段表示推送到 Azure Monitor 日志的诊断数据类别-AddonAzureBackupStorage |
| BackupItemUniqueId             | 文本          | 用于识别使用 DPM 备份的 Vm 的备份项的唯一 ID，MABS |
| BackupManagementServerUniqueId | 文本          | 用于唯一标识备份项目受保护的备份管理服务器的字段（如果适用） |
| BackupManagementType           | 文本          | 执行备份作业的服务器的提供程序类型。 例如，IaaSVM、FileFolder |
| PreferredWorkloadOnVolume      | 文本          | 此卷为首选存储的工作负荷      |
| ProtectedContainerUniqueId     | 文本          | 与警报关联的受保护服务器的唯一标识符 |
| schemaVersion                  | 文本          | 架构的版本。 例如， **V2**                   |
| 状态                          | 文本          | 备份项对象的状态。 例如，Active、Deleted |
| StorageAllocatedInMBs          | Number        | 在磁盘类型的相应存储中，相应备份项分配的存储大小 |
| StorageConsumedInMBs           | Number        | 相应存储中的相应备份项使用的存储大小 |
| StorageName                    | 文本          | 存储实体的名称。 例如，E:\                      |
| StorageTotalSizeInGBs          | 文本          | 存储实体占用的存储的总大小（GB）     |
| StorageType                    | 文本          | 存储类型，例如云、卷、磁盘             |
| StorageUniqueId                | 文本          | 用于标识存储实体的唯一 ID                |
| VaultUniqueId                  | 文本          | 用于标识与存储实体相关的保管库的唯一 ID |
| VolumeFriendlyName             | 文本          | 存储卷的友好名称                          |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="next-steps"></a>后续步骤

- [了解如何将诊断数据发送到 Log Analytics](https://aka.ms/AzureBackupDiagnosticsDocs)
- [了解如何编写针对特定于资源的表的查询](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)