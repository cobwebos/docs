---
title: 教程：在 Azure 数据资源管理器中不使用任何代码引入诊断和活动日志数据
description: 本教程介绍如何在不使用任何代码和查询数据的情况下将该数据引入到 Azure 数据资源管理器。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 39019c4b11d055aa8f550928bd677e4ce33d6252
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885191"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>教程：在 Azure 数据资源管理器中不使用任何代码引入数据

本教程讲述如何在不使用任何代码的情况下，将诊断和活动日志数据引入到 Azure 数据资源管理器群集。 使用这种简单的引入方法可在 Azure 数据资源管理器中快速开始进行数据分析。

在本教程中，将了解如何：
> [!div class="checklist"]
> * 在 Azure 数据资源管理器数据库中创建表和引入映射。
> * 使用更新策略设置引入数据的格式。
> * 创建一个[事件中心](/azure/event-hubs/event-hubs-about)并将其连接到 Azure 数据资源管理器。
> * 将 [Azure Monitor 诊断日志](/azure/azure-monitor/platform/diagnostic-logs-overview)和 [Azure Monitor 活动日志](/azure/azure-monitor/platform/activity-logs-overview)中的数据流式传输到事件中心。
> * 使用 Azure 数据资源管理器查询引入的数据。

> [!NOTE]
> 在同一 Azure 位置/区域中创建所有资源。 这是 Azure Monitor 诊断日志的一项要求。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)。 在本教程中，数据库名为 *AzureMonitoring*。

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Azure 监视数据提供程序 - 诊断和活动日志

查看并了解 Azure 监视诊断和活动日志提供的数据。 我们将基于这些数据架构创建引入管道。

### <a name="diagnostic-logs-example"></a>诊断日志示例

Azure 诊断日志由 Azure 服务发出，提供与该服务的操作相关的数据。 数据以 1 分钟时间粒度聚合。 每个诊断日志事件包含一条记录。 下面是在查询持续时间内 Azure 数据资源管理器指标事件架构的示例：

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>活动日志示例

Azure 活动日志是包含记录集合的订阅级日志。 这些日志可让用户洞察对订阅中的资源执行的操作。 与诊断日志不同，活动日志事件包含记录数组。 稍后在本教程中，我们需要拆分此记录数组。 下面是用于检查访问权限的活动日志事件示例：

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>在 Azure 数据资源管理器中设置引入管道 

