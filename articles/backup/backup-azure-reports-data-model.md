---
title: Azure 备份诊断事件的数据模型
description: 此数据模型参考了将诊断事件发送到 Log Analytics (LA) 的资源特定模式。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: c2c5d37596be104c4b1dc7e865586a4728a27bae
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569599"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure 备份诊断事件的数据模型

## <a name="coreazurebackup"></a>CoreAzureBackup

此表提供了有关核心备份实体（例如保管库和备份项）的信息。

| **字段**                         | **数据类型** | **说明**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | 文本          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID。 |
| OperationName                     | 文本          | 此字段表示当前操作的名称 - BackupItem、BackupItemAssociation 或 ProtectedContainer。 |
| Category                          | 文本          | 此字段表示推送到 Azure Monitor 日志的诊断数据的类别。 例如，CoreAzureBackup。 |
| AgentVersion                      | 文本          | 在 SC DPM 和 MABS 的情况下，代理备份的版本号或保护代理 ()  |
| AzureBackupAgentVersion           | 文本          | 备份管理服务器上的 Azure 备份代理版本 |
| AzureDataCenter                   | 文本          | 保管库所在的数据中心                       |
| BackupItemAppVersion              | 文本          | 备份项的应用程序版本                       |
| BackupItemFriendlyName            | 文本          | 备份项的友好名称                             |
| BackupItemName                    | 文本          | 备份项的名称                                      |
| BackupItemProtectionState         | 文本          | 备份项的保护状态                          |
| BackupItemFrontEndSize            | 文本          | 备份项的前端大小                            |
| BackupItemType                    | 文本          | 备份项的类型。 例如：VM、FileFolder             |
| BackupItemUniqueId                | 文本          | 备份项的唯一标识符                         |
| BackupManagementServerType        | 文本          | 备份管理服务器的类型：与在 MABS 或 SC DPM 中一样     |
| BackupManagementServerUniqueId    | 文本          | 用于唯一标识备份管理服务器的字段      |
| BackupManagementType              | 文本          | 执行备份作业的服务器的提供程序类型。 例如 IaaSVM、FileFolder |
| BackupManagementServerName        | 文本          | 备份管理服务器的名称                         |
| BackupManagementServerOSVersion   | 文本          | 备份管理服务器的 OS 版本                   |
| BackupManagementServerVersion     | 文本          | 备份管理服务器的版本                      |
| LatestRecoveryPointLocation       | 文本          | 备份项的最新恢复点的位置    |
| LatestRecoveryPointTime           | DateTime      | 备份项的最新恢复点的日期时间   |
| OldestRecoveryPointLocation       | 文本          | 备份项的最旧恢复点的位置    |
| OldestRecoveryPointTime           | DateTime      | 备份项的最新恢复点的日期时间   |
| PolicyUniqueId                    | 文本          | 用于标识策略的唯一 ID                             |
| ProtectedContainerFriendlyName    | 文本          | 受保护的服务器的友好名称                        |
| ProtectedContainerLocation        | 文本          | 受保护容器是位于本地还是 Azure 中 |
| ProtectedContainerName            | 文本          | 受保护容器的名称                            |
| ProtectedContainerOSType          | 文本          | 受保护容器的 OS 类型                          |
| ProtectedContainerOSVersion       | 文本          | 受保护容器的 OS 版本                        |
| ProtectedContainerProtectionState | 文本          | 受保护容器的保护状态                  |
| ProtectedContainerType            | 文本          | 受保护容器是服务器还是容器  |
| ProtectedContainerUniqueId        | 文本          | 用于标识受保护容器（除使用 DPM、MABS 备份的 VM 以外的所有内容）的唯一 ID |
| ProtectedContainerWorkloadType    | 文本          | 受保护的已备份容器的类型。 例如 IaaSVMContainer |
| ProtectionGroupName               | 文本          | SC DPM 和 MABS 的保护组名称，其中的备份项受保护（如果适用） |
| ResourceGroupName                 | 文本          | 所收集数据的资源（例如，恢复服务保管库）的资源组 |
| schemaVersion                     | 文本          | 此字段表示架构的当前版本。 它为 **V2** |
| SecondaryBackupProtectionState    | 文本          | 是否为备份项启用了辅助保护  |
| 状态                             | 文本          | 备份项对象的状态。 例如 Active、Deleted |
| StorageReplicationType            | 文本          | 保管库的存储复制类型。 例如 GeoRedundant |
| SubscriptionId                    | 文本          | 正在收集其数据的资源（例如，恢复服务保管库）的订阅标识符 |
| VaultName                         | 文本          | 保管库的名称                                            |
| VaultTags                         | 文本          | 与保管库资源关联的标记                    |
| VaultUniqueId                     | 文本          | 保管库的唯一标识符                             |
| SourceSystem                      | 文本          | 当前数据的源系统 - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

