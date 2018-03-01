---
title: "预配 Azure Cosmos DB 的吞吐量 | Microsoft Docs"
description: "了解如何为 Azure Cosmos DB 容器、集合以及关系图和表设置预配吞吐量。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: afbb3392a4726ea067bf19c117792b866d9e79f3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>为 Azure Cosmos DB 容器设置吞吐量

可在 Azure 门户中或使用客户端 SDK 为 Azure Cosmos DB 容器设置吞吐量。 

下表列出了容器可用的吞吐量：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p>单分区容器<strong></strong></p></td>
            <td valign="top"><p>分区容器<strong></strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小吞吐量</p></td>
            <td valign="top"><p>400 个请求单位/秒</p></td>
            <td valign="top"><p>1000 个请求单位/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大吞吐量</p></td>
            <td valign="top"><p>10,000 个请求单位/秒</p></td>
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

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>使用 SQL API for Java 设置吞吐量

此代码片段取自 [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 存储库中的 OfferCrudSamples.java 文件。 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 10300;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="throughput-faq"></a>吞吐量常见问题

**是否可以将吞吐量设置为小于 400 RU/s？**

400 RU/s 是 Cosmos DB 单个分区容器上可用的最小吞吐量（1000 RU/s 是分区容器的最小值）。 请求单位按 100 RU/s 间隔进行设置，但吞吐量不能设置为 100 RU/s 或小于 400 RU/s 的任何值。 如果在寻找一种经济高效的方法来开发和测试 Cosmos DB，则可以使用免费的 [ 模拟器](local-emulator.md)（可以在本地免费部署）。 

如何使用 MongoDB API 设置吞吐量？

没有可设置吞吐量的 MongoDB API 扩展。 建议使用 SQL API，如[使用 SQL API for .NET 设置吞吐量](#set-throughput-sdk)中所示。

## <a name="next-steps"></a>后续步骤

若要了解有关使用 Cosmos DB 进行预配和全球扩展的详细信息，请参阅[使用 Cosmos DB 进行分区和缩放](partition-data.md)。
