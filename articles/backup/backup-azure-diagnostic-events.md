---
title: 使用恢复服务保管库的诊断设置
description: 本文介绍如何使用 Azure 备份的新旧诊断事件。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: be99b73a4dac12c9e70e4cb8a85f34b97f5c42d7
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854802"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>使用恢复服务保管库的诊断设置

Azure 备份发送诊断事件，可以收集这些事件并使用它们来实现分析、警报和报告目的。

可以通过 Azure 门户配置恢复服务保管库的诊断设置，方法是转到该保管库并选择“诊断设置”****。 选择“+ 添加诊断设置”可将一个或多个诊断事件发送到存储帐户、事件中心或 Log Analytics 工作区****。

![诊断设置窗格](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 备份用户可用的诊断事件

Azure 备份提供以下诊断事件。 每个事件都提供一组特定的备份相关项目的详细数据：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

如果你使用的是[旧的事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)AzureBackupReport，则建议你尽快切换到使用上述事件。

有关详细信息，请参阅 [Azure 备份诊断事件的数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)。

可以将这些事件的数据发送到存储帐户、Log Analytics 工作区或事件中心。 如果要将这些数据发送到 Log Analytics 工作区，请在“诊断设置”**** 屏幕上选择“资源专用”**** 开关。 有关详细信息，请参阅以下部分。

## <a name="use-diagnostics-settings-with-log-analytics"></a>将诊断设置与 Log Analytics 配合使用

现在，可以使用 Azure 备份将保管库诊断数据发送到专用 Log Analytics 表进行备份。 这些表称为[资源专用表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

若要将保管库诊断数据发送到 Log Analytics，请执行以下操作：

1. 转到保管库，选择“诊断设置”****。 选择“+ 添加诊断设置”****。
1. 为诊断设置提供名称。
1. 选中“发送到 Log Analytics”**** 复选框，然后选择 Log Analytics 工作区。
1. 在开关中选择“资源专用”****，然后选择以下六个事件：CoreAzureBackup****、AddonAzureBackupJobs****、AddonAzureBackupAlerts****、AddonAzureBackupPolicy****、AddonAzureBackupStorage**** 和 **AddonAzureBackupProtectedInstance**。
1. 选择“保存” ****。

   ![资源专用模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

数据流入 Log Analytics 工作区后，将在工作区中为以上各个事件创建专用表。 你可以直接查询这些表中的任何一个。 如果需要，还可以在这些表之间执行联接或联合。

> [!IMPORTANT]
> 只有在资源专用模式下，[备份报表](https://docs.microsoft.com/azure/backup/configure-reports)才** 支持这六个事件，即 CoreAzureBackup、AddonAzureBackupJobs、AddonAzureBackupAlerts、AddonAzureBackupPolicy、AddonAzureBackupStorage 和 AddonAzureBackupProtectedInstance。 如果尝试在 Azure 诊断模式下发送这六个事件的数据，则在备份报表中将看不到任何数据。**

## <a name="legacy-event"></a>旧事件

在传统上，保管库的所有与备份相关的诊断数据都包含在名为“AzureBackupReport”的单个事件中。 此处所述的这六个事件实质上是 AzureBackupReport 中包含的所有数据的分解。

目前，如果用户仍在对此事件运行自定义查询，为了实现向后兼容，我们将继续支持 AzureBackupReport 事件。 示例包括自定义日志警报和自定义可视化效果。 建议尽早迁移到[新事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)**。 新事件：

* 在日志查询中使用数据时更方便。
* 提高了架构及其结构的可发现性。
* 改善了引入延迟和查询时间的性能。

Azure 诊断模式下的旧事件最终将被弃用。** 选择新事件可以帮助你避免以后进行复杂的迁移。 使用 Log Analytics 的[报告解决方案](https://docs.microsoft.com/azure/backup/configure-reports)也将停止支持旧事件的数据。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>迁移到 Log Analytics 工作区的新诊断设置的步骤

1. 通过使用旧事件及其所属的订阅，确定哪些保管库正在将数据发送到 Log Analytics 工作区。 运行以下工作区以标识这些保管库和订阅。

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

1. 使用 Azure 备份的[内置 Azure Policy 定义](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)，为指定范围内的所有保管库添加新的诊断设置。 此策略会将新的诊断设置添加到没有诊断设置或仅具有旧诊断设置的保管库。 可以一次性将此策略分配给整个订阅或资源组。 你必须对分配了此策略的每个订阅都具有所有者访问权限。

在迁移所有自定义查询以使用新表中的数据之前，可以选择为 AzureBackupReport 和六个新事件创建单独的诊断设置。 下图显示了采用两项诊断设置的保管库示例。 第一项设置名为 Setting1****，它以 Azure 诊断模式将 AzureBackupReport 事件的数据发送到 Log Analytics 工作区。 第二项设置名为 Setting2****，它以资源专用模式将六个新 Azure 备份事件的数据发送到 Log Analytics 工作区。

![两项设置](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> 仅** 在 Azure 诊断模式下才支持 AzureBackupReport 事件。 如果尝试以资源专用模式发送此事件的数据，则不会将任何数据传送到 Log Analytics 工作区**。

> [!NOTE]
> 仅当用户选中了“发送到 Log Analytics”时，才会显示“Azure 诊断”或“资源专用”开关**** **** ****。 若要将数据发送到存储帐户或事件中心，用户可选择所需的目标并选中任何所需事件的复选框，而无需提供任何其他输入。 同样，建议不要选择旧事件 AzureBackupReport 或更早的事件。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>将 Azure Site Recovery 事件发送到 Log Analytics

Azure 备份和 Azure Site Recovery 事件从同一个恢复服务保管库发送。 Azure Site Recovery 目前不适用于资源专用表。 对于想要将 Azure Site Recovery 事件发送到 Log Analytics 的用户，系统会指示他们仅** 使用 Azure 诊断模式，如下图所示。 为 Azure Site Recovery 事件选择资源专用模式会导致无法将所需的数据发送到 Log Analytics 工作区**。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

总结：

* 如果已使用 Azure 诊断设置了 Log Analytics 诊断，并在其顶层编写了自定义查询，请在迁移查询以使用新事件中的数据之前，保持该设置不变**。
* 如果你还想要加入到新表（我们建议这样做），请创建新的**** 诊断设置，选择“资源专用”，并选择六个新事件****。
* 如果当前正在将 Azure Site Recovery 事件发送到 Log Analytics，请不要** 为这些事件选择资源专用模式。 否则，这些事件的数据不会流入 Log Analytics 工作区。 应该创建附加的诊断设置****，选择“Azure 诊断”，然后选择相关的 Azure Site Recovery 事件。

下图显示了用户对保管库使用三项诊断设置的示例。 第一项设置名为 Setting1****，它以 Azure 诊断模式将 AzureBackupReport 事件中的数据发送到 Log Analytics 工作区。 第二项设置名为 Setting2****，它以资源专用模式将六个新 Azure 备份事件中的数据发送到 Log Analytics 工作区。 第三项设置名为 Setting3****，它以 Azure 诊断模式将 Azure Site Recovery 事件中的数据发送到 Log Analytics 工作区。

![三项设置](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>后续步骤

[了解诊断事件的 Log Analytics 数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
