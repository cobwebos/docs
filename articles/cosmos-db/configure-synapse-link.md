---
title: 配置和使用 Azure Synapse Link for Azure Cosmos DB（预览版）
description: 了解如何为 Azure Cosmos 帐户启用 Azure Synapse Link，创建启用了分析存储的容器，将 Azure Cosmos 数据库连接到 Synapse 工作区，并运行查询。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/31/2020
ms.author: rosouz
ms.openlocfilehash: d77922d5957a0f6602e00621ebbabeaa3f3ac8f9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322460"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>配置和使用 Azure Synapse Link for Azure Cosmos DB（预览版）

Azure Synapse Link for Azure Cosmos 是一种云原生混合事务和分析处理 (HTAP) 功能，可用于在 Azure Cosmos DB 中对操作数据运行准实时分析。 Synapse Link 在 Azure Cosmos DB 和 Azure Synapse Analytics 之间建立紧密的无缝集成。


> [!IMPORTANT]
> 若要使用 Azure Synapse 链接，请确保在一个支持的区域中预配 Azure Cosmos 帐户 & Azure Synapse Analytics 工作区。 Azure Synapse 链接目前在以下 Azure 区域中提供：美国西部、美国东部、西2、北欧、西欧、美国中南部、东南亚、澳大利亚东部、东 U2、英国南部。

使用以下步骤，通过 Azure Synapse Link for Azure Cosmos 运行分析查询：

* [为 Azure Cosmos 帐户启用 Synapse Link](#enable-synapse-link)
* [创建启用了分析存储的 Azure Cosmos 容器](#create-analytical-ttl)
* [将 Azure Cosmos 数据库连接到 Synapse 工作区](#connect-to-cosmos-database)
* [使用 Synapse Spark 查询分析存储](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>启用 Azure Synapse Link for Azure Cosmos 帐户

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos 帐户。

1. 导航到你的 Azure Cosmos 帐户，打开“功能”**** 窗格。

1. 从“功能”列表选择“Synapse Link”****。

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="查找 Synapse Link 预览功能":::

1. 接下来，它会提示你在帐户上启用 Synapse Link。 选择“启用”。

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="启用 Synapse Link 功能":::

1. 你的帐户现已启用，可以使用 Synapse Link。 接下来，了解如何创建启用了分析存储的容器，以便自动开始将操作数据从事务性存储复制到分析存储。

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

[Azure 资源管理器模板](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store)为 SQL API 创建启用了 Synapse Link 的 Azure Cosmos 帐户。 此模板在一个区域中创建一个 Core (SQL) API 帐户，其中包含配置了启用分析 TTL 的容器和一个选择使用手动或自动缩放吞吐量的选项。 若要部署此模板，请在自述文件页上单击“部署到 Azure”。

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> 使用分析存储创建 Azure Cosmos 容器

创建容器时，可以在 Azure Cosmos 容器上启用分析存储。 可以使用 Azure 门户，或在创建容器期间使用 Azure Cosmos DB SDK 配置 `analyticalTTL` 属性。

> [!NOTE]
> 目前，可以为新**** 容器（在新帐户和现有帐户中）启用分析存储。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)或 [Azure Cosmos 资源管理器](https://cosmos.azure.com/)。

1. 导航到你的 Azure Cosmos 帐户，打开“数据资源管理器”**** 选项卡。

1. 选择“新容器”****，并输入数据库、容器、分区键和吞吐量详细信息的名称。 打开“分析存储”**** 选项。 启用分析存储后，它将创建一个容器，该容器的 `AnalyicalTTL` 属性设置为默认值 -1（无限保留期）。 此分析存储保留所有记录的历史版本。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="启用 Azure Cosmos 容器的分析存储":::

1. 如果你以前未在此帐户上启用 Synapse Link，系统将提示你执行此操作，因为它是创建启用了分析存储的容器的先决条件。 如果系统提示，请选择“启用 Synapse Link”****。

1. 选择“确定”****，创建启用了分析存储的 Azure Cosmos 容器。

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

Python 2.7 和 Azure Cosmos DB SDK 4.1.0 是所需的最低版本，SDK 仅与 SQL API 兼容。

第一步是确保至少使用版本4.1.0 的 [Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)：

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
下一步使用 Azure Cosmos DB Python SDK 创建包含分析存储的容器：

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

1. 导航到你的 Azure Cosmos 帐户，打开“数据资源管理器”选项卡。

1. 选择已启用分析存储的现有容器。 展开容器并修改以下值：

  * 打开“规模和设置”窗口。****
  * 在“设置”**** 下，找到“分析存储生存时间”。
  * 选择“启用(无默认值)”或选择“启用”，然后设置一个 TTL 值**** ****
  * 单击“保存” **** 以保存更改。

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

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> 使用 Synapse Spark 进行查询

使用[查询 Azure Cosmos DB 分析存储](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)文章中的说明，了解如何使用 Synapse Spark 进行查询。 这篇文章提供了一些示例，说明如何通过 Synapse 笔势与分析存储交互。 右键单击某个容器时，即可看到这些笔势。 借助笔势，可以快速生成代码，并根据需要进行调整。 它们还适用于只需单击一下即可发现数据的功能。

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Azure Synpase Link 入门 - 示例

可以在 [GitHub](https://aka.ms/cosmosdb-synapselink-samples) 上找到有关 Azure Synapse Link 入门的示例。 这些示例展示了物联网和零售场景的端到端解决方案。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅下列文档：

* [Azure Synapse Link for Azure Cosmos DB。](synapse-link.md)

* [Azure Cosmos DB 分析存储概述。](analytical-store-introduction.md)

* [有关 Azure Synapse Link for Azure Cosmos 的常见问题。](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics 中的 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse 分析中的 SQL 无服务器运行时支持](../synapse-analytics/sql/on-demand-workspace-overview.md)。
