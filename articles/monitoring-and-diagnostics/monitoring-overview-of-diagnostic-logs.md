---
title: Azure 诊断日志概述
description: 了解什么是 Azure 诊断日志，以及如何使用该诊断日志了解发生在 Azure 资源内的事件。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 9d2a20ce681ea7e7c4ff2f9b492653e9d9a57b2b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248160"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>从 Azure 资源收集和使用日志数据

## <a name="what-are-azure-monitor-diagnostic-logs"></a>什么是 Azure Monitor 诊断日志

**Azure Monitor 诊断日志**是 Azure 服务发出的日志，提供与该服务的操作相关的各种频繁生成的数据。 Azure Monitor 提供两种类型的诊断日志：
* **租户日志** - 这些日志来自 Azure 订阅之外存在的租户级服务，例如 Azure Active Directory 日志。
* **资源日志** - 这些日志来自在 Azure 订阅中部署资源的 Azure 服务，例如网络安全组或存储帐户。

    ![资源诊断日志与其他类型的日志 ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

这些日志的内容因 Azure 服务和资源类型而异。 例如，网络安全组规则计数器和 Key Vault 审核是两种类型的诊断日志。

这些日志与[活动日志](monitoring-overview-activity-logs.md)不同。 通过活动日志，可以深入了解使用资源管理器在订阅中的资源上执行的操作，例如创建虚拟机或删除逻辑应用。 活动日志是一个订阅级别的日志。 通过资源级诊断日志深入了解在资源本身内执行的操作，例如，从 Key Vault 获取机密。

这些日志也与来宾 OS 级诊断日志不同。 来宾 OS 级诊断日志是由在虚拟机内部或其他受支持的资源类型中运行的代理收集的日志。 资源级诊断日志不需要代理并从 Azure 平台本身捕获特定于资源的数据，而来宾 OS 级诊断日志从操作系统和在虚拟机上运行的应用程序捕获数据。

并非所有服务都支持此处所述的诊断日志。 [本文包含一个列出哪些服务支持诊断日志的部分](./monitoring-diagnostic-logs-schema.md)。

## <a name="what-you-can-do-with-diagnostic-logs"></a>可以对诊断日志执行的操作
可以对诊断日志执行的部分操作如下：

![诊断日志的逻辑位置](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* 将诊断日志保存到[**存储帐户**](monitoring-archive-diagnostic-logs.md)进行审核或手动检查。 可以使用**资源诊断设置**指定保留时间（天）。
* [将诊断日志流式传输到**事件中心**](monitoring-stream-diagnostic-logs-to-event-hubs.md)，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。
* 使用 [Log Analytics](../log-analytics/log-analytics-azure-storage.md) 对诊断日志进行分析

可以使用与发出日志的订阅不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有合适的 RBAC 访问权限。

> [!NOTE]
>  当前无法将数据存档到安全虚拟网络中的存储帐户。

> [!WARNING]
> 存储帐户中日志数据的格式将在 2018 年 11 月 1 日更改为 JSON Lines。 [请参阅此文章来了解此影响，以及如何通过更新工具来处理新格式。](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="diagnostic-settings"></a>诊断设置

使用资源诊断设置配置资源诊断日志。 使用租户诊断设置配置租户诊断日志。 用于服务控制的**诊断设置**：

* 将诊断日志和指标发送到的位置（存储帐户、事件中心和/或 Log Analytics）。
* 发送哪些日志类别，是否也会发送指标数据。
* 应该将每个日志类别在存储帐户中保留多长时间
    - 保留期为零天表示日志将永久保留。 如果不需永久保留，则可将该值设置为 1 到 2147483647 之间的任意天数。
    - 如果设置了保留策略，但禁止将日志存储在存储帐户中（例如，如果仅选择了“事件中心”或“Log Analytics”选项），则保留策略无效。
    - 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。 删除过程从 UTC 晚上 12 点开始，但请注意，可能需要最多 24 小时才能将日志从存储帐户中删除。

这些设置可以通过门户中的诊断设置、Azure PowerShell 和 CLI 命令或 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 轻松进行配置。

> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>如何启用诊断日志的收集

可以[在资源管理器模板中创建资源的过程中](./monitoring-enable-diagnostic-logs-using-template.md)或在创建资源后通过门户中该资源的页面启用诊断日志的收集。 也可使用 Azure PowerShell 或 CLI 命令（或者使用 Azure Monitor REST API）在任何时间点启用集合。

> [!TIP]
> 这些说明可能不能直接应用到每个资源。 请参阅此页底部的架构链接，了解适用于特定资源类型的特殊步骤。

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>在门户中启用诊断日志的收集

可以在创建资源后，在 Azure 门户中通过转到特定的资源或导航到 Azure Monitor 来启用资源诊断日志的收集。 通过 Azure Monitor 启用日志收集：

1. 在 [Azure 门户](http://portal.azure.com)中，导航到 Azure Monitor 并单击“诊断设置”

    ![Azure Monitor 的“监视”部分](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. （可选）按资源组或资源类型筛选列表，并单击要为其设置诊断设置的资源。

3. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   如果资源上有现有的设置，则会看到已在此资源上配置的设置列表。 单击“添加诊断设置”。

   ![添加诊断设置 - 现有的设置](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. 为设置命名，选中要将数据发送到的每个目标对应的复选框，并配置要对每个目标使用哪个资源。 （可选）使用“保留期(天)”滑块设置这些日志的保留天数（仅适用于存储帐户目标）。 如果保留期为 0 天，则会无限期存储日志。

   ![添加诊断设置 - 现有的设置](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. 单击“ **保存**”。

几分钟后，新设置会显示在此资源的设置列表中，只要生成新的事件数据，就会立即将诊断日志发送到指定的目标。

租户诊断设置只能在租户服务的门户边栏选项卡中配置 - 这些设置不会显示在“Azure Monitor 诊断设置”边栏选项卡中。 例如，通过单击“审核日志”边栏选项卡中的“数据导出设置”来配置 Azure Active Directory 审核日志。

![AAD 诊断设置](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>通过 PowerShell 启用资源诊断日志的集合

若要通过 Azure PowerShell 启用资源诊断日志的集合，请使用以下命令：

若要允许在存储帐户中存储诊断日志，请使用以下命令：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

若要允许将诊断日志流式传输到事件中心，请使用以下命令：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

服务总线规则 ID 是以下格式的字符串：`{Service Bus resource ID}/authorizationrules/{key name}`。

若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

可以使用以下命令获取 Log Analytics 工作区的资源 ID：

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

可以组合这些参数以启用多个输出选项。

目前无法使用 Azure PowerShell 配置租户诊断设置。

### <a name="enable-collection-of-resource-diagnostic-logs-via-azure-cli-20"></a>通过 Azure CLI 2.0 启用资源诊断日志收集

若要通过 Azure CLI 2.0 启用资源诊断日志收集，请使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 命令。

要允许在存储帐户中存储诊断日志，请使用以下命令：

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

仅当 `--storage-account` 不是对象 ID 时，才需要 `--resource-group` 参数。

要允许将诊断日志流式传输到事件中心，请使用以下命令：

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

规则 ID 是一个字符串，采用以下格式：`{Service Bus resource ID}/authorizationrules/{key name}`。

要允许将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

仅当 `--workspace` 不是对象 ID 时，才需要 `--resource-group` 参数。

使用任一命令时，都可以通过向作为 `--logs` 参数传递的 JSON 数组添加字典来向诊断日志添加其他类别。 可以组合使用 `--storage-account`、`--event-hub` 和 `--workspace` 参数来启用多个输出选项。

目前无法使用 CLI 配置租户诊断设置。

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>通过 REST API 启用资源诊断日志的集合

若要使用 Azure Monitor REST API 更改诊断设置，请参阅[此文档](https://docs.microsoft.com/rest/api/monitor/)。

目前无法使用 Azure Monitor REST API 配置租户诊断设置。

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>在门户中管理资源诊断设置

确保使用诊断设置来设置所有资源。 在门户中导航到“监视”并打开“诊断设置”。

![门户中的“诊断日志”边栏选项卡](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

可能需要单击“所有服务”才能找到“监视”部分。

在此处可以查看和筛选所有支持诊断设置的资源，确定它们是否启用了诊断。 还可以向下钻取，以查看是否在某个资源上指定了多个设置，并检查这些数据流向哪个存储帐户、事件中心命名空间和/或 Log Analytics 工作区。

![门户中的“诊断日志”结果](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

添加诊断设置会打开“诊断设置”视图，可在其中启用、禁用或修改选定资源的诊断设置。

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>诊断日志支持的服务、类别和架构

[参阅此文](monitoring-diagnostic-logs-schema.md)获取受支持服务的完整列表，以及这些服务使用的日志类别和架构。

## <a name="next-steps"></a>后续步骤

* [将资源诊断日志流式传输到事件中心](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [使用 Azure Monitor REST API 更改资源诊断设置](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md)
