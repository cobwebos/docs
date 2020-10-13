---
title: 配置和使用 Azure Synapse Link for Azure Cosmos DB（预览版）
description: 了解如何为 Azure Cosmos DB 帐户启用 synapse 链接，创建启用了分析存储的容器，将 Azure Cosmos 数据库连接到 synapse 工作区，并运行查询。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/31/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: a634743441866c4f1a9f6d634efe0ff9e368b5e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757837"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>配置和使用 Azure Synapse Link for Azure Cosmos DB（预览版）

[适用于 Azure Cosmos DB 的 Azure Synapse 链接](synapse-link.md) 是 (HTAP) 功能的云本机混合事务性和分析处理，使你能够以 Azure Cosmos DB 中的操作数据运行近乎实时的分析。 Synapse Link 在 Azure Cosmos DB 和 Azure Synapse Analytics 之间建立紧密的无缝集成。

> [!IMPORTANT]
> 若要使用 Azure Synapse 链接，请确保在一个受支持区域 & Azure Synapse Analytics 工作区中预配 Azure Cosmos DB 帐户。 Azure Synapse 链接目前在以下 Azure 区域中提供：美国西部、美国东部、西2、北欧、西欧、美国中南部、东南亚、澳大利亚东部、东 U2、英国南部。

Azure Synapse 链接可用于 Azure Cosmos DB SQL API 容器 Azure Cosmos DB 或 Mongo DB 集合的 API。 使用以下步骤，通过用于 Azure Cosmos DB 的 Azure Synapse 链接运行分析查询：

