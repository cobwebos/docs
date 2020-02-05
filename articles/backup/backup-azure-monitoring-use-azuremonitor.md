---
title: 利用 Azure Monitor 监视 Azure 备份
description: 监视 Azure 备份工作负荷，并使用 Azure Monitor 创建自定义警报。
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 4ff51080d675c53e53397a070c1f6f1766aa9e85
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989580"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure Monitor 进行大规模监视

Azure 备份在恢复服务保管库中提供[内置的监视和警报功能](backup-azure-monitoring-built-in-monitor.md)。 无需任何附加的管理基础结构即可使用这些功能。 但在以下方案中，此内置服务将受到限制：

- 如果在多个订阅中监视多个恢复服务保管库中的数据
- 如果首选的通知通道*不*是电子邮件
- 如果用户需要有关更多方案的警报
- 如果要在 Azure 中查看本地组件（如 System Center Data Protection Manager）中的信息，而该门户不会在[**备份作业**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或[**备份警报**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)中显示该组件

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作区

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 创建警报

在 Azure Monitor 中，你可以在 Log Analytics 工作区中创建自己的警报。 在工作区中，使用*Azure 操作组*选择首选的通知机制。

> [!IMPORTANT]
> 有关创建此查询的成本的信息，请参阅[Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

选择任意图表以打开 "Log Analytics" 工作区的 "**日志**" 部分。 在 "**日志**" 部分中，编辑查询并对其创建警报。

![在 Log Analytics 工作区中创建警报](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

选择 "**新建警报规则**" 时，将打开 "Azure Monitor 警报创建" 页，如下图所示。 此处的资源已标记为 Log Analytics 工作区，并且提供了操作组集成。

![Log Analytics 警报-创建页](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警报条件

警报的定义特征是其触发条件。 选择 "**条件**"，以在 "**日志**" 页上自动加载 Kusto 查询，如下图所示。 你可以在此处编辑条件以满足你的需求。 有关详细信息，请参阅[Sample Kusto 查询](#sample-kusto-queries)。

![设置警报条件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如有必要，您可以编辑 Kusto 查询。 选择阈值、时间和频率。 阈值确定何时将发出警报。 句点是运行查询的时间窗口。 例如，如果阈值大于0，则周期为5分钟，频率为5分钟，则该规则每5分钟运行一次查询，并检查前5分钟。 如果结果数大于0，将通过所选操作组通知您。

#### <a name="alert-action-groups"></a>警报操作组

使用操作组来指定通知通道。 若要查看可用的通知机制，请在 "**操作组**" 下选择 "**新建**"。

!["添加操作组" 窗口中的可用通知机制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

你可以仅满足 Log Analytics 中的所有警报和监视要求，或者可以使用 Log Analytics 来补充内置通知。

有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)，以及[在 Azure 门户中创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

默认关系图为您提供了 Kusto 查询，用于生成警报的基本方案。 您还可以修改查询以获取您要向其发出警报的数据。 将以下示例 Kusto 查询粘贴到 "**日志**" 页，然后在查询上创建警报：

- 所有成功的备份作业

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
| where JobStatus=="Completed"
    ````

- 所有失败的备份作业

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
| where JobStatus=="Failed"
    ````

- 所有成功的 Azure VM 备份作业

    ````Kusto
    AddonAzureBackupJobs
| where JobOperation=="Backup"
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

### <a name="diagnostic-data-update-frequency"></a>诊断数据更新频率

保管库中的诊断数据会抽取到 Log Analytics 工作区，但有一些延迟。 每个事件从恢复服务保管库推送到 Log Analytics 工作区*20 到30分钟*。 下面是有关延迟的更多详细信息：

- 在所有解决方案中，备份服务的内置警报会在创建后立即推送。 因此，它们通常会在20到30分钟后显示在 Log Analytics 的工作区中。
- 在所有解决方案中，按需备份作业和还原作业将在*完成*后立即推送。
- 对于除 SQL 备份之外的所有解决方案，计划的备份作业在*完成*后立即推送。
- 对于 SQL 备份，因为日志备份每15分钟发生一次，所有已完成的计划备份作业（包括日志）的信息每隔6小时进行批处理和推送。
- 在所有解决方案中，其他信息（如备份项、策略、恢复点、存储等）*每日推送至少一次。*
- 备份配置中的更改（例如更改策略或编辑策略）触发了所有相关备份信息的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用恢复服务保管库的活动日志

> [!CAUTION]
> 以下步骤仅适用于*AZURE VM 备份。* 不能对 Azure 备份代理、Azure 中的 SQL 备份或 Azure 文件等解决方案使用这些步骤。

你还可以使用活动日志获取有关备份成功的事件的通知。 若要开始，请执行以下步骤：

1. 登录 Azure 门户。
1. 打开相关的恢复服务保管库。
1. 在保管库的属性中，打开 "**活动日志**" 部分。

标识适当的日志并创建警报：

1. 通过应用以下图像中所示的筛选器，验证是否正在接收成功备份的活动日志。 根据需要更改时间**跨度**值以查看记录。

   ![筛选以查找 Azure VM 备份的活动日志](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 选择操作名称可查看相关详细信息。
1. 选择 "**新建警报规则**" 以打开 "**创建规则**" 页。
1. 按照[使用 Azure Monitor 创建、查看和管理活动日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)中的步骤创建警报。

   ![新建警报规则](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

此处的资源是恢复服务保管库本身。 对于要通过活动日志通知的所有保管库，重复执行相同的步骤。 此条件没有阈值、时间段或频率，因为此警报基于事件。 一旦生成了相关的活动日志，就会引发警报。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 大规模监视

可以在 Azure Monitor 中查看从活动日志和 Log Analytics 工作区创建的所有警报。 只需打开左侧的 "**警报**" 窗格。

尽管可以通过活动日志获取通知，但我们强烈建议使用 Log Analytics 而不是活动日志进行大规模监视。 原因是：

- **有限方案**：通过活动日志的通知仅适用于 Azure VM 备份。 必须为每个恢复服务保管库设置通知。
- **定义大小**：计划的备份活动不适用于活动日志的最新定义。 相反，它会与[资源日志](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace)对齐。 当流过活动日志通道的数据发生变化时，此对齐方式会导致意外的影响。
- **活动日志通道的问题**：在恢复服务保管库中，从 Azure 备份抽取的活动日志遵循新的模型。 遗憾的是，此更改会影响 Azure 政府、Azure 德国和 Azure 中国世纪互联活动日志的生成。 如果这些云服务的用户在 Azure Monitor 中的活动日志中创建或配置任何警报，则不会触发警报。 此外，在所有 Azure 公共区域中，如果用户将[恢复服务活动日志收集到 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)中，则不会显示这些日志。

使用 Log Analytics 工作区进行大规模监视和警报，以适合受 Azure 备份保护的所有工作负荷。

## <a name="next-steps"></a>后续步骤

若要创建自定义查询，请参阅[Log Analytics 数据模型](backup-azure-log-analytics-data-model.md)。