此表提供警报相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 与收集的数据相关的资源的唯一标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | 文本          | 当前操作的名称。 例如 Alert            |
| Category                       | 文本          | 推送到 Azure Monitor 日志的诊断数据类别 - AddonAzureBackupAlerts |
| AlertCode                      | 文本          | 用于唯一标识警报类型的代码                     |
| AlertConsolidationStatus       | 文本          | 确定警报是否为合并的警报         |
| AlertOccurrenceDateTime        | DateTime      | 警报的创建日期和时间                     |
| AlertRaisedOn                  | 文本          | 对其引发警报的实体的类型                        |
| AlertSeverity                  | 文本          | 警报的严重性。 例如 Critical                 |
| AlertStatus                    | 文本          | 警报的状态。 例如 Active                     |
| AlertTimeToResolveInMinutes    | Number        | 解决某个警报所花费的时间。 对于活动警报为空白。     |
| AlertType                      | 文本          | 警报类型。 例如 Backup                           |
| AlertUniqueId                  | 文本          | 生成的警报的唯一标识符                    |
| BackupItemUniqueId             | 文本          | 与警报关联的备份项的唯一标识符 |
| BackupManagementServerUniqueId | 文本          | 用于唯一标识保护备份项所用的备份管理服务器的字段（如果适用） |
| BackupManagementType           | 文本          | 服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| CountOfAlertsConsolidated      | Number        | 合并警报的警报数  |
| ProtectedContainerUniqueId     | 文本          | 与警报关联的受保护服务器的唯一标识符 |
| RecommendedAction              | 文本          | 建议用于解决警报的操作                      |
| schemaVersion                  | 文本          | 架构的当前版本，例如 **V2**            |
| 状态                          | 文本          | 警报对象的当前状态，例如 Active、Deleted |
| StorageUniqueId                | 文本          | 用于标识存储实体的唯一 ID                |
| VaultUniqueId                  | 文本          | 用于标识与警报相关的保管库的唯一 ID    |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

下表提供了与受保护实例相关的基本字段。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 与收集的数据相关的资源的唯一标识符。 例如“恢复服务保管库资源 ID” |
| OperationName                  | 文本          | 操作的名称，例如 ProtectedInstance         |
| Category                       | 文本          | 推送到 Azure Monitor 日志的诊断数据的类别 - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | 文本          | 备份项的唯一 ID                                 |
| BackupManagementServerUniqueId | 文本          | 用于唯一标识保护备份项所用的备份管理服务器的字段（如果适用） |
| BackupManagementType           | 文本          | 服务器正在对其执行备份作业的提供程序类型，例如 IaaSVM、FileFolder |
| ProtectedContainerUniqueId     | 文本          | 用于标识运行作业的受保护容器的唯一 ID |
| ProtectedInstanceCount         | 文本          | 关联的备份项或受保护容器在该日期时间的受保护实例计数 |
| schemaVersion                  | 文本          | 架构的当前版本，例如 **V2**            |
| 状态                          | 文本          | 备份项对象的状态，例如 Active、Deleted |
| VaultUniqueId                  | 文本          | 与受保护实例关联的受保护保管库的唯一标识符 |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

