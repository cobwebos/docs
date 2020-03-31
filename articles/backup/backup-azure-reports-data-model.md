---
title: Azure 备份诊断事件的数据模型
description: 此数据模型参考了将诊断事件发送到日志分析 （LA） 的资源特定模式。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583378"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure 备份诊断事件的数据模型

## <a name="coreazurebackup"></a>CoreAzureBackup

此表提供有关核心备份实体（如保管库和备份项）的信息。

| **字段**                         | **数据类型** | **说明**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID。 |
| OperationName                     | Text          | 此字段表示当前操作的名称 - 备份项目、备份项目关联或受保护容器。 |
| 类别                          | Text          | 此字段表示推送到 Azure 监视器日志的诊断数据类别。 例如，核心 Azure 备份。 |
| 代理版本                      | Text          | 代理备份或保护代理的版本号（在 SC DPM 和 MABS 的情况下） |
| AzureBackupAgentVersion           | Text          | 备份管理服务器上的 Azure 备份代理版本 |
| AzureDataCenter                   | Text          | 保管库所在的数据中心                       |
| 备份项目应用版本              | Text          | 备份项的应用程序版本                       |
| BackupItemFriendlyName            | Text          | 备份项的友好名称                             |
| BackupItemName                    | Text          | 备份项的名称                                      |
| 备份项目保护状态         | Text          | 备份项的保护状态                          |
| 备份项目前端大小            | Text          | 备份项的前端大小                            |
| BackupItemType                    | Text          | 备份项的类型。 例如：VM、文件文件夹             |
| 备份项目唯一 ID                | Text          | 备份项的唯一标识符                         |
| 备份管理服务器类型        | Text          | 备份管理服务器的类型，如 MABS、SC DPM     |
| 备份管理服务器唯一 ID    | Text          | 用于唯一标识备份管理服务器的字段      |
| BackupManagementType              | Text          | 服务器执行备份作业的提供程序类型。 例如 IaaSVM、FileFolder |
| 备份管理服务器名称        | Text          | 备份管理服务器的名称                         |
| 备份管理服务器操作系统版本   | Text          | 备份管理服务器的 OS 版本                   |
| 备份管理服务器版本     | Text          | 备份管理服务器的版本                      |
| 最新恢复点位置       | Text          | 备份项的最新恢复点的位置    |
| 最新恢复点时间           | DateTime      | 备份项的最新恢复点的日期时间   |
| 最老的恢复点位置       | Text          | 备份项的最旧恢复点的位置    |
| 最老的恢复点时间           | DateTime      | 备份项的最新恢复点的日期时间   |
| PolicyUniqueId                    | Text          | 用于标识策略的唯一 ID                             |
| 受保护的容器友好名称    | Text          | 受保护服务器的友好名称                        |
| 受保护的容器位置        | Text          | 受保护容器是位于本地还是位于 Azure 中 |
| 受保护的容器名称            | Text          | 受保护容器的名称                            |
| 受保护容器类型          | Text          | 受保护容器的操作系统类型                          |
| 受保护容器操作系统版本       | Text          | 受保护容器的 OS 版本                        |
| 受保护的容器保护状态 | Text          | 受保护容器的保护状态                  |
| 受保护容器类型            | Text          | 受保护容器是服务器还是容器  |
| 受保护的容器唯一 ID        | Text          | 用于标识受保护容器（除使用 DPM、MABS 备份的 VM 以外的所有内容）的唯一 ID |
| 受保护的容器工作负载类型    | Text          | 已备份的受保护容器的类型。 例如 IaaSVMContainer |
| ProtectionGroupName               | Text          | 备份项目受保护组的名称，适用于 SC DPM 和 MABS（如果适用） |
| ResourceGroupName                 | Text          | 要收集的数据的资源组（例如，恢复服务保管库） |
| schemaVersion                     | Text          | 此字段表示架构的当前版本，它是**V2** |
| 辅助备份保护状态    | Text          | 是否为备份项启用了辅助保护  |
| 状态                             | Text          | 备份项对象的状态。 例如 Active、Deleted |
| StorageReplicationType            | Text          | 保管库的存储复制类型。 例如 GeoRedundant |
| SubscriptionId                    | Text          | 为其收集数据的资源（例如，恢复服务保管库）的订阅标识符 |
| VaultName                         | Text          | 保管库的名称                                            |
| VaultTags                         | Text          | 与保管库资源关联的标记                    |
| 库库唯一Id                     | Text          | 保管库的唯一标识符                             |
| SourceSystem                      | Text          | 当前数据的源系统 - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

