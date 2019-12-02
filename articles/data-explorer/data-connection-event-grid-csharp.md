---
title: 使用为 Azure 数据资源管理器创建事件网格数据连接C#
description: 本文介绍如何使用C#为 Azure 数据资源管理器创建事件网格数据连接。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0accf502df3616a686a34fc6c96cb2cfc47e6db1
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667824"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>使用为 Azure 数据资源管理器创建事件网格数据连接C#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 模板](data-connection-event-grid-resource-manager.md)


Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure 数据资源管理器提供从事件中心、IoT 中心和写入 blob 容器的 blob 的引入（数据加载）。 本文介绍如何使用C#为 Azure 数据资源管理器创建事件网格数据连接。

## <a name="prerequisites"></a>必备组件

* 如果尚未安装 Visual Studio 2019，可以下载并使用**免费的** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。
* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 创建[群集和数据库](create-cluster-database-csharp.md)
* 创建[表和列映射](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* 设置[数据库和表策略](database-table-policies-csharp.md)（可选）
* [使用事件网格订阅创建存储帐户](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)。

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>添加事件网格数据连接

下面的示例演示如何以编程方式添加事件网格数据连接。 请参阅[在 Azure 中创建事件网格数据连接数据资源管理器](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)使用 Azure 门户添加事件网格数据连接。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**设置** | **建议的值** | **字段说明**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 租户 ID。 也称为目录 ID。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 用于创建资源的订阅 ID。|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 可以访问租户中资源的应用程序的客户端 ID。|
| clientSecret | *xxxxxxxxxxxxxx* | 可以访问租户中资源的应用程序的客户端机密。 |
| resourceGroupName | *testrg* | 包含群集的资源组的名称。|
| clusterName | mykustocluster | 群集的名称。|
| databaseName | mykustodatabase | 群集中的目标数据库的名称。|
| dataConnectionName | *myeventhubconnect* | 所需的数据连接名称。|
| tableName | *StormEvents* | 目标数据库中目标表的名称。|
| mappingRuleName | *StormEvents_CSV_Mapping* | 与目标表相关的列映射的名称。|
| dataFormat | *.csv* | 消息的数据格式。|
| eventHubResourceId | *资源 ID* | 事件中心的资源 ID，事件网格将配置为发送事件。 |
| storageAccountResourceId | *资源 ID* | 保存用于引入的数据的存储帐户的资源 ID。 |
| consumerGroup | *$Default* | 事件中心的使用者组。|
| 位置 | *美国中部* | 数据连接资源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