此表提供作业相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 正在收集其数据的资源标识符。 例如“恢复服务保管库资源 ID” |
| OperationName                  | 文本          | 此字段表示当前操作的名称 - Job    |
| 类别                       | 文本          | 此字段表示推送到 Azure Monitor 日志的诊断数据的类别 - AddonAzureBackupJobs |
| AdhocOrScheduledJob            | 文本          | 用于指定作业是临时作业还是计划作业的字段           |
| BackupItemUniqueId             | 文本          | 用于标识与存储实体相关的备份项的唯一 ID |
| BackupManagementServerUniqueId | 文本          | 用于标识与存储实体相关的备份管理服务器的唯一 ID |
| BackupManagementType           | 文本          | 执行备份的提供程序类型，例如此作业所属的 IaaSVM、FileFolder |
| DataTransferredInMB            | Number        | 此作业传输的数据量，以 MB 为单位                          |
| JobDurationInSecs              | Number        | 作业的总持续时间，以秒为单位                                |
| JobFailureCode                 | 文本          | 导致作业失败的故障代码字符串    |
| JobOperation                   | 文本          | 为其运行作业的操作，例如备份、还原、配置备份 |
| JobOperationSubType            | 文本          | 作业操作的子类型。 例如，如果是日志备份作业，则为“Log” |
| JobStartDateTime               | DateTime      | 开始运行作业的日期和时间                       |
| JobStatus                      | 文本          | 作业的状态，例如 Completed、Failed   |
| JobUniqueId                    | 文本          | 用于标识作业的唯一 ID                                |
| ProtectedContainerUniqueId     | 文本          | 与作业关联的受保护服务器的唯一标识符 |
| RecoveryJobDestination         | 文本          | 恢复作业的目标，数据将恢复到其中   |
| RecoveryJobRPDateTime          | DateTime      | 创建要恢复的恢复点的日期和时间 |
| RecoveryJobLocation            | 文本          | 要恢复的恢复点的存储位置 |
| RecoveryLocationType           | 文本          | 恢复位置的类型                                |
| schemaVersion                  | 文本          | 架构的当前版本，例如 **V2**            |
| 状态                          | 文本          | 作业对象的当前状态，例如 Active、Deleted |
| VaultUniqueId                  | 文本          | 与作业关联的受保护保管库的唯一标识符 |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

此表提供策略相关字段的详细信息。

| **字段**                       | **数据类型**  | **说明**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | 文本           | 与收集的数据相关的资源的唯一标识符。 例如“恢复服务保管库资源 ID” |
| OperationName                   | 文本           | 操作的名称，例如 Policy 或 PolicyAssociation |
| Category                        | 文本           | 推送到 Azure Monitor 日志的诊断数据的类别 - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | 文本           | 计划备份时的日期（星期几）            |
| BackupFrequency                 | 文本           | 运行备份的频率。 例如 daily、weekly |
| BackupManagementType            | 文本           | 执行备份作业的服务器的提供程序类型。 例如 IaaSVM、FileFolder |
| BackupManagementServerUniqueId  | 文本           | 用于唯一标识保护备份项所用的备份管理服务器的字段（如果适用） |
| BackupTimes                     | 文本           | 计划备份时的日期和时间                     |
| DailyRetentionDuration          | 整数   | 所配置备份的总保留时间（按天算）      |
| DailyRetentionTimes             | 文本           | 配置每日保留时的日期和时间            |
| DiffBackupDaysOfTheWeek         | 文本           | Azure VM 备份中 SQL 差异备份的星期 |
| DiffBackupFormat                | 文本           | Azure VM 备份中 SQL 差异备份的格式   |
| DiffBackupRetentionDuration     | 十进制数 | Azure VM 备份中 SQL 差异备份的保留期 |
| DiffBackupTime                  | 时间           | Azure VM 备份中 SQL 差异备份的时间     |
| LogBackupFrequency              | 十进制数 | SQL 日志备份的频率                            |
| LogBackupRetentionDuration      | 十进制数 | Azure VM 备份中 SQL 日志备份的保留期 |
| MonthlyRetentionDaysOfTheMonth  | 文本           | 配置每月保留时的当月周次。  例如，First，Last |
| MonthlyRetentionDaysOfTheWeek   | 文本           | 选择进行每月保留的日期（星期几）              |
| MonthlyRetentionDuration        | 文本           | 所配置备份的总保留时间（按月算）    |
| MonthlyRetentionFormat          | 文本           | 每月保留的配置类型。 例如基于日期的每日、基于周次的每周 |
| MonthlyRetentionTimes           | 文本           | 配置每月保留时的日期和时间           |
| MonthlyRetentionWeeksOfTheMonth | 文本           | 配置每月保留时的当月周次。   例如，First，Last |
| PolicyName                      | 文本           | 已定义的策略的名称                                   |
| PolicyUniqueId                  | 文本           | 用于标识策略的唯一 ID                             |
| PolicyTimeZone                  | 文本           | 在日志中指定策略时间字段时采用的时区 |
| RetentionDuration               | 文本           | 所配置备份的保留持续时间                    |
| RetentionType                   | 文本           | 保留类型                                            |
| schemaVersion                   | 文本           | 此字段表示架构的当前版本，值为 **V2** |
| 状态                           | 文本           | 策略对象的当前状态。 例如 Active、Deleted |
| SynchronisationFrequencyPerDay  | 整数   | 一天中同步 SC DPM 和 MABS 文件备份的次数 |
| VaultUniqueId                   | 文本           | 此策略所属的保管库的唯一 ID          |
| WeeklyRetentionDaysOfTheWeek    | 文本           | 选择进行每周保留的日期（星期几）               |
| WeeklyRetentionDuration         | 十进制数 | 所配置备份的每周保留总时间（按周算） |
| WeeklyRetentionTimes            | 文本           | 配置每周保留时的日期和时间            |
| YearlyRetentionDaysOfTheMonth   | 文本           | 选择进行每年保留的日期             |
| YearlyRetentionDaysOfTheWeek    | 文本           | 选择进行每年保留的日期（星期几）               |
| YearlyRetentionDuration         | 十进制数 | 所配置备份的总保留时间（按年算）     |
| YearlyRetentionFormat           | 文本           | 每年保留的配置类型，例如基于日期的每日、基于周次的每周 |
| YearlyRetentionMonthsOfTheYear  | 文本           | 选择进行每年保留的月份             |
| YearlyRetentionTimes            | 文本           | 配置每年保留时的日期和时间            |
| YearlyRetentionWeeksOfTheMonth  | 文本           | 选择进行每年保留的月份的周             |
| SourceSystem                    | 文本           | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