此表提供警报相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 与收集的数据相关的资源的唯一标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | Text          | 当前操作的名称。 例如，警报            |
| 类别                       | Text          | 推送到 Azure 监视器日志的诊断数据类别 - 附加 Azure 备份警报 |
| 警报代码                      | Text          | 唯一标识警报类型的代码                     |
| 警报整合状态       | Text          | 确定警报是否为合并的警报         |
| 警报发生日期时间        | DateTime      | 创建警报的日期和时间                     |
| 警报提示                  | Text          | 对其引发警报的实体的类型                        |
| AlertSeverity                  | Text          | 警报的严重性。 例如 Critical                 |
| AlertStatus                    | Text          | 警报的状态。 例如 Active                     |
| 警报时间到解决分钟数    | Number        | 解决某个警报所花费的时间。 对于活动警报为空白。     |
| AlertType                      | Text          | 警报类型。 例如 Backup                           |
| AlertUniqueId                  | Text          | 生成的警报的唯一标识符                    |
| 备份项目唯一 ID             | Text          | 与警报关联的备份项的唯一标识符 |
| 备份管理服务器唯一 ID | Text          | 字段，以唯一标识备份项目通过保护的备份管理服务器（如果适用） |
| BackupManagementType           | Text          | 服务器执行备份作业的提供程序类型，例如 IaaSVM、文件文件夹 |
| 警报计数合并      | Number        | 合并的警报数目（如果为合并的警报）  |
| 受保护的容器唯一 ID     | Text          | 与警报关联的受保护服务器的唯一标识符 |
| 推荐行动              | Text          | 建议用于解决警报的操作                      |
| schemaVersion                  | Text          | 架构的当前版本，例如 **V2**            |
| 状态                          | Text          | 警报对象的当前状态，例如 Active、Deleted |
| 存储唯一 ID                | Text          | 用于标识存储实体的唯一 ID                |
| 库库唯一Id                  | Text          | 用于标识与警报相关的保管库的唯一 ID    |
| SourceSystem                   | Text          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

下表提供了与受保护实例相关的基本字段。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 收集数据的资源的唯一标识符。 例如“恢复服务保管库资源 ID” |
| OperationName                  | Text          | 操作的名称，例如受保护实例         |
| 类别                       | Text          | 推送到 Azure 监视器日志的诊断数据类别 - AddonAzure 备份保护实例 |
| 备份项目唯一 ID             | Text          | 备份项的唯一 ID                                 |
| 备份管理服务器唯一 ID | Text          | 字段，以唯一标识备份项目通过保护的备份管理服务器（如果适用） |
| BackupManagementType           | Text          | 服务器执行备份作业的提供程序类型，例如 IaaSVM、文件文件夹 |
| 受保护的容器唯一 ID     | Text          | 标识作业上运行的受保护容器的唯一 ID |
| 受保护实例计数         | Text          | 该日期时间关联备份项或受保护容器的受保护实例计数 |
| schemaVersion                  | Text          | 架构的当前版本，例如 **V2**            |
| 状态                          | Text          | 备份项对象的状态，例如"活动"、已删除 |
| 库库唯一Id                  | Text          | 与受保护实例关联的受保护保管库的唯一标识符 |
| SourceSystem                   | Text          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

