---
title: 将未分区的 Azure Cosmos DB 容器迁移到已分区的容器
description: 了解如何将所有现有的未分区容器迁移到已分区的容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241923"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>将未分区的容器迁移到已分区的容器

Azure Cosmos DB 支持创建不带分区键的容器。 目前可以使用 Azure CLI 以及版本低于或等于 2.x 的 Azure Cosmos DB SDK（.Net、Java、NodeJs）创建未分区的容器。 无法使用 Azure 门户创建未分区的容器。 但是，此类非分区容器不是弹性，并且具有固定的存储容量为 10 GB 和吞吐量限制为 10 个 K RU/秒。

未分区的容器已经过时，应将现有的未分区容器迁移到已分区的容器，以扩展存储量和吞吐量。 Azure Cosmos DB 提供系统定义的机制用于将未分区的容器迁移到已分区的容器。 本文档介绍如何将所有现有的未分区容器自动迁移到已分区的容器。 仅当使用的是支持所有语言的 V3 版 SDK 时，才能利用自动迁移功能。

> [!NOTE] 
> 目前，无法使用本文档中所述的步骤迁移 Azure Cosmos DB MongoDB 和 Gremlin API 帐户。 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>使用系统定义的分区键迁移容器

若要支持迁移，Azure Cosmos DB 定义名为的系统定义分区键`/_partitionkey`没有分区键的所有容器。 迁移容器后，无法更改分区键定义。 例如，已迁移到分区容器的容器的定义如下所示： 

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
 
迁移容器后，可以通过填充 `_partitionKey` 属性以及其他文档属性来创建文档。 `_partitionKey` 属性表示文档的分区键。 

选择适当的分区键非常重要，这样才能以最佳方式利用预配的吞吐量。 有关详细信息，请参阅[如何选择分区键](partitioning-overview.md)一文。 

> [!NOTE]
> 仅当使用的是支持所有语言的最新/V3 版 SDK 时，才能利用系统定义的分区键。

以下示例代码演示如何使用系统定义的分区键创建一个文档，然后读取该文档：

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

有关完整示例，请参阅 [.Net 示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub 存储库。 
                      
## <a name="migrate-the-documents"></a>迁移文档

尽管容器定义的分区键属性得到了增强，在容器的文档将不自动迁移。 这意味着，系统分区键属性 `/_partitionKey` 路径不会自动添加到现有的文档。 需要通过读取创建的不带分区键的文档将现有文档重新分区，然后使用文档中的 `_partitionKey` 属性重新写入这些文档。 

## <a name="access-documents-that-dont-have-a-partition-key"></a>访问不带分区键的文档

应用程序可以访问的情况下使用名为"CosmosContainerSettings.NonePartitionKeyValue"的特殊的系统属性中没有分区键的现有文档，这是非迁移文档的值。 可以在所有 CRUD 和查询操作中使用此属性。 以下示例演示如何从 NonePartitionKey 读取单个文档。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

有关如何将文档重新分区的完整示例，请参阅 [.Net 示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub 存储库。 

## <a name="compatibility-with-sdks"></a>与 SDK 的兼容性

较旧版本的 Azure Cosmos DB Sdk 如 V2.x.x 和 V1.x.x 不支持系统定义的分区键属性。 因此，当从较旧的 SDK 读取容器定义，它不包含任何分区键定义和这些容器的行为将与之前完全相同。 使用旧版 SDK 生成的应用程序将继续使用未分区的容器，就如同未发生任何更改一样。 

如果迁移的容器已由最新/V3 版 SDK 使用，当你开始在新文档中填充系统定义的分区键时，不再可以从旧版 SDK 访问（读取、更新、删除、查询）此类文档。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](account-overview.md)