此表提供存储相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文本          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | 文本          | 此字段表示当前操作的名称 - Storage 或 StorageAssociation |
| Category                       | 文本          | 此字段表示推送到 Azure Monitor 日志的诊断数据的类别 - AddonAzureBackupStorage |
| BackupItemUniqueId             | 文本          | 用于标识使用 DPM、MABS 备份的 VM 的备份项的唯一 ID |
| BackupManagementServerUniqueId | 文本          | 用于唯一标识保护备份项所用的备份管理服务器的字段（如果适用） |
| BackupManagementType           | 文本          | 执行备份作业的服务器的提供程序类型。 例如 IaaSVM、FileFolder |
| PreferredWorkloadOnVolume      | 文本          | 使用此卷作为首选存储的工作负荷      |
| ProtectedContainerUniqueId     | 文本          | 与备份项关联的受保护容器的唯一标识符 |
| schemaVersion                  | 文本          | 架构的版本。 例如 **V2**                   |
| 状态                          | 文本          | 备份项对象的状态。 例如 Active、Deleted |
| StorageAllocatedInMBs          | Number        | 磁盘类型的相应存储中相应备份项分配的存储大小 |
| StorageConsumedInMBs           | Number        | 相应存储中相应备份项占用的存储大小 |
| StorageName                    | 文本          | 存储实体的名称。 例如 E:\                      |
| StorageTotalSizeInGBs          | 文本          | 存储实体占用的存储总大小 (GB)     |
| StorageType                    | 文本          | 存储类型，例如 Cloud、Volume、Disk             |
| StorageUniqueId                | 文本          | 用于标识存储实体的唯一 ID                |
| VaultUniqueId                  | 文本          | 用于标识与存储实体相关的保管库的唯一 ID |
| VolumeFriendlyName             | 文本          | 存储卷的友好名称                          |
| SourceSystem                   | 文本          | 当前数据的源系统 - Azure                    |

## <a name="valid-operation-names-for-each-table"></a>每个表的有效操作名称

上述表中的每条记录都有一个关联的 **操作名称**。 "操作名称" 描述 (的记录类型，还指示为该记录填充表中的哪些字段) 。 每个表 (类别) 支持一个或多个不同的操作名称。 下面汇总了上述每个表中支持的操作名称。

| **表名称/类别**                   | **支持的操作名称** | **描述**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | 表示一条记录，其中包含给定备份项的所有详细信息，例如 ID、名称、类型等。 |
| CoreAzureBackup | BackupItemAssociation | 表示备份项与其关联的受保护容器 (（如果适用) ）之间的映射。 |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | 表示备份项与其前端大小之间的映射。 |
| CoreAzureBackup | ProtectedContainer | 表示一条记录，其中包含给定受保护容器的所有详细信息，例如 ID、名称、类型等。 |
| CoreAzureBackup | ProtectedContainerAssociation | 表示受保护容器与用于其备份的保管库之间的映射。 |
| CoreAzureBackup | 保管库 | 表示一个记录，其中包含给定保管库的所有详细信息，例如。 ID、名称、标记、位置等。 |
| CoreAzureBackup | RecoveryPoint | 表示一条记录，其中包含给定备份项的最早和最新恢复点。 |
| AddonAzureBackupJobs | 作业 |  表示包含给定作业的所有详细信息的记录。 例如，作业操作、开始时间、状态等。 |
| AddonAzureBackupAlerts | 警报 | 表示包含给定警报的所有详细信息的记录。 例如，警报创建时间、严重性、状态等。  |
| AddonAzureBackupStorage | 存储 | 表示包含给定存储实体的所有详细信息的记录。 例如，存储名称、类型等。 |
| AddonAzureBackupStorage | StorageAssociation | 表示备份项与备份项使用的总云存储之间的映射。 |
| AddonAzureBackupProtectedInstance | ProtectedInstance | 表示包含每个容器或备份项的受保护实例计数的记录。 对于 Azure VM 备份，受保护的实例计数在备份项级别可用，对于其他工作负荷，在受保护的容器级别上提供。 |
| AddonAzureBackupPolicy | 策略 |  表示包含备份和保留策略的所有详细信息的记录。 例如，ID、名称、保留设置等。 |
| AddonAzureBackupPolicy | PolicyAssociation | 表示备份项和应用到备份策略的备份策略之间的映射。 |   

通常，您需要在不同的表中执行联接，以及将不同的记录集作为同一个表的一部分 (按操作名称进行区分) 以获取分析所需的所有字段。 若要开始，请参阅 [示例查询](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries) 。 

## <a name="next-steps"></a>后续步骤

- [了解如何将诊断数据发送到 Log Analytics](./backup-azure-diagnostic-events.md)
- [了解如何针对特定于资源的表编写查询](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)
