---
title: 预配 Azure Cosmos DB 的吞吐量 | Microsoft Docs
description: 了解如何为 Azure Cosmos DB 容器、集合以及关系图和表设置预配吞吐量。
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.openlocfilehash: bede91ed3ffc456740a0eb63ed7a15278e99ebe2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>为 Azure Cosmos DB 容器设置和获取吞吐量

可在 Azure 门户中或使用客户端 SDK 为 Azure Cosmos DB 容器或一组容器设置吞吐量。 

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

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>使用 Azure 门户设置吞吐量

1. 在新窗口中，打开 [Azure 门户](https://portal.azure.com)。
2. 在左侧栏中单击“Azure Cosmos DB”，或者单击底部的“所有服务”，滚动到“数据库”，并单击“Azure Cosmos DB”。
3. 选择 Cosmos DB 帐户。
4. 在新窗口中，单击导航菜单中的“数据资源管理器”。
5. 在新窗口中，展开数据库和容器，然后单击“缩放和设置”。
6. 在新窗口中，在“吞吐量”框中键入新的吞吐量值，然后单击“保存”。

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>使用 SQL API for .NET 设置吞吐量

下面的代码片段检索当前吞吐量并将其更改为 500 RU/s。 若要查看完整的代码示例，请参阅 GitHub 上的 [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) 项目。

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
// To change the throughput for a set of containers, use the database's selflink instead of the collection's selflink
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

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

## <a id="GetLastRequestStatistics"></a>通过 MongoDB API 的 GetLastRequestStatistics 命令获取吞吐量

MongoDB API 支持使用自定义命令 *getLastRequestStatistics* 来检索给定操作的请求费用。

例如，在 Mongo Shell 中，执行所需的操作来验证请求费用。
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

基于这一点，有一种方法可以估计应用程序所需的保留的吞吐量：记录与针对应用程序所使用的代表性项运行典型操作相关联的请求单位费用，并估计预计每秒执行的操作数。

> [!NOTE]
> 如果有多种项类型，它们的索引属性大小和数目截然不同，则记录与每种类型的典型项相关联的适用操作请求单位费用。
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>使用 MongoDB API 门户指标获取吞吐量

准确估算 MongoDB API 数据库请求单位费用的最简单方法是使用 [Azure 门户](https://portal.azure.com)指标。 使用“请求数”和“请求费用”图表，可以估算每个操作消耗的请求单位数，以及每个操作相对于其他操作消耗的请求单位数。

![MongoDB API 门户指标][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a>超过 MongoDB API 中保留的吞吐量限制
如果应用程序超出针对某个容器或一组容器预配的吞吐量，则会对该应用程序进行速率限制，直到使用速率降至低于预配的吞吐量速率。 进行速率限制时，后端会提前结束请求并返回 `16500` 错误代码 -`Too Many Requests`。 默认情况下，在返回`Too Many Requests`错误代码之前，MongoDB API 会自动重试最多 10 次。 如果收到大量的`Too Many Requests`错误代码，可能需要考虑在应用程序的错误处理例程中添加重试逻辑，或者[提高容器的预配吞吐量](set-throughput.md)。

## <a name="throughput-faq"></a>吞吐量常见问题

**是否可以将吞吐量设置为小于 400 RU/s？**

400 RU/s 是 Cosmos DB 单个分区容器上可用的最小吞吐量（1000 RU/s 是分区容器的最小值）。 请求单位按 100 RU/s 间隔进行设置，但吞吐量不能设置为 100 RU/s 或小于 400 RU/s 的任何值。 如果在寻找一种经济高效的方法来开发和测试 Cosmos DB，则可以使用免费的 [ 模拟器](local-emulator.md)（可以在本地免费部署）。 

**如何使用 MongoDB API 设置吞吐量？**

没有可设置吞吐量的 MongoDB API 扩展。 建议使用 SQL API，如[使用 SQL API for .NET 设置吞吐量](#set-throughput-sdk)中所示。

## <a name="next-steps"></a>后续步骤

若要了解有关使用 Cosmos DB 进行预配和全球扩展的详细信息，请参阅[使用 Cosmos DB 进行分区和缩放](partition-data.md)。

[1]: ./media/set-throughput/api-for-mongodb-metrics.png