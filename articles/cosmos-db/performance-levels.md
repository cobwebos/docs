---
title: 已停用的 Azure Cosmos DB 性能级别 | Microsoft Docs
description: 了解以前 Azure Cosmos DB 中可用的 S1、S2 和 S3 性能级别。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d1bb7551e6dfb6c42853ab95096f17f5285c69c1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796642"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>停用 S1、S2 和 S3 性能级别

> [!IMPORTANT] 
> 本文中所讨论的 S1、S2 和 S3 性能级别即将停用，到时将不再适用于新的 Azure Cosmos DB 帐户。
>

本文概述 S1、S2 和 S3 性能级别，并介绍如何将使用这些性能级别的集合迁移到单分区集合。 阅读本文之后，能够回答以下问题：

- [为何要停用 S1、S2 和 S3 性能级别？](#why-retired)
- [单分区集合和分区集合与 S1、S2、S3 性能级别相比存在哪些差别？](#compare)
- [我需要做些什么才能确保不间断地访问我的数据？](#uninterrupted-access)
- [迁移后我的集合会发生怎样的变化？](#collection-change)
- [迁移到单分区集合后，帐单会发生怎样的变化？](#billing-change)
- [如果需要 10 GB 以上的存储该怎么办？](#more-storage-needed)
- [在计划的迁移期限之前，是否可以在 S1、S2 和 S3 性能级别之间切换？](#change-before)
- [如何自行从 S1、S2、S3 性能级别迁移到单分区集合？](#migrate-diy)
- [如果我是 EA 客户，我会受到怎样的影响？](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>为何要停用 S1、S2 和 S3 性能级别？

S1、S2 和 S3 性能级别无法提供标准 Azure Cosmos DB 服务可提供的灵活性。 使用 S1、S2、S3 性能级别时，吞吐量和存储容量都是预设的，不具有弹性。 现在，Azure Cosmos DB 可让用户自定义吞吐量和存储，随着需求的变化，它们能够以大幅提高的灵活性进行缩放。

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>单分区集合和分区集合与 S1、S2、S3 性能级别相比存在哪些差别？

下表对单分区集合和分区集合与 S1、S2、S3 性能级别提供的吞吐量及存储选项做了比较。 下面是美国东部 2 区域的一个示例：

|   |分区集合|单分区集合|S1|S2|S3|
|---|---|---|---|---|---|
|最大吞吐量|不受限制|10K RU/秒|250 RU/秒|1 K RU/秒|2.5 K RU/秒|
|最小吞吐量|2.5 K RU/秒|400 RU/秒|250 RU/秒|1 K RU/秒|2.5 K RU/秒|
|最大存储|不受限制|10 GB|10 GB|10 GB|10 GB|
|价格（每月）|吞吐量：6 美元/100 RU/秒<br><br>存储：0.25 美元/GB|吞吐量：6 美元/100 RU/秒<br><br>存储：0.25 美元/GB|25 美元|50 美元|100 美元|

是 EA 客户？ 如果是，请参阅[如果我是 EA 客户，我会受到怎样的影响？](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>我需要做些什么才能确保不间断地访问我的数据？

如果有 S1、S2 或 S3 集合，则应[使用 .NET SDK](#migrate-diy) 以编程方式将该集合迁移到单分区集合。 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>迁移后我的集合会发生怎样的变化？

如果有 S1 集合，可以将该集合迁移到吞吐量为 400 RU/秒的单分区集合。 400 RU/秒是单分区集合提供的最低吞吐量。 但是，单分区集合中 400 RU/秒吞吐量的费用大致与 S1 集合中 250 RU/秒吞吐量的费用相同 - 因此，不需要为额外的 150 RU/秒付费。

如果有 S2 集合，可以将该集合迁移到吞吐量为 1000 RU/秒的单分区集合。 不会察觉到吞吐量级别有变化。

如果有 S3 集合，可以将该集合迁移到吞吐量为 2500 RU/秒的单分区集合。 不会察觉到吞吐量级别有变化。

对于上面的每种情况，在迁移集合后，便可以根据需要自定义吞吐量级别，或者将它调高和调低，以便向用户提供低延迟的访问。 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>迁移到单分区集合后，帐单会发生怎样的变化？

假设你在美国东部区域使用 10 个 S1 集合，其中每个集合的存储为 1 GB，现在要将这 10 个 S1 集合迁移到吞吐量为 400 RU/秒（最低级别）的 10 个单分区集合。 如果将 10 个单分区集合保留一整月，帐单将如下所示：

![10 个 S1 集合价格与 10 个单分区集合价格的比较](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>如果需要 10 GB 以上的存储该怎么办？

无论使用的是 S1、S2 或 S3 性能级别的集合还是单分区集合，它们的可用存储都是 10 GB。可以使用 Azure Cosmos DB 数据迁移工具将数据迁移到分区集合，它们的存储空间几乎无限。 有关分区集合优势的信息，请参阅 [Azure Cosmos DB 中的分区和缩放](sql-api-partition-data.md)。 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>在计划的迁移期限之前，是否可以在 S1、S2 和 S3 性能级别之间切换？

只有使用 S1、S2 和 S3 性能的现有帐户才能[使用 .NET SDK](#migrate-diy) 以编程方式切换和更改性能级别层。 如果从 S1、S3 或 S3 切换到了单分区集合，将无法恢复 S1、S2 或 S3 性能级别。

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>如何自行从 S1、S2、S3 性能级别迁移到单分区集合？

可以[使用 .NET SDK](#migrate-diy) 以编程方式从 S1、S2 和 S3 性能级别迁移到单分区集合。 可以在计划的迁移期限之前自行执行此操作，即可享用单分区集合提供的灵活吞吐量选项。

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>使用 .NET SDK 迁移到单分区集合

本部分只介绍使用 [SQL .NET API](sql-api-sdk-dotnet.md) 更改集合的性能级别，但对于我们的其他 SDK，过程也是相似的。

下面是可将集合吞吐量更改为每秒 5,000 个请求单位的代码片段：
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

请访问 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) 以查看其他示例并了解更多有关服务方法的信息：

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>如果我是 EA 客户，我会受到怎样的影响？

EA 客户在结束其当前合同之前，价格受到保护。

## <a name="next-steps"></a>后续步骤
若要详细了解 Azure Cosmos DB 的定价和管理数据的相关信息，请浏览以下资源：

1.  [Cosmos DB 中的分区数据](sql-api-partition-data.md)。 了解单分区容器与分区容器之间的差别，以及有关实施分区策略以进行无缝缩放的提示。
2.  [Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)。 了解吞吐量预配费用和存储使用费用。
3.  [请求单位](request-units.md)。 了解不同操作类型（例如读取、写入和查询）的吞吐量消耗。
