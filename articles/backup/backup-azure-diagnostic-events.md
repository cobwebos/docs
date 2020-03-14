---
title: 使用恢复服务保管库的诊断设置
description: 介绍如何使用 Azure 备份的新旧诊断事件的文章
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e3919d120e5f741af6cd30dd27e5a1dfa2b06cf2
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136933"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>使用恢复服务保管库的诊断设置

Azure 备份发送诊断事件，这些事件可收集并用于分析、警报和报告目的。 

通过导航到保管库并单击 "**诊断设置**" 菜单项，可以通过 Azure 门户配置恢复服务保管库的诊断设置。 单击 " **+ 添加诊断设置**" 可将一个或多个诊断事件发送到存储帐户、事件中心或 LOG ANALYTICS （LA）工作区。

![诊断设置边栏选项卡](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 备份用户可用的诊断事件

Azure 备份提供以下诊断事件，每个事件都提供一组特定的备份相关项目的详细数据：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Azure 备份诊断事件的数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

这些事件的数据可以发送到存储帐户、LA 工作区或事件中心。 如果要将此数据发送到 "LA" 工作区，则需要在 "**诊断设置**" 屏幕中选择**特定于资源**的切换（有关详细信息，请参阅以下部分）。

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>将诊断设置用于 Log Analytics （LA）

利用 azure Log Analytics 路线图，Azure 备份现在允许将保管库诊断数据发送到专用的 LA 表以供备份。 这些表称为 "[资源特定表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)"。

将保管库诊断数据发送到 LA：

1.  导航到保管库，并单击 "**诊断设置**"。 单击 " **+ 添加诊断设置**"。
2.  为诊断设置指定一个名称。
3.  选中 "**发送到 Log Analytics** " 框，然后选择 "Log Analytics" 工作区。
4.  在切换时选择 "**资源特定**"，并检查以下六个事件- **CoreAzureBackup**、 **AddonAzureBackupAlerts**、 **AddonAzureBackupProtectedInstance**、 **AddonAzureBackupJobs**、 **AddonAzureBackupPolicy**和**AddonAzureBackupStorage**。
5.  单击“保存”。

![资源特定模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

数据流进入 LA 工作区后，将在工作区中创建每个事件的专用表。 如果需要，可以直接查询其中的任何表，并在这些表之间执行联接或联合。

> [!IMPORTANT]
> 以上六个事件，即 CoreAzureBackup、AddonAzureBackupAlerts、AddonAzureBackupProtectedInstance、AddonAzureBackupJobs、AddonAzureBackupPolicy 和 AddonAzureBackupStorage**仅**在资源特定模式下受支持。 **请注意，如果尝试在 Azure 诊断模式下发送这六个事件的数据，则不会将任何数据流向 LA 工作区。**

## <a name="legacy-event"></a>旧事件

通常，保管库的所有与备份相关的诊断数据都包含在一个名为 "AzureBackupReport" 的事件中。 上述六个事件实质上是 AzureBackupReport 中包含的所有数据的分解。 

目前，在用户对此事件具有现有自定义查询的情况下（例如，自定义日志警报、自定义可视化效果等），我们将继续支持 AzureBackupReport 事件以获得向后兼容性。但是，**我们建议尽快迁移到新事件**，因为这样可以使数据更易于在日志查询中使用，从而可以更好地发现架构及其结构，从而提高引入延迟和查询时间的性能。 **对使用 Azure 诊断模式的支持最终将会逐步推出，因此选择新事件可能会帮助你在以后避免复杂的迁移**。

使用 Azure 备份的内置策略为指定范围内的所有保管库添加包含6个新事件的新诊断设置：[大规模配置保管库诊断设置](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

你可以选择为 AzureBackupReport 和六个新事件创建单独的诊断设置，直到你将所有自定义查询迁移到使用新表中的数据。 下图显示了一个具有两个诊断设置的保管库的示例。 名为**Setting1**的第一个设置在 AzureDiagnostics 模式下将 AzureBackupReport 事件的数据发送到 LA 工作区。 名为**Setting2**的第二个设置将六个新的 Azure 备份事件的数据发送到 "资源特定" 模式下的 "LA" 工作区。

![两个设置](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> **仅**Azure 诊断模式下支持 AzureBackupReport 事件。 **请注意，如果在 "资源特定" 模式下尝试发送此事件的数据，则不会有任何数据流向 LA 工作区。**

> [!NOTE]
> 仅当用户检查 "**发送到 Log Analytics**" 时，才会显示特定于 Azure 诊断/资源的切换。 若要将数据发送到存储帐户或事件中心，用户可以直接选择所需的目标，并检查任何所需的事件，而无需任何其他输入。 同样，建议不要选择旧的事件 AzureBackupReport。

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>向 Log Analytics （LA）发送 Azure Site Recovery 事件的用户

Azure 备份和 Azure Site Recovery 事件是从同一恢复服务保管库发送的。 由于 Azure Site Recovery 当前未载入到特定于资源的表，因此希望将 Azure Site Recovery 事件发送到 LA 的用户将被定向到**仅**使用 Azure 诊断模式（请参阅下图）。 **为 Azure Site Recovery 事件选择资源特定模式将阻止所需的数据发送到 LA 工作区**。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

汇总上述细微差别：

* 如果已使用 Azure 诊断设置了 LA 诊断，并在其顶部编写了自定义查询，请将此设置保持不**变**，直到将查询迁移到使用新事件中的数据。
* 如果还想要载入新表（如建议），请创建**新**的诊断设置，选择 "**资源特定**"，并选择上面指定的六个新事件。
* 如果当前正在向 LA 发送 Azure Site Recovery 事件，请**不要为这些事件选择资源**特定模式，否则这些事件的数据将不会流入你的 LA 工作区。 相反，请创建**其他诊断设置**，选择**Azure 诊断**，然后选择相关的 Azure Site Recovery 事件。

下图显示了一个具有保管库的三个诊断设置的用户示例。 名为**Setting1**的第一个设置在 AzureDiagnostics 模式下将数据从 AzureBackupReport 事件发送到 LA 工作区。 名为 " **Setting2** " 的第二个设置将数据从六个新的 Azure 备份事件发送到 "资源特定" 模式下的 "LA" 工作区。 第三个名为 " **Setting3**" 的设置将 Azure Site Recovery 事件中的数据发送到 Azure 诊断模式下的 "LA" 工作区。

![三个设置](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>后续步骤

[了解诊断事件的 Log Analytics 数据模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
