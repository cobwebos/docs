---
title: 使用诊断设置收集平台指标和日志，并在 Azure 中收集
description: 使用诊断设置将 Azure 监视器平台指标和日志发送到 Azure 监视器日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681165"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>创建诊断设置以在 Azure 中收集资源日志和指标

Azure 中[的平台日志](platform-logs-overview.md)（包括 Azure 活动日志和资源日志）为 Azure 资源和它们所依赖的 Azure 平台提供详细的诊断和审核信息。 [默认情况下收集平台指标](data-platform-metrics.md)，通常存储在 Azure 监视器指标数据库中。

本文详细介绍了创建和配置诊断设置，以便将平台指标和平台日志发送到不同目标。

> [!IMPORTANT]
> 应先禁用任何旧的配置，然后再创建用于收集活动日志的诊断设置。 有关详细信息，请参阅[通过旧版设置收集 Azure 活动日志](diagnostic-settings-legacy.md)。

每个 Azure 资源都需要其自己的诊断设置，该设置定义以下条件：

- 发送到设置中所定义目标的日志和指标数据的类别。 不同资源类型的可用类别各不相同。
- 要将日志发送到的一个或多个目标。 当前目标包括 Log Analytics 工作区、事件中心和 Azure 存储。

一个诊断设置只能为每个目标定义一种类型。 若要将数据发送到多个特定的目标类型（例如，两个不同的 Log Analytics 工作区），请创建多个设置。 每个资源最多可以有 5 个诊断设置。

> [!NOTE]
> [平台指标](metrics-supported.md)自动收集到 [Azure Monitor 指标](data-platform-metrics.md)中。 使用诊断设置可将特定 Azure 服务的指标收集到 Azure Monitor 日志中，以使用[日志查询](../log-query/log-query-overview.md)结合其他监视数据进行分析。

## <a name="destinations"></a>Destinations

平台日志和指标可以发送到下表中的目标。 有关将数据发送到该目标的详细信息，请参阅下表中的每个链接。

| 目标 | 说明 |
|:---|:---|
| [Log Analytics 工作区](resource-logs-collect-workspace.md) | 通过将日志和指标收集到日志分析工作区中，您可以使用 Azure 监视器使用强大的日志查询收集的其他监视数据进行分析，并利用其他 Azure 监视器功能（如警报和可视化）。 |
| [事件中心](resource-logs-stream-event-hubs.md) | 向事件中心发送日志和指标允许您将数据流式传输到外部系统，如第三方 SIEM 和其他日志分析解决方案。 |
| [Azure 存储帐户](resource-logs-collect-storage.md) | 将日志和指标存档到 Azure 存储帐户对于审核、静态分析或备份非常有用。 与 Azure 监视器日志和日志分析工作区相比，Azure 存储成本较低，日志可以无限期地保留在那里。 |

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

   如果资源上有现有设置，您将看到已配置的设置列表。 单击“添加诊断设置”以添加新设置，或单击“编辑设置”以编辑现有设置。******** 每个设置最多只能包含一个目标类型。

   ![添加诊断设置 - 现有的设置](media/diagnostic-settings/edit-setting.png)

3. 为设置指定名称（如果未指定）。

    ![添加诊断设置](media/diagnostic-settings/setting-new-blank.png)

4. **类别详细信息（路由内容）** - 选中要发送到以后指定的目标的每个数据类别的复选框。 类别列表因每个 Azure 服务而异。

     - **AllMetrics**将资源的平台指标路由到 Azure 日志存储，但以日志形式进行。 这些指标通常仅发送到 Azure 监视器指标时间序列数据库。 将它们发送到 Azure 监视器日志存储（可通过日志分析进行搜索），以便将它们集成到跨其他日志搜索的查询中。 此选项可能不适用于所有资源类型。 当它受支持时[，Azure 监视器支持的指标](metrics-supported.md)会列出为哪些资源类型收集哪些指标。

       > [!NOTE]
       > 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
       >
       > *例如*：区块链上的"IORead字节"指标可以探索，并绘制每个节点级别的图表。 但是，当通过诊断设置导出时，导出的指标表示为所有节点的所有读取字节。

     - **日志**列出不同的类别可用，具体取决于资源类型。 检查要路由到目标的任何类别。

5. **目的地详细信息**- 选中每个目的地的复选框。 选中每个框时，将显示选项以允许您添加其他信息。

      ![发送到日志分析或事件中心](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **日志分析**- 输入订阅和工作区。  如果没有工作区，则需要[在继续 之前创建一个](../learn/quick-create-workspace.md)工作区。

    1. **事件中心**- 指定以下条件：
       - 事件中心参与的订阅
       - 事件中心命名空间 - 如果还没有，则需要[创建一个](../../event-hubs/event-hubs-create.md)
       - 要将所有数据发送到的事件中心名称（可选）。 如果未指定名称，将为每个日志类别创建一个事件中心。 如果发送多个类别，可能需要指定一个名称来限制创建的事件中心数。 有关详细信息，请参阅 [Azure 事件中心配额和限制](../../event-hubs/event-hubs-quotas.md)。
       - 事件中心策略（可选）策略定义流式处理机制具有的权限。 有关详细信息，请参阅[事件中心功能](../../event-hubs/event-hubs-features.md#publisher-policy)。

    1. **存储**- 选择订阅、存储帐户和保留策略。

        ![发送到存储](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 请考虑将保留策略设置为 0，并使用计划作业手动从存储中删除数据，以避免将来可能出现的混淆。
        >
        > 首先，如果您使用的存储用于存档，则通常希望数据在 365 天内进行。 其次，如果选择大于 0 的保留策略，则到期日期在存储时附加到日志。 存储后无法更改这些日志的日期。
        >
        > 例如，如果将*WorkflowRuntime*的保留策略设置为 180 天，然后在 24 小时后将其设置为 365 天，则前 24 小时内存储的日志将在 180 天后自动删除，而该类型的所有后续日志将在 365 天后自动删除。 以后更改保留策略不会使日志的前 24 小时保留 365 天。

6. 单击“ **保存**”。

片刻之后，新设置会显示在此资源的设置列表中，生成新的事件数据后，日志会立即流式传输到指定的目标。 从发出事件到[在日志分析工作区中出现](data-ingestion-time.md)之间可能需要长达 15 分钟的时间。

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
> 不能将此方法用于 Azure 活动日志。 相反，[请使用使用资源管理器模板在 Azure 监视器中创建诊断设置](diagnostic-settings-template.md)来创建资源管理器模板，并将其与 CLI 一起部署。

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
