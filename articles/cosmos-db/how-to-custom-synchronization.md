---
title: 如何实现自定义同步以根据 Azure Cosmos DB 的更高可用性和性能进行优化
description: 了解如何实现自定义同步以根据 Azure Cosmos DB 的更高可用性和性能进行优化。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 8fce14496b9f8fa17f2dbfd04b7ea42f1495a8a9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093338"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>实现自定义同步以根据更高可用性和性能进行优化

Azure Cosmos DB 提供[五个定义明确的一致性级别](consistency-levels.md)供你选择，以便在一致性、性能和可用性之间进行权衡。 非常一致性可确保数据同步复制，并在 Azure Cosmos 帐户可用的每个区域中持久保存。 此配置提供最高的持久性级别，但会降低性能和可用性。 如果希望应用程序在不影响可用性的情况下根据需要控制或放宽数据持久性，可以在应用层使用“自定义同步”，以实现所需的持久性级别。

下图直观描绘了自定义同步模型：

![自定义同步](./media/how-to-custom-synchronization/custom-synchronization.png)

在此方案中，Azure Cosmos 容器将在多个大洲的多个区域中进行全局复制。 针对此方案中的所有区域使用非常一致性会影响性能。 为确保在不影响写入延迟的情况下提高数据持久性级别，应用程序可以使用两个共享同一[会话令牌](how-to-manage-consistency.md#utilize-session-tokens)的客户端。

第一个客户端可将数据写入本地区域（例如美国西部）。 第二个客户端（例如位于美国东部的客户端）是用于确保同步的读取客户端。 通过将写入响应中的会话令牌传送到后续读取操作，读取操作可确保美国东部的写入操作保持同步。 Azure Cosmos DB 确保至少有一个区域可以看到写入操作， 可以保证在发生区域性服务中断时，原始写入区域出现故障的情况下数据能够幸存。 在此方案中，每次写入操作将同步到美国东部，从而减小了在所有区域中使用非常一致性所存在的延迟。 在每个区域都发生写入操作的多主数据库方案中，可以扩展此模型，使之以并行方式同步到多个区域。

## <a name="configure-the-clients"></a>配置客户端

以下示例演示了为了自定义同步而实例化两个客户端的数据访问层：

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>实现自定义同步

初始化客户端后，应用程序可向本地区域（美国西部）执行写入，并强制将写入操作同步到美国东部，如下所示。

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

可以扩展此模型，使之以并行方式同步到多个区域。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Cosmos DB 中的全局分布和一致性，请阅读以下文章：

* [在 Azure Cosmos DB 中选择适当的一致性级别](consistency-levels-choosing.md)
* [Azure Cosmos DB 中的一致性、可用性和性能权衡](consistency-levels-tradeoffs.md)
* [在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md)
* [Azure Cosmos DB 中的分区和数据分布](partition-data.md)
