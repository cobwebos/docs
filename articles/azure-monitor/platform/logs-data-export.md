---
title: 'Log Analytics Azure Monitor (预览中的工作区数据导出) '
description: 使用 Log Analytics 数据导出功能，可以将所选表中的数据从 Log Analytics 工作区连续导出到 Azure 存储帐户或 Azure 事件中心（在收集时）。
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 10/13/2020
ms.openlocfilehash: 59febbac1a83e45c8b2bf9c233c3772f561eb111
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049725"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics Azure Monitor (预览中的工作区数据导出) 
使用 Azure Monitor 中的工作区数据导出，你可以在收集数据时，将数据从 Log Analytics 工作区中的选定表连续导出到 Azure 存储帐户或 Azure 事件中心。 Log Analytics 本文提供了有关此功能的详细信息以及在工作区中配置数据导出的步骤。

## <a name="overview"></a>概述
为 Log Analytics 工作区配置数据导出后，发送到工作区中所选表的任何新数据都会按小时自动导出到你的存储帐户，或以近乎实时的形式导出到你的事件中心。

![数据导出概述](media/logs-data-export/data-export-overview.png)

包含表中的所有数据都是在不使用筛选器的情况下导出的。 例如，在配置 *SecurityEvent* 表的数据导出规则时，从配置时间开始导出发送到 *SecurityEvent* 表的所有数据。


## <a name="other-export-options"></a>其他导出选项
Log Analytics 工作区数据导出会持续从 Log Analytics 工作区中导出数据。 用于导出特定情况的数据的其他选项包括：

