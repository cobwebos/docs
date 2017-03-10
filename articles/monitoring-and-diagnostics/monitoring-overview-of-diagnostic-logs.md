---
title: "Azure 诊断日志概述 | Microsoft Docs"
description: "了解什么是 Azure 诊断日志，以及如何使用该诊断日志了解发生在 Azure 资源内的事件。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: johnkem; magoedte
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2e011fbde0ee1b070d51a38b23193a4b48a3a154
ms.lasthandoff: 03/08/2017


---
# <a name="overview-of-azure-diagnostic-logs"></a>Azure 诊断日志概述
**Azure 诊断日志**是资源发出的日志，记录与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异（例如，Windows 事件系统日志是一类针对 VM 的诊断日志，而 blob、表和队列日志是针对存储帐户的诊断日志类别），并且不同于[活动日志（以前称为审核日志或操作日志）](monitoring-overview-activity-logs.md)，后者用于了解在订阅的资源上执行的操作。 并非所有资源都支持此处所述的诊断日志新类型。 下面的受支持服务的列表显示了哪些资源类型支持新的诊断日志。

![诊断日志的逻辑位置](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>可以对诊断日志执行的操作
可以对诊断日志执行的部分操作如下：

* 将诊断日志保存到[**存储帐户**](monitoring-archive-diagnostic-logs.md)进行审核或手动检查。 可以使用“诊断设置”指定保留时间（天）。
* [将诊断日志流式传输到**事件中心**](monitoring-stream-diagnostic-logs-to-event-hubs.md)，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。
* 使用 [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md) 对诊断日志进行分析

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户或事件中心命名空间就不必与资源发出日志位于同一订阅中。

## <a name="diagnostic-settings"></a>诊断设置
可以使用“诊断设置”配置非计算资源的诊断日志。 资源控制的“诊断设置”：

* 将诊断日志发送到何处：存储帐户、事件中心和/或 OMS Log Analytics。
* 发送哪些日志类别。
* 应该将每个日志类别保留在存储帐户中多长时间 – 保留期为&0; 天表示永久保留日志。 如果不需永久保留，可将此值的范围设置为 1 到 2147483647。 如果设置了保留策略，但禁止将日志存储在存储帐户中（例如，如果仅选择事件中心或 OMS 选项），则保留策略无效。 保留策略按天应用，因此在一天结束时 (UTC)，将会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，将会删除前天的日志。

这些设置可以通过“诊断”边栏选项卡（适用于 Azure 门户中的资源）、Azure PowerShell 和 CLI 命令或 [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) 轻松进行配置。

> [!WARNING]
> 计算资源（例如，VM 或 Service Fabric）的诊断日志和指标使用[单独的输出配置和选择机制](../azure-diagnostics.md)。
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>如何启用诊断日志集合
可以在门户中通过资源的边栏选项卡在创建资源的过程中或在创建资源以后启用诊断日志集合。 也可使用 Azure PowerShell 或 CLI 命令（或者使用 Azure 监视器 REST API）在任何时间点启用诊断日志。

> [!TIP]
> 这些说明可能不能直接应用到每个资源。 请参阅此页底部的架构链接，了解适用于特定资源类型的特殊步骤。
>
>

[此文介绍如何使用资源模板在创建资源时启用诊断设置](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>在门户中启用诊断日志
通过启用 Windows 或 Linux Azure 诊断扩展来创建计算资源类型时，可以在 Azure 门户中启用诊断日志：

1. 转到“新建”并选择感兴趣的资源。
2. 在配置基本设置并选择大小以后，即可在“设置”边栏选项卡的“监视”下选择“启用”，然后选择一个存储帐户，在其中存储诊断日志。 当用户向存储帐户发送诊断时，系统会针对存储和事务收取正常数据费率。

   ![在资源创建过程中启用诊断日志](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. 单击“确定”并创建资源。

对于非计算资源，可以通过执行以下操作在资源创建后在 Azure 门户中启用诊断日志：

1. 转到资源的边栏选项卡，打开“诊断”边栏选项卡。
2. 单击“启用”，然后选取存储帐户和/或事件中心。

   ![在创建资源以后启用诊断日志](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. 在“日志”下，选择要进行收集或流式传输操作的“日志类别”。
4. 单击“保存” 。

### <a name="enable-diagnostic-logs-via-powershell"></a>通过 PowerShell 启用诊断日志
若要通过 Azure PowerShell Cmdlet 启用诊断日志，请使用以下命令。

若要允许在存储帐户中存储诊断日志，请使用以下命令：

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

若要允许将诊断日志流式传输到事件中心，请使用以下命令：

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

服务总线规则 ID 是以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。

若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

你可以使用以下命令获取 Log Analytics 工作区的资源 ID：

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

你可以组合这些参数以启用多个输出选项。

### <a name="enable-diagnostic-logs-via-cli"></a>通过 CLI 启用诊断日志
若要通过 Azure CLI 启用诊断日志，请使用以下命令：

若要允许在存储帐户中存储诊断日志，请使用以下命令：

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

若要允许将诊断日志流式传输到事件中心，请使用以下命令：

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

服务总线规则 ID 是以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。

若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

你可以组合这些参数以启用多个输出选项。

### <a name="enable-diagnostic-logs-via-rest-api"></a>通过 REST API 启用诊断日志
若要使用 Azure 监视器 REST API 更改诊断设置，请参阅[此文档](https://msdn.microsoft.com/library/azure/dn931931.aspx)。

## <a name="manage-diagnostic-settings-in-the-portal"></a>在门户中管理诊断设置
为了确保使用诊断设置正确地设置所有资源，你可以导航到门户中的“监视”边栏选项卡，然后打开“诊断日志”边栏选项卡。

![门户中的“诊断日志”边栏选项卡](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

可能需要单击“更多服务”才能找到“监视”边栏选项卡。

在此边栏选项卡中，可以查看和筛选所有支持诊断日志的资源，以明确它们是否启用了诊断，以及这些日志流向哪个存储帐户、事件中心和/或 Log Analytics 工作区。

![门户中的“诊断日志”边栏选项卡结果](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

单击资源将显示已存储在存储帐户中的所有日志，并且可以选择关闭或修改诊断设置。 单击下载图标以下载特定时间段的日志。

![“诊断日志”边栏选项卡一个资源](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> 如果已将诊断设置配置为将其保存到存储帐户，则诊断日志将仅显示在此视图中并且可供下载。
>
>

单击“诊断设置”链接将弹出“诊断设置”边栏选项卡，可以在其中启用、禁用或修改所选资源的诊断设置。

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>诊断日志支持的服务和架构
诊断日志的架构因资源和日志类别而异。 以下是受支持的服务及其架构。

| 服务 | 架构和文档 |
| --- | --- |
| 负载均衡器 |[用于 Azure 负载均衡器的 Log Analytics（预览版）](../load-balancer/load-balancer-monitor-log.md) |
| 网络安全组 |[网络安全组 (NSG) 的 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md) |
| 应用程序网关 |[应用程序网关的诊断日志记录](../application-gateway/application-gateway-diagnostics.md) |
| 密钥保管库 |[Azure 密钥保管库日志记录](../key-vault/key-vault-logging.md) |
| Azure 搜索 |[允许并使用搜索流量分析](../search/search-traffic-analytics.md) |
| Data Lake Store |[访问 Azure Data Lake Store 的诊断日志](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 数据湖分析 |[访问 Azure Data Lake Analytics 的诊断日志](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Logic Apps |[逻辑应用 B2B 自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure 批处理 |[Azure Batch 诊断日志记录](../batch/batch-diagnostics.md) |
| Azure 自动化 |[Azure 自动化的 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| 事件中心 |[Azure 事件中心诊断日志](../event-hubs/event-hubs-diagnostic-logs.md) |
| 流分析 |[作业诊断日志](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 服务总线 |[Azure 服务总线诊断日志](../service-bus-messaging/service-bus-diagnostic-logs.md) |


## <a name="supported-log-categories-per-resource-type"></a>每种资源类型支持的日志类别
|资源类型|类别|类别显示名称|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|作业日志|
|Microsoft.Automation/automationAccounts|JobStreams|作业流|
|Microsoft.Batch/batchAccounts|ServiceLog|服务日志|
|Microsoft.DataLakeAnalytics/accounts|审核|审核日志|
|Microsoft.DataLakeAnalytics/accounts|请求|请求日志|
|Microsoft.DataLakeStore/accounts|审核|审核日志|
|Microsoft.DataLakeStore/accounts|请求|请求日志|
|Microsoft.EventHub/namespaces|ArchiveLogs|存档日志|
|Microsoft.EventHub/namespaces|OperationalLogs|运行日志|
|Microsoft.KeyVault/vaults|AuditEvent|审核日志|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流运行时诊断事件|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|集成帐户跟踪事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|网络安全组事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|网络安全组规则计数器|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|负载均衡器警报事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|负载均衡器探测运行状况|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|应用程序网关访问日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|应用程序网关性能日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|应用程序网关防火墙日志|
|Microsoft.Search/searchServices|OperationLogs|操作日志|
|Microsoft.ServerManagement/nodes|RequestLogs|请求日志|
|Microsoft.ServiceBus/namespaces|OperationalLogs|运行日志|
|Microsoft.StreamAnalytics/streamingjobs|执行|执行|
|Microsoft.StreamAnalytics/streamingjobs|创作|创作|

## <a name="next-steps"></a>后续步骤
* [将诊断日志流式传输到**事件中心**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [使用 Azure 监视器 REST API 更改诊断设置](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [使用 OMS Log Analytics 对日志进行分析](../log-analytics/log-analytics-azure-storage.md)

