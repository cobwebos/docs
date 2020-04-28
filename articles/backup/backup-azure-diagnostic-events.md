---
title: 使用恢复服务保管库的诊断设置
description: 本文介绍如何使用 Azure 备份的新的和新的诊断事件。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 4efc00da96493c751c4a85dbdcc280d1ca0ef5ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183698"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>使用恢复服务保管库的诊断设置

Azure 备份发送诊断事件，这些事件可收集并用于分析、警报和报告目的。

你可以通过转到保管库，然后选择 "**诊断设置**"，通过 Azure 门户配置恢复服务保管库的诊断设置。 选择 " **+ 添加诊断设置**" 可将一个或多个诊断事件发送到存储帐户、事件中心或 Log Analytics 工作区。

![诊断设置窗格](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 备份用户可用的诊断事件

Azure 备份提供以下诊断事件。 每个事件都提供一组特定的备份相关项目的详细数据：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

有关详细信息，请参阅[Azure 备份诊断事件的数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)。

这些事件的数据可以发送到存储帐户、Log Analytics 工作区或事件中心。 如果要将此数据发送到 Log Analytics 工作区，请选择 "**诊断设置**" 屏幕上的 "**特定于资源**" 切换。 有关详细信息，请参阅以下部分。

## <a name="use-diagnostics-settings-with-log-analytics"></a>将诊断设置用于 Log Analytics

你现在可以使用 Azure 备份将保管库诊断数据发送到专用 Log Analytics 表进行备份。 这些表称为[资源特定的表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

将保管库诊断数据发送到 Log Analytics：

1. 中转到保管库，并选择 "**诊断设置**"。 选择 " **+ 添加诊断设置**"。
1. 为诊断设置指定一个名称。
1. 选中 "**发送到 Log Analytics** " 复选框，然后选择 "Log Analytics" 工作区。
1. 选择切换中的 "**资源特定**"，并选择以下六个事件： **CoreAzureBackup**、 **AddonAzureBackupJobs**、 **AddonAzureBackupAlerts**、 **AddonAzureBackupPolicy**、 **AddonAzureBackupStorage**和**AddonAzureBackupProtectedInstance**。
1. 选择“保存”。 

   ![特定于资源的模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

数据流入 Log Analytics 工作区后，将在工作区中创建每个事件的专用表。 您可以直接查询这些表中的任何一个。 如果需要，还可以在这些表之间执行联接或联合。

> [!IMPORTANT]
> 只有在[备份报表](https://docs.microsoft.com/azure/backup/configure-reports)的特定于资源的模式下*才*支持六个事件，即 CoreAzureBackup、AddonAzureBackupJobs、AddonAzureBackupAlerts、AddonAzureBackupPolicy、AddonAzureBackupStorage 和 AddonAzureBackupProtectedInstance。 *如果尝试在 Azure 诊断模式下发送这六个事件的数据，则备份报表中将不会显示任何数据。*

## <a name="legacy-event"></a>旧事件

通常，保管库的所有与备份相关的诊断数据都包含在一个名为 AzureBackupReport 的事件中。 此处所述的六个事件实际上是 AzureBackupReport 中包含的所有数据的分解。

目前，在用户对此事件具有现有自定义查询的情况下，我们将继续支持 AzureBackupReport 事件以实现向后兼容。 例如，自定义日志警报和自定义可视化效果。 *建议尽快迁移到[新的事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)*。 新事件：

* 使数据更易于在日志查询中使用。
* 提供更好的架构及其结构的可发现性。
* 跨引入延迟和查询时间提高性能。

*Azure 诊断模式下的旧事件最终将被弃用。选择新事件可能会帮助你避免在以后进行复杂的迁移*。 使用 Log Analytics 的[报表解决方案](https://docs.microsoft.com/azure/backup/configure-reports)也将停止来自旧事件的支持数据。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>移动到 Log Analytics 工作区的新诊断设置的步骤

1. 通过使用旧事件及其所属订阅来识别哪些保管库正在向 Log Analytics 工作区发送数据。 运行以下工作区来识别这些保管库和订阅。

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

1. 使用 Azure 备份中的[内置 Azure 策略定义](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)为指定范围内的所有保管库添加新的诊断设置。 此策略向保管库添加新的诊断设置，该设置可能没有诊断设置或仅具有旧的诊断设置。 可以一次将此策略分配给整个订阅或资源组。 你必须具有为其分配策略的每个订阅的所有者访问权限。

你可以选择为 AzureBackupReport 和六个新事件使用单独的诊断设置，直到你将所有自定义查询迁移到使用新表中的数据。 下图显示了一个具有两个诊断设置的保管库的示例。 第一个名为 " **Setting1**" 的设置将 AzureBackupReport 事件的数据发送到 Azure 诊断模式下的 Log Analytics 工作区。 第二个名为 " **Setting2**" 的设置将六个新的 Azure 备份事件的数据发送到特定于资源的模式下的 Log Analytics 工作区。

![两个设置](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> *仅*在 Azure 诊断模式下支持 AzureBackupReport 事件。 *如果尝试在特定于资源的模式下发送此事件的数据，则不会将任何数据传递到 Log Analytics 工作区。*

> [!NOTE]
> 仅当用户选择 "**发送到 Log Analytics**" 时，才会显示**Azure 诊断**或**资源特定**的切换。 若要将数据发送到存储帐户或事件中心，用户需要选择所需的目标，并选中任何所需事件的复选框，无需任何其他输入。 同样，我们建议您不要选择 AzureBackupReport 的旧事件。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>向 Log Analytics 发送 Azure Site Recovery 事件

Azure 备份和 Azure Site Recovery 事件是从同一恢复服务保管库发送的。 Azure Site Recovery 当前不可用于特定于资源的表。 要将 Azure Site Recovery 事件发送到 Log Analytics 的用户*仅*定向到使用 Azure 诊断模式，如图中所示。 *为 Azure Site Recovery 事件选择特定于资源的模式将阻止所需的数据发送到 Log Analytics 工作区*。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

总结：

* 如果已将 Log Analytics 的诊断设置为 Azure 诊断并已在其顶部编写了自定义查询，请将此设置保持不*变*，直到迁移查询以使用新事件中的数据。
* 如果还想要载入新表，请创建**新**的诊断设置，选择 "**资源特定**"，并选择六个新的事件。
* 如果当前正在向 Log Analytics 发送 Azure Site Recovery 事件，请*不要为这些事件选择特定*于资源的模式。 否则，这些事件的数据不会流向 Log Analytics 工作区。 相反，请创建其他诊断设置，选择 " **Azure 诊断**"，然后选择相关的 Azure Site Recovery 事件。

下图显示了一个具有保管库的三个诊断设置的用户的示例。 第一个名为 " **Setting1**" 的设置将数据从 AzureBackupReport 事件发送到 Azure 诊断模式下的 Log Analytics 工作区。 第二个名为 " **Setting2**" 的设置将数据从六个新的 Azure 备份事件发送到特定于资源的模式下的 Log Analytics 工作区。 第三个名为 " **Setting3**" 的设置将数据从 Azure Site Recovery 事件发送到 Azure 诊断模式下的 Log Analytics 工作区。

![三个设置](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>后续步骤

[了解诊断事件的 Log Analytics 数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
