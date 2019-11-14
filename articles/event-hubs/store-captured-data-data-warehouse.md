---
title: 教程：将事件数据迁移到 SQL 数据仓库 - Azure 事件中心
description: 教程：本教程介绍如何使用事件网格触发的 Azure 函数将事件中心的数据捕获到 SQL 数据仓库中。
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 92c414afbb8121eb03353c79dfe3a51e0cfa7ec0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718887"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>教程：使用事件网格和 Azure Functions 将捕获的事件中心数据迁移到 SQL 数据仓库

若要将事件中心的流式处理数据自动传递到 Azure Blob 存储或 Azure Data Lake Store，最容易的方式是使用事件中心[捕获](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)。 可以随后处理数据并将其传递到所选的任何其他存储目标，例如 SQL 数据仓库或 Cosmos DB。 本教程介绍如何使用[事件网格](https://docs.microsoft.com/azure/event-grid/overview)触发的 Azure 函数将事件中心的数据捕获到 SQL 数据仓库中。

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   首先，使用启用的**捕获**功能创建一个事件中心并将 Azure Blob 存储设置为目标。 WindTurbineGenerator 生成的数据会流式传输到事件中心，然后以 Avro 文件形式自动捕获到 Azure 存储中。 
*   接下来，请创建一个 Azure 事件网格订阅，使用事件中心命名空间作为其源，使用 Azure Function 终结点作为其目标。
*   通过事件中心捕获功能将新的 Avro 文件传递到 Azure 存储 Blob 时，事件网格就会通过 Blob URI 通知 Azure Function。 然后，Function 会将数据从 Blob 迁移到 SQL 数据仓库。

在本教程中，将执行以下操作： 

> [!div class="checklist"]
> * 部署基础结构
> * 将代码发布到 Functions 应用
> * 通过 Functions 应用创建事件网格订阅
> * 将示例数据流式传输到事件中心。 
> * 在 SQL 数据仓库中验证捕获的数据

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual Studio 2019](https://www.visualstudio.com/vs/)。 在安装时，请确保安装以下工作负荷：.NET 桌面开发、Azure 开发、ASP.NET 和 Web 开发、Node.js 开发、Python 开发
- 下载 [Git 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。 示例解决方案包含以下组件：
    - *WindTurbineDataGenerator* – 一个简单的发布服务器，可以将示例性的风力涡轮机数据发送到启用了捕获功能的事件中心
    - *FunctionDWDumper* – 一个 Azure Function，可以在 Avro 文件捕获到 Azure 存储 Blob 时接收事件网格通知。 它接收 Blob 的 URI 路径、读取其内容并将该数据推送到 SQL 数据仓库。

### <a name="deploy-the-infrastructure"></a>部署基础结构
使用 Azure PowerShell 或 Azure CLI 通过此 [Azure 资源管理器模板](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json)部署本教程所需的基础结构。 此模板可创建以下资源：

-   已启用捕获功能的事件中心
-   适用于已捕获事件数据的存储帐户
-   用于托管 Functions 应用的 Azure 应用服务计划
-   用于处理已捕获事件文件的 Function App
-   用于托管数据仓库的 SQL Server
-   用于存储已迁移数据的 SQL 数据仓库

以下部分提供的 Azure CLI 和 Azure PowerShell 命令适用于部署本教程所需的基础结构。 在运行这些命令之前，请更新以下对象的名称： 

- Azure 资源组 
- 资源组的区域
- 事件中心命名空间
- 事件中心
- Azure SQL Server
- SQL 用户（和密码）
- Azure SQL 数据库
- Azure 存储 
- Azure Functions 应用

这些脚本需要一定的时间来创建所有 Azure 项目。 请等待脚本完成，然后再继续操作。 如果部署因某种原因而失败，请删除资源组，修复报告的问题，然后重新运行命令。 

#### <a name="azure-cli"></a>Azure CLI
若要通过 Azure CLI 部署该模板，请使用以下命令：

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
若要通过 PowerShell 部署该模板，请使用以下命令：

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>在 SQL 数据仓库中创建表 
在门户中使用 [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md)、[SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) 或查询编辑器，通过运行 [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 脚本在 SQL 数据仓库中创建表。 

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

## <a name="publish-code-to-the-functions-app"></a>将代码发布到 Functions 应用

1. 在 Visual Studio 2019 中打开解决方案 EventHubsCaptureEventGridDemo.sln  。

1. 在“解决方案资源管理器”中，右键单击“FunctionEGDWDumper”  ，再选择“发布”  。

   ![发布函数应用](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. 选择“Azure Function App”  ，再选中“选择现有”  。 选择“发布”  。

   ![目标函数应用](./media/store-captured-data-data-warehouse/pick-target.png)

1. 选择通过模板部署的函数应用。 选择“确定”  。

   ![选择函数应用](./media/store-captured-data-data-warehouse/select-function-app.png)

1. 在 Visual Studio 配置好配置文件后，选择“发布”  。

   ![选择发布](./media/store-captured-data-data-warehouse/select-publish.png)

发布函数后，即可订阅事件中心的捕获事件！


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>通过 Functions 应用创建事件网格订阅
 
1. 转到 [Azure 门户](https://portal.azure.com/)。 选择资源组和函数应用。

   ![查看函数应用](./media/store-captured-data-data-warehouse/view-function-app.png)

1. 选择函数。

   ![选择函数](./media/store-captured-data-data-warehouse/select-function.png)

1. 选择“添加事件网格订阅”  。

   ![添加订阅](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. 为事件网格订阅指定名称。 使用“事件中心命名空间”  作为事件类型。 提供用于选择事件中心命名空间实例的值。 将订阅服务器终结点保留为提供的值。 选择“创建”  。

   ![创建订阅](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>生成示例数据  
现在已设置事件中心、SQL 数据仓库、Azure Function App 和事件网格订阅。 在源代码中更新连接字符串和事件中心以后，即可运行 WindTurbineDataGenerator.exe 以生成发往事件中心的数据流。 

1. 在门户中，选择“事件中心”命名空间。 选择“连接字符串”  。

   ![选择“连接字符串”](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. 选择“RootManageSharedAccessKey” 

   ![选择密钥](./media/store-captured-data-data-warehouse/show-root-key.png)

3. 复制“连接字符串 - 主密钥” 

   ![复制密钥](./media/store-captured-data-data-warehouse/copy-key.png)

4. 返回到 Visual Studio 项目。 在 *WindTurbineDataGenerator* 项目中，打开 *program.cs*。

5. 将 **EventHubConnectionString** 和 **EventHubName** 的值更新为事件中心的连接字符串和名称。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 生成解决方案，然后运行 WindTurbineGenerator.exe 应用程序。 

## <a name="verify-captured-data-in-data-warehouse"></a>在数据仓库中验证捕获的数据
几分钟后，查询 SQL 数据仓库中的表。 可以看到 WindTurbineDataGenerator 生成的数据已被 Azure Function 流式传输到事件中心，接着捕获到 Azure 存储容器中，然后迁移到 SQL 数据仓库。  

## <a name="next-steps"></a>后续步骤 
可以将强大的数据可视化工具与数据仓库配合使用，以便获取可行的见解。

本文介绍如何[将 Power BI 与 SQL 数据仓库配合使用](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi)



