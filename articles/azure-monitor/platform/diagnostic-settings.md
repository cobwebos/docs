---
title: 使用诊断设置在 Azure 中收集平台指标和日志
description: 使用诊断设置将 Azure Monitor 平台指标和日志发送到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: cbef0244f30a7cf14f8fea4c6a445cf0de662dc4
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737889"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>创建诊断设置以收集 Azure 中的资源日志和指标

Azure 中的[平台日志](platform-logs-overview.md)，包括 azure 活动日志和资源日志，提供 azure 资源及其所依赖的 azure 平台的详细诊断和审核信息。 [平台指标](data-platform-metrics.md)在默认情况下收集，通常存储在 Azure Monitor 度量值数据库中。

本文详细介绍了如何创建和配置诊断设置，以便将平台指标和平台日志发送到不同的目标。

> [!IMPORTANT]
> 应先禁用任何旧的配置，然后再创建用于收集活动日志的诊断设置。 有关详细信息，请参阅[通过旧版设置收集 Azure 活动日志](diagnostic-settings-legacy.md)。

每个 Azure 资源都需要其自己的诊断设置，该设置定义以下标准：

- 发送到设置中所定义目标的日志和指标数据的类别。 不同资源类型的可用类别各不相同。
- 要将日志发送到的一个或多个目标。 当前目标包括 Log Analytics 工作区、事件中心和 Azure 存储。

一个诊断设置只能为每个目标定义一种类型。 若要将数据发送到多个特定的目标类型（例如，两个不同的 Log Analytics 工作区），请创建多个设置。 每个资源最多可以有 5 个诊断设置。

