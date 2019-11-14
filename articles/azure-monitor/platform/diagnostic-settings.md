---
title: 创建诊断设置以收集 Azure 中的日志和指标 |Microsoft Docs
description: 创建用于将 Azure 平台日志转发到 Azure Monitor 日志、Azure 存储或 Azure 事件中心的诊断设置。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: b90e5ccf38e95d33c4b5b6f3b8da0e91a4facb5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023739"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>创建诊断设置以在 Azure 中收集平台日志和指标
Azure 中的[平台日志](resource-logs-overview.md)提供有关 azure 资源及其所依赖的 azure 平台的详细诊断和审核信息。 本文详细介绍了如何创建和配置诊断设置，以便将平台日志收集到不同的目标。

每个 Azure 资源都需要其自己的诊断设置。 诊断设置为该资源定义了以下内容：

- 发送到设置中定义的目标的日志和指标数据的类别。 不同的资源类型的可用类别将有所不同。
- 用于发送日志的一个或多个目标。 当前目标包括 Log Analytics 工作区、事件中心和 Azure 存储。
- 存储在 Azure 存储中的数据的保留策略。
 
单个诊断设置可以定义每个目标中的一个。 如果要将数据发送到多个特定目标类型（例如，两个不同的 Log Analytics 工作区），请创建多个设置。 每个资源最多可以有5个诊断设置。

> [!NOTE]
> 活动日志可以转发到与其他平台日志相同的目标，但不会配置为具有诊断设置。 有关详细信息，请参阅[Azure 中的平台日志概述](platform-logs-overview.md#destinations)。

> [!NOTE]
> [平台指标](metrics-supported.md)会自动收集到[Azure Monitor 度量值](data-platform-metrics.md)。 可以使用诊断设置将特定 Azure 服务的指标收集到 Azure Monitor 日志中，以便通过[日志查询](../log-query/log-query-overview.md)使用其他监视数据进行分析。

## <a name="destinations"></a>Destinations 
平台日志可发送到下表中的目标。 每个目标的配置是使用与创建此文章中所述诊断设置相同的过程执行的。 按照下表中的每个链接，了解有关将数据发送到该目标的详细信息。

| 目标 | 说明 |
|:---|:---|
| [Log Analytics 工作区](resource-logs-collect-workspace.md) | 通过将日志收集到 Log Analytics 工作区中，你可以使用通过功能强大的日志查询 Azure Monitor 收集的其他监视数据对其进行分析，还可以利用其他 Azure Monitor 功能，如警报和可视化效果。 |
| [事件中心](resource-logs-stream-event-hubs.md) | 向事件中心发送日志使你能够将数据流式传输到外部系统，例如第三方 Siem 和其他 log analytics 解决方案。 |
| [Azure 存储帐户](resource-logs-collect-storage.md) | 将日志存档到 Azure 存储帐户对于审核、静态分析或备份非常有用。 |


> [!IMPORTANT]
> Azure Data Lake Storage Gen2 帐户目前不支持作为诊断设置的目标，即使它们可能在 Azure 门户中被列为有效选项。

## <a name="create-diagnostic-settings-in-azure-portal"></a>在 Azure 门户中创建诊断设置
你可以从 "Azure Monitor" 菜单或资源的菜单中配置 Azure 门户的诊断设置。

1. 从 Azure 门户中的 "Azure Monitor" 菜单中，单击 "**设置**" 下的 "**诊断设置**"，然后单击 "资源"。

    ![诊断设置](media/diagnostic-settings/menu-monitor.png)

    或者，在 Azure 门户的 "资源" 菜单中，单击 "**监视**" 下的 "**诊断设置**"。

    ![诊断设置](media/diagnostic-settings/menu-resource.png)

2. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/diagnostic-settings/add-setting.png)

   如果资源上存在设置，你将看到已配置的设置列表。 单击 "**添加诊断设置**" 添加新设置或**编辑设置**以编辑现有设置。 每个设置只能有一个目标类型。

   ![添加诊断设置 - 现有的设置](media/diagnostic-settings/edit-setting.png)

3. 为设置指定一个名称（如果没有）。
4. 选中每个目标的框以发送日志。 单击 "**配置**" 以指定其设置，如下表所述。

    | 设置 | 说明 |
    |:---|:---|
    | Log Analytics 工作区 | 工作区的名称。 |
    | 存储帐户 | 存储帐户的名称。 |
    | 事件中心命名空间 | 在其中创建事件中心的命名空间（如果这是你的第一次流式传输日志）或流式传输到（如果已有资源将日志类别流式传输到此命名空间）。
    | 事件中心名称 | 根据需要指定要在设置中发送所有数据的事件中心名称。 如果未指定名称，则将为每个日志类别创建一个事件中心。 如果要发送多个类别，可能需要指定一个名称以限制创建的事件中心的数量。 有关详细信息，请参阅[Azure 事件中心配额和限制](../../event-hubs/event-hubs-quotas.md)。 |
    | 事件中心策略名称 | 定义流式传输机制所具有的权限。 |

    ![添加诊断设置 - 现有的设置](media/diagnostic-settings/setting-details.png)

5. 选中要发送到指定目标的每个数据类别的复选框。 如果选择了 "**存档到存储帐户**" 选项，则还需指定[保持期](resource-logs-collect-storage.md#data-retention)。



> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。

4. 单击“保存”。

几分钟后，新设置会显示在此资源的设置列表中，并在生成新的事件数据时将日志流式传输到指定的目标。 请注意，发出事件的时间与[在 Log Analytics 工作区中显示](data-ingestion-time.md)的时间相比，最多可能需要15分钟。



## <a name="create-diagnostic-settings-using-powershell"></a>使用 PowerShell 创建诊断设置
使用[AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet 创建[Azure PowerShell](powershell-quickstart-samples.md)的诊断设置。 有关参数的说明，请参阅此 cmdlet 的文档。

下面是一个示例 PowerShell cmdlet，用于创建使用所有三个目标的诊断设置。


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>使用 Azure CLI 创建诊断设置
使用[az monitor diagnostics settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create)命令创建具有[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)的诊断设置。 有关其参数的说明，请参阅此命令的文档。

下面是一个示例 CLI 命令，用于创建使用所有三个目标的诊断设置。



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>使用 REST API 配置诊断设置
请参阅[诊断设置](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)，使用[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)创建或更新诊断设置。


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>使用资源管理器模板配置诊断设置
请参阅[使用资源管理器模板在创建资源时自动启用诊断设置](diagnostic-settings-template.md)，以使用资源管理器模板创建或更新诊断设置。

## <a name="next-steps"></a>后续步骤

* [阅读有关 Azure 平台日志的详细信息](resource-logs-overview.md)