---
title: "Azure 备份的 Log Analytics 数据模型"
description: "本文介绍 Azure 备份数据的 Log Analytics 数据模型详细信息。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041a8835a1dd185739b23d4073fd5811bb4490b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure 备份数据的 Log Analytics 数据模型
本文介绍用于将报告数据推送到 Log Analytics 的数据模型。 使用此数据模型可以创建自定义查询和仪表板，并在 OMS 中利用这些对象。 

## <a name="using-azure-backup-data-model"></a>使用 Azure 备份数据模型
可以使用数据模型随附的以下字段，根据要求创建视觉对象、自定义查询和仪表板。

### <a name="alert"></a>警报
此表提供警报相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| AlertUniqueId_s |文本 |生成的警报的唯一 ID |
| AlertType_s |文本 |生成的警报的类型，例如 Backup |
| AlertStatus_s |文本 |警报的状态，例如 Active |
| AlertOccurenceDateTime_s |日期/时间 |警报的创建日期和时间 |
| AlertSeverity_s |文本 |警报的严重性，例如 Critical |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |文本 |此警报所属备份项的唯一 ID |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |警报对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此警报所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - Alert |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |此警报所属保护项的唯一 ID |
| VaultUniqueId_s |文本 |此警报所属保护项的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="backupitem"></a>BackupItem
此表提供备份项相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |文本 |备份项的唯一 ID |
| BackupItemId_s |文本 |备份项 ID |
| BackupItemName_s |文本 |备份项的名称 |
| BackupItemFriendlyName_s |文本 |备份项的友好名称 |
| BackupItemType_s |文本 |备份项的类型，例如 VM、FileFolder |
| ProtectedServerName_s |文本 |备份项所属的受保护服务器的名称 |
| ProtectionState_s |文本 |备份项的当前保护状态，例如 Protected、ProtectionStopped |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |备份项对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此备份项所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - BackupItem |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="backupitemassociation"></a>BackupItemAssociation
此表提供有关备份项与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |  
| BackupItemUniqueId_s |文本 |备份项的唯一 ID |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |备份项关联对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此备份项所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - BackupItemAssociation |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |文本 |用于标识备份项关联到的策略的唯一 ID |
| ProtectedServerUniqueId_s |文本 |此备份项所属的受保护服务器的唯一 ID |
| VaultUniqueId_s |文本 |此备份项所属的保管库的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="job"></a>作业
此表提供作业相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| BackupItemUniqueId_s |文本 |此作业所属备份项的唯一 ID |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |作业对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此作业所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - Job |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |此作业所属保护项的唯一 ID |
| VaultUniqueId_s |文本 |此作业所属保护项的唯一 ID |
| JobOperation_s |文本 |为其运行作业的操作，例如备份、还原、配置备份 |
| JobStatus_s |文本 |作业的状态，例如 Completed、Failed |
| JobFailureCode_s |文本 |导致作业失败的故障代码字符串 |
| JobStartDateTime_s |日期/时间 |开始运行作业的日期和时间 |
| BackupStorageDestination_s |文本 |备份存储目标，例如 Cloud、Disk  |
| JobDurationInSecs_s | Number |作业的总持续时间，以秒为单位 |
| DataTransferredInMB_s | Number |此作业传输的数据量，以 MB 为单位|
| JobUniqueId_g |文本 |用于标识作业的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="policy"></a>策略
此表提供策略相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此策略所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - Policy |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |文本 |用于标识策略的唯一 ID |
| PolicyName_s |文本 |已定义的策略的名称 |
| BackupFrequency_s |文本 |运行备份的频率，例如 daily、weekly |
| BackupTimes_s |文本 |计划备份时的日期和时间 |
| BackupDaysOfTheWeek_s |文本 |计划备份时的日期（星期几） |
| RetentionDuration_s |整数 |所配置备份的保留持续时间 |
| DailyRetentionDuration_s |整数 |所配置备份的总保留时间（按天算） |
| DailyRetentionTimes_s |文本 |配置每日保留时的日期和时间 |
| WeeklyRetentionDuration_s |十进制数 |所配置备份的每周保留总时间（按周算） |
| WeeklyRetentionTimes_s |文本 |配置每周保留时的日期和时间 |
| WeeklyRetentionDaysOfTheWeek_s |文本 |选择进行每周保留的日期（星期几） |
| MonthlyRetentionDuration_s |十进制数 |所配置备份的总保留时间（按月算） |
| MonthlyRetentionTimes_s |文本 |配置每月保留时的日期和时间 |
| MonthlyRetentionFormat_s |文本 |每月保留的配置类型，例如基于日期的每日、基于周次的每周 |
| MonthlyRetentionDaysOfTheWeek_s |文本 |选择进行每月保留的日期（星期几） |
| MonthlyRetentionWeeksOfTheMonth_s |文本 |配置每月保留时的当月周次，例如 First、Last 等 |
| YearlyRetentionDuration_s |十进制数 |所配置备份的总保留时间（按年算） |
| YearlyRetentionTimes_s |文本 |配置每年保留时的日期和时间 |
| YearlyRetentionMonthsOfTheYear_s |文本 |选择进行每年保留的月份 |
| YearlyRetentionFormat_s |文本 |每年保留的配置类型，例如基于日期的每日、基于周次的每周 |
| YearlyRetentionDaysOfTheMonth_s |文本 |选择进行每年保留的日期 |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="policyassociation"></a>PolicyAssociation
此表提供有关策略与各个实体之间的关联的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |策略对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此策略所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - PolicyAssociation |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| PolicyUniqueId_g |文本 |用于标识策略的唯一 ID |
| VaultUniqueId_s |文本 |此策略所属的保管库的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="protectedserver"></a>ProtectedServer
此表提供了受保护服务器相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| ProtectedServerName_s |文本 |受保护的服务器的名称 |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |受保护服务器对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此受保护服务器所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - ProtectedServer |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |受保护服务器的唯一 ID |
| RegisteredContainerId_s |文本 |注册用于备份的容器的 ID |
| ProtectedServerType_s |文本 |备份的受保护服务器的类型，例如 Windows |
| ProtectedServerFriendlyName_s |文本 |受保护的服务器的友好名称 |
| AzureBackupAgentVersion_s |文本 |代理备份版本的版本号 |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
此表提供有关受保护服务器与其他实体之间的关联的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |受保护服务器关联对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此受保护服务器所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - ProtectedServerAssociation |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |受保护服务器的唯一 ID |
| VaultUniqueId_s |文本 |此受保护服务器所属的保管库的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="storage"></a>存储
此表提供存储相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| CloudStorageInBytes_s |十进制数 |备份所用的云备份存储量，基于最新值进行计算 |
| ProtectedInstances_s |十进制数 |用于计算账单中前端存储量的受保护实例的数目，基于最新值进行计算 |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |存储对象的当前状态，例如 Active、Deleted |
| BackupManagementType_s |文本 |执行备份的提供程序类型，例如此存储所属的 IaaSVM、FileFolder |
| OperationName |文本 |此字段表示当前操作的名称 - Storage |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| ProtectedServerUniqueId_s |文本 |为其计算存储的受保护服务器的唯一 ID |
| VaultUniqueId_s |文本 |为其计算存储的保管库的唯一 ID |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

