---
title: 使用 Azure Monitor 监视 Azure 备份
description: 使用 Azure Monitor 监视 Azure 备份工作负载并创建自定义警报。
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 81e4f9f63df19ed57f26be8eb246c6dab1bf512c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714825"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure Monitor 进行大规模监视

Azure 备份在恢复服务保管库中提供[内置的监视和警报功能](backup-azure-monitoring-built-in-monitor.md)。 无需任何附加的管理基础结构即可使用这些功能。 但该内置服务在以下方案中会受到限制：

- 如果在多个订阅中监视多个恢复服务保管库中的数据
- 如果首选通知通道不是电子邮件
- 如果用户需要更多方案的警报
- 如果要在 Azure 中查看本地组件（如 System Center Data Protection Manager）中的信息，而该门户在[备份作业](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或[备份警报](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)中不显示该信息

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作区

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 创建警报

在 Azure Monitor 中，你可以在 Log Analytics 工作区中创建自己的警报。 在工作区中，可使用 Azure 操作组选择首选的通知机制。

> [!IMPORTANT]
> 有关创建此查询的成本的信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

打开 Log Analytics 工作区的“日志”部分，并为自己的日志创建查询。 选择“新建预警规则”后，会打开 Azure Monitor 警报创建页，如下图所示。

![在 Log Analytics 工作区中创建警报](media/backup-azure-monitoring-laworkspace/custom-alert.png)

此处的资源已标记为 Log Analytics 工作区，并提供了操作组集成。

![Log Analytics 警报创建页](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警报条件

警报的定义特征是其触发条件。 选择“条件”，以在“日志”页自动加载 Kusto 查询，如下图所示。 可以在此处编辑条件以满足你的需求。 有关详细信息，请参阅 [Kusto 查询示例](#sample-kusto-queries)。

![设置警报条件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如有必要，可以编辑 Kusto 查询。 选择阈值、时间范围和频率。 阈值确定引发警报的时间。 时间范围是运行查询的时间段。 例如，如果阈值大于 0，时间范围为 5 分钟，且频率为 5 分钟，则该规则每 5 分钟运行一次查询，并检查前 5 分钟。 如果结果数大于 0，将通过所选操作组通知你。

> [!NOTE]
> 若要为在给定日期创建的所有事件/日志每天运行一次预警规则，请将“时间范围”和“频率”值都更改为 1440（即 24小时）。

#### <a name="alert-action-groups"></a>警报操作组

使用操作组指定通知通道。 若要查看可用的通知机制，请在“操作组”下选择“新建”。

![“添加操作组”窗口中的可用通知机制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

可以仅通过 Log Analytics 中满足所有警报和监视要求，也可使用 Log Analytics 来补充内置通知。

有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)和[在 Azure 门户中创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

### <a name="sample-kusto-queries"></a>Kusto 查询示例

默认关系图为你提供了在基本方案中可生成警报的 Kusto 查询。 此外，你还可以修改查询，以获取要向其发出警报的数据。 在“日志”页中粘贴以下 Kusto 查询示例，然后在查询上创建警报：

- 所有成功的备份作业

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- 所有失败的备份作业

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- 所有成功的 Azure VM 备份作业

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 所有成功的 SQL 日志备份作业

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 所有成功的 Azure 备份代理作业

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 每个备份项使用的备份存储

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>诊断数据更新频率

保管库中的诊断数据会被抽取到 Log Analytics 工作区，但会有一些延迟。 每个事件会在从恢复服务保管库推送后的 20到 30 分钟到达 Log Analytics 工作区。 下面是有关延迟的更多详细信息：

- 在所有解决方案中，备份服务的内置警报会在其创建后立即推送。 因此，它们通常会在 20 到 30 分钟后出现在 Log Analytics 工作区中。
- 在所有解决方案中，按需备份作业和还原作业会在其完成后立即推送。
- 对于除 SQL 备份以外的所有解决方案，计划的备份作业会在其完成后立即推送。
- 对于 SQL 备份，由于日志备份每 15 分钟执行一次，所有已完成的计划备份作业（包括日志）的信息每 6 小时进行一次批量推送。
- 在所有解决方案中，其他信息（如备份项、策略、恢复点、存储等）至少每日推送一次。
- 备份配置中的更改（如更改策略或编辑策略）会触发所有相关备份信息的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用恢复服务保管库的活动日志

> [!CAUTION]
> 以下步骤仅适用于 Azure VM 备份。 你不能对 Azure 备份代理、Azure 中的 SQL 备份或 Azure 文件存储等解决方案使用这些步骤。

此外，还可以使用活动日志来获取事件通知（如备份成功）。 请按照下列步骤开始：

1. 登录 Azure 门户。
2. 打开相关的恢复服务保管库。
3. 在保管库的属性中，打开“活动日志”部分。

若要标识相应日志并创建警报：

1. 应用下图所示的筛选器，验证是否接收到成功备份的活动日志。 根据需要更改 Timespan 值以查看记录。

   ![进行筛选以查找 Azure VM 备份的活动日志](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. 选择操作名称以查看相关详细信息。
3. 选择“新建预警规则”，以打开“创建规则”页。
4. 按照[使用 Azure Monitor 创建、查看和管理活动日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)中的步骤创建警报。

   ![新建警报规则](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

此处的资源是恢复服务保管库本身。 对于要通过活动日志通知的所有保管库，请重复相同的步骤。 此条件没有阈值、时间范围或频率，因为此警报是基于事件触发的。 一旦生成相关的活动日志，就会引发警报。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 进行大规模监视

你可以在 Azure Monitor 中查看从活动日志和 Log Analytics 工作区创建的所有警报。 只需打开左侧的“警报”窗格。

尽管可以通过活动日志获取通知，但我们强烈建议使用 Log Analytics 代替活动日志来进行大规模监视。 原因如下：

- 方案受限：通过活动日志通知仅适用于 Azure VM 备份。 必须为每个恢复服务保管库设置通知。
- 定义适用范围：计划的备份活动不适用于活动日志的最新定义。 相反，它适用于[资源日志](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace)。 当通过活动日志通道传输的数据发生更改时，这种符合性会导致意外结果。
- 活动日志通道问题：在恢复服务保管库中，从 Azure 备份抽取的活动日志将遵循新模型。 遗憾的是，这一更改会影响 Azure 政府、Azure 德国和 Azure 中国世纪互联中的活动日志的生成。 如果使用这些云服务的用户在 Azure Monitor 的活动日志中创建或配置任何警报，则不会触发这些警报。 此外，在所有 Azure 公共区域中，如果用户[将恢复服务活动日志收集到 Log Analytics 工作区中](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)，那么这些日志不会显示。

使用 Log Analytics 工作区对受 Azure 备份保护的所有工作负载进行大规模监视和警报。

## <a name="next-steps"></a>后续步骤

若要创建自定义查询，请参阅 [Log Analytics 数据模型](backup-azure-reports-data-model.md)。
