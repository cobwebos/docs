---
title: Azure 备份的数据模型
description: 本文介绍 Azure 备份报表的 Power BI 数据模型详细信息。
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: c6160570644da108ba713e8229b38f9587495c92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337572"
---
# <a name="data-model-for-azure-backup-reports"></a>Azure 备份报表的数据模型
本文介绍用于创建 Azure 备份报表的 Power BI 数据模型。 使用此数据模型，可基于相关字段筛选现有报表，更重要是，还可使用模型中表格和字段创建自己的报表。 

## <a name="creating-new-reports-in-power-bi"></a>在 Power BI 中创建新报表
Power BI 提供了自定义功能，可用于[通过数据模型创建报表](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)。

## <a name="using-azure-backup-data-model"></a>使用 Azure 备份数据模型
可使用下面提供的字段作为数据模型的一部分，创建报表和自定义现有报表。

### <a name="alert"></a>警报
此表提供针对各种警报相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #AlertsCreatedInPeriod |整数 |在选定时间段内创建的警报数 |
| %ActiveAlertsCreatedInPeriod |百分比 |所选时间段内活动警报的百分比 |
| %CriticalAlertsCreatedInPeriod |百分比 |所选时间段内严重警报的百分比 |
| AlertOccurrenceDate |Date |警报的创建日期 |
| AlertSeverity |Text |警报的严重性，例如“严重” |
| AlertStatus |Text |警报的状态，例如“活动” |
| AlertType |Text |生成的警报的类型，例如“备份” |
| AlertUniqueId |Text |生成的警报的唯一 ID |
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |十进制数 |用于解析选定时间段内警报的平均时间（以分钟计） |
| EntityState |Text |警报对象的当前状态，例如“活动”、“已删除” |

### <a name="backup-item"></a>备份项
此表提供针对各种备份项相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #BackupItems |整数 |备份项的数量 |
| #UnprotectedBackupItems |整数 |停止以进行保护或配置进行备份但未启动备份的备份项的数量|
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| BackupItemFriendlyName |Text |备份项的友好名称 |
| BackupItemId |Text |备份项 ID |
| BackupItemName |Text |备份项的名称 |
| BackupItemType |Text |备份项的类型，例如 VM、FileFolder |
| EntityState |Text |备份项对象的当前状态，例如“活动”、“已删除” |
| LastBackupDateTime |Date/Time |所选备份项的上次备份时间 |
| LastBackupState |Text |所选备份项的上次备份状态，例如“成功”、“失败” |
| LastSuccessfulBackupDateTime |Date/Time |所选备份项上次成功备份的时间 |
| ProtectionState |Text |备份项的当前保护状态，例如“受保护”、“保护停止” |

### <a name="calendar"></a>日历
此表提供了日历相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| Date |Date |用于筛选数据的日期 |
| 日期键 |Text |每个日期项的唯一键 |
| DayDiff |十进制数 |用于筛选数据的天数差别，例如 0 表示当天的数据，-1 表示前一天的数据，0 和 -1 表示当天和前一天的数据  |
| 月份 |Text |用于筛选数据的月份，月份以第一天开始，在第 31 天结束 |
| MonthDate | Date |月份中当月结束的日期，用于筛选数据 |
| MonthDiff |十进制数 |用于筛选数据的月份差别，例如 0 表示本月的数据，-1 表示上个月的数据，0 和 -1 表示本月和上个月的数据 |
| 周次 |Text |用于筛选数据的周次，每周从星期天开始，星期六结束 |
| WeekDate |Date |一周结束的日期，用于筛选数据 |
| WeekDiff |十进制数 |用于筛选数据的周次差别，例如 0 表示本周的数据，-1 表示上一周的数据，0 和 -1 表示本周和上一周的数据 |
| 年份 |Text |用于筛选数据的日历年 |
| YearDate |Date |一年结束的日期，用于筛选数据 |

### <a name="job"></a>作业
此表提供针对各种作业相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #JobsCreatedInPeriod |整数 |在选定时间段内创建的作业数 |
| %FailuresForJobsCreatedInPeriod |百分比 |所选时间段内作业失败总数的百分比 |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |十进制数 |所选时间段内为创建的“备用”作业转换的数据的第 80 百分位值（以 MB 计）  |
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |十进制数 |所选时间段内创建的“已完成备份”作业所用的平均时间（以分钟计）  |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |十进制数 |所选时间段内创建的“已完成还原”作业所用的平均时间（以分钟计）  |
| BackupStorageDestination |Text |备份存储目标，例如云、磁盘  |
| EntityState |Text |作业对象的当前状态，例如“活动”、“已删除” |
| JobFailureCode |Text |导致作业失败的故障代码字符串 |
| JobOperation |Text |为其运行作业的操作，例如备份、还原、配置备份 |
| JobStartDate |Date |作业开始运行的日期 |
| JobStartTime |Time |作业开始运行的时间 |
| JobStatus |Text |作业的状态，例如“已完成”、“失败” |
| JobUniqueId |Text |用于标识作业的唯一 ID |