* [为 Azure Cosmos DB 帐户启用 Synapse 链接](#enable-synapse-link)
* [创建 Azure Cosmos DB 容器启用的分析存储](#create-analytical-ttl)
* [将 Azure Cosmos DB 数据库连接到 Synapse 工作区](#connect-to-cosmos-database)
* [使用 Synapse Spark 查询分析存储](#query-analytical-store-spark)
* [使用 Synapse SQL 无服务器查询分析存储](#query-analytical-store-sql-on-demand)
* [使用 Synapse SQL 无服务器来分析和可视化 Power BI 中的数据](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>启用 Azure Cosmos DB 帐户的 Azure Synapse 链接

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos DB 帐户。

1. 导航到 Azure Cosmos DB 帐户并打开 " **功能** " 窗格。

1. 从“功能”列表选择“Synapse Link”****。

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="查找 Synapse Link 预览功能":::

1. 接下来，它会提示你在帐户上启用 Synapse Link。 选择“启用”。 完成此过程可能需要1到5分钟。

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="查找 Synapse Link 预览功能":::

1. 你的帐户现已启用，可以使用 Synapse Link。 接下来，了解如何创建启用了分析存储的容器，以便自动开始将操作数据从事务性存储复制到分析存储。

> [!NOTE]
> 启用 Synapse 链接不会自动打开分析存储。 在 Cosmos DB 帐户上启用 Synapse 链接后，在创建容器时对其启用分析存储，开始将操作数据复制到分析存储。 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> 使用分析存储创建 Azure Cosmos 容器

创建容器时，可以在 Azure Cosmos 容器上启用分析存储。 可以使用 Azure 门户，或在创建容器期间使用 Azure Cosmos DB SDK 配置 `analyticalTTL` 属性。

> [!NOTE]
> 目前，可以为新**** 容器（在新帐户和现有帐户中）启用分析存储。 可以使用[Azure Cosmos DB 迁移工具](cosmosdb-migrationchoices.md)将数据从现有容器迁移到新容器。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)或 [Azure Cosmos 资源管理器](https://cosmos.azure.com/)。

1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。

1. 选择“新容器”****，并输入数据库、容器、分区键和吞吐量详细信息的名称。 打开“分析存储”**** 选项。 启用分析存储后，它将创建一个容器，该容器的 `AnalyicalTTL` 属性设置为默认值 -1（无限保留期）。 此分析存储保留所有记录的历史版本。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="查找 Synapse Link 预览功能" 选项是否已打开。

### <a name="net-sdk"></a>.NET SDK

以下代码使用 .NET SDK 创建具有分析存储的容器。 将分析 TTL 属性设置为所需的值。 有关允许值的列表，请参阅[分析 TTL 支持的值](analytical-store-introduction.md#analytical-ttl)一文：

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

以下代码使用 Java V4 SDK 创建具有分析存储的容器。 将 `AnalyticalStoreTimeToLiveInSeconds` 属性设置为所需的值：

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 和 Azure Cosmos DB SDK 4.1.0 是所需的最低版本，此 SDK 仅与 SQL API 兼容。

第一步是确保至少使用 [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 版本 4.1.0：

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
下一步是使用 Azure Cosmos DB Python SDK 创建具有分析存储的容器：

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 更新分析存储生存时间

使用特定的 TTL 值启用分析存储后，可以在以后将其更新为其他有效值。 可以使用 Azure 门户或 SDK 更新此值。 有关各种分析 TTL 配置选项的信息，请参阅[分析 TTL 支持的值](analytical-store-introduction.md#analytical-ttl)一文。

#### <a name="azure-portal"></a>Azure 门户

如果通过 Azure 门户创建了已启用分析存储的容器，则它将包含默认为 -1 的分析 TTL。 使用以下步骤来更新此值：

1. 登录到 [Azure 门户](https://portal.azure.com/)或 [Azure Cosmos 资源管理器](https://cosmos.azure.com/)。

1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。

1. 选择已启用分析存储的现有容器。 展开容器并修改以下值：

  * 打开“规模和设置”窗口。****
  * 在“设置”**** 下，找到“分析存储生存时间”。
  * 选择“启用(无默认值)”或选择“启用”，然后设置一个 TTL 值**** ****
  * 单击“保存”以保存更改。

#### <a name="net-sdk"></a>.NET SDK

下面的代码演示如何使用 .NET SDK 更新分析存储的 TTL：

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java V4 SDK

下面的代码演示如何使用 Java V4 SDK 更新分析存储的 TTL：

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> 连接到 Synapse 工作区

使用[连接到 Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) 中的说明，了解如何使用 Azure Synapse Link 从 Azure Synapse Analytics Studio 访问 Azure Cosmos DB 数据库。

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> 使用 Azure Synapse Analytics 的 Apache Spark 查询分析存储

使用[查询 Azure Cosmos DB 分析存储](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)文章中的说明，了解如何使用 Synapse Spark 进行查询。 这篇文章提供了一些示例，说明如何通过 Synapse 笔势与分析存储交互。 右键单击某个容器时，即可看到这些笔势。 借助笔势，可以快速生成代码，并根据需要进行调整。 它们还适用于只需单击一下即可发现数据的功能。

## <a name="query-the-analytical-store-using-synapse-sql-serverless"></a><a id="query-analytical-store-sql-on-demand"></a> 使用 Synapse SQL 无服务器查询分析存储

Synapse SQL 无服务器 (预览功能，该功能以前称为 " **SQL 点播"**) 使你可以查询和分析使用 Azure Synapse 链接启用的 Azure Cosmos DB 容器中的数据。 你可以近实时分析数据，而不会影响事务工作负荷的性能。 它提供了一种熟悉的 T-sql 语法，用于查询分析存储中的数据，并通过 T-sql 接口集成到各种 BI 和即席查询工具。 若要了解详细信息，请参阅 [使用 SYNAPSE SQL 无服务器的查询分析存储](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) 一文。

> [!NOTE]
> 将 Azure Cosmos DB 分析存储与 Synapse SQL 无服务器配合使用当前位于封闭预览版下。 若要请求访问权限，请联系 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com)。

## <a name="use-synapse-sql-serverless-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>使用 Synapse SQL 无服务器来分析和可视化 Power BI 中的数据

可以生成 Synapse SQL 无服务器数据库，并通过 Synapse 链接查看 Azure Cosmos DB。 稍后，你可以查询 Azure Cosmos 容器，然后生成一个模型，该模型通过这些视图 Power BI 来反映该查询。 若要了解详细信息，请参阅如何使用 [SYNAPSE SQL 无服务器通过 Synapse 链接分析 Azure Cosmos DB 数据](synapse-link-power-bi.md) 。

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

[Azure 资源管理器模板](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store)为 SQL API Azure Cosmos DB 帐户创建启用 Synapse 链接。 此模板在一个区域中创建一个 Core (SQL) API 帐户，其中包含配置了启用分析 TTL 的容器和一个选择使用手动或自动缩放吞吐量的选项。 若要部署此模板，请在自述文件页上单击“部署到 Azure”****。

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Azure Synpase Link 入门 - 示例

可以在 [GitHub](https://aka.ms/cosmosdb-synapselink-samples) 上找到有关 Azure Synapse Link 入门的示例。 其中展示了具有 IoT 和零售方案的端到端解决方案。 你还可以在 [mongodb](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/MongoDB) 文件夹下的相同存储库中找到与 MONGODB Azure Cosmos DB API 对应的示例。 

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅下列文档：

* [Azure Synapse Link for Azure Cosmos DB。](synapse-link.md)

* [Azure Cosmos DB 分析存储概述。](analytical-store-introduction.md)

* [有关 Azure Synapse Link for Azure Cosmos 的常见问题。](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics 中的 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse 分析中的 SQL 无服务器运行时支持](../synapse-analytics/sql/on-demand-workspace-overview.md)。
