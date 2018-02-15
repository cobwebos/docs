---
title: "Azure 事件网格和事件中心集成"
description: "介绍了如何使用 Azure 事件网格和事件中心将数据迁移到 SQL 数据仓库"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: dba17a860dffd87b3784c53cf288b7a312c77e33
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>将大数据流式传输到数据仓库

Azure [事件网格](overview.md)是一项智能事件路由服务，可用于对应用和服务的通知作出响应。 [事件中心捕获和事件网格示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)展示了如何将 Azure 事件中心捕获与 Azure 事件网格结合使用，从而将数据从事件中心顺畅迁移到 SQL 数据仓库。

![应用概览](media/event-grid-event-hubs-integration/overview.png)

当数据发送到事件中心时，捕获功能会从数据流中拉取数据，并生成存储 blob（其中数据为 Avro 格式）。 当捕获功能生成 blob 时，便会触发事件。 事件网格将事件数据分发给订阅者。 在此示例中，事件数据发送到 Azure Functions 终结点。 事件数据包括生成的 blob 的路径。 函数使用此 URL 检索文件，并将文件发送到数据仓库。

本文内容：

* 部署以下基础结构：
  * 已启用捕获的事件中心
  * 捕获事件文件的存储帐户
  * 用于托管函数应用的 Azure 应用服务计划
  * 用于处理事件的函数应用
  * 用于托管数据仓库的 SQL Server
  * 用于存储已迁移数据的 SQL 数据仓库
* 在数据仓库中创建表
* 将代码添加到函数应用
* 订阅事件
* 运行将数据发送到事件中心的应用
* 查看数据仓库中的已迁移数据

## <a name="about-the-event-data"></a>关于事件数据

事件网格将事件数据分发给订阅者。 下面示例中展示的事件数据用于创建捕获事件文件。 特别要注意 `data` 对象中的 `fileUrl` 属性。 函数应用获取此值，并用它来检索捕获事件文件。

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须满足以下先决条件：

* Azure 订阅。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Visual Studio 2017 版本 15.3.2 或更高版本](https://www.visualstudio.com/vs/)，并包含适用于以下用途的工作负载：.NET 桌面开发、Azure 开发、ASP.NET 和 Web 开发、Node.js 开发和 Python 开发。
* 已将 [EventHubsCaptureEventGridDemo 示例项目](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)下载到计算机上。

## <a name="deploy-the-infrastructure"></a>部署基础结构

为了缩短本文的篇幅，请使用资源管理器模板部署所需的基础结构。 若要查看已部署的资源，请查看[模板](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)。 请使用[支持的区域](overview.md)之一作为资源组位置。

对于 Azure CLI，请使用：

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

对于 PowerShell，请使用：

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

当看到提示时，输入密码值。

## <a name="create-a-table-in-sql-data-warehouse"></a>在 SQL 数据仓库中创建表

运行 [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 脚本，将表添加到数据仓库。 若要运行此脚本，请使用 Visual Studio 或门户中的查询编辑器。

要运行的脚本为：

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>发布 Azure Functions 应用

1. 在 Visual Studio 2017（15.3.2 或更高版本）中，打开 [EventHubsCaptureEventGridDemo 示例项目](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。

2. 在“解决方案资源管理器”中，右键单击“FunctionDWDumper”，再选择“发布”。

   ![发布函数应用](media/event-grid-event-hubs-integration/publish-function-app.png)

3. 选择“Azure Function App”，再选中“选择现有”。 选择“确定”。

   ![目标函数应用](media/event-grid-event-hubs-integration/pick-target.png)

4. 选择通过模板部署的函数应用。 选择“确定”。

   ![选择函数应用](media/event-grid-event-hubs-integration/select-function-app.png)

5. 在 Visual Studio 配置好配置文件后，选择“发布”。

   ![选择发布](media/event-grid-event-hubs-integration/select-publish.png)

6. 发布函数应用后，转到 [Azure门户](https://portal.azure.com/)。 选择资源组和函数应用。

   ![查看函数应用](media/event-grid-event-hubs-integration/view-function-app.png)

7. 选择函数。

   ![选择函数](media/event-grid-event-hubs-integration/select-function.png)

8. 获取函数的 URL。 创建事件订阅时，需要用到此 URL。

   ![获取函数 URL](media/event-grid-event-hubs-integration/get-function-url.png)

9. 复制此值。

   ![复制 URL](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>订阅事件

可以使用 Azure CLI 或门户订阅事件。 本文介绍了这两种方法。

### <a name="portal"></a>门户

1. 在“事件中心”命名空间中，选择左侧的“事件网格”。

   ![选择“事件网格”](media/event-grid-event-hubs-integration/select-event-grid.png)

2. 添加事件订阅。

   ![添加事件订阅](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. 输入事件订阅值。 使用复制的 Azure Functions URL。 选择“创建”。

   ![输入订阅值](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>Azure CLI

若要订阅事件，请运行以下命令（需要 Azure CLI 版本 2.0.24 或更高版本）：

```azurecli-interactive
namespaceid=$(az resource show --namespace Microsoft.EventHub --resource-type namespaces --name <your-EventHubs-namespace> --resource-group rgDataMigrationSample --query id --output tsv)
az eventgrid event-subscription create \
  --resource-id $namespaceid \
  --name captureEventSub \
  --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>运行应用以生成数据

至此，已完成设置事件中心、SQL 数据仓库、Azure 函数应用和事件订阅。 现在，解决方案可以将数据从事件中心迁移到数据仓库。 需要先配置几个值，然后再运行应用来生成事件中心数据。

1. 在门户中，选择“事件中心”命名空间。 选择“连接字符串”。

   ![选择“连接字符串”](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. 选择“RootManageSharedAccessKey”

   ![选择密钥](media/event-grid-event-hubs-integration/show-root-key.png)

3. 复制“连接字符串 - 主密钥”

   ![复制密钥](media/event-grid-event-hubs-integration/copy-key.png)

4. 返回到 Visual Studio 项目。 在 WindTurbineDataGenerator 项目中，打开 program.cs。

5. 替换两个常数值。 使用复制的 EventHubConnectionString 值。 使用 EventHubName 的事件中心名称。

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 生成解决方案。 运行 WindTurbineGenerator.exe 应用。 几分钟后，查询数据仓库中的表，获取已迁移数据。

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 有关事件中心捕获的介绍，请参阅[使用 Azure 门户启用事件中心捕获](../event-hubs/event-hubs-capture-enable-through-portal.md)。
* 若要详细了解如何设置和运行示例，请参阅[事件中心捕获和事件网格示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。