此表提供作业相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | Text          | 此字段表示当前操作的名称 - Job    |
| 类别                       | Text          | 此字段表示推送到 Azure 监视器日志的诊断数据类别 - AddonAzure 备份作业 |
| 临时计划作业            | Text          | 用于指定作业是临时作业还是计划作业的字段           |
| 备份项目唯一 ID             | Text          | 用于标识与存储实体相关的备份项的唯一 ID |
| 备份管理服务器唯一 ID | Text          | 用于标识与存储实体相关的备份管理服务器的唯一 ID |
| BackupManagementType           | Text          | 用于执行备份的提供程序类型，例如，此警报所属的 IaaSVM、文件文件夹 |
| 数据传输INMB            | Number        | 此作业传输的数据量，以 MB 为单位                          |
| 作业持续时间              | Number        | 作业的总持续时间，以秒为单位                                |
| JobFailureCode                 | Text          | 导致作业失败的故障代码字符串    |
| JobOperation                   | Text          | 运行作业的操作，例如，备份、还原、配置备份 |
| 作业操作子类型            | Text          | 作业操作的子类型。 例如，在日志备份作业中，"日志" |
| 作业开始日期时间               | DateTime      | 开始运行作业的日期和时间                       |
| JobStatus                      | Text          | 作业的状态，例如 Completed、Failed   |
| JobUniqueId                    | Text          | 用于标识作业的唯一 ID                                |
| 受保护的容器唯一 ID     | Text          | 与警报关联的受保护服务器的唯一标识符 |
| 恢复作业目标         | Text          | 恢复作业的目标，数据将恢复到其中   |
| 恢复作业时间          | DateTime      | 创建正在恢复的恢复点的日期、时间 |
| 恢复作业位置            | Text          | 正在恢复的恢复点存储的位置 |
| 恢复位置类型           | Text          | 恢复位置的类型                                |
| schemaVersion                  | Text          | 架构的当前版本，例如 **V2**            |
| 状态                          | Text          | 警报对象的当前状态，例如"活动"、已删除 |
| 库库唯一Id                  | Text          | 与警报关联的受保护保管库的唯一标识符 |
| SourceSystem                   | Text          | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

此表提供策略相关字段的详细信息。

| **字段**                       | **数据类型**  | **说明**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | 收集数据的资源的唯一标识符。 例如“恢复服务保管库资源 ID” |
| OperationName                   | Text           | 操作的名称，例如策略或策略关联 |
| 类别                        | Text           | 推送到 Azure 监视器日志的诊断数据类别 - 附加 Azure 备份策略 |
| BackupDaysOfTheWeek             | Text           | 计划备份时的日期（星期几）            |
| BackupFrequency                 | Text           | 运行备份的频率。 例如，每日、每周 |
| BackupManagementType            | Text           | 服务器执行备份作业的提供程序类型。 例如，IaaSVM，文件文件夹 |
| 备份管理服务器唯一 ID  | Text           | 字段，以唯一标识备份项目通过保护的备份管理服务器（如果适用） |
| BackupTimes                     | Text           | 计划备份时的日期和时间                     |
| DailyRetentionDuration          | 整数   | 所配置备份的总保留时间（按天算）      |
| DailyRetentionTimes             | Text           | 配置每日保留时的日期和时间            |
| 本周的"差异备份日"         | Text           | Azure VM 备份中 SQL 差异备份的一周天数 |
| DiffBackup格式                | Text           | Azure VM 备份中 SQL 差异备份的格式   |
| 差异备份保留时间     | 小数 | Azure VM 备份中 SQL 差异备份的保留持续时间 |
| 差异备份时间                  | 时间           | Azure VM 备份中 SQL 差异备份的时间     |
| 日志备份频率              | 小数 | SQL 日志备份的频率                            |
| 日志备份保留持续时间      | 小数 | Azure VM 备份中 SQL 的日志备份保留期 |
| MonthlyRetentionDaysOfTheMonth  | Text           | 配置每月保留时的当月周次。  例如，第一、最后等。 |
| MonthlyRetentionDaysOfTheWeek   | Text           | 选择进行每月保留的日期（星期几）              |
| MonthlyRetentionDuration        | Text           | 所配置备份的总保留时间（按月算）    |
| MonthlyRetentionFormat          | Text           | 每月保留的配置类型。 例如，每天基于一天，每周基于一周 |
| MonthlyRetentionTimes           | Text           | 配置每月保留时的日期和时间           |
| MonthlyRetentionWeeksOfTheMonth | Text           | 配置每月保留时的当月周次。   例如，第一、最后等。 |
| PolicyName                      | Text           | 已定义的策略的名称                                   |
| PolicyUniqueId                  | Text           | 用于标识策略的唯一 ID                             |
| 策略时区                  | Text           | 在日志中指定策略时间字段的时区 |
| 保留期               | Text           | 所配置备份的保留持续时间                    |
| RetentionType                   | Text           | 保留类型                                            |
| schemaVersion                   | Text           | 此字段表示架构的当前版本，它是**V2** |
| 状态                           | Text           | 策略对象的当前状态。 例如，活动，已删除 |
| 同步频率  | 整数   | 为 SC DPM 和 MABS 同步文件备份的一天次数 |
| 库库唯一Id                   | Text           | 此策略所属的保管库的唯一 ID          |
| WeeklyRetentionDaysOfTheWeek    | Text           | 选择进行每周保留的日期（星期几）               |
| WeeklyRetentionDuration         | 小数 | 配置备份的每周总保留时间（周数） |
| WeeklyRetentionTimes            | Text           | 配置每周保留时的日期和时间            |
| YearlyRetentionDaysOfTheMonth   | Text           | 选择进行每年保留的日期             |
| YearlyRetentionDaysOfTheWeek    | Text           | 选择进行每年保留的日期（星期几）               |
| YearlyRetentionDuration         | 小数 | 所配置备份的总保留时间（按年算）     |
| YearlyRetentionFormat           | Text           | 年度保留的配置类型，例如，基于一天的每日配置类型，每周基于周的配置类型 |
| YearlyRetentionMonthsOfTheYear  | Text           | 选择进行每年保留的月份             |
| YearlyRetentionTimes            | Text           | 配置每年保留时的日期和时间            |
| YearlyRetentionWeeksOfTheMonth  | Text           | 为年度保留选择的月份数             |
| SourceSystem                    | Text           | 当前数据的源系统 - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