### <a name="vault"></a>保管库
此表提供保管库相关字段的详细信息。

| 字段 | 数据类型 | 说明 |
| --- | --- | --- |
| EventName_s |文本 |此字段表示此事件的名称，始终为 AzureBackupCentralReport |
| SchemaVersion_s |文本 |此字段表示架构的当前版本，值为 **V1** |
| State_s |文本 |保管库对象的当前状态，例如 Active、Deleted |
| OperationName |文本 |此字段表示当前操作的名称 - Vault |
| 类别 |文本 |此字段表示推送到 Log Analytics 的诊断数据的类别，值为 AzureBackupReport |
| 资源 |文本 |这是正在收集其数据的资源，显示恢复服务保管库名称 |
| VaultUniqueId_s |文本 |保管库的唯一 ID |
| VaultName_s |文本 |保管库的名称 |
| AzureDataCenter_s |文本 |保管库所在的数据中心 |
| StorageReplicationType_s |文本 |保管库存储复制的类型，例如 GeoRedundant |
| SourceSystem |文本 |当前数据的源系统 - Azure |
| ResourceId |文本 |此字段表示正在收集其数据的资源 ID，显示恢复服务保管库资源 ID |
| SubscriptionId |文本 |此字段表示正在收集其数据的资源（RS 保管库）的订阅 ID |
| resourceGroup |文本 |此字段表示正在收集其数据的资源（RS 保管库）的资源组 |
| ResourceProvider |文本 |此字段表示正在收集其数据的资源提供程序 - Microsoft.RecoveryServices |
| ResourceType |文本 |此字段表示正在收集其数据的资源的类型 - Vaults |

## <a name="next-steps"></a>后续步骤
查看用于创建 Azure 备份报告的数据模型后，可以开始在 Log Analytics 和 OMS 中[创建仪表板](../log-analytics/log-analytics-dashboards.md)。