- 使用逻辑应用从日志查询中计划导出。 这类似于数据导出功能，但允许向 Azure 存储发送已筛选或聚合的数据。 尽管此方法受 [日志查询限制的限制](../service-limits.md#log-analytics-workspaces)  ，请参阅 [使用逻辑应用将数据从 Log Analytics 工作区存档到 Azure 存储](logs-export-logic-app.md)。
- 使用逻辑应用进行一次导出。 请参阅 [Azure Monitor 适用于逻辑应用和电源自动的日志连接器](logicapp-flow-connector.md)。
- 使用 PowerShell 脚本一次性导出到本地计算机。 请参阅 [AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。


## <a name="current-limitations"></a>当前限制

- 当前只能使用 CLI 或 REST 请求执行配置。 不能使用 Azure 门户或 PowerShell。
- 受支持的表当前仅限于下面 ( # tabes) 部分中的特定项。 如果数据导出规则包含不受支持的表，则操作将会成功，但不会为该表导出任何数据。 如果数据导出规则包含不存在的表，则它将失败，并且 * <tableName> 工作区中不存在错误表。*
- Log Analytics 工作区可以位于除以下项之外的任何区域：
  - 瑞士北部
  - 瑞士西部
  - Azure 政府区域
- 目标存储帐户或事件中心必须与 Log Analytics 工作区位于同一区域。
- 对于存储帐户，要导出的表的名称长度不能超过60个字符，并且事件中心不能超过47个字符。 名称较长的表将不会导出。

> [!NOTE]
> Log Analytics 数据导出将数据作为附加 blob 写入，该 blob 当前为 Azure Data Lake Storage Gen2 的预览。 在将导出配置到此存储之前，必须先打开支持请求。 对于此请求，请使用以下详细信息。
> - 问题类型：技术
> - 订阅：你的订阅
> - 服务： Data Lake Storage Gen2
> - 资源：资源名称
> - 摘要：请求订阅注册，以接受来自 Log Analytics 数据导出的数据。
> - 问题类型：连接
> - 问题子类型：连接问题

## <a name="data-completeness"></a>数据完整性
当目标不可用时，数据导出将继续重试发送数据最多30分钟。 如果30分钟后仍不可用，则数据将被丢弃，直到目标变为可用。

## <a name="cost"></a>成本
数据导出功能当前没有额外的费用。 数据导出的定价将在将来公布，在帐单开始之前提供一项通知。 如果你选择在通知期后继续使用数据导出，将按适用的费率向你收费。

## <a name="export-destinations"></a>导出目标

### <a name="storage-account"></a>存储帐户
数据每小时发送到存储帐户。 数据导出配置为存储帐户中的每个表创建一个容器，*名称后跟该表的名称。* 例如，表 *SecurityEvent* 将发送到名为 *SecurityEvent 的*容器。

存储帐户 blob 路径为 *WorkspaceResourceId =/subscriptions/subscription-id/resourcegroups/ \<resource-group\> /providers/microsoft.operationalinsights/workspaces//y =/m = \<workspace\> \<four-digit numeric year\> \<two-digit numeric month\> /d = \<two-digit numeric day\> /h =/m = \<two-digit 24-hour clock hour\> 00/PT1H.json*。 由于追加 blob 仅限于存储中的50K 写入，因此，如果追加数较高，则导出的 blob 的数目可能会延长。 在这种情况下，blob 的命名模式将是 PT1H_ # json，其中 # 是增量 blob 计数。

存储帐户数据格式为 [JSON 行](diagnostic-logs-append-blobs.md)。 这意味着每个记录都由一个换行符分隔，无外部记录数组，JSON 记录之间没有逗号。 

[![存储示例数据](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

如果基于时间的保留策略启用了 *allowProtectedAppendWrites* 设置，则 Log Analytics 数据导出可以将追加 blob 写入不可变的存储帐户。 这允许将新的块写入追加 blob，同时维持永久性保护和合规性。 请参阅 [允许受保护的追加 blob 写入](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes)。

### <a name="event-hub"></a>事件中心
数据将以近乎实时的方式发送到事件中心，因为它达到 Azure Monitor。 将使用名称 *am-* 后跟表名称的每个数据类型来创建事件中心。 例如，表 *SecurityEvent* 将发送到名为 *am-SecurityEvent*的事件中心。 如果要将导出的数据传递到特定的事件中心，或如果表的名称超过47个字符的限制，则可以提供自己的事件中心名称并将所有表导出到其中。

随着时间的推移，导出的数据量经常增加，需要提高事件中心的规模，以处理更大的传输速率，并避免限制情况和数据延迟。 应该使用事件中心的自动扩展功能来自动增加和增加吞吐量单位数，并满足使用量需求。 有关详细信息，请参阅 [自动增加 Azure 事件中心吞吐量单位](../../event-hubs/event-hubs-auto-inflate.md) 。


## <a name="prerequisites"></a>必备条件
下面是在配置 Log Analytics 数据导出之前必须完成的先决条件。

- 存储帐户和事件中心必须已创建，并且必须与 Log Analytics 工作区位于同一区域。 如果需要将数据复制到其他存储帐户，可以使用任何 [Azure 存储冗余选项](../../storage/common/storage-redundancy.md)。  
- 存储帐户必须是 StorageV1 或 StorageV2。 不支持经典存储  
- 如果已将存储帐户配置为允许从所选网络进行访问，则需要在存储帐户设置中添加例外，以允许 Azure Monitor 写入存储。

## <a name="enable-data-export"></a>启用数据导出
必须执行以下步骤以启用 Log Analytics 数据导出。 有关每个的详细信息，请参阅以下各节。

- 注册资源提供程序。
- 允许受信任的 Microsoft 服务。
- 创建一个或多个用于定义要导出的表及其目标的数据导出规则。

### <a name="register-resource-provider"></a>注册资源提供程序
需要为你的订阅注册以下 Azure 资源提供程序才能启用 Log Analytics 数据导出。 

- Microsoft.Insights

此资源提供程序可能已注册到大多数 Azure Monitor 用户。 若要验证，请在 Azure 门户中转到 **订阅** 。 选择订阅，然后单击菜单的 "**设置**" 部分中的 "**资源提供程序**"。 找到 " **Microsoft Insights**"。 如果其 **状态为 "已注册"**，则已注册。 如果没有，请单击 " **注册** " 以注册它。

你还可以使用任何可用的方法来注册资源提供程序，如 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述。 下面是使用 PowerShell 的示例命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>允许受信任的 Microsoft 服务
如果已将存储帐户配置为允许从所选网络进行访问，则需要添加例外，以允许 Azure Monitor 写入帐户。 在存储帐户的 **防火墙和虚拟网络** 中，选择 " **允许受信任的 Microsoft 服务访问此存储帐户**"。

[![存储帐户防火墙和虚拟网络](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>创建或更新数据导出规则
数据导出规则定义要从所有表或从一组特定表导出到单个目标的数据。 如果需要发送到多个目标，请创建多个规则。

使用以下命令，使用 CLI 创建到存储帐户的数据导出规则。

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

使用以下命令，使用 CLI 创建到事件中心的数据导出规则。

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

使用以下请求创建使用 REST API 的数据导出规则。 请求应使用持有者令牌授权和内容类型 application/json。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

请求正文指定了表目标。 下面是针对存储帐户的 REST 请求的示例正文。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": true
    }
}
```

下面是事件中心 REST 请求的示例主体。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}

```


## <a name="view-data-export-configuration"></a>查看数据导出配置
使用以下命令查看使用 CLI 的数据导出规则的配置。

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

使用以下请求来查看使用 REST API 的数据导出规则的配置。 请求应使用持有者令牌授权。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="disable-an-export-rule"></a>禁用导出规则
如果不需要保留特定时间段（例如在执行测试时）的数据，则可以禁用导出规则。 使用以下命令通过 CLI 禁用数据导出规则。

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

使用以下请求通过 REST API 禁用数据导出规则。 请求应使用持有者令牌授权。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

## <a name="delete-an-export-rule"></a>删除导出规则
使用以下命令删除使用 CLI 的数据导出规则。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

使用以下请求删除使用 REST API 的数据导出规则。 请求应使用持有者令牌授权。

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="view-all-data-export-rules-in-a-workspace"></a>查看工作区中的所有数据导出规则
使用以下命令可使用 CLI 查看工作区中的所有数据导出规则。

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

使用以下请求可以使用 REST API 查看工作区中的所有数据导出规则。 请求应使用持有者令牌授权。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

## <a name="unsupported-tables"></a>不支持的表
如果数据导出规则包含不受支持的表，则配置将会成功，但不会为该表导出任何数据。 如果以后支持该表，则将在此时导出其数据。

如果数据导出规则包含不存在的表，则它将失败，并且 * <tableName> 工作区中不存在错误表*。


## <a name="supported-tables"></a>支持的表
支持的表当前仅限于下面指定的表。 除非指定限制，否则将导出表中的所有数据。 将更新此列表，因为添加了对其他表的支持。


| 表 | 限制 |
|:---|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| 警报 | 此表中的某些数据是通过存储帐户引入的。 当前导出中缺少此部分。 |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| 事件 | 此表中的某些数据是通过存储帐户引入的。 当前导出中缺少此部分。 |
| ExchangeAssessmentRecommendation | |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| 检测信号 | 支持 | |
| HuntingBookmark | |
| InsightsMetrics | 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| IntuneAuditLogs | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | 某些数据通过 webhook 从 O365 引入到 LA。 当前导出中缺少此部分。 |
| 操作 | 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| 性能 | 支持 | |
| SCCMAssessmentRecommendation | | 
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| SecurityDetection | |
| SecurityEvent | 支持 | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| Syslog |部分 | 此表中的某些数据是通过存储帐户引入的。 当前导出中缺少此部分。 |
| ThreatIntelligenceIndicator | |
| 更新 |部分 | 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| UpdateRunProgress | |
| UpdateSummary | |
| 使用情况 | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| WindowsEvent | |
| WindowsFirewall | |
| WireData |部分 | 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| WorkloadMonitoringPerf | |
| WorkloadMonitoringPerf | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |

## <a name="next-steps"></a>后续步骤

- [从 Azure 数据资源管理器中查询导出的数据](azure-data-explorer-query-storage.md)。