### <a name="policy"></a>策略
此表提供了针对多个策略相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #Policies |整数 |系统中存在的备份策略数目 |
| #PoliciesInUse |整数 |当前正用于配置备份的策略数目 |
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| BackupDaysOfTheWeek |Text |计划备份时的日期（星期几） |
| BackupFrequency |Text |备份的频率，例如每日、每周 |
| BackupTimes |Text |计划备份时的日期和时间 |
| DailyRetentionDuration |整数 |所配置备份的总保留时间（按天算） |
| DailyRetentionTimes |Text |配置每日保留时的日期和时间 |
| EntityState |Text |策略对象的当前状态，例如“活动”、“已删除” |
| MonthlyRetentionDaysOfTheMonth |Text |选择进行每月保留的月份日期 |
| MonthlyRetentionDaysOfTheWeek |Text |选择进行每月保留的日期（星期几） |
| MonthlyRetentionDuration |十进制数 |所配置备份的总保留时间（按月算） |
| MonthlyRetentionFormat |Text |每月保留的配置类型，例如基于日期的每日、基于周次的每周 |
| MonthlyRetentionTimes |Text |配置每月保留时的日期和时间 |
| MonthlyRetentionWeeksOfTheMonth |Text |配置每月保留时的当月时间（第几周），例如第一周、最后一周等 |
| PolicyName |Text |已定义的策略的名称 |
| PolicyUniqueId |Text |用于标识策略的唯一 ID |
| RetentionType |Text |保留策略的类型，例如每日、每周、每月、每年 |
| WeeklyRetentionDaysOfTheWeek |Text |选择进行每周保留的日期（星期几） |
| WeeklyRetentionDuration |十进制数 |所配置备份的每周保留总时间（按周算） |
| WeeklyRetentionTimes |Text |配置每周保留时的日期和时间 |
| YearlyRetentionDaysOfTheMonth |Text |选择进行每年保留的日期 |
| YearlyRetentionDaysOfTheWeek |Text |选择进行每年保留的日期（星期几） |
| YearlyRetentionDuration |十进制数 |所配置备份的总保留时间（按年算） |
| YearlyRetentionFormat |Text |每年保留的配置类型，例如基于日期的每日、基于周次的每周 |
| YearlyRetentionMonthsOfTheYear |Text |选择进行每年保留的月份 |
| YearlyRetentionTimes |Text |配置每年保留时的日期和时间 |
| YearlyRetentionWeeksOfTheMonth |Text |配置每年保留时的当月时间（第几周），例如第一周、最后一周等 |

### <a name="protected-server"></a>受保护的服务器
此表提供针对多个受保护服务器相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #ProtectedServers |整数 |受保护服务器的数目 |
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| AzureBackupAgentOSType |Text |Azure 备份代理的 OS 类型 |
| AzureBackupAgentOSVersion |Text |Azure 备份代理的 OS 版本 |
| AzureBackupAgentUpdateDate |Text |备份代理更新的日期 |
| AzureBackupAgentVersion |Text |代理备份版本的版本号 |
| BackupManagementType |Text |执行备份的提供程序类型，例如 IaaSVM、FileFolder |
| EntityState |Text |受保护的服务器对象的当前状态，例如“活动”、“已删除” |
| ProtectedServerFriendlyName |Text |受保护的服务器的友好名称 |
| ProtectedServerName |Text |受保护的服务器的名称 |
| ProtectedServerType |Text |备份的受保护服务器的类型，例如 IaaSVMContainer |
| ProtectedServerName |Text |备份项所属的受保护服务器的名称 |
| RegisteredContainerId |Text |注册用于备份的容器的 ID |

### <a name="storage"></a>存储
此表提供针对各种存储相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #ProtectedInstances |十进制数 |用于计算账单中前端存储量的受保护实例的数目，基于所选时间中的最新值进行计算 |
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| CloudStorageInMB |十进制数 |备份所用的云备份存储量，基于所选时间中的最新值进行计算 |
| EntityState |Text |对象的当前状态，例如“活动”、“已删除” |
| LastUpdatedDate |Date |所选行的上次更新时间 |

### <a name="time"></a>Time
此表提供时间相关字段的详细信息。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| 小时 |Time |当天的小时，例如 1:00:00 PM |
| 小时数 |十进制数 |当天的具体小时，例如 13.00 |
| 分钟 |十进制数 |一小时中所处分钟 |
| PeriodOfTheDay |Text |当天所处时间段，例如凌晨 12-3 |
| Time |Time |当天的时间，例如 12:00:01 AM |
| TimeKey |Text |表示时间的键值 |

### <a name="vault"></a>保管库
此表提供针对各种保管库相关字段的基本字段和聚合。

| 字段 | 数据类型 | 描述 |
| --- | --- | --- |
| #Vaults |整数 |保管库的数目 |
| AsOnDateTime |Date/Time |所选行的最近刷新时间 |
| AzureDataCenter |Text |保管库所在的数据中心 |
| EntityState |Text |保管库对象的当前状态，例如“活动”、“已删除” |
| StorageReplicationType |Text |保管库存储复制的类型，例如 GeoRedundant |
| SubscriptionId |Text |被选中生成报表的客户的订阅 ID |
| VaultName |Text |保管库的名称 |
| VaultTags |Text |与保管库关联的标记 |

## <a name="next-steps"></a>后续步骤
查看用于创建 Azure 备份报表的数据模型之后，请参阅以下文章，更详细地了解如何在 Power BI 中创建和查看报表。

* [在 Power BI 中创建报表](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [在 Power BI 中筛选报表](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
