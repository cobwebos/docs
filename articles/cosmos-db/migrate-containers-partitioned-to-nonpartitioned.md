---
title: 将未分区的 Azure Cosmos 容器迁移到已分区容器
description: 了解如何将所有现有的非分区容器迁移到已分区容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: b7eed4089a65f62056027c70f08902f531567c17
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445271"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>将非分区容器迁移到已分区容器

Azure Cosmos DB 支持不使用分区键创建容器。 目前，可以使用 Azure CLI 和 Azure Cosmos DB Sdk （.Net、Java、NodeJs）创建非分区容器，该版本的版本小于或等于2.x。 不能使用 Azure 门户创建未分区的容器。 但是，此类未分区容器不具有弹性，并且具有 10 GB 的固定存储容量，且吞吐量限制为 10K RU/s。

非分区容器是旧的，应将现有的非分区容器迁移到已分区容器，以缩放存储和吞吐量。 Azure Cosmos DB 提供了一种系统定义的机制，用于将未分区容器迁移到已分区容器。 本文档介绍如何将所有现有的非分区容器自动迁移到已分区容器。 仅当在所有语言中使用 Sdk 版本的 Sdk 时，才能利用自动迁移功能。

> [!NOTE]
> 目前，无法使用本文档中所述的步骤迁移 Azure Cosmos DB MongoDB 和 Gremlin API 帐户。

## <a name="migrate-container-using-the-system-defined-partition-key"></a>使用系统定义的分区键迁移容器

为支持迁移，Azure Cosmos DB 在不具有分区键的所有容器上提供名为 `/_partitionkey` 的系统定义分区键。 迁移容器后，不能更改分区键定义。 例如，迁移到已分区容器的容器定义将如下所示：

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

迁移容器后，可以通过填充 `_partitionKey` 属性以及文档的其他属性来创建文档。 `_partitionKey` 属性表示文档的分区键。

选择正确的分区键对于充分利用预配的吞吐量非常重要。 有关详细信息，请参阅[如何选择分区键一](partitioning-overview.md)文。

> [!NOTE]
> 只有在所有语言中使用最新的 Sdk 版本的 Sdk 时，才能利用系统定义的分区键。

下面的示例演示使用系统定义的分区键创建文档的示例代码，并阅读该文档：

**文档的 JSON 表示形式**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

有关完整示例，请参阅[.net 示例][1]GitHub 存储库。
                      
## <a name="migrate-the-documents"></a>迁移文档

当使用分区键属性增强容器定义时，容器中的文档不会自动迁移。 这意味着系统分区键属性 `/_partitionKey` 路径不会自动添加到现有文档中。 你需要通过阅读在不使用分区键创建的文档来重新分区现有文档，并使用文档中的 `_partitionKey` 属性重新编写它们。

## <a name="access-documents-that-dont-have-a-partition-key"></a>访问没有分区键的文档

应用程序可以通过使用名为 "PartitionKey" 的特殊系统属性来访问没有分区键的现有文档，这是未迁移的文档的值。 可以在所有 CRUD 和查询操作中使用此属性。 下面的示例演示了从 NonePartitionKey 读取单个文档的示例。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

有关如何对文档进行重新分区的完整示例，请参阅[.net 示例][1]GitHub 存储库。 

## <a name="compatibility-with-sdks"></a>与 Sdk 的兼容性

旧版本的 Azure Cosmos DB Sdk （例如 V2. x. x. x. x. x）不支持系统定义的分区键属性。 因此，当从较旧的 SDK 读取容器定义时，它不包含任何分区键定义，这些容器的行为与以前完全相同。 使用早期版本的 Sdk 构建的应用程序将继续使用非分区方式，而无需进行任何更改。 

如果迁移的容器由 SDK 的最新版本和 V3 版本使用，并且你开始在新文档中填充系统定义的分区键，则不能再从较旧的 Sdk 访问（读取、更新、删除、查询）此类文档。

## <a name="known-issues"></a>已知问题

**使用 V3 SDK 查询未通过分区键插入的项的计数可能涉及更高的吞吐量消耗**

如果从 V3 SDK 中查询使用 V2 SDK 插入的项，或通过使用带 `PartitionKey.None` 参数的 V3 SDK 来插入的项，则在 FeedOptions 中提供了 `PartitionKey.None` 参数时，计数查询可能会消耗更多的 RU/秒。 如果未使用分区键插入其他项，则建议你不要提供 `PartitionKey.None` 参数。

如果使用不同的分区键值插入新项，则通过在 `FeedOptions` 中传递适当的密钥来查询此类项计数不会有任何问题。 插入具有分区键的新文档后，如果只需要查询不带分区键值的文档计数，则该查询可能会再次产生比常规分区集合更高的 RU/秒。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration