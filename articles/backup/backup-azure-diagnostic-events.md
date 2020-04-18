---
title: 对恢复服务保管库使用诊断设置
description: 本文介绍如何使用 Azure 备份的新旧诊断事件。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617309"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>对恢复服务保管库使用诊断设置

Azure 备份发送可用于分析、警报和报告的诊断事件。

您可以通过访问保管库并选择诊断设置，通过 Azure 门户配置恢复服务保管库**的诊断设置**。 选择 **" 添加诊断设置**"允许您将一个或多个诊断事件发送到存储帐户、事件中心或日志分析工作区。

![诊断设置窗格](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>可用于 Azure 备份用户的诊断事件

Azure 备份提供以下诊断事件。 每个事件都提供有关一组特定的备份相关项目的详细数据：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

有关详细信息，请参阅[Azure 备份诊断事件的数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)。

这些事件的数据可以发送到存储帐户、日志分析工作区或事件中心。 如果要将此数据发送到日志分析工作区，请在 **"诊断设置"** 屏幕上选择 **"资源特定**切换"。 有关详细信息，请参阅以下部分。

## <a name="use-diagnostics-settings-with-log-analytics"></a>将诊断设置与日志分析一起使用

现在，可以使用 Azure 备份将保管库诊断数据发送到专用日志分析表进行备份。 这些表称为[特定于资源的表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

要将保管库诊断数据发送到日志分析：

1. 转到保管库，然后选择 **"诊断设置**"。 选择 **= 添加诊断设置**。
1. 为诊断设置指定名称。
1. 选中"**发送到日志分析**"复选框，然后选择日志分析工作区。
1. 选择切换中**特定于的资源**，然后选择以下六个事件：核心**Azure 备份**、**附加 Azure 备份作业**、**附加 Azure 备份警报**、**附加 Azure 备份策略**、**附加Azure备份存储**和**AddonAzure 备份保护实例**。
1. 选择“保存”。 

   ![特定于资源的模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

将数据流入日志分析工作区后，将为每个事件在工作区中创建专用表。 您可以直接查询这些表中的任何一个。 如有必要，还可以在这些表之间执行联接或联合。

> [!IMPORTANT]
> 这六个事件，即 CoreAzure 备份、附加 Azure 备份作业、附加 Azure 备份警报、附加Azure备份策略、AddonAzure 备份存储和 AddonAzure 备份保护实例，*仅在*[备份报告中](https://docs.microsoft.com/azure/backup/configure-reports)的资源特定模式下受支持。 *如果尝试在 Azure 诊断模式下发送这六个事件的数据，则备份报告中不会显示任何数据。*

## <a name="legacy-event"></a>传统事件

传统上，保管库的所有与备份相关的诊断数据都包含在称为 AzureBackupReport 的单个事件中。 这里描述的六个事件实质上是 AzureBackup 报告中包含的所有数据的分解。 

目前，在用户对此事件存在自定义查询的情况下，我们将继续支持 AzureBackupReport 事件，以进行向后兼容性。 示例包括自定义日志警报和自定义可视化效果。 *我们建议您尽早转到[新事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)*。 新事件：

- 使数据更易于在日志查询中使用。
- 提供更好的架构及其结构的可发现性。
- 提高引入延迟和查询时间的性能。 

*Azure 诊断模式下的旧事件最终将被弃用。选择新事件可能有助于您避免以后的复杂迁移*。 我们使用日志分析[的报告解决方案](https://docs.microsoft.com/azure/backup/configure-reports)也将停止支持来自旧事件的数据。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>移动到日志分析工作区的新诊断设置的步骤

1. 使用旧事件及其所属的订阅，确定哪些保管库将数据发送到日志分析工作区。 运行以下工作区以标识这些保管库和订阅。

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

1. 使用 Azure 备份中的[内置 Azure 策略](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)为指定范围内的所有保管库添加新的诊断设置。 此策略向没有诊断设置或仅具有旧诊断设置的保管库添加新的诊断设置。 此策略可以一次分配给整个订阅或资源组。 您必须具有所有者对为其分配策略的每个订阅的访问权限。

您可以选择为 AzureBackupReport 和六个新事件设置单独的诊断设置，直到迁移所有自定义查询以使用新表中的数据。 下图显示了具有两个诊断设置的保管库的示例。 第一个设置名为 **"设置1"，** 在 Azure 诊断模式下将 AzureBackupReport 事件的数据发送到日志分析工作区。 第二个设置名为 **"设置2"，** 以特定于资源的模式将六个新的 Azure 备份事件的数据发送到日志分析工作区。

![两个设置](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Azure 备份报告事件*仅在*Azure 诊断模式下受支持。 *如果尝试以特定于资源的模式发送此事件的数据，则不会将数据流向日志分析工作区。*

> [!NOTE]
> 仅当用户选择 **"发送到日志分析**"时，才会显示**Azure 诊断**或**特定于资源的**切换。 要将数据发送到存储帐户或事件中心，用户选择所需的目标，并为任何所需事件选择复选框，而无需任何其他输入。 同样，我们建议您不要选择旧事件 AzureBackupReport。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>将 Azure 站点恢复事件发送到日志分析

Azure 备份和 Azure 站点恢复事件从同一恢复服务保管库发送。 Azure 站点恢复当前不适用于特定于资源的表。 想要将 Azure 站点恢复事件发送到日志分析的用户将定向为*仅*使用 Azure 诊断模式，如映像所示。 *为 Azure 站点恢复事件选择特定于资源的模式将阻止将所需的数据发送到日志分析工作区*。

![站点恢复事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

总结：

* 如果已使用 Azure 诊断设置了日志分析诊断，并在其上面编写了自定义查询，请保留该设置*不变*，直到迁移查询以使用新事件中的数据。
* 如果还希望像我们建议的那样，加入新表，请**创建新**的诊断设置，选择**特定于"资源"** 并选择六个新事件。
* 如果当前正在向日志分析发送 Azure 站点恢复事件，*请不要*为这些事件选择特定于资源的模式。 否则，这些事件的数据不会流入日志分析工作区。 而是创建其他诊断设置，选择**Azure 诊断**，然后选择相关的 Azure 站点恢复事件。

下图显示了具有三个保管库诊断设置的用户的示例。 第一个设置名为 **"设置1"，** 将数据从 Azure BackupReport 事件发送到 Azure 诊断模式下的日志分析工作区。 第二个设置名为 **"设置2"，** 以特定于资源的模式将六个新的 Azure 备份事件的数据发送到日志分析工作区。 第三个设置名为 **"设置3"，** 在 Azure 诊断模式下将数据从 Azure 站点恢复事件发送到日志分析工作区。

![三种设置](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>后续步骤

[了解诊断事件的日志分析数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