Azure 数据资源管理器管道设置包含各种步骤，其中包括[表创建和数据引入](/azure/data-explorer/ingest-sample-data#ingest-data)。 此外，你可以处理、映射和更新数据。

### <a name="connect-to-azure-data-explorer-web-ui"></a>连接到 Azure 数据资源管理器 Web UI

1. 在 Azure 数据资源管理器的“AzureMonitoring”数据库中，选择“查询”打开 Azure 数据资源管理器 Web UI。

    ![Query](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>创建目标表

使用 Azure 数据资源管理器 Web UI 在 Azure 数据资源管理器数据库中创建目标表。

#### <a name="diagnostic-logs-table"></a>诊断日志表

1. 在 *AzureMonitoring* 数据库中创建 *DiagnosticLogsRecords* 表，用于通过 `.create table` 控制命令接收诊断日志记录：

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. 选择“运行”以创建该表。

    ![运行查询](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>活动日志表

由于活动日志结构不是表格式的，你需要处理数据并将每个事件扩展到一个或多个记录。 原始数据将引入到中间表 *ActivityLogsRawRecords*。 此时，数据已经过处理和扩展。 然后，将使用更新策略将扩展的数据引入到 *ActivityLogsRecords* 表。 因此，需要为活动日志引入创建两个单独的表。

1. 在 *AzureMonitoring* 数据库中创建 *ActivityLogsRecords* 表，用于接收活动日志记录。 运行以下 Azure 数据资源管理器查询以创建表：

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. 在 *AzureMonitoring* 数据库中创建中间数据表 *ActivityLogsRawRecords* 用于处理数据：

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>创建表映射

 数据格式为 `json`，因此数据映射是必需的。 `json` 映射将每个 JSON 路径映射到表列名。

#### <a name="diagnostic-logs-table-mapping"></a>诊断日志表映射

使用以下查询将数据映射到表：

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
{"column":"Timestamp","path":"$.time"},
{"column":"ResourceId","path":"$.resourceId"},
{"column":"MetricName","path":"$.metricName"},
{"column":"Count","path":"$.count"},
{"column":"Total","path":"$.total"},
{"column":"Minimum","path":"$.minimum"},
{"column":"Maximum","path":"$.maximum"},
{"column":"Average","path":"$.average"},
{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>活动日志表映射

使用以下查询将数据映射到表：

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>创建更新策略

1. 创建一个[函数](/azure/kusto/management/functions)用于扩展记录集合，使集合中的每个值收到一个单独的行。 使用 [`mvexpand`](/azure/kusto/query/mvexpandoperator) 运算符：

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events["identity.authorization"],
            IdentityClaims = events["identity.claims"],
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. 将[更新策略](/azure/kusto/concepts/updatepolicy)添加到目标表。 此策略针对 *ActivityLogsRawRecords* 中间数据表中任何新引入的数据自动运行查询，并将查询结果引入到 *ActivityLogsRecords* 表中：

    ```kusto
    .alter table ActivityLogRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>创建事件中心命名空间

Azure 诊断日志允许将指标导出到存储帐户或事件中心。 本教程通过事件中心路由指标。 在以下步骤中为诊断日志创建事件中心命名空间和事件中心。 Azure 监视将为活动日志创建事件中心 *insights-operational-logs*。

1. 在 Azure 门户中使用 Azure 资源管理器模板创建事件中心。 使用下面的按钮启动部署。 右键单击并选择“在新窗口中打开”，以便按本文中的剩余步骤操作。 单击“部署到 Azure”按钮可转到 Azure 门户。

    [![部署到 Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. 为诊断日志创建事件中心命名空间和事件中心。

    ![创建事件中心](media/ingest-data-no-code/event-hub.png)

    使用以下信息填写窗体。 对下表中未列出的任何设置使用默认值。

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 订阅 | 选择要用于事件中心的 Azure 订阅。|
    | 资源组 | *test-resource-group* | 创建新的资源组。 |
    | 位置 | 选择最符合需求的区域。 | 在其他资源所在的同一位置创建事件中心命名空间。
    | 命名空间名称 | *AzureMonitoringData* | 选择用于标识命名空间的唯一名称。
    | 事件中心名称 | *DiagnosticLogsData* | 事件中心位于命名空间下，该命名空间提供唯一的范围容器。 |
    | 使用者组名称 | *adxpipeline* | 创建使用者组名称。 使多个使用方应用程序各自获得事件流的单独视图。 |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>将 Azure 监视日志连接到事件中心

### <a name="diagnostic-logs-connection-to-event-hub"></a>将诊断日志连接到事件中心

选择要从其中导出指标的资源。 有多种资源类型允许导出诊断日志，这些资源包括事件中心命名空间、KeyVault、IoT 中心和 Azure 数据资源管理器群集。 本教程使用 Azure 数据资源管理器群集作为资源。

1. 在 Azure 门户中选择你的 Kusto 群集。

    ![诊断设置](media/ingest-data-no-code/diagnostic-settings.png)

1. 从左侧菜单中选择“诊断设置”。
1. 单击“启用诊断”链接。 此时会打开“诊断设置”窗口。

    ![“诊断设置”窗口](media/ingest-data-no-code/diagnostic-settings-window.png)

1. 在“诊断设置”窗格中：
    1. 为诊断日志数据命名：*ADXExportedData*
    1. 选中“所有指标”复选框（可选）。
    1. 选中“流式传输到事件中心”复选框。
    1. 单击“配置”。

1. 在“选择事件中心”窗格中，配置为导出到所创建的事件中心：
    1. 从下拉列表中**选择事件中心命名空间** *AzureMonitoringData*。
    1. 从下拉列表中**选择事件中心名称** *diagnosticlogsdata*。
    1. 从下拉列表中**选择事件中心策略名称**。
    1. 单击“确定”。

1. 单击“ **保存**”。 事件中心命名空间、名称和策略名称将显示在窗口中。

    ![保存诊断设置](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>将活动日志连接到事件中心

1. 在 Azure 门户的左侧菜单中，选择“活动日志”
1. 此时会打开“活动日志”窗口。 **单击“导出到事件中心”**

    ![活动日志](media/ingest-data-no-code/activity-log.png)

1. 在“导出活动日志”窗口中：
 
    ![导出活动日志](media/ingest-data-no-code/export-activity-log.png)

    1. 选择订阅。
    1. 在“区域”下拉列表中，选择“全选”
    1. 选中“导出到事件中心”复选框。
    1. 单击“选择服务总线命名空间”打开“选择事件中心”窗格。
    1. 在“选择事件中心”窗格中，从下拉菜单中进行选择：订阅、事件命名空间 *AzureMonitoringData* 和默认的事件中心策略名称。
    1. 单击“确定”。
    1. 单击窗口右上方的“保存”。 随即会创建名为 *insights-operational-logs* 的事件中心。

### <a name="see-data-flowing-to-your-event-hubs"></a>查看流入事件中心的数据

1. 等待几分钟来定义连接，并完成将活动日志导出到事件中心的过程。 转到事件中心命名空间以查看创建的事件中心。

    ![创建的事件中心](media/ingest-data-no-code/event-hubs-created.png)

1. 查看流入事件中心的数据：

    ![事件中心数据](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>将事件中心连接到 Azure 数据资源管理器

### <a name="diagnostic-logs-data-connection"></a>诊断日志数据连接

1. 在 Azure 数据资源管理器群集 *kustodocs* 中，选择左侧菜单中的“数据库”。
1. 在“数据库”窗口中，选择名为 *AzureMonitoring* 的数据库
1. 在左侧菜单中选择“数据引入”
1. 在“数据引入”窗口中，单击“+ 添加数据连接”
1. 在“数据连接”窗口中输入以下信息：

    ![事件中心连接](media/ingest-data-no-code/event-hub-data-connection.png)

    数据源：

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 数据连接名称 | *DiagnosticsLogsConnection* | 要在 Azure 数据资源管理器中创建的连接的名称。|
    | 事件中心命名空间 | *AzureMonitoringData* | 先前选择的用于标识命名空间的名称。 |
    | 事件中心 | *diagnosticlogsdata* | 你创建的事件中心。 |
    | 使用者组 | *adxpipeline* | 在创建的事件中心定义的使用者组。 |
    | | |

    目标表：

    有两个路由选项：静态和动态。 本教程使用静态路由（默认），你将在其中指定表名、文件格式和映射。 因此，请让“我的数据包含路由信息”保留未选中状态。

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 表 | *DiagnosticLogsRecords* | 在 *AzureMonitoring* 数据库中创建的表。 |
    | 数据格式 | *JSON* | 表中的格式。 |
    | 列映射 | *DiagnosticLogsRecordsMapping* | 在 *AzureMonitoring* 数据库中创建的映射，它将传入的 JSON 数据映射到 *DiagnosticLogsRecords* 的列名和数据类型。|
    | | |

1. 单击“创建”   

### <a name="activity-logs-data-connection"></a>活动日志数据连接

重复[诊断日志数据连接](#diagnostic-logs-data-connection)部分中的步骤，以创建活动日志数据连接。

1. 在“数据连接”窗口中插入以下设置：

    数据源：

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 数据连接名称 | *ActivityLogsConnection* | 要在 Azure 数据资源管理器中创建的连接的名称。|
    | 事件中心命名空间 | *AzureMonitoringData* | 先前选择的用于标识命名空间的名称。 |
    | 事件中心 | *insights-operational-logs* | 你创建的事件中心。 |
    | 使用者组 | *$Default* | 默认的使用者组。 如果需要，可以创建不同的使用者组。 |
    | | |

    目标表：

    有两个路由选项：静态和动态。 本教程使用静态路由（默认），你将在其中指定表名、文件格式和映射。 因此，请让“我的数据包含路由信息”保留未选中状态。

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 表 | *ActivityLogsRawRecords* | 在 *AzureMonitoring* 数据库中创建的表。 |
    | 数据格式 | *JSON* | 表中的格式。 |
    | 列映射 | *ActivityLogsRawRecordsMapping* | 在 *AzureMonitoring* 数据库中创建的映射，它将传入的 JSON 数据映射到 *ActivityLogsRawRecords* 的列名和数据类型。|
    | | |

1. 单击“创建”   

## <a name="query-the-new-tables"></a>查询新表

你已创建一个用于流送数据的管道。 通过群集引入数据默认需要 5 分钟，因此，请先让数据流动几分钟，然后开始查询。

### <a name="diagnostic-logs-table-query-example"></a>诊断日志表查询示例

以下查询分析 Azure 数据资源管理器诊断日志记录中的查询持续时间数据：

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

查询结果：

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="activity-logs-table-query-example"></a>活动日志表查询示例

以下查询分析 Azure 数据资源管理器活动日志记录中的数据：

```kusto
ActivityLogsRecords
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

## <a name="next-steps"></a>后续步骤

通过以下文章了解如何针对从 Azure 数据资源管理器提取的数据编写其他许多查询：

> [!div class="nextstepaction"]
> [为 Azure 数据资源管理器编写查询](write-queries.md)
