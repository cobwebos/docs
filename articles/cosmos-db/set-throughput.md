---
title: 预配 Azure Cosmos DB 的吞吐量 | Microsoft Docs
description: 了解如何为 Azure Cosmos DB 容器、集合以及关系图和表设置预配吞吐量。
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: andrl
ms.openlocfilehash: 2c3e4806aef506ef9016699b46eadd5f8a187224
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037927"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>为 Azure Cosmos DB 容器和数据库设置和获取吞吐量

可通过 Azure 门户或客户端 SDK 为一个 Azure Cosmos DB 容器或一组容器设置吞吐量。 

**为单个容器预配吞吐量：** 为一组容器预配吞吐量时，所有这些容器共享预配的吞吐量。 为单个容器预配吞吐量可确保为该特定容器预留吞吐量。 在单个容器级别分配 RU/秒时，可以将容器创建为*固定*或*无限制*模式。 固定大小的容器最大限制为 10 GB，10,000 RU/s 吞吐量。 若要创建无限制容器，必须指定最低 1,000 RU/秒的吞吐量和一个[分区键](partition-data.md)。 由于数据可能需要跨多个分区拆分，因此需要选择一个基数较高（一百到几百万个非重复值）的分区键。 通过选择具有大量非重复值的分区键，可以确保 Azure Cosmos DB 能够统一缩放容器/表/图形与请求。 

**为一组容器或一个数据库预配吞吐量：** 为一个数据库预配吞吐量可在属于该数据库的所有容器间共享吞吐量。 在 Azure Cosmos DB 数据库中，可以让一组容器共享吞吐量，也可以让容器使用专用的吞吐量。 在一组容器中分配 RU/秒时，会将属于该组的容器视为“无限制”容器，必须指定一个分区键。

Azure Cosmos DB 会根据预配的吞吐量分配物理分区，以便托管容器并拆分/重新均衡分区中不断增长的数据。 容器级和数据库级吞吐量预配是不同的产品，在这两者之间切换需要将数据从源迁移到目标。 这意味着你需要创建新数据库或新集合，然后使用[批量执行程序库](bulk-executor-overview.md)或 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)迁移数据。 下图说明了不同级别的预配吞吐量：

![预配一个容器和一组容器的请求单位数](./media/request-units/provisioning_set_containers.png)

后续部分将介绍在不同级别为 Azure Cosmos DB 帐户配置吞吐量所需的步骤。 

## <a name="provision-throughput-by-using-azure-portal"></a>使用 Azure 门户预配吞吐量

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>为一个容器（集合/图/表）预配吞吐量