此表提供存储相关字段的详细信息。

| **字段**                      | **数据类型** | **说明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 正在收集其数据的资源标识符。 例如，恢复服务保管库资源 ID |
| OperationName                  | Text          | 此字段表示当前操作的名称 - 存储或存储关联 |
| 类别                       | Text          | 此字段表示推送到 Azure 监视器日志 - AddonAzure 备份存储的诊断数据类别 |
| 备份项目唯一 ID             | Text          | 使用 DPM MABS 标识备份的 VM 备份项目的唯一 ID |
| 备份管理服务器唯一 ID | Text          | 字段，以唯一标识备份项目通过保护的备份管理服务器（如果适用） |
| BackupManagementType           | Text          | 服务器执行备份作业的提供程序类型。 例如，IaaSVM，文件文件夹 |
| PreferredWorkloadOnVolume      | Text          | 此卷是首选存储的工作负载      |
| 受保护的容器唯一 ID     | Text          | 与警报关联的受保护服务器的唯一标识符 |
| schemaVersion                  | Text          | 架构的版本。 例如 **，V2**                   |
| 状态                          | Text          | 备份项对象的状态。 例如，活动，已删除 |
| 存储分配在MB中          | Number        | 在类型磁盘的相应存储中由相应备份项分配的存储大小 |
| 存储在MB中消耗           | Number        | 相应存储中相应备份项消耗的存储大小 |
| StorageName                    | Text          | 存储实体的名称。 例如，E：*                      |
| 存储总尺寸          | Text          | 存储实体占用的存储总大小 (GB)     |
| StorageType                    | Text          | 存储类型，例如 Cloud、Volume、Disk             |
| 存储唯一 ID                | Text          | 用于标识存储实体的唯一 ID                |
| 库库唯一Id                  | Text          | 用于标识与存储实体相关的保管库的唯一 ID |
| 卷友好名称             | Text          | 存储卷的友好名称                          |
| SourceSystem                   | Text          | 当前数据的源系统 - Azure                    |

## <a name="next-steps"></a>后续步骤

- [了解如何将诊断数据发送到日志分析](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [了解如何在资源特定表上写入查询](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)