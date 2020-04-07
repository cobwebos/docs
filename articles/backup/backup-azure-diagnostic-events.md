---
title: 使用恢复服务保管库的诊断设置
description: 本文介绍如何使用 Azure 备份服务的新旧诊断事件
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672785"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>使用恢复服务保管库的诊断设置

Azure 备份发送诊断事件，可以收集这些事件并使用它们来实现分析、警报和报告目的。 

可以通过 Azure 门户配置恢复服务保管库的诊断设置，方法是导航到该保管库并单击“诊断设置”菜单项。**** 单击“+ 添加诊断设置”可将一个或多个诊断事件发送到存储帐户、事件中心或 Log Analytics (LA) 工作区。****

![诊断设置边栏选项卡](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 备份用户可用的诊断事件

Azure 备份提供以下诊断事件，其中每个事件提供有关特定一组备份相关项目的详细数据：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Azure 备份诊断事件的数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

这些事件的数据可以发送到存储帐户、LA 工作区或事件中心。 如果将此数据发送到 LA 工作区，则需要在“诊断设置”屏幕中选择“特定于资源”开关（请参阅后续部分中的详细信息）。********

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>将诊断设置与日志分析 （LA） 一起使用

与 Azure 日志分析路线图一致，Azure 备份现在允许您将保管库诊断数据发送到专用 LA 表进行备份。 这些称为[特定于资源的表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

要将保管库诊断数据发送到洛杉矶：

1.    导航到保管库，然后单击 **"诊断设置**"。 单击 **+ 添加诊断设置**。
2.    为"诊断"设置指定名称。
3.    选中"**发送到日志分析"** 复选框，然后选择日志分析工作区。
4.    在切换中选择**特定于资源**，并检查以下六个事件 -**酷睿备份**、**附加Azure备份警报**、**附加Azure备份保护实例**、**附加Azure备份作业**、**附加Azure备份策略**和**附加Azure备份存储**。
5.    单击"**保存**"。

![资源特定模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

数据流入 LA 工作区后，将为每个事件在工作区中创建专用表。 您可以直接查询这些表中的任何一个，并在必要时在这些表之间执行联接或联合。

> [!IMPORTANT]
> 上述六个事件，即核心 Azure 备份、附加 Azure 备份警报、AddonAzure 备份保护实例、附加Azure备份作业、附加Azure备份策略和附加Azure备份存储，**仅在**[备份报告中](https://docs.microsoft.com/azure/backup/configure-reports)的资源特定模式下支持。 **请注意，如果尝试在 Azure 诊断模式下发送这六个事件的数据，则备份报告中不会显示任何数据。**

## <a name="legacy-event"></a>旧事件

传统上，保管库的所有与备份相关的诊断数据都包含在称为"AzureBackupReport"的单个事件中。 上述六个事件实质上是 AzureBackupReport 中包含的所有数据的分解。 

目前，我们将继续支持 AzureBackupReport 事件，以进行向后兼容性，如果用户在此事件中存在自定义查询，例如自定义日志警报、自定义可视化等。但是，**我们建议尽早迁移到[新事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)**，因为这使数据更容易在日志查询中处理，从而提供更好的架构及其结构的可发现性，从而提高了引入延迟和查询时间的性能。 

**Azure 诊断模式下的旧事件最终将被弃用，因此选择新事件可能有助于您避免以后的复杂迁移**。 我们利用日志分析[的报告解决方案](https://docs.microsoft.com/azure/backup/configure-reports)也将停止支持来自旧事件的数据。

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>移动到新诊断设置的步骤（到日志分析工作区）

1. 使用旧事件确定哪些保管库将数据发送到日志分析工作区，以及它们所属的订阅。 运行以下工作区以标识这些保管库和订阅：

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

2. 使用 Azure 备份的[内置 Azure 策略](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)为指定范围内的所有保管库添加新的诊断设置。 此策略向没有诊断设置（或）仅具有旧诊断设置的保管库添加新的诊断设置。 此策略可以一次分配给整个订阅或资源组。 请注意，您需要对为其分配策略的每个订阅的"所有者"访问权限。

您可以选择为 AzureBackupReport 和六个新事件设置单独的诊断设置，直到迁移了所有自定义查询以使用新表中的数据。 下图显示了采用两项诊断设置的保管库示例。 第一项设置名为 **Setting1**，它以“Azure 诊断”模式将 AzureBackupReport 事件的数据发送到 LA 工作区。 第二项设置名为 **Setting2**，它以“特定于资源”模式将六个新 Azure 备份事件的数据发送到 LA 工作区。

![两项设置](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> **仅**在“Azure 诊断”模式下才支持 AzureBackupReport 事件。 **请注意，如果尝试以“特定于资源”模式发送此事件的数据，则不会将任何数据传送到 LA 工作区。**

> [!NOTE]
> 仅当用户选中了“发送到 Log Analytics”时，才会显示“Azure 诊断”/“特定于资源”开关。**** 若要将数据发送到存储帐户或事件中心，用户只需选择所需的目标并检查任何所需的事件，而无需提供任何其他输入。 同样，建议不要选择旧事件 AzureBackupReport 或更早的事件。

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>用户将 Azure Site Recovery 事件发送到 Log Analytics (LA)

Azure 备份和 Azure Site Recovery 事件从同一个恢复服务保管库发送。 由于 Azure Site Recovery 目前尚未加入到“特定于资源”表，对于想要将 Azure Site Recovery 事件发送到 LA 的用户，系统会指示他们**仅**使用“Azure 诊断”模式（参阅下图）。 **为 Azure Site Recovery 事件选择“特定于资源”模式会阻止将所需数据发送到 LA 工作区**。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

上述细节的汇总：

* 如果已使用 Azure 诊断设置了 LA 诊断，并在其顶层编写了自定义查询，请在迁移查询以使用新事件中的数据之前，保持该设置**不变**。
* 如果你还想要加入到新表（我们建议这样做），请创建**新的**诊断设置，选择“特定于资源”，并选择前面指定的六个新事件。****
* 如果你目前正在向 LA 发送 Azure Site Recovery 事件，请**不要**为这些事件选择“特定于资源”模式，否则这些事件的数据将不会传送到 LA 工作区。 应该创建**附加的诊断设置**，选择“Azure 诊断”，然后选择相关的 Azure Site Recovery 事件。****

下图显示了用户对保管库使用三项诊断设置的示例。 第一项设置名为 **Setting1**，它以“Azure 诊断”模式将 AzureBackupReport 事件中的数据发送到 LA 工作区。 第二项设置名为 **Setting2**，它以“特定于资源”模式将六个新 Azure 备份事件中的数据发送到 LA 工作区。 第三项设置名为 **Setting3**，它以“Azure 诊断”模式将 Azure Site Recovery 事件中的数据发送到 LA 工作区。

![三项设置](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>后续步骤

[了解诊断事件的 Log Analytics 数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