1. 登录到 [Azure 门户](https://portal.azure.com)。  
2. 在左侧导航中选择“所有资源”，找到自己的 Azure Cosmos DB 帐户。  
3. 可以在创建容器（集合、图、表）或更新现有容器的吞吐量时配置吞吐量。  
4. 若要在创建容器时分配吞吐量，请打开“数据资源管理器”边栏选项卡，然后选择“新建集合”（其他 API 可选择“新建图”或“新建表”）  
5. 在“添加集合”边栏选项卡中填充窗体。 下表描述了此边栏选项卡中的字段：  

   |**设置**  |**说明**  |
   |---------|---------|
   |数据库 ID  |  提供用于标识数据库的唯一名称。 数据库是一个或多个集合的逻辑容器。 数据库名称必须包含 1 到 255 个字符，不能包含 /、\\、#、? 或尾随空格。 |
   |集合 ID  | 提供用于标识集合的唯一名称。 集合 ID 与数据库名称的字符要求相同。 |
   |存储容量   | 此值表示数据库的存储容量。 预配单个集合的吞吐量时，存储容量可以是“固定的(10 GB)”，也可以是“无限制的”。 无限制的存储容量要求为数据设置分区键。  |
   |Throughput   | 每个集合和数据库都可以按每秒的请求单位数来设置吞吐量。  固定存储容量的最小吞吐量为每秒 400 请求单位（RU/秒），无限制存储容量的最小吞吐量设置为 1000 RU/秒。|

6. 输入这些字段的值以后，请选择“确定”以保存设置。  

   ![设置集合的吞吐量](./media/set-throughput/set-throughput-for-container.png)

7. 若要更新现有容器的吞吐量，请展开数据库和容器，然后单击“设置”。 在新窗口中键入新的吞吐量值，然后选择“保存”。  

   ![更新集合的吞吐量](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>为一组容器预配吞吐量，或者在数据库级别预配吞吐量

1. 登录到 [Azure 门户](https://portal.azure.com)。  
2. 在左侧导航中选择“所有资源”，找到自己的 Azure Cosmos DB 帐户。  
3. 可以在创建数据库或更新现有数据库的吞吐量时配置吞吐量。  
4. 若要在创建数据库时分配吞吐量，请打开“数据资源管理器”边栏选项卡，然后选择“新建数据库”  
5. 填充“数据库 ID”值，勾选“预配吞吐量”选项，然后配置吞吐量值。 可以使用最小吞吐量值“50,000 RU/秒”来预配数据库。  

   ![使用新数据库选项设置吞吐量](./media/set-throughput/set-throughput-with-new-database-option.png)

6. 若要更新现有数据库的吞吐量，请展开数据库和容器，然后单击“缩放”。 在新窗口中键入新的吞吐量值，然后选择“保存”。  

   ![更新数据库的吞吐量](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>为数据库中的一组容器和一个容器预配吞吐量

1. 登录到 [Azure 门户](https://portal.azure.com)。  
2. 在左侧导航中选择“所有资源”，找到自己的 Azure Cosmos DB 帐户。  
3. 创建数据库并向其分配吞吐量。 打开“数据资源管理器”边栏选项卡，然后选择“新建数据库”  
4. 填充“数据库 ID”值，勾选“预配吞吐量”选项，然后配置吞吐量值。 可以使用最小吞吐量值“50,000 RU/秒”来预配数据库。  

   ![使用新数据库选项设置吞吐量](./media/set-throughput/set-throughput-with-new-database-option.png)

5. 接下来，在数据库（已在上面的步骤中创建）中创建一个集合。 若要创建集合，请右键单击数据库，然后选择“新建集合”。  

6. 在“添加集合”边栏选项卡中输入集合的名称，然后输入分区键。 如果选择不分配吞吐量值，也可为该特定容器预配吞吐量。分配给数据库的吞吐量由集合共享。  

   ![（可选）设置容器的吞吐量](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>预配吞吐量时的注意事项

下面是一些注意事项，可以帮助你确定吞吐量预留策略。

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>在数据库级别预配吞吐量时的注意事项

在以下情况下，考虑在数据库级别（即针对一组容器）预配吞吐量：

* 至少有十几个容器可以部分或全部共享吞吐量。  

* 从专用于在 IaaS 托管的 VM 上运行或本地运行的单租户数据库（例如，NoSQL 数据库或关系数据库）迁移到 Azure Cosmos DB，并且有许多容器。  

* 需要根据数据库级别的共用吞吐量来考虑工作负荷的计划外峰值。  

* 需获取数据库中一组容器的聚合吞吐量，不需在单个容器上设置吞吐量。

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>在容器级别预配吞吐量时的注意事项

出现以下情况时，考虑在单个容器上预配吞吐量：

* Azure Cosmos DB 容器数目较少。  

* 需要在 SLA 所支持的某个给定容器上获取保证的吞吐量。

## <a name="throughput-ranges"></a>吞吐量范围

下表列出了容器可用的吞吐量：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p>单分区容器</p></td>
            <td valign="top"><p>分区容器</p></td>
            <td valign="top"><p><strong>一组容器</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小吞吐量</p></td>
            <td valign="top"><p>400 个请求单位/秒</p></td>
            <td valign="top"><p>1,000 个请求单位/秒</p></td>
            <td valign="top"><p>50,000 个请求单位/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大吞吐量</p></td>
            <td valign="top"><p>10,000 个请求单位/秒</p></td>
            <td valign="top"><p>不受限制</p></td>
            <td valign="top"><p>不受限制</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>使用 SQL API for .NET 设置吞吐量

### <a name="set-throughput-at-the-container-level"></a>在容器级别设置吞吐量
以下代码片段使用 SQL API 的 .NET SDK 创建每秒 3,000 个请求单位（适用于单个容器）的容器：

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-for-a-set-of-containers-at-the-database-level"></a>在数据库级别为一组容器设置吞吐量

以下代码片段使用 SQL API 的 .NET SDK 为一组容器预配每秒 100,000 个请求单位的吞吐量：

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = await client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 });

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB 运行一个预留模型来预配吞吐量。 也就是说，用户需要根据保留的吞吐量付费，不管实际使用的吞吐量是多少。 随着应用程序的负载、数据和使用模式的更改，可以通过 SDK 或 [Azure 门户](https://portal.azure.com)轻松增加和减少保留的 RU 数。

每个或每组容器都会映射到 Azure Cosmos DB 中的 `Offer` 资源，该资源包含有关预配吞吐量的元数据。 可以通过查找容器的相应服务资源，并使用新的吞吐量值来对它进行更新，来更改分配的吞吐量。 以下代码片段使用 .NET SDK 将容器的吞吐量更改为每秒 5,000 个请求单位。 更改吞吐量后，应该刷新任何现有的 Azure 门户窗口以显示已更改的吞吐量。 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

更改吞吐量不会影响一个或一组容器的可用性。 通常，新的保留吞吐量在几秒内就会在应用程序上生效。

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>使用 SQL API for Java 设置吞吐量

下面的代码片段检索当前吞吐量并将其更改为 500 RU/s。 若要查看完整的代码示例，请参阅 GitHub 上的 [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 文件。 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>使用 MongoDB API 门户指标获取吞吐量

准确估算 MongoDB API 数据库请求单位费用的最简单方法是使用 [Azure 门户](https://portal.azure.com)指标。 使用“请求数”和“请求费用”图表，可以估算每个操作消耗的请求单位数，以及每个操作相对于其他操作消耗的请求单位数。

![MongoDB API 门户指标][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a>超过 MongoDB API 中保留的吞吐量限制
如果应用程序超出针对某个容器或一组容器预配的吞吐量，则会对该应用程序进行速率限制，直到使用速率降至低于预配的吞吐量速率。 进行速率限制时，后端会结束请求并返回 `16500` 错误代码 -`Too Many Requests`。 默认情况下，在返回`Too Many Requests`错误代码之前，MongoDB API 会自动重试最多 10 次。 如果收到大量的`Too Many Requests`错误代码，可能需要考虑在应用程序的错误处理例程中添加重试逻辑，或者[提高容器的预配吞吐量](set-throughput.md)。

## <a id="GetLastRequestStatistics"></a>通过 MongoDB API 的 GetLastRequestStatistics 命令获取请求费用

MongoDB API 支持使用自定义命令 *getLastRequestStatistics* 来检索给定操作的请求费用。

例如，在 Mongo Shell 中执行所需的操作来验证其请求费用。
```
> db.sample.find()
```

接下来，执行命令 *getLastRequestStatistics*。
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

有一种方法可以估算应用程序所需的保留吞吐量：在针对应用程序所用代表性项运行典型操作时，记录与之相关的请求单位费用，然后估算预计每秒会执行的操作数。

> [!NOTE]
> 如果有多种项类型，它们的索引属性大小和数目截然不同，则记录与每种类型的典型项相关联的适用操作请求单位费用。
> 
> 

## <a name="throughput-faq"></a>吞吐量常见问题

**是否可以将吞吐量设置为小于 400 RU/s？**

400 RU/s 是 Cosmos DB 单个分区容器上可用的最小吞吐量（1000 RU/s 是分区容器的最小值）。 请求单位按 100 RU/s 间隔进行设置，但吞吐量不能设置为 100 RU/s 或小于 400 RU/s 的任何值。 如果在寻找一种经济高效的方法来开发和测试 Cosmos DB，则可以使用免费的 [ 模拟器](local-emulator.md)（可以在本地免费部署）。 

**如何使用 MongoDB API 设置吞吐量？**

没有可设置吞吐量的 MongoDB API 扩展。 建议使用 SQL API，如[使用 SQL API for .NET 设置吞吐量](#set-throughput-sdk)中所示。

## <a name="next-steps"></a>后续步骤

* 若要了解如何估算吞吐量和请求单位数，请参阅 [Azure Cosmos DB 中的请求单位数和估算吞吐量](request-units.md)

* 若要了解有关使用 Cosmos DB 进行预配和全球扩展的详细信息，请参阅[使用 Cosmos DB 进行分区和缩放](partition-data.md)。

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
