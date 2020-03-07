---
title: 教程：不使用代码在 Azure 数据资源管理器中引入监视数据
description: 本教程介绍如何在不使用任何代码和查询数据的情况下将监视数据引入到 Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198042"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>教程：在 Azure 数据资源管理器中引入和查询监视数据 

本教程介绍如何在不编写代码的情况下，将数据从诊断日志和活动日志引入到 Azure 数据资源管理器群集。 使用这种简单的引入方法，可快速开始查询 Azure 数据资源管理器，从而进行数据分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 在 Azure 数据资源管理器数据库中创建表和引入映射。
> * 使用更新策略设置引入数据的格式。
> * 创建[事件中心](/azure/event-hubs/event-hubs-about)并将其连接到 Azure 数据资源管理器。
> * 将 Azure Monitor [诊断指标和日志](/azure/azure-monitor/platform/diagnostic-settings)以及[活动日志](/azure/azure-monitor/platform/activity-logs-overview)中的数据流式传输到事件中心。
> * 使用 Azure 数据资源管理器查询引入的数据。

> [!NOTE]
> 在同一 Azure 位置或区域中创建所有资源。 

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。 在本教程中，数据库名为 TestDatabase  。

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor 数据提供程序：诊断指标和日志以及活动日志

查看并了解以下 Azure Monitor 诊断指标和日志以及活动日志提供的数据。 你将基于这些数据架构创建引入管道。 请注意，日志中的每个事件都具有记录数组。 稍后在本教程中，将拆分此记录数组。

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>诊断指标和日志以及活动日志的示例

Azure 诊断指标和日志以及活动日志是由 Azure 服务发出的，用于提供与该服务的操作相关的数据。 

# <a name="diagnostic-metrics"></a>[诊断指标](#tab/diagnostic-metrics)
#### <a name="example"></a>示例

