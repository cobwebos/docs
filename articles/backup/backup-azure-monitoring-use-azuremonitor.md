---
title: Azure 备份：使用 Azure Monitor 监视 Azure 备份
description: 监视 Azure 备份工作负荷，并使用 Azure Monitor 创建自定义警报。
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics;Azure 备份;警报;诊断设置;操作组
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786316"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure Monitor 监视大规模

Azure 备份提供[内置的监视和警报功能](backup-azure-monitoring-built-in-monitor.md)恢复服务保管库中。 这些功能则无需任何附加的管理基础结构。 但在此内置的服务限制在以下方案中：

- 如果跨订阅监视多个恢复服务保管库中的数据
- 如果首选的通知通道*不*电子邮件
- 如果用户希望警报的更多方案
- 如果你想要在门户中不显示其在 Azure 中，查看信息从一个本地组件，如 System Center Data Protection Manager [**备份作业**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或[ **备份警报**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作区

> [!NOTE]
> 从 Azure VM 备份，Azure 备份代理、 System Center Data Protection Manager、 SQL Azure Vm 中备份和 Azure 文件共享备份的数据抽取到通过诊断设置 Log Analytics 工作区中。 

若要监视在规模较大，需要两个 Azure 服务的功能。 *诊断设置*将数据从多个 Azure 资源管理器资源发送到另一个资源。 *Log Analytics*生成自定义警报，可以使用操作组可以定义其他通知通道。 

以下各节详细说明了如何使用 Log Analytics 监视 Azure 备份在规模较大。

### <a name="configure-diagnostic-settings"></a>配置诊断设置

Azure 资源管理器资源，例如恢复服务保管库中，记录有关计划的操作和用户触发的操作为诊断数据的信息。 

在监视部分，选择**诊断设置**并指定恢复服务保管库的诊断数据的目标。

![恢复服务保管库的诊断设置，针对 Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

你可以面向其他订阅中的 Log Analytics 工作区。 若要跨订阅在一个位置监视保管库，请选择多个恢复服务保管库的同一个 Log Analytics 工作区。 若要渠道到 Log Analytics 工作区与 Azure 备份的所有信息，请选择**azure 备份报表**作为的日志。

> [!IMPORTANT]
> 完成配置后，应等待 24 小时后，若要完成初始数据推送。 初始数据推送后，如中所述在本文中，更高版本中，所有事件都推送[频率部分](#diagnostic-data-update-frequency)。

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>将解决方案部署到 Log Analytics 工作区

数据是在 Log Analytics 工作区中后,[部署 GitHub 模板](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/)到 Log Analytics 来可视化数据。 若要正确标识工作区，请确保为其提供相同的资源组、 工作区名称和工作区的位置。 在工作区，然后安装此模板。

> [!NOTE]
> 如果你的 Log Analytics 工作区中，不包含警报、 备份作业或还原作业，可能会看到在门户中的"BadArgumentError"错误代码。 忽略此错误并继续使用该解决方案。 相关的数据类型开始后传输到工作区中，可视化效果将反映相同的并不会再看到此错误。

### <a name="view-azure-backup-data-by-using-log-analytics"></a>使用 Log Analytics 查看 Azure 备份的数据

部署模板后，用于监视 Azure 备份解决方案将显示在工作区摘要区域中。 若要转到摘要，请按照以下方式之一操作：

- **Azure Monitor**：在中**Insights**部分中，选择**详细**，然后选择相关的工作区。
- **Log Analytics 工作区**:选择相关的工作区，然后在**常规**，选择**工作区摘要**。

![Log Analytics 监视磁贴](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

当选中监视磁贴时，设计器的模板将打开从 Azure 备份的一系列关于基本监视数据的关系图。 下面是一些你将看到的图形：

* 所有备份作业

   ![备份作业的日志分析图](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* 还原作业

   ![还原作业的日志分析关系图](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Azure 资源的内置 Azure 备份警报

   ![Azure 资源的内置 Azure 备份警报的日志分析关系图](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* 内置的本地资源的 Azure 备份警报

   ![日志分析关系图的本地资源的内置 Azure 备份警报](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* 活动数据源

   ![日志分析活动的备份实体关系图](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* 恢复服务保管库的云存储

   ![恢复服务保管库的云存储的日志分析关系图](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

这些关系图模板一起提供。 可以编辑关系图，也可以在需要时添加更多的关系图。

> [!IMPORTANT]
> 在部署模板时，您实质上创建只读锁。 若要编辑并保存模板，需要删除该锁定。 您可以删除中的锁定**设置**部分中的 Log Analytics 工作区，然后在**锁定**窗格。

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 中创建警报

在 Azure Monitor，可以创建自己的 Log Analytics 工作区中的警报。 在工作区中，你使用*Azure 操作组*以选择您首选的通知机制。 

> [!IMPORTANT]
> 有关创建此查询的成本的信息，请参阅[Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

选择关系图来打开任一**日志**Log Analytics 工作区部分。 在中**日志**部分、 编辑查询并对其创建警报。

![在 Log Analytics 工作区中创建警报](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

当选择**新的警报规则**，Azure Monitor 警报创建页面打开时，在下图中所示。 此处的资源已标记为 Log Analytics 工作区中，并提供操作组集成。

![Log Analytics 警报创建页](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警报条件

警报的鲜明特征是及其触发条件。 选择**条件**上的自动负载 Kusto 查询**日志**页上，在下图中所示。 在这里您可以编辑以满足你需求的条件。 有关详细信息，请参阅[示例 Kusto 查询](#sample-kusto-queries)。

![设置警报条件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如有必要，可以编辑 Kusto 查询。 选择阈值、 期和频率。 阈值确定时，将生成警报。 段是时间的在其中运行查询窗口。 例如，如果阈值为大于 0，时间是 5 分钟，频率为 5 分钟，然后该规则运行查询每隔 5 分钟，查看之前的 5 分钟。 如果结果数大于 0，则被通知通过选定的操作组。

#### <a name="alert-action-groups"></a>警报操作组

使用操作组指定通知通道。 若要查看可用的通知机制，在**操作组**，选择**创建新**。

![在"添加操作组"窗口中可用的通知机制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

可以满足所有警报和监视从 Log Analytics，单独的要求，也可以使用 Log Analytics 来补充内置通知。

有关详细信息，请参阅[创建、 查看和管理通过使用 Azure Monitor 的日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)并[创建和管理在 Azure 门户中的操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

默认图形得出 Kusto 查询其生成警报的基本方案。 此外可以修改这些查询以获取数据你想要对其发出警报。 将下面的示例 Kusto 查询中粘贴**日志**然后的查询创建警报：

* 所有成功的备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* 所有失败的备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* 所有成功的 Azure VM 备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

* 所有成功 SQL 日志备份作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

* 所有成功的 Azure 备份代理作业

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>诊断数据更新频率

在保管库中的诊断数据抽取到一些延迟 Log Analytics 工作区中。 在 Log Analytics 工作区的每个事件到达*20 到 30 分钟*推送从恢复服务保管库后。 下面是更多有关延迟的详细信息：

- 跨所有解决方案，只要它们创建推送备份服务的内置警报。 因此它们通常显示在 Log Analytics 工作区后 20 到 30 分钟。
- 即席备份作业和还原作业尽快它们跨所有解决方案推送*完成*。
- 计划备份作业，只要它们推送除 SQL 备份的所有解决方案，*完成*。
- SQL 备份，因为日志备份可能会发生每隔 15 分钟，对于所有已完成计划备份作业，包括日志，信息是批处理和推送每 6 小时。
- 跨所有解决方案，例如备份项、 策略、 恢复点、 存储等其他信息至少推送*每天一次。*
- 备份配置 （如更改策略或编辑策略） 中的更改将触发备份的所有相关信息的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用恢复服务保管库的活动日志

> [!CAUTION]
> 以下步骤仅适用于*Azure VM 备份。* Azure 备份代理、 Azure 或 Azure 文件中的 SQL 备份等解决方案，不能使用这些步骤。

此外可以使用活动日志以获取事件，例如备份成功的通知。 若要开始，请执行以下步骤：

1. 登录 Azure 门户。
1. 打开相关的恢复服务保管库。 
1. 在保管库的属性中，打开**活动日志**部分。

若要确定相应的日志并创建警报：

1. 验证要收到的成功备份的活动日志通过应用筛选器，在下图中所示。 更改**Timespan**必要时，若要查看记录的值。

   ![筛选以查找 Azure VM 备份活动日志](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 选择要查看相关的详细信息的操作名称。
1. 选择**新的警报规则**以打开**创建规则**页。 
1. 中的步骤创建的警报[创建、 查看和管理通过使用 Azure Monitor 活动日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)。

   ![新建警报规则](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

此处的资源是在恢复服务保管库本身。 所有想要通过活动日志通知的保管库，必须重复相同的步骤。 条件不会具有阈值、 句号或频率，因为此警报基于事件。 只要生成相关的活动日志，则会发出警报。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 监视大规模

可以查看从活动日志和 Azure Monitor 中的 Log Analytics 工作区中创建的所有警报。 只需打开**警报**左窗格中的。

虽然可以获取通过活动日志的通知，我们强烈建议使用 Log Analytics，而不是活动日志进行大规模监视。 原因是：

- **有限的方案**:通过活动日志的通知仅适用于 Azure VM 备份。 必须为每个恢复服务保管库设置通知。
- **定义适合**:计划的备份活动不适合使用活动日志的最新定义。 相反，它与对齐[诊断日志](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs)。 通过活动流的数据记录通道更改时，这种调整会导致意外的效果。
- **活动日志通道问题**:在恢复服务保管库，从 Azure 备份抽取的活动日志按照新的模型。 遗憾的是，此更改会影响在 Azure 政府、 Azure Germany 和 Azure 中国的 21Vianet 的活动日志的生成。 如果这些云服务的用户创建或配置 Azure Monitor 中的活动日志中的任何警报，则不会触发警报。 此外，所有 Azure 公共区域中，如果用户[将恢复服务活动日志收集到 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)，这些日志不会显示。

使用 Log Analytics 工作区进行监视和警报，所有由 Azure 备份保护工作负荷的规模。

## <a name="next-steps"></a>后续步骤

若要创建自定义查询，请参阅[Log Analytics 数据模型](backup-azure-log-analytics-data-model.md)。
