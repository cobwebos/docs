---
title: 使用 Azure Monitor 监视数据工厂
description: 了解如何在 Azure Monitor 中，使用数据工厂中的信息通过启用诊断日志来监视 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: b7f58c13181c9ec966d548096ffc2756d5d333e3
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124863"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>使用 Azure Monitor 监视数据工厂和发警报

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

云应用程序比较复杂，包含很多移动部件。 监视功能可以提供数据来确保应用程序始终处于正常运行状态。 监视功能还有助于避免潜在问题，或者排查以往的问题。 可以使用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性。 此外，还有助于实现本来需要手动干预的操作的自动化。

Azure Monitor 针对大多数 Azure 服务提供基本级别的基础结构指标和日志。 Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 Azure 数据工厂（ADF）可以 Azure Monitor 中写入诊断日志。 有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

有关详细信息，请参阅[Azure Monitor 概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>保留 Azure 数据工厂指标和管道运行数据

数据工厂仅将管道运行数据存储 45 天。 若要将这些数据保留更长时间，请使用 Azure Monitor。 使用 Monitor，可以将诊断日志路由到多个不同目标进行分析。

* **存储帐户**：将诊断日志保存到存储帐户进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。
* **事件中心**：将日志流式传输到 Azure 事件中心。 日志将成为合作伙伴服务/自定义分析解决方案（例如 Power BI）的输入。
* **Log Analytics**：使用 Log Analytics 分析日志。 在以下情况下，将数据工厂与 Azure Monitor 集成非常有用：
  * 需要针对由数据工厂发布到 Monitor 的丰富指标集编写复杂查询。 可以通过 Monitor 创建针对这些查询的自定义警报。
  * 你希望跨数据工厂进行监视。 可将来自多个数据工厂的数据路由到单个 Monitor 工作区。

还可使用与发出日志的资源不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有适当的基于角色的访问控制 (RBAC) 访问权限。

## <a name="configure-diagnostic-settings-and-workspace"></a>配置诊断设置和工作区

为数据工厂创建或添加诊断设置。

1. 在门户中，转到“监视”。 选择 "**设置**" "  >  **诊断设置**"。

1. 选择要为其设置诊断设置的数据工厂。

1. 如果所选数据工厂不存在任何设置，则系统会提示你创建设置。 选择“启用诊断”。

   ![如果不存在任何设置，请创建一个诊断设置](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果数据工厂中存在现有设置，你将看到数据工厂中已配置的设置列表。 选择“添加诊断设置”。

   ![如果存在设置，则添加诊断设置](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 为设置指定名称，选择“发送到 Log Analytics”，然后从 **Log Analytics 工作区**中选择一个工作区。

    * 在_Azure 诊断_模式下，诊断日志流入到_AzureDiagnostics_表中。

    * 在_特定于资源_的模式中，Azure 数据工厂中的诊断日志分为以下几个表：
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      你可以选择与工作负荷相关的各种日志以发送到 Log Analytics 表。 例如，如果根本不使用 SQL Server Integration Services （SSIS），则无需选择任何 SSIS 日志。 如果要记录 SSIS Integration Runtime （IR）启动/停止/维护操作，则可以选择 "SSIS IR 日志"。 如果在 SQL Server Management Studio （SSMS）、SQL Server 代理或其他指定的工具上通过 T-sql 调用 SSIS 包执行，则可以选择 "SSIS 包日志"。 如果通过 ADF 管道中的 "执行 SSIS 包" 活动调用 SSIS 包执行，则可以选择 "所有日志"。

    * 如果选择 " _AllMetrics_"，则会提供各种 ADF 指标用于监视或引发警报，其中包括 ADF 活动、管道和触发器运行的指标以及 ssis IR 操作和 ssis 包执行。

   ![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > 由于 Azure 日志表的列数不能超过500，因此**强烈建议**选择 "_特定于资源的模式_"。 有关详细信息，请参阅 [Log Analytics 已知限制](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)。

1. 选择“保存” 。

几分钟后，新设置将出现在此数据工厂的设置列表中。 生成新的事件数据后，诊断日志将立即流式传输到该工作区。 发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>从 Azure Marketplace 安装 Azure 数据工厂分析解决方案

此解决方案提供了数据工厂总体运行状况的摘要，并提供了深入了解详细信息和排查意外行为模式的选项。 通过丰富的现成视图，你可以深入了解密钥处理，包括：

* 查看数据工厂管道、活动和触发器运行的摘要
* 能够按类型钻取到数据工厂活动运行
* 数据工厂顶部管道的摘要，活动错误

1. 请参阅**Azure Marketplace**，选择 "**分析**" "筛选器"，然后搜索**Azure 数据工厂分析（预览版）**

   ![进入 "Azure Marketplace"，输入 "Analytics filter"，并选择 "Azure 数据工厂分析（预览版）"](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. 有关**Azure 数据工厂分析的详细信息（预览版）**

   ![有关 "Azure 数据工厂分析（预览版）" 的详细信息](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. 选择 "**创建**"，然后创建或选择 " **Log Analytics" 工作区**。

   ![创建新解决方案](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>监视数据工厂指标

安装此解决方案会在所选 "Log Analytics" 工作区的 "工作簿" 部分中创建一组默认视图。 因此，会启用以下度量值：

* ADF 运行-1）管道按数据工厂运行
* ADF 运行-2）活动按数据因子运行
* ADF 运行-3）触发器按数据因子运行
* ADF 错误-1）数据工厂前10个管道错误
* ADF 错误-2）由数据工厂运行的前10个活动
* ADF 错误-3）按数据工厂列出的十大触发器错误
* ADF 统计信息-1）活动按类型运行
* ADF 统计信息-2）触发器按类型运行
* ADF 统计信息-3）最大管道运行持续时间

![突出显示 "工作簿（预览）" 和 "AzureDataFactoryAnalytics" 的窗口](media/data-factory-monitor-oms/monitor-oms-image6.png)

您可以可视化前面的指标，查看这些指标后面的查询，编辑查询，创建警报，以及执行其他操作。

![管道按数据工厂运行的图形表示形式](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure 数据工厂分析（预览版）将诊断日志发送到_特定于资源的_目标表。 可以针对以下表编写查询： _ADFPipelineRun_、 _ADFTriggerRun_和_ADFActivityRun_。

## <a name="data-factory-metrics"></a>数据工厂指标

使用 Monitor 可以洞察 Azure 工作负荷的性能与运行状况。 最重要的 Monitor 数据类型是指标（也称为性能计数器）。 大多数 Azure 资源都会发出指标。 Monitor 提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

下面是 Azure 数据工厂版本2发出的一些指标：

| **指标**                           | **指标显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 已取消的活动运行指标数           | 计数    | 总计                | 在一分钟时间范围内取消的活动运行总数。 |
| ActivityFailedRuns                   | 失败的活动运行数指标             | 计数    | 总计                | 在一分钟时段内失败的活动运行总数。 |
| ActivitySucceededRuns                | 成功的活动运行数指标          | 计数    | 总计                | 在一分钟时段内成功的活动运行总数。 |
| PipelineCancelledRuns                 | 已取消的管道运行指标数           | 计数    | 总计                | 在一分钟时间范围内取消的管道运行总数。 |
| PipelineFailedRuns                   | 失败的管道运行数指标             | 计数    | 总计                | 在一分钟时段内失败的管道运行总数。 |
| PipelineSucceededRuns                | 成功的管道运行数指标          | 计数    | 总计                | 在一分钟时段内成功的管道运行总数。 |
| TriggerCancelledRuns                  | 已取消的触发器运行指标数            | 计数    | 总计                | 在一分钟时间范围内取消的触发器运行总数。 |
| TriggerFailedRuns                    | 失败的触发器运行数指标              | 计数    | 总计                | 在一分钟时段内失败的触发器运行总数。 |
| TriggerSucceededRuns                 | 成功的触发器运行数指标           | Count    | 总计                | 在一分钟时段内成功的触发器运行总数。 |
| SSISIntegrationRuntimeStartCancelled  | 已取消 SSIS IR 启动指标           | 计数    | 总计                | 在一分钟时间范围内取消的 SSIS IR 开始的总数。 |
| SSISIntegrationRuntimeStartFailed    | 失败的 SSIS IR 启动指标             | 计数    | 总计                | 在一分钟时间范围内失败的 SSIS IR 启动总数。 |
| SSISIntegrationRuntimeStartSucceeded | 成功的 SSIS IR 启动指标          | 计数    | 总计                | 在一分钟时间范围内成功启动的 SSIS IR 的总数。 |
| SSISIntegrationRuntimeStopStuck      | 停滞 SSIS IR 停止指标               | 计数    | 总计                | 在分钟窗口中停滞的 SSIS IR 停止点总数。 |
| SSISIntegrationRuntimeStopSucceeded  | 成功的 SSIS IR 停止指标           | 计数    | 总计                | 在一分钟时间范围内成功的 SSIS IR 停止总数。 |
| SSISPackageExecutionCancelled         | 已取消 SSIS 包执行指标  | 计数    | 总计                | 在一分钟时间范围内取消的 SSIS 包执行总数。 |
| SSISPackageExecutionFailed           | 失败的 SSIS 包执行指标    | 计数    | 总计                | 在一分钟时间段内失败的 SSIS 包执行总数。 |
| SSISPackageExecutionSucceeded        | 已成功执行 SSIS 包执行指标 | 计数    | 总计                | 在分钟时间范围内成功执行的 SSIS 包执行的总数。 |

若要访问指标，请参阅 [Azure Monitor 数据平台](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)中的说明。

> [!NOTE]
> 仅发出来自已完成、已触发活动和管道运行的事件。 正在进行，并且**不**发出调试运行。 另一方面，将发出来自**所有**SSIS 包执行的事件，包括已完成和正在进行的事件，而不考虑它们的调用方法。 例如，可以在启用了 Azure 的 SQL Server Data Tools （SSDT）上，通过 SSMS 上的 T-sql、SQL Server 代理或其他指定的工具，以及在 ADF 管道中执行 SSIS 包活动的触发或调试运行来调用包执行。

## <a name="data-factory-alerts"></a>数据工厂警报

登录到 Azure 门户，选择“Monitor” > “警报”以创建警报。 

![门户菜单中的警报](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>创建警报

1. 选择“+ 创建新的预警规则”，创建新的警报。

    ![新建警报规则](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定义警报条件。

    > [!NOTE]
    > 请务必在“按资源类型筛选”下拉列表中选择“所有”。 

    ![“定义警报条件”>“选择目标”，此时会打开“选择资源”窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![“定义警报条件”>“添加条件”，此时会打开“配置信号逻辑”窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    ![“配置信号类型”窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定义警报详细信息。

    ![警报详细信息](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定义操作组。

    ![创建规则的屏幕截图，其中突出显示了“新建操作组”](media/monitor-using-azure-monitor/alerts_image9.png)

    ![创建新的操作组](media/monitor-using-azure-monitor/alerts_image10.png)

    ![配置电子邮件、短信、推送和语音](media/monitor-using-azure-monitor/alerts_image11.png)

    ![删除操作组](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 设置诊断日志

### <a name="diagnostic-settings"></a>诊断设置

可以使用诊断设置来配置非计算资源的诊断日志。 用于资源控制的诊断设置具有以下功能：

* 指定要将诊断日志发送到何处。 例如，发送到 Azure 存储帐户、Azure 事件中心或 Monitor 日志。
* 指定要发送的日志类别。
* 指定要将每个日志类别保留在存储帐户中多长时间。
* 保留期为 0 天表示永久保留日志。 如果不需要永久保留，可将该值设置为 1 到 2,147,483,647 的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 例如，如果仅选择了“事件中心”或“Monitor 日志”选项，此可能会发生这种情况。
* 保留策略按天应用。 一天的结束时间可能出现在协调世界时 (UTC) 的午夜。 在一天结束时，会删除当天已超过保留策略期限的日志。 例如，如果保留策略的期限为一天，则在今天开始时，会删除前天的日志。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 启用诊断日志

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在 Monitor REST API 中创建或更新诊断设置

##### <a name="request"></a>请求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory (Azure AD) 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-scenarios.md)。

##### <a name="body"></a>正文

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| properties | 类型 | 说明 |
| --- | --- | --- |
| **storageAccountId** |字符串 | 要将诊断日志发送到的存储帐户的资源 ID。 |
| **serviceBusRuleId** |字符串 | 服务总线命名空间的服务总线规则 ID。你要在该服务总线命名空间中创建事件中心，以便流式传输诊断日志。 规则 ID 的格式为 `{service bus resource ID}/authorizationrules/{key name}`。|
| **workspaceId** | 复杂类型 | 指标时间粒度及其保留策略的数组。 此属性的值为空。 |
|**指标**| 要传递到被调用管道的管道运行的参数值| 将参数名映射为自变量值的 JSON 对象。 |
| **logs**| 复杂类型| 某个资源类型的诊断日志类别的名称。 若要获取资源的诊断日志类别列表，请先执行 GET 诊断设置操作。 |
| **category**| 字符串| 日志类别及其保留策略的数组。 |
| **timeGrain** | String | 以 ISO 8601 持续时间格式捕获的指标的粒度。 该属性值必须为 `PT1M`（1 分钟）。 |
| **enabled**| 布尔 | 指定是否为此资源启用了该指标或日志类别的收集。 |
| **retentionPolicy**| 复杂类型| 描述指标或日志类别的保留策略。 此属性仅用于存储帐户。 |
|**days**| int| 指标或日志的保留天数。 如果该属性值为 0，则永久保留日志。 此属性仅用于存储帐户。 |

##### <a name="response"></a>响应

200 正常。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>在 Monitor REST API 中获取有关诊断设置的信息

##### <a name="request"></a>请求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure AD 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-scenarios.md)。

##### <a name="response"></a>响应

200 正常。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
有关详细信息，请参阅[诊断设置](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)。

## <a name="schema-of-logs-and-events"></a>日志和事件的架构

### <a name="monitor-schema"></a>监视架构

#### <a name="activity-run-log-attributes"></a>活动运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 属性 | 类型 | 描述 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| 字符串| 活动运行的 ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| 字符串| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `ActivityRuns`。 | `ActivityRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| 字符串 | 活动的名称及其状态。 如果活动是启动检测信号，则属性值为 `MyActivity -`。 如果活动是结束检测信号，则属性值为 `MyActivity - Succeeded`。 | `MyActivity - Succeeded` |
|**pipelineName**| 字符串 | 管道的名称。 | `MyPipeline` |
|**activityName**| 字符串 | 活动的名称。 | `MyActivity` |
|**start**| 字符串 | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| 字符串 | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>管道运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| properties | 类型 | 描述 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| 字符串| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| 字符串 | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**level**| 字符串 | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| 字符串 | 管道的名称及其状态。 管道运行完成后，该属性值为 `Pipeline - Succeeded`。 | `MyPipeline - Succeeded`. |
|**pipelineName**| 字符串 | 管道的名称。 | `MyPipeline` |
|**start**| 字符串 | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`. |
|**end**| 字符串 | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| 字符串 | 管道运行的最终状态。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>触发器运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 属性 | 类型 | 描述 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 触发器运行的 ID。 | `08587023010602533858661257311` |
|**resourceId**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| 字符串 | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**level**| 字符串 | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| 字符串 | 触发器的名称及其最终状态（指示是否已成功激发该触发器）。 如果检测信号成功，则该属性值为 `MyTrigger - Succeeded`。 | `MyTrigger - Succeeded` |
|**triggerName**| 字符串 | 触发器的名称。 | `MyTrigger` |
|**triggerType**| 字符串 | 触发器的类型。 可能的属性值为 `Manual Trigger` 和 `Schedule Trigger`。 | `ScheduleTrigger` |
|**triggerEvent**| String | 触发器的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| 字符串 | 激发触发器的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 最终状态（指示是否已成功激发该触发器）。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS 集成运行时日志属性

下面是 SSIS IR 启动/停止/维护操作的日志属性。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 描述                                                   | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字符串 | UTC 格式的事件时间：`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字符串 | SSIS IR 操作的名称                            | `Start/Stop/Maintenance` |
| **category**               | 字符串 | 诊断日志的类别                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | 字符串 | 用于跟踪特定操作的唯一 ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | 字符串 | ADF 的名称                                          | `MyADFv2` |
| **integrationRuntimeName** | 字符串 | SSIS IR 的名称                                      | `MySSISIR` |
| **level**                  | 字符串 | 诊断日志级别                                  | `Informational` |
| **resultType**             | 字符串 | SSIS IR 操作的结果                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | 字符串 | SSIS IR 操作的输出消息                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | 字符串 | ADF 资源的唯一 ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS 事件消息上下文日志属性

下面是与 SSIS IR 上的 SSIS 包执行生成的事件消息相关的条件的日志属性。 它们将类似的信息作为[ssis 目录（SSISDB）事件消息上下文表或视图](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15)（显示多个 ssis 包属性的运行时值）传递。 它们是在选择 `Basic/Verbose` 日志记录级别时生成的，适用于调试/符合性检查。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 描述                                                          | 示例                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字符串 | UTC 格式的事件时间：`YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字符串 | 此元素设置为 。`YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | 字符串 | 诊断日志的类别                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | 字符串 | 用于跟踪特定操作的唯一 ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字符串 | ADF 的名称                                                 | `MyADFv2` |
| **integrationRuntimeName** | 字符串 | SSIS IR 的名称                                             | `MySSISIR` |
| **level**                  | 字符串 | 诊断日志级别                                         | `Informational` |
| **operationId**            | 字符串 | 用于在 SSISDB 中跟踪特定操作的唯一 ID          | `1`（1表示与**未**存储在 SSISDB/通过 t-sql 调用的包有关的操作） |
| **contextDepth**           | 字符串 | 事件消息上下文的深度                              | `0`（0表示在包执行开始之前的上下文，1表示发生错误时的上下文，并随着上下文从错误中的发生进一步增加而增加） |
| **packagePath**            | 字符串 | 作为事件消息上下文源的包对象的路径      | `\Package` |
| **contextType**            | 字符串 | 作为事件消息上下文源的包对象类型      | `60`（请参阅[更多上下文类型](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks)） |
| **contextSourceName**      | 字符串 | 作为事件消息上下文源的包对象的名称      | `MyPackage` |
| **contextSourceId**        | 字符串 | 作为事件消息上下文源的包对象的唯一 ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | 字符串 | 事件消息上下文源的包属性的名称   | `DelayValidation` |
| **propertyValue**          | 字符串 | 事件消息上下文源的包属性的值  | `False` |
| **resourceId**             | 字符串 | ADF 资源的唯一 ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS 事件消息日志属性

下面是 SSIS IR 上的 SSIS 包执行生成的事件消息的日志属性。 它们将类似信息作为[SSISDB 事件消息表或视图](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15)传达，显示事件消息的详细文本/元数据。 它们是在任何日志记录级别生成的，但除外 `None` 。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| properties                   | 类型   | 描述                                                        | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | 字符串 | UTC 格式的事件时间：`YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此元素设置为 。`YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | 字符串 | 诊断日志的类别                                    | `SSISPackageEventMessages` |
| **correlationId**          | 字符串 | 用于跟踪特定操作的唯一 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字符串 | ADF 的名称                                               | `MyADFv2` |
| **integrationRuntimeName** | 字符串 | SSIS IR 的名称                                           | `MySSISIR` |
| **level**                  | 字符串 | 诊断日志级别                                       | `Informational` |
| **operationId**            | 字符串 | 用于在 SSISDB 中跟踪特定操作的唯一 ID        | `1`（1表示与**未**存储在 SSISDB/通过 t-sql 调用的包有关的操作） |
| **messageTime**            | 字符串 | 以 UTC 格式创建事件消息的时间          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | 字符串 | 事件消息的类型                                     | `70`（请参阅[更多消息类型](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)） |
| **messageSourceType**      | 字符串 | 事件消息源的类型                              | `20`（请参阅[更多消息源类型](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)） |
| **message**                | 字符串 | 事件消息的文本                                     | `MyPackage:Validation has started.` |
| **packageName**            | 字符串 | 已执行的包文件的名称                             | `MyPackage.dtsx` |
| **eventName**              | 字符串 | 相关运行时事件的名称                                 | `OnPreValidate` |
| **messageSourceName**      | 字符串 | 作为事件消息源的包组件的名称         | `Data Flow Task` |
| **messageSourceId**        | 字符串 | 作为事件消息源的包组件的唯一 ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | 字符串 | 作为事件消息源的数据流组件的名称       | `SSIS.Pipeline` |
| **packagePath**            | 字符串 | 作为事件消息源的包对象的路径            | `\Package\Data Flow Task` |
| **executionPath**          | 字符串 | 从父包到已执行组件的完整路径            | `\Transformation\Data Flow Task`（此路径还捕获组件迭代） |
| **threadId**               | 字符串 | 记录事件消息时执行的线程的唯一 ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 可执行统计信息日志属性

下面是 ssis IR 上的 SSIS 包执行生成的可执行统计信息的日志属性，其中可执行文件是包控制流中的容器或任务。 它们将类似信息作为[SSISDB 可执行统计信息表或视图](https://docs.microsoft.com/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15)传达，其中每个正在运行的可执行文件（包括其迭代）都显示一行。 它们是在任何日志记录级别生成 `None` 的，除了用于识别任务级瓶颈/故障。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 描述                                                      | 示例                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字符串 | UTC 格式的事件时间：`YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此元素设置为 。`YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | 字符串 | 诊断日志的类别                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | 字符串 | 用于跟踪特定操作的唯一 ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字符串 | ADF 的名称                                             | `MyADFv2` |
| **integrationRuntimeName** | 字符串 | SSIS IR 的名称                                         | `MySSISIR` |
| **level**                  | 字符串 | 诊断日志级别                                     | `Informational` |
| **executionId**            | 字符串 | 用于在 SSISDB 中跟踪特定执行的唯一 ID      | `1`（1表示与**未**存储在 SSISDB/通过 t-sql 调用的包有关的执行） |
| **executionPath**          | 字符串 | 从父包到已执行组件的完整路径          | `\Transformation\Data Flow Task`（此路径还捕获组件迭代） |
| **startTime**              | 字符串 | 可执行文件以 UTC 格式输入预执行阶段的时间  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | 字符串 | 可执行文件以 UTC 格式输入后执行阶段的时间 | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | 字符串 | 可执行文件的运行时间（以毫秒为单位）                   | `1,125` |
| **executionResult**        | 字符串 | 运行可执行文件的结果                                 | `0`（0表示成功，1表示失败，2表示完成，3表示取消） |
| **executionValue**         | 字符串 | 通过运行可执行文件返回的用户定义值            | `1` |
| **resourceId**             | 字符串 | ADF 资源的唯一 ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 执行组件阶段日志属性

下面是 SSIS IR 上的 SSIS 包执行生成的数据流组件的运行时统计信息的日志属性。 它们将类似信息视为[SSISDB 执行组件阶段表或视图](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15)，显示数据流组件在所有执行阶段所花费的时间。 它们是在选择 `Performance/Verbose` 日志记录级别时生成的，用于捕获数据流执行统计信息。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 描述                                                         | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字符串 | UTC 格式的事件时间：`YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此元素设置为 。`YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | 字符串 | 诊断日志的类别                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | 字符串 | 用于跟踪特定操作的唯一 ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字符串 | ADF 的名称                                                | `MyADFv2` |
| **integrationRuntimeName** | 字符串 | SSIS IR 的名称                                            | `MySSISIR` |
| **level**                  | 字符串 | 诊断日志级别                                        | `Informational` |
| **executionId**            | 字符串 | 用于在 SSISDB 中跟踪特定执行的唯一 ID         | `1`（1表示与**未**存储在 SSISDB/通过 t-sql 调用的包有关的执行） |
| **packageName**            | 字符串 | 已执行的包文件的名称                              | `MyPackage.dtsx` |
| **taskName**               | 字符串 | 已执行的数据流任务的名称                                 | `Data Flow Task` |
| **subcomponentName**       | 字符串 | 数据流组件的名称                                     | `Derived Column` |
| **阶段**                  | 字符串 | 执行阶段的名称                                         | `AcquireConnections` |
| **startTime**              | 字符串 | 执行阶段的开始时间（UTC 格式）                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | 字符串 | 执行阶段的结束时间（UTC 格式）                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | 字符串 | 数据流任务的执行路径                            | `\Transformation\Data Flow Task` |
| **resourceId**             | 字符串 | ADF 资源的唯一 ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 执行数据统计信息日志属性

下面是在 SSIS IR 上的 SSIS 包执行生成的数据流管道的每个阶段（从上游到下游组件）的数据移动的日志属性。 它们将类似信息作为[SSISDB 执行数据统计信息表或视图](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15)传达，显示通过数据流任务移动的数据的行计数。 它们是在选择 `Verbose` 日志记录级别时生成的，用于计算数据流吞吐量。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| properties                     | 在任务栏的搜索框中键入   | 描述                                                        | 示例                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | 字符串 | UTC 格式的事件时间：`YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | 此元素设置为 。`YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | 字符串 | 诊断日志的类别                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | 字符串 | 用于跟踪特定操作的唯一 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | 字符串 | ADF 的名称                                               | `MyADFv2` |
| **integrationRuntimeName**   | 字符串 | SSIS IR 的名称                                           | `MySSISIR` |
| **level**                    | 字符串 | 诊断日志级别                                       | `Informational` |
| **executionId**              | 字符串 | 用于在 SSISDB 中跟踪特定执行的唯一 ID        | `1`（1表示与**未**存储在 SSISDB/通过 t-sql 调用的包有关的执行） |
| **packageName**              | 字符串 | 已执行的包文件的名称                             | `MyPackage.dtsx` |
| **taskName**                 | 字符串 | 已执行的数据流任务的名称                                | `Data Flow Task` |
| **dataflowPathIdString**     | 字符串 | 跟踪数据流路径的唯一 ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | 字符串 | 数据流路径的名称                                         | `ADO NET Source Output` |
| **sourceComponentName**      | 字符串 | 发送数据的数据流组件的名称                    | `SQLDB Table3` |
| **destinationComponentName** | 字符串 | 接收数据的数据流组件的名称                 | `Derived Column` |
| **rowsSent**                 | 字符串 | 源组件发送的行数                        | `500` |
| **createdTime**              | 字符串 | 以 UTC 格式获取行值的时间                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | 字符串 | 数据流任务的执行路径                           | `\Transformation\Data Flow Task` |
| **resourceId**               | 字符串 | ADF 资源的唯一 ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics 架构

Log Analytics 从 Monitor 继承架构，但存在以下例外情况：

* 每个列名中的第一个字母会大写。 例如，Monitor 中的列名“correlationId”在 Log Analytics 中为“CorrelationId”。
* 没有“Level”列。
* 动态“properties”列保留为以下动态 JSON Blob 类型。

    | Azure Monitor 列 | Log Analytics 列 | 类型 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 动态 |
    | $.properties.Annotations | 批注 | 动态 |
    | $.properties.Input | 输入 | 动态 |
    | $.properties.Output | 输出 | 动态 |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | 错误 | 动态 |
    | $.properties.Predecessors | Predecessors | 动态 |
    | $.properties.Parameters | parameters | 动态 |
    | $.properties.SystemParameters | SystemParameters | 动态 |
    | $.properties.Tags | Tags | 动态 |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>监视具有 Azure Monitor 的 SSIS 操作

若要提升 & 转换 SSIS 工作负载，可以[在支持以下项的 ADF 中预配 SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) ：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

预配后，可以[使用 Azure PowerShell 或 ADF 门户的**监视**中心来检查 SSIS IR 操作状态](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)。 使用项目部署模型时，SSIS 包执行日志存储在 SSISDB 内部表或视图中，因此您可以使用指定的工具（如 SSMS）对其进行查询、分析和可视化。 使用包部署模型时，SSIS 包执行日志可以存储在文件系统中，也可以存储在 Azure 文件中，这些文件仍需使用其他指定的工具进行分析和处理，然后才能对其进行查询、分析和可视化。

现在，通过[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)集成，你可以查询、分析和直观显示从 ssis IR 操作和 Azure 门户上的 ssis 包执行生成的所有指标和日志。 此外，还可以对其发出警报。

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>为 SSIS 操作配置诊断设置和工作区

若要将从 SSIS IR 操作和 SSIS 包执行生成的所有指标和日志发送到 Azure Monitor，需要为[ADF 配置诊断设置和工作区](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#configure-diagnostic-settings-and-workspace)。

### <a name="ssis-operational-metrics"></a>SSIS 操作指标

SSIS 操作[度量](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)值是性能计数器或数值，用于描述 ssis IR 启动和停止操作的状态，以及在特定时间点的 ssis 包执行。 它们是[Azure Monitor 中 ADF 指标](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)的组成部分。

在 Azure Monitor 上为 ADF 配置诊断设置和工作区时，选中 " _AllMetrics_ " 复选框将使 SSIS 操作指标可用于[使用 Azure 指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)、 [azure 仪表板上的演示文稿](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)和[近乎实时的警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)进行交互式分析。

![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS 操作警报

若要从 ADF 门户发出有关 SSIS 操作指标的警报，请[选择 Adf**监视器**集线器的 "**警报 & 指标**" 页，然后按照提供](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)的分步说明进行操作。

![从 ADF 门户引发 SSIS 操作警报](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

若要从 Azure 门户中发出有关 SSIS 操作指标的警报，请[选择 Azure **Monitor** Hub 的 "**警报**" 页，并按照提供的分步说明进行](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-alerts)操作。

![从 Azure 门户引发 SSIS 操作警报](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS 操作日志

SSIS 操作[日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)是由 ssis IR 操作和 ssis 包执行生成的事件，这些事件在任何确定的问题上提供足够的上下文，并且对于根本原因分析很有用。 

在 Azure Monitor 上为 ADF 配置诊断设置和工作区时，可以选择相关的 SSIS 操作日志，并将其发送到基于 Azure 数据资源管理器 Log Analytics。 在这里，可[使用丰富的查询语言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)、 [Azure 仪表板上的演示文稿](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)和[近乎实时的警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)进行分析。

![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure Monitor 和 Log Analytics 中 SSIS 包执行日志的架构和内容类似于 SSISDB 内部表或视图的架构。

| Azure Monitor 日志类别          | Log Analytics 表                     | SSISDB 内部表/视图              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

有关 SSIS 操作日志特性/属性的详细信息，请参阅[ADF Azure Monitor 和 Log Analytics 架构](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)。

选择的 SSIS 包执行日志始终发送到 Log Analytics，而不考虑它们的调用方法。 例如，你可以通过 SSMS 上的 SSDT、SQL Server 代理或其他指定的工具，以及在 ADF 管道中执行 SSIS 包活动的触发或调试运行，在启用了 Azure 的上调用包执行。

查询日志分析上的 SSIS IR 操作日志时，可以分别使用设置为和的**OperationName**和**ResultType**属性 `Start/Stop/Maintenance` `Started/InProgress/Succeeded/Failed` 。 

![查询 Log Analytics 上的 SSIS IR 操作日志](media/data-factory-monitor-oms/log-analytics-query.png)

查询日志分析上的 SSIS 包执行日志时，可以使用**OperationId** / **ExecutionId** / **CorrelationId**属性联接它们。 **OperationId** /**ExecutionId** `1` 对于**未**存储在 SSISDB/通过 t-sql 调用的包的所有操作/执行，始终将 ExecutionId 设置为。

![查询 Log Analytics 上的 SSIS 包执行日志](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>后续步骤
[以编程方式监视和管理管道](monitor-programmatically.md)