诊断指标以 1 分钟的时间粒度聚合。 下面是有关查询持续时间的 Azure 数据资源管理器指标事件架构示例：

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[诊断日志](#tab/diagnostic-logs)
#### <a name="example"></a>示例

下面是 Azure 数据资源管理器[诊断引入日志](using-diagnostic-logs.md#diagnostic-logs-schema)的示例：

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)
#### <a name="example"></a>示例

Azure 活动日志是订阅级日志，提供对订阅中的资源执行的操作的深入见解。 下面是用于检查访问权限的活动日志事件示例：

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>在 Azure 数据资源管理器中设置引入管道

设置 Azure 数据资源管理器管道需要执行若干步骤，如[表创建和数据引入](/azure/data-explorer/ingest-sample-data#ingest-data)。 此外，你可以处理、映射和更新数据。

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>连接到 Azure 数据资源管理器 Web UI

在 Azure 数据资源管理器的 TestDatabase 数据库中，选择“查询”打开 Azure 数据资源管理器 Web UI   。

![查询页](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>创建目标表

Azure Monitor 日志的结构不是表格。 你将操纵数据并将每个事件扩展到一个或多个记录。 将原始数据引入到活动日志的中间表 *ActivityLogsRawRecords* 以及诊断指标和日志的中间表 *DiagnosticRawRecords*。 此时，数据已经过处理和扩展。 接着，使用更新策略将扩展的数据引入到活动日志的 *ActivityLogs* 表、诊断指标的 *DiagnosticMetrics* 表以及诊断日志的 *DiagnosticLogs* 表。 这意味着需要创建两个单独的表来引入活动日志，并创建三个单独的表来引入诊断指标和日志。

使用 Azure 数据资源管理器 Web UI 在 Azure 数据资源管理器数据库中创建目标表。

# <a name="diagnostic-metrics"></a>[诊断指标](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>创建诊断指标表

1. 在 *TestDatabase* 数据库中，创建名为 *DiagnosticMetrics* 的表来存储诊断指标记录。 使用以下 `.create table` 控制命令：

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. 选择“运行”以创建该表  。

    ![运行查询](media/ingest-data-no-code/run-query.png)

1. 使用以下查询在 *TestDatabase* 数据库中创建名为 *DiagnosticRawRecords* 的中间数据表，以进行数据操作。 选择“运行”以创建该表  。

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 为中间表设置零[保留策略](/azure/kusto/management/retention-policy)：

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[诊断日志](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>创建诊断日志表 

1. 在 *TestDatabase* 数据库中，创建名为 *DiagnosticLogs* 的表来存储诊断日志记录。 使用以下 `.create table` 控制命令：

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. 选择“运行”以创建该表  。

1. 使用以下查询在 *TestDatabase* 数据库中创建名为 *DiagnosticRawRecords* 的中间数据表，以进行数据操作。 选择“运行”以创建该表  。

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 为中间表设置零[保留策略](/azure/kusto/management/retention-policy)：

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>创建活动日志表 

1. 在 *TestDatabase* 数据库中，创建名为 *ActivityLogs* 的表来接收活动日志记录。 若要创建该表，请运行以下 Azure 数据资源管理器查询：

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. 在 TestDatabase 数据库中创建名为 ActivityLogsRawRecords 的中间数据表，以便处理数据   ：

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. 为中间表设置零[保留策略](/azure/kusto/management/retention-policy)：

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>创建表映射

 由于数据格式为 `json`，因此需要数据映射。 `json` 映射将每个 JSON 路径映射到表列名。

# <a name="diagnostic-metrics--diagnostic-logs"></a>[诊断指标/诊断日志](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>将诊断指标和日志映射到表

若要将诊断指标和日志数据映射到表，请使用以下查询：

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>将活动日志映射到表

若要将活动日志数据映射到表，请使用以下查询：

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>为指标和日志数据创建更新策略

# <a name="diagnostic-metrics"></a>[诊断指标](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>为诊断指标创建数据更新策略

1. 创建一个[函数](/azure/kusto/management/functions)来扩展诊断指标记录集合，使集合中的每个值收到一个单独的行。 使用 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 运算符：
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. 将[更新策略](/azure/kusto/concepts/updatepolicy)添加到目标表。 此策略将针对 *DiagnosticRawRecords* 中间数据表中任何新引入的数据自动运行查询，并将查询结果引入到 *DiagnosticMetrics* 表中：

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[诊断日志](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>为诊断日志创建数据更新策略

1. 创建一个[函数](/azure/kusto/management/functions)来扩展诊断日志记录集合，使集合中的每个值收到一个单独的行。 你将在 Azure 数据资源管理器群集上启用引入日志，并使用[引入日志架构](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)。 你将为成功和失败的引入创建一个表，对于成功的引入，某些字段将为空（例如，ErrorCode）。 使用 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 运算符：

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. 将[更新策略](/azure/kusto/concepts/updatepolicy)添加到目标表。 此策略将针对 *DiagnosticRawRecords* 中间数据表中任何新引入的数据自动运行查询，并将查询结果引入到 *DiagnosticLogs* 表中：

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>为活动日志创建数据更新策略

1. 创建一个[函数](/azure/kusto/management/functions)用于扩展活动日志记录集合，使集合中的每个值收到一个单独的行。 使用 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 运算符：

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. 将[更新策略](/azure/kusto/concepts/updatepolicy)添加到目标表。 此策略将针对 *ActivityLogsRawRecords* 中间数据表中任何新引入的数据自动运行查询，并将查询结果引入到 *ActivityLogs* 表中：

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>创建一个 Azure 事件中心命名空间

通过 Azure 诊断设置，能够将指标和日志导出到存储帐户或事件中心。 本教程通过事件中心路由指标和日志。 按照以下步骤，为诊断指标和日志创建事件中心命名空间和事件中心。 Azure Monitor 将为活动日志创建事件中心 insights-operational-logs  。

1. 在 Azure 门户中使用 Azure 资源管理器模板创建事件中心。 若要执行本文的剩余步骤，请右键单击“部署到 Azure”，然后选择“在新窗口中打开”   。 单击“部署到 Azure”按钮可转到 Azure 门户。 

    [![“部署到 Azure”按钮](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. 为诊断日志创建事件中心命名空间和事件中心。

    ![创建事件中心](media/ingest-data-no-code/event-hub.png)

1. 使用以下信息填写窗体。 对于下表中未列出的任何设置，请使用默认值。

    **设置** | **建议的值** | **说明**
    |---|---|---|
    | **订阅** | 用户的订阅  | 选择要用于事件中心的 Azure 订阅。|
    | **资源组** | *test-resource-group* | 创建新的资源组。 |
    | **位置** | 选择最符合需求的区域。 | 在其他资源所在的同一位置创建事件中心命名空间。
    | **命名空间名称** | *AzureMonitoringData* | 选择用于标识命名空间的唯一名称。
    | **事件中心名称** | *DiagnosticData* | 事件中心位于命名空间下，该命名空间提供唯一的范围容器。 |
    | **使用者组名称** | *adxpipeline* | 创建使用者组名称。 使用者组允许多个使用应用程序各自具有事件流的单独视图。 |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>将 Azure Monitor 指标和日志连接到事件中心

现在需要将诊断指标和日志以及活动日志连接到事件中心。

# <a name="diagnostic-metrics--diagnostic-logs"></a>[诊断指标/诊断日志](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>将诊断指标和日志连接到事件中心

选择要从其中导出指标的资源。 支持导出诊断数据的资源类型有多种，包括事件中心命名空间、Azure Key Vault、Azure IoT 中心和 Azure 数据资源管理器群集。 在本教程中，我们将使用 Azure 数据资源管理器群集作为资源，并查看查询性能指标和引入结果日志。

1. 在 Azure 门户中选择你的 Kusto 群集。
1. 选择“诊断设置”，然后选择“启用诊断”链接   。 

    ![诊断设置](media/ingest-data-no-code/diagnostic-settings.png)

1. “诊断设置”窗格打开  。 执行以下步骤：
   1. 将诊断日志数据命名为 ADXExportedData  。
   1. 在“日志”下，选中“SucceededIngestion”和“FailedIngestion”复选框    。
   1. 在“指标”下，选中“查询性能”复选框   。
   1. 选择“流式传输到事件中心”复选框  。
   1. 选择“配置”  。

      ![诊断设置窗格](media/ingest-data-no-code/diagnostic-settings-window.png)

1. 在“选择事件中心”窗格中，配置将数据从诊断日志导出到所创建事件中心的方法  ：
    1. 在“选择事件中心命名空间”列表中，选择 AzureMonitoringData   。
    1. 在“选择事件中心名称”列表中，选择“DiagnosticData”   。
    1. 在“选择事件中心策略名称”列表中，选择 RootManagerSharedAccessKey   。
    1. 选择“确定”  。

1. 选择“保存”。 

# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>将活动日志连接到事件中心

1. 在 Azure 门户的左侧菜单中，选择“活动日志”  。
1. 此时会打开“活动日志”窗口  。 选择“导出到事件中心”  。

    ![活动日志窗口](media/ingest-data-no-code/activity-log.png)

1. 此时会打开“导出活动日志”窗口  ：
 
    ![导出活动日志窗口](media/ingest-data-no-code/export-activity-log.png)

1. 在“导出活动日志”窗口中，执行以下步骤  ：
      1. 选择订阅。
      1. 在“区域”列表中，选择“全选”   。
      1. 选择“导出到事件中心”复选框  。
      1. 选择“选择服务总线命名空间”，打开“选择事件中心”窗格   。
      1. 在“选择事件中心”窗格中，选择订阅  。
      1. 在“选择事件中心命名空间”列表中，选择 AzureMonitoringData   。
      1. 在“选择事件中心策略名称”列表中，选择默认的事件中心策略名称  。
      1. 选择“确定”  。
      1. 在窗口的左上角，选择“保存”  。
   随即会创建名为 *insights-operational-logs* 的事件中心。
---

### <a name="see-data-flowing-to-your-event-hubs"></a>查看流入事件中心的数据

1. 需等待几分钟，才能定义连接，完成活动日志到事件中心的数据导出。 转到事件中心命名空间，查看创建的事件中心。

    ![创建的事件中心](media/ingest-data-no-code/event-hubs-created.png)

1. 查看流入事件中心的数据：

    ![事件中心的数据](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>将事件中心连接到 Azure 数据资源管理器

现在需要为诊断指标和日志以及活动日志创建数据连接。

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>为诊断指标和日志以及活动日志创建数据连接

1. 在名为 kustodocs 的 Azure 数据资源管理器群集中，选择左侧菜单中的“数据库”   。
1. 在“数据库”窗口中，选择 TestDatabase 数据库   。
1. 在左侧菜单中，选择“数据引入”  。
1. 在“数据引入”窗口中，单击“+ 添加数据连接”   。
1. 在“数据连接”窗口中输入以下信息： 

    ![事件中心数据连接](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[诊断指标/诊断日志](#tab/diagnostic-metrics+diagnostic-logs) 

1. 在“数据连接”窗口中使用以下设置  ：

    数据源：

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | **数据连接名称** | *DiagnosticsLogsConnection* | 要在 Azure 数据资源管理器中创建的连接的名称。|
    | **事件中心命名空间** | *AzureMonitoringData* | 先前选择的用于标识命名空间的名称。 |
    | **事件中心** | *DiagnosticData* | 你创建的事件中心。 |
    | **使用者组** | *adxpipeline* | 在创建的事件中心定义的使用者组。 |
    | | |

    目标表：

    有两个路由选项：静态和动态。   本教程将使用静态路由（默认），需在其中指定表名、数据格式和映射。 让“我的数据包含路由信息”保持取消选中状态。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | **表** | *DiagnosticRawRecords* | 在 TestDatabase 数据库中创建的表  。 |
    | **数据格式** | *JSON* | 表中使用的格式。 |
    | **列映射** | *DiagnosticRawRecordsMapping* | 在 *TestDatabase* 数据库中创建的映射，它将传入的 JSON 数据映射到 *DiagnosticRawRecords* 表的列名和数据类型。|
    | | |

1. 选择“创建”  。  

# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)

1. 在“数据连接”窗口中使用以下设置  ：

    数据源：

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | **数据连接名称** | *ActivityLogsConnection* | 要在 Azure 数据资源管理器中创建的连接的名称。|
    | **事件中心命名空间** | *AzureMonitoringData* | 先前选择的用于标识命名空间的名称。 |
    | **事件中心** | *insights-operational-logs* | 你创建的事件中心。 |
    | **使用者组** | *$Default* | 默认的使用者组。 如果需要，可以创建不同的使用者组。 |
    | | |

    目标表：

    有两个路由选项：静态和动态。   本教程将使用静态路由（默认），需在其中指定表名、数据格式和映射。 让“我的数据包含路由信息”保持取消选中状态。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | **表** | *ActivityLogsRawRecords* | 在 TestDatabase 数据库中创建的表  。 |
    | **数据格式** | *JSON* | 表中使用的格式。 |
    | **列映射** | *ActivityLogsRawRecordsMapping* | 在 TestDatabase 数据库中创建的映射，它将传入的 JSON 数据映射到 ActivityLogsRawRecords 表的列名和数据类型   。|
    | | |

1. 选择“创建”  。  
---

## <a name="query-the-new-tables"></a>查询新表

现已创建用于流送数据的管道。 默认情况下，通过群集引入数据需要 5 分钟，因此，请先让数据流动几分钟，然后再开始查询。

# <a name="diagnostic-metrics"></a>[诊断指标](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>查询诊断指标表

以下查询分析 Azure 数据资源管理器诊断指标记录中的查询持续时间数据：

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

查询结果：

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[诊断日志](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>查询诊断日志表

此管道通过事件中心生成引入。 你将查看这些引入的结果。
以下查询分析一分钟内应计的引入数，包括每个时间间隔的 `Database`、`Table` 和 `IngestionSourcePath` 示例：

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

查询结果：

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[活动日志](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>查询活动日志表

以下查询分析 Azure 数据资源管理器活动日志记录中的数据：

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

查询结果：

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>后续步骤

* 通过[为 Azure 数据资源管理器编写查询](write-queries.md)了解如何针对从 Azure 数据资源管理器提取的数据编写更多查询。
* [使用诊断日志监视 Azure 数据资源管理器引入操作](using-diagnostic-logs.md)
* [使用指标监视群集运行状况](using-metrics.md)
