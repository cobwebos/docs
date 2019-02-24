---
title: 如何实现自定义同步以根据 Azure Cosmos DB 的更高可用性和性能进行优化
description: 了解如何实现自定义同步以根据 Azure Cosmos DB 的更高可用性和性能进行优化
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414875"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>如何实现自定义同步以根据更高可用性和性能进行优化

Azure Cosmos DB 提供五个妥善定义的一致性级别供你选择，以便在一致性、性能和可用性之间进行权衡。 非常一致性确保数据同步复制，并在 Azure Cosmos 帐户可用的每个区域中持久保存。 此配置虽然提供最高的持久性级别，但会降低性能和可用性。 如果应用程序想要在不影响性能的情况下根据需要控制/放宽数据持久性，可以在应用层采用自定义同步，以实现所需的持久性级别。

下图直观描绘了自定义同步模型。

![自定义同步](./media/how-to-custom-synchronization/custom-synchronization.png)

在此方案中，Azure Cosmos 容器将在跨多个大洲的多个区域中进行全局复制。 针对此方案中的所有区域使用非常一致性会影响性能。 为确保在不影响写入延迟的情况下提高数据持久性级别，应用程序可以使用两个共享同一会话令牌的客户端。

第一个客户端可将数据写入本地区域（例如美国西部）。 第二个客户端（例如位于美国东部）是用于确保同步的读取客户端。 通过将写入响应中的会话令牌传送到后续读取操作，读取操作可确保美国东部的写入操作保持同步。 Azure Cosmos DB 确保至少有一个区域可以看到写入操作，可以保证在发生区域性服务中断时，原始写入区域出现故障的情况下数据能够幸存。 在此方案中，每次写入操作将同步到美国东部，从而减小了在所有区域中使用非常一致性所存在的延迟。 在每个区域都发生写入操作的多主数据库方案中，可以扩展此模型，以并行同步到多个区域。

## <a name="configure-the-clients"></a>配置客户端

以下示例演示了出于此目的实例化两个客户端的数据访问层。

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

## <a name="implement-custom-synchronization"></a>实现自定义同步

初始化客户端后，应用程序可向本地区域（美国西部）执行写入，并可强制将写入操作同步到美国东部，如下所示。

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

可以扩展此模型，以并行同步到多个区域。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Cosmos DB 中的全局分布和一致性，请阅读以下文章：

* [在 Azure Cosmos DB 中选择适当的一致性级别](consistency-levels-choosing.md)

* [Azure Cosmos DB 中的一致性、可用性和性能权衡](consistency-levels-tradeoffs.md)

* [如何在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md)

* [Azure Cosmos DB 中的分区和数据分布](partition-data.md)
