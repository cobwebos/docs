---
title: 创建诊断设置以将平台日志和指标发送到不同的目标
description: 通过使用某个诊断设置，将 Azure Monitor 平台指标和日志发送到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 74e0a63da87a79cbd582cd6da5992251fc256504
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135430"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>创建诊断设置以将平台日志和指标发送到不同的目标
Azure 中的[平台日志](platform-logs-overview.md)（包括 Azure 活动日志和资源日志）提供 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。 默认情况下会收集[平台指标](data-platform-metrics.md)，它们通常存储在 Azure Monitor 指标数据库中。 本文详细介绍如何创建和配置诊断设置，以将平台指标和平台日志发送到不同的目标。

> [!IMPORTANT]
> 应先禁用任何旧的配置，然后再为活动日志创建诊断设置。 有关详细信息，请参阅[旧式收集方法](activity-log.md#legacy-collection-methods)。

每个 Azure 资源都需有自身的诊断设置，其设置定义了以下条件：

- 发送到设置中所定义目标的日志和指标数据的类别。 不同资源类型的可用类别各不相同。
- 要将日志发送到的一个或多个目标。 当前目标包括 Log Analytics 工作区、事件中心和 Azure 存储。

一个诊断设置只能为每个目标定义一种类型。 若要将数据发送到多个特定的目标类型（例如，两个不同的 Log Analytics 工作区），请创建多个设置。 每个资源最多可以有 5 个诊断设置。

以下视频演示如何使用诊断设置来路由平台日志。
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [平台指标](metrics-supported.md)自动发送到 [Azure Monitor 指标](data-platform-metrics.md)中。 使用诊断设置可将特定 Azure 服务的指标发送到 Azure Monitor 日志中，以使用具有特定限制的[日志查询](../log-query/log-query-overview.md)结合其他监视数据进行分析。 
>  
>  
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。 例如：可以在每个节点级别浏览区块链上的“IOReadBytes”指标并为其绘制图表。 但是，当通过诊断设置导出时，导出的指标将表示为所有节点的所有读取字节数。 此外，由于内部限制，并非所有指标都可以导出到 Azure Monitor 日志/Log Analytics。 有关详细信息，请参阅[可导出指标的列表](metrics-supported-export-diagnostic-settings.md)。 
>  
>  
> 若要解决特定指标的这些限制，建议你使用[指标 REST API](/rest/api/monitor/metrics/list) 手动提取它们并使用 [Azure Monitor 数据收集器 API](data-collector-api.md) 将其导入到 Azure Monitor 日志中。  


## <a name="destinations"></a>Destinations
平台日志和指标可以发送到下表中列出的目标。 

| 目标 | 说明 |
|:---|:---|
| [Log Analytics 工作区](design-logs-deployment.md) | 将日志和指标发送到 Log Analytics 工作区可以使用强大的日志查询结合 Azure Monitor 收集的其他监视数据对其进行分析，并利用其他 Azure Monitor 功能，例如警报和可视化。 |
| [事件中心](/azure/event-hubs/) | 向事件中心发送日志和指标可将数据流式传输到外部系统，例如第三方 SIEM 和其他日志分析解决方案。  |
| [Azure 存储帐户](/azure/storage/blobs/) | 将日志和指标存档到 Azure 存储帐户有助于审核、静态分析或备份。 与 Azure Monitor 日志和 Log Analytics 工作区相比，Azure 存储成本较低，并且日志可以无限期保留。  |


### <a name="destination-requirements"></a>目标要求

在创建诊断设置之前，必须创建诊断设置的任何目标。 只要配置设置的用户具有对这两个订阅的相应 RBAC 访问权限，目标就不必与资源发送日志位于同一订阅中。 下表为每个目标提供了独特的要求，包括任何区域限制。

| 目标 | 要求 |
|:---|:---|
| Log Analytics 工作区 | 工作区不需要与所监视的资源位于同一区域。|
| 事件中心 | 命名空间的共享访问策略定义流式处理机制具有的权限。 流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 若要更新诊断设置，使之包括流式传输，则必须在事件中心授权规则中拥有 ListKey 权限。<br><br>如果资源是区域，事件中心命名空间必须与受监视的资源位于同一区域。 |
| Azure 存储帐户 | 不应使用其中存储了其他非监视数据的现有存储帐户，以便更好地控制数据所需的访问权限。 不过，如果要将活动日志和资源日志一同存档，则可以选择使用该存储帐户在一个中心位置保留所有监视数据。<br><br>若要将数据发送到不可变存储，请按照[为 Blob 存储设置和管理不可变策略](../../storage/blobs/storage-blob-immutability-policies-manage.md)中所述为存储帐户设置不可变策略。 必须按照本文中的所有步骤操作，包括启用受保护的追加 blob 写入操作。<br><br>如果资源是区域，则存储帐户必须与受监视的资源位于同一区域。 |

> [!NOTE]
> Azure Data Lake Storage Gen2 帐户目前不支持作为诊断设置的目标，即使它们可能在 Azure 门户中被列为有效选项。



## <a name="create-in-azure-portal"></a>在 Azure 门户中创建

可以在 Azure 门户中通过“Azure Monitor”菜单或资源菜单配置诊断设置。

1. 在 Azure 门户中配置诊断设置的位置取决于资源。

   - 对于单项资源，在资源菜单中的“监视器”下，单击“诊断设置”。 

        ![诊断设置](media/diagnostic-settings/menu-resource.png)

   - 对于一项或多项资源，在 Azure Monitor 菜单中，单击“设置”下的“诊断设置”，然后单击相应资源。 

      ![诊断设置](media/diagnostic-settings/menu-monitor.png)

   - 对于活动日志，在“Azure Monitor”菜单中，单击“活动日志”，然后单击“诊断设置”。   请确保禁用活动日志的任何旧配置。 有关详细信息，请参阅[禁用现有设置](./activity-log.md#legacy-collection-methods)。

        ![诊断设置](media/diagnostic-settings/menu-activity-log.png)

2. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“添加诊断设置”。

   ![添加诊断设置 - 没有现有的设置](media/diagnostic-settings/add-setting.png)

   如果资源上有现有的设置，则会看到已配置的设置列表。 单击“添加诊断设置”以添加新设置，或单击“编辑设置”以编辑现有设置。  每个设置最多只能包含一个目标类型。

   ![添加诊断设置 - 现有的设置](media/diagnostic-settings/edit-setting.png)

3. 为设置指定名称（如果未指定）。

    ![添加诊断设置](media/diagnostic-settings/setting-new-blank.png)

4. **类别详细信息(要路由的内容)** - 选中要发送到稍后指定的目标的每个数据类别对应的框。 每种 Azure 服务的类别列表各不相同。

     - **所有指标** - 将资源的平台指标路由到 Azure 日志存储，但采用日志格式。 这些指标平常只发送到 Azure Monitor 指标时序数据库。 将它们发送到 Azure Monitor 日志存储（可通过 Log Analytics 进行搜索），以将它们集成到跨其他日志进行搜索的查询中。 此选项不一定适用于所有资源类型。 当受支持时，[Azure Monitor 支持的指标](metrics-supported.md)会列出为具体资源类型收集的具体指标。

       > [!NOTE]
       > 请参阅本文前面部分介绍的将指标路由到 Azure Monitor 日志时的限制。  


     - **日志** - 列出根据资源类型提供的各种类别。 请选中你希望路由到目标的所有类别。

5. **目标详细信息** - 选中每个目标对应的框。 当选中每个框时，将显示用于添加其他信息的选项。

      ![发送到 Log Analytics 或事件中心](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - 输入订阅和工作区。  如果没有工作区，则需[在继续操作之前创建一个](../learn/quick-create-workspace.md)。

    1. **事件中心** - 指定以下条件：
       - 事件中心所属的订阅
       - 事件中心命名空间 - 如果还没有，则需[创建一个](../../event-hubs/event-hubs-create.md)
       - 要向其发送所有数据的事件中心的名称（可选）。 如果未指定名称，将为每个日志类别创建一个事件中心。 如果发送多个类别，可能需要指定一个名称来限制创建的事件中心数。 有关详细信息，请参阅 [Azure 事件中心配额和限制](../../event-hubs/event-hubs-quotas.md)。
       - 事件中心策略（可选）- 策略定义流式处理机制具有的权限。 有关详细信息，请参阅 [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy)。

    1. **存储** - 选择订阅、存储帐户和保留策略。

        ![发送到存储](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 请考虑将保留策略设置为 0，并使用计划的作业手动从存储中删除数据，避免将来可能会造成的混乱。
        >
        > 首先，如果要使用存储进行存档，你通常希望数据保留 365 天以上。 其次，如果选择的保留策略大于 0，则在存储时会向日志附加一个到期日期。 日志一旦存储，则无法为其更改该日期。
        >
        > 例如，如果将 *WorkflowRuntime* 的保留策略设置为 180 天，24 小时后又将其设置为 365 天，则在前 24 小时内存储的日志将在 180 天后自动删除，而该类型的所有后续日志将在 365 天后自动删除。 后来进行的保留策略更改不会使前 24 小时的日志保留 365 天。

6. 单击“保存” 。

片刻之后，新设置会显示在此资源的设置列表中，生成新的事件数据后，日志会立即流式传输到指定的目标。 发出事件后可能需要长达 15 分钟的时间该事件才会[出现在 Log Analytics 工作区中](data-ingestion-time.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 创建

在 [Azure PowerShell](../samples/powershell-samples.md) 中使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet 创建诊断设置。 有关参数说明，请参阅此 cmdlet 的文档。

> [!IMPORTANT]
> 不能将此方法用于 Azure 活动日志。 请改为利用[使用资源管理器模板在 Azure Monitor 中创建诊断设置](diagnostic-settings-template.md)，创建资源管理器模板并使用 PowerShell 进行部署。

以下示例 PowerShell cmdlet 使用所有三个目标创建诊断设置。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>使用 Azure CLI 创建

在 [Azure CLI](/cli/azure/monitor?view=azure-cli-latest) 中使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 命令创建诊断设置。 有关参数说明，请参阅此命令的文档。

> [!IMPORTANT]
> 不能将此方法用于 Azure 活动日志。 请改为按[使用资源管理器模板在 Azure Monitor 中创建诊断设置](diagnostic-settings-template.md)中的说明操作，创建资源管理器模板并使用 CLI 进行部署。

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

## <a name="create-using-resource-manager-template"></a>使用资源管理器模板创建
若要使用资源管理器模板创建或更新诊断设置，请参阅 [Azure Monitor 中的诊断设置的资源管理器模板示例](../samples/resource-manager-diagnostic-settings.md)。

## <a name="create-using-rest-api"></a>使用 REST API 创建
若要使用 [Azure Monitor REST API](/rest/api/monitor/) 创建或更新诊断设置，请参阅[诊断设置](/rest/api/monitor/diagnosticsettings)。

## <a name="create-using-azure-policy"></a>使用 Azure Policy 创建
由于需要为每个 Azure 资源创建诊断设置，因此在创建每个资源时，可以使用 Azure Policy 来自动创建诊断设置。 有关详细信息，请参阅[使用 Azure Policy 大规模部署 Azure Monitor](../deploy-scale.md)。


## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 平台日志](platform-logs-overview.md)