> [!NOTE]
> [平台指标](metrics-supported.md)自动收集到 [Azure Monitor 指标](data-platform-metrics.md)中。 可以使用诊断设置将特定 Azure 服务的指标收集到 Azure Monitor 日志中，以便使用具有特定限制的[日志查询](../log-query/log-query-overview.md)的其他监视数据进行分析。 
>  
>  
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。 *例如*：区块链上的 "IOReadBytes" 指标可在每个节点级别上浏览和绘制图表。 但是，在通过诊断设置导出时，导出的度量值表示为所有节点的所有读取字节数。 此外，由于内部限制，所有指标都无法导出到 Azure Monitor 日志/Log Analytics。 有关详细信息，请参阅可[导出指标的列表](metrics-supported-export-diagnostic-settings.md)。 
>  
>  
> 若要解决特定指标的这些限制，建议使用[度量值](https://docs.microsoft.com/rest/api/monitor/metrics/list)手动提取它们 REST API，并使用[Azure Monitor 数据收集器 API](data-collector-api.md)将它们导入 Azure Monitor 日志。  

## <a name="destinations"></a>Destinations

平台日志和指标可发送到下表中的目标。 有关将数据发送到该目标的详细信息，请参阅下表中的每个链接。

| 目标 | 说明 |
|:---|:---|
| [Log Analytics 工作区](resource-logs-collect-workspace.md) | 通过将日志和度量值收集到 Log Analytics 工作区中，你可以使用通过功能强大的日志查询 Azure Monitor 收集的其他监视数据对其进行分析，还可以利用其他 Azure Monitor 功能，如警报和可视化效果。 |
| [事件中心](resource-logs-stream-event-hubs.md) | 通过向事件中心发送日志和指标，可将数据流式传输到外部系统，例如第三方 Siem 和其他 log analytics 解决方案。 |
| [Azure 存储帐户](resource-logs-collect-storage.md) | 将日志和指标存档到 Azure 存储帐户对于审核、静态分析或备份非常有用。 与 Azure Monitor 日志和 Log Analytics 工作区相比，Azure 存储空间开销较低，日志可能会无限期保留。 |

## <a name="create-diagnostic-settings-in-azure-portal"></a>在 Azure 门户中创建诊断设置

可以在 Azure 门户中通过“Azure Monitor”菜单或资源菜单配置诊断设置。

1. 在 Azure 门户中配置诊断设置的位置取决于资源。

   - 对于单项资源，在资源菜单中的“监视器”下，单击“诊断设置”。********

        ![诊断设置](media/diagnostic-settings/menu-resource.png)

   - 对于一项或多项资源，在 Azure Monitor 菜单中，单击“设置”下的“诊断设置”，然后单击相应资源。********

      ![诊断设置](media/diagnostic-settings/menu-monitor.png)

   - 对于活动日志，在“Azure Monitor”菜单中，单击“活动日志”，然后单击“诊断设置”。************ 请确保禁用活动日志的任何旧配置。 有关详细信息，请参阅[禁用现有设置](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log)。

        ![诊断设置](media/diagnostic-settings/menu-activity-log.png)

2. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“添加诊断设置”****。

   ![添加诊断设置 - 没有现有的设置](media/diagnostic-settings/add-setting.png)

   如果资源上存在设置，则会看到已配置的设置列表。 单击“添加诊断设置”以添加新设置，或单击“编辑设置”以编辑现有设置。******** 每个设置最多只能包含一个目标类型。

   ![添加诊断设置 - 现有的设置](media/diagnostic-settings/edit-setting.png)

3. 为设置指定名称（如果未指定）。

    ![添加诊断设置](media/diagnostic-settings/setting-new-blank.png)

4. **类别详细信息（要路由的内容）** -选中要发送到稍后指定的目标的每个数据类别的框。 每个 Azure 服务的类别列表有所不同。

     - **AllMetrics**将资源的平台指标路由到 Azure 日志存储区，但采用日志格式。 通常，这些指标只发送到 Azure Monitor 度量时间系列数据库。 将它们发送到 Azure Monitor 日志存储区（可通过 Log Analytics 搜索），将其集成到跨其他日志进行搜索的查询中。 此选项可能不适用于所有资源类型。 支持时， [Azure Monitor 支持的指标](metrics-supported.md)会列出为哪些资源类型收集了哪些指标。

       > [!NOTE]
       > 请参阅本文前面 Azure Monitor 日志中的路由指标的 limitatation。  


     - **日志**列出了可用的不同类别，具体取决于资源类型。 检查想要路由到目标的所有类别。

5. **目标详细信息**-选中每个目标对应的复选框。 选中每个框时，将显示选项，以允许你添加其他信息。

      ![发送到 Log Analytics 或事件中心](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -输入订阅和工作区。  如果没有工作区，则需要在[继续操作之前创建一个](../learn/quick-create-workspace.md)工作区。

    1. **事件中心**-指定以下条件：
       - 事件中心所属的订阅
       - 事件中心命名空间-如果你还没有，则需要[创建一个](../../event-hubs/event-hubs-create.md)
       - 要将所有数据发送到的事件中心名称（可选）。 如果未指定名称，将为每个日志类别创建一个事件中心。 如果发送多个类别，可能需要指定一个名称来限制创建的事件中心数。 有关详细信息，请参阅 [Azure 事件中心配额和限制](../../event-hubs/event-hubs-quotas.md)。
       - 事件中心策略（可选）策略定义流式处理机制具有的权限。 有关详细信息，请参阅[事件中心-功能](../../event-hubs/event-hubs-features.md#publisher-policy)。

    1. **存储**-选择订阅、存储帐户和保留策略。

        ![发送到存储](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 考虑将保留策略设置为0，并使用计划的作业从存储中手动删除数据，以避免将来可能会造成混淆。
        >
        > 首先，如果要使用存储进行存档，通常会将数据大约超过365天。 第二种情况下，如果选择的保留策略大于0，则在存储时将到期日期附加到日志。 存储后，不能更改这些日志的日期。
        >
        > 例如，如果你将*WorkflowRuntime*的保留策略设置为180天，而在24小时后将其设置为365天，则在180天后会自动删除在这前24小时内存储的日志，而该类型的所有后续日志将在365天后自动删除。 稍后更改保留策略不会使前24小时的日志保留365天。

6. 单击“保存”  。

片刻之后，新设置会显示在此资源的设置列表中，生成新的事件数据后，日志会立即流式传输到指定的目标。 事件发出后，最多可能需要15分钟时间才[会出现在 Log Analytics 工作区中](data-ingestion-time.md)。

## <a name="create-diagnostic-settings-using-powershell"></a>使用 PowerShell 创建诊断设置

在 [Azure PowerShell](powershell-quickstart-samples.md) 中使用 [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet 创建诊断设置。 有关参数说明，请参阅此 cmdlet 的文档。

> [!IMPORTANT]
> 不能将此方法用于 Azure 活动日志。 请改为利用[使用资源管理器模板在 Azure Monitor 中创建诊断设置](diagnostic-settings-template.md)，创建资源管理器模板并使用 PowerShell 进行部署。

以下示例 PowerShell cmdlet 使用所有三个目标创建诊断设置。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>使用 Azure CLI 创建诊断设置

在 [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) 中使用 [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 命令创建诊断设置。 有关参数说明，请参阅此命令的文档。

> [!IMPORTANT]
> 不能将此方法用于 Azure 活动日志。 相反，请使用[Azure Monitor 中资源管理器的 "创建诊断" 设置](diagnostic-settings-template.md)来创建资源管理器模板并使用 CLI 对其进行部署。

以下示例 CLI 命令使用所有三个目标创建诊断设置。

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>使用 REST API 配置诊断设置

若要使用 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 创建或更新诊断设置，请参阅[诊断设置](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)。

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>使用资源管理器模板配置诊断设置

若要使用资源管理器模板创建或更新诊断设置，请参阅[使用资源管理器模板在 Azure Monitor 中创建诊断设置](diagnostic-settings-template.md)。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 平台日志](platform-logs-overview.md)
