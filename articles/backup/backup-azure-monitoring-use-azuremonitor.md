---
title: 使用 Azure Monitor 监视 Azure 备份
description: 使用 Azure Monitor 监视 Azure 备份工作负荷及创建自定义警报。
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 3f5f663a2f0ed0f91cc414d352e975a2ff3b9649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88827148"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure Monitor 进行大规模监视

Azure 备份在恢复服务保管库中提供[内置的监视和警报功能](backup-azure-monitoring-built-in-monitor.md)。 无需配置任何附加的管理基础结构即可使用这些功能。 但是，仅限在以下方案中使用此内置服务：

- 监视不同订阅中多个恢复服务保管库中的数据
- 首选的通知通道不是电子邮件**
- 用户想要接收更多方案的警报
- 在 Azure 中查看来自本地组件（例如 System Center Data Protection Manager）的信息。门户不会在[**备份作业**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或[**备份警报**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)中显示这些信息

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作区

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 创建警报

在 Azure Monitor 中，可以在 Log Analytics 工作区内创建你自己的警报。 在工作区中，可以使用 Azure 操作组来选择首选的通知机制。**

> [!IMPORTANT]
> 有关创建此查询所产生的成本的信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

打开 Log Analytics 工作区的“日志”部分，并为自己的日志创建查询****。 选择“新建警报规则”时，将打开 Azure Monitor 警报创建页，如下图所示。****

![在 Log Analytics 工作区中创建警报](media/backup-azure-monitoring-laworkspace/custom-alert.png)

此处的资源已标记为 Log Analytics 工作区，并提供了操作组集成。

![Log Analytics 警报创建页](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警报条件

警报的定义特征是其触发条件。 选择“条件”可在“日志”页上自动加载 Kusto 查询，如下图所示。**** **** 在此处可根据需要编辑条件。 有关详细信息，请参阅[示例 Kusto 查询](#sample-kusto-queries)。

![设置警报条件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如果需要，可以编辑 Kusto 查询。 选择阈值、期限和频率。 阈值确定何时引发警报。 期限是运行查询的时间范围。 例如，如果阈值大于 0，期限为 5 分钟，频率为 5 分钟，那么，该规则将每隔 5 分钟运行一次查询，并检查前 5 分钟的数据。 如果结果数大于 0，则系统将通过所选的操作组通知你。

> [!NOTE]
> 若要为在给定日期创建的所有事件/日志每天运行一次预警规则，请将“时间范围”和“频率”值都更改为 1440（即 24小时）。

#### <a name="alert-action-groups"></a>警报操作组

使用操作组指定通知通道。 若要查看可用的通知机制，请在“操作组”下选择“新建”。**** ****

![“添加操作组”窗口中的可用通知机制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

单纯地在 Log Analytics 中就能满足所有的警报和监视要求；你也可以使用 Log Analytics 来补充内置通知。

有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/platform/alerts-log.md)以及[在 Azure 门户中创建和管理操作组](../azure-monitor/platform/action-groups.md)。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

默认图形提供可对其生成警报的基本方案的 Kusto 查询。 还可以修改查询，以获取要对其发出警报的数据。 将以下示例 Kusto 查询粘贴到“日志”页中，然后基于查询创建警报：****

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

保管库中的诊断数据将传送到 Log Analytics 工作区，但会出现一定的延迟。 从恢复服务保管库推送每个事件 20 到 30 分钟后，这些事件将抵达 Log Analytics 工作区。** 下面是有关延迟的更多详细信息：

- 在所有解决方案中，一旦创建备份服务的内置警报，就会立即推送这些警报。 因此，它们通常会在 20 到 30 分钟后显示在 Log Analytics 工作区中。
- 在所有解决方案中，在完成按需备份作业和还原作业后，会立即推送这些作业。**
- 对于除 SQL 备份以外的所有解决方案，在完成计划的备份作业后，会立即推送这些作业。**
- 对于 SQL 备份，由于日志备份可每隔 15 分钟发生，所有已完成的计划备份作业的信息（包括日志）将每隔 6 小时进行批处理和推送。
- 在所有解决方案中，备份项、策略、恢复点、存储等其他信息每天至少推送一次。**
- 备份配置发生更改（例如更改策略或编辑策略）会触发所有相关备份信息的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用恢复服务保管库的活动日志

> [!CAUTION]
> 以下步骤仅适用于 Azure VM 备份。** 不能对 Azure 备份代理、Azure 中的 SQL 备份或 Azure 文件等解决方案使用这些步骤。

还可以使用活动日志来获取事件通知，例如备份成功。 遵循以下步骤开始：

1. 登录 Azure 门户。
2. 打开相关的恢复服务保管库。
3. 在保管库的属性中，打开“活动日志”部分。****

若要识别相应的日志并创建警报：

1. 应用下图中所示的筛选器，验证是否能够接收成功备份的活动日志。 根据需要更改“时间跨度”值以查看记录。****

   ![通过筛选找到 Azure VM 备份的活动日志](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. 选择操作名称以查看相关详细信息。
3. 选择“新建警报规则”打开“创建规则”页。**** ****
4. 遵循[使用 Azure Monitor 创建、查看和管理活动日志警报](../azure-monitor/platform/alerts-activity-log.md)中的步骤创建警报。

   ![新建警报规则](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

此处的资源是恢复服务保管库本身。 请针对要在其中通过活动日志接收通知的所有保管库重复相同的步骤。 条件中不包含阈值、期限或频率，因为此警报基于事件。 生成相关的活动日志后，会立即引发警报。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 进行大规模监视

可以在 Azure Monitor 中查看从活动日志和 Log Analytics 工作区创建的所有警报。 只需打开左侧的“警报”窗格即可。****

尽管你可以通过活动日志获取通知，但我们强烈建议使用 Log Analytics（而不是活动日志）进行大规模监视。 原因如下：

- **方案受限**：通过活动日志发送通知仅适用于 Azure VM 备份。 必须为每个恢复服务保管库设置通知。
- **定义适应**：计划的备份活动不能适应活动日志的最新定义。 相反，它适用于[资源日志](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。 当通过活动日志通道传送数据发生变化时，这种相符性会导致意外的影响。
- **活动日志通道的问题**：在恢复服务保管库中，从 Azure 备份传送的活动日志遵循一个新的模型。 遗憾的是，这一更改会影响 Azure 政府、Azure 德国和 Azure 中国世纪互联中的活动日志的生成。 如果这些云服务的用户在 Azure Monitor 中基于活动日志创建或配置了任何警报，将不会触发警报。 此外，在所有 Azure 公共区域，如果用户[将恢复服务活动日志收集到 Log Analytics 工作区中](../azure-monitor/platform/activity-log.md)，这些日志不会显示。

使用 Log Analytics 工作区可对 Azure 备份保护的所有工作负荷进行大规模监视和发出警报。

## <a name="next-steps"></a>后续步骤

若要创建自定义查询，请参阅 [Log Analytics 数据模型](backup-azure-reports-data-model.md)。
