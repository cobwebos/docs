---
title: Azure Cosmos DB 中的区域故障转移 | Microsoft Docs
description: 了解如何使用 Azure Cosmos DB 完成手动和自动故障转移工作。
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977027de0627a6eca5f9eb5d1ab83dea5347c6d4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Azure Cosmos DB 中可实现业务连续性的自动区域故障转移
Azure Cosmos DB 可通过提供完全托管的[多区域数据库帐户](distribute-data-globally.md)简化数据的全球分布。这些帐户在一致性、可用性和性能方面进行了很好的平衡，各方面的效果都有相应的保证。 Cosmos DB 帐户具有以下特点：高可用性、10 毫秒以下的延迟、[妥善定义的一致性级别](consistency-levels.md)、使用多宿主 API 实现透明的区域故障转移，以及在全球范围内弹性缩放吞吐量和存储。 

Cosmos DB 支持显式和策略驱动型故障转移，方便用户在发生故障时控制端到端系统行为。 本文介绍：

* 如何在 Cosmos DB 中进行手动故障转移？
* 如何在 Cosmos DB 中进行自动故障转移，以及数据中心不可用时会发生什么情况？
* 如何在应用程序体系结构中使用手动故障转移？

你还可以在这个 Azure Cosmos DB 计划经理 Andrew Liu 提供的视频中了解区域故障转移，他演示了包括区域故障转移在内的全局分发功能。

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>配置多区域应用程序
在探讨故障转移模式之前，我们先介绍如何配置应用程序，以便在需要进行区域故障转移时充分利用多区域可用性，确保足够的还原能力。

* 首先，在多个区域中部署应用程序
* 为了确保从每个部署了应用程序的区域进行访问时的低延迟性，请通过一个支持的 SDK 为每个区域配置相应的[首选区域列表](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations)。

以下代码片段演示如何初始化多区域应用程序。 其中，Azure Cosmos DB 帐户 `contoso.documents.azure.com` 配置了两个区域 - 美国西部和北欧。 

* 应用程序部署在美国西部区域（例如，使用 Azure 应用服务） 
* 配置了 `West US` 作为第一个首选区域，确保读取时的低延迟性
* 配置了 `North Europe` 作为第二个首选区域（确保区域故障时的高可用性）

在 SQL API 中，该配置类似于以下代码片段：

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

该应用程序也部署在北欧区域，只是首选区域的顺序相反。 即，先指定北欧区域，确保读取时的低延迟性。 然后指定美国西部作为第二个首选区域，确保区域性故障时的高可用性。

以下体系结构示意图显示多区域应用程序部署，其中 Cosmos DB 和该应用程序配置为可在四个 Azure 地理区域使用。  

![通过 Azure Cosmos DB 实施全球分布式应用程序部署](./media/regional-failover/app-deployment.png)

现在，让我们看看 Cosmos DB 服务如何通过自动故障转移处理区域性故障。 

## <a id="AutomaticFailovers"></a>自动故障转移
在极少的情况下出现 Azure 区域性中断或数据中心中断时，Cosmos DB 会自动触发故障转移，转移受影响区域中存在的所有 Cosmos DB 帐户。 

**某个读取区域中断时会发生什么情况？**

读取区域位于其中一个受影响区域的 Cosmos DB 帐户会自动断开与其写入区域的连接，并会被标记为脱机。 Cosmos DB SDK 会实现一个区域性发现协议，以便自动检测可用的区域，从而将读取调用重定向到首选区域列表中的下一个可用区域。 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。 处理区域性故障转移不需在应用程序代码中进行更改。 在这整个过程中，Cosmos DB 会始终确保一致性。

![Azure Cosmos DB 中的读取区域故障](./media/regional-failover/read-region-failures.png)

受影响区域从中断恢复以后，该区域所有受影响的 Cosmos DB 帐户会自动由服务恢复。 然后，读取区域位于受影响区域的 Cosmos DB 帐户会自动与当前的写入区域同步并转为联机状态。 Cosmos DB SDK 发现有新区域可用以后，就会根据应用程序配置的首选区域列表评估该区域是否可选作当前的读取区域。 后续的读取会重定向到恢复的区域，不需更改应用程序代码。

**某个写入区域中断时会发生什么情况？**

如果受影响区域是当前写入区域且为 Azure Cosmos DB 帐户启用了自动故障转移，则会自动将该区域标记为脱机。 然后会针对受影响的 Azure Cosmos DB 帐户，将一个备用区域提升为写入区域。 可以通过 Azure 门户或[以编程方式](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange)启用自动故障转移并完全控制 Azure Cosmos DB 帐户的区域选择顺序。 

![Azure Cosmos DB 的故障转移优先级](./media/regional-failover/failover-priorities.png)

在自动故障转移过程中，Azure Cosmos DB 会根据指定的优先级顺序自动选择给定 Azure Cosmos DB 帐户的下一个写入区域。 应用程序可以使用 DocumentClient 类的 WriteEndpoint 属性检测写入区域中的更改。

![Azure Cosmos DB 中的写入区域故障](./media/regional-failover/write-region-failures.png)

受影响区域从中断恢复以后，该区域所有受影响的 Cosmos DB 帐户会自动由服务恢复。 

* 前一写入区域中存在的、服务中断期间未复制到读取区域的数据将作为冲突源发布。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并根据需要将更新后的数据写回 Azure Cosmos DB 帐户。 
* 前一写入区域将重新创建为读取区域并自动重新联机。 
* 可以通过 Azure 门户或[以编程方式](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)执行手动故障转移将已自动重新联机的读取区域重新配置为写入区域。

以下代码片段演示在受影响的区域从中断中恢复后如何处理冲突。

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>手动故障转移

除了自动故障转移，还可以通过动态方式将给定 Cosmos DB 帐户当前的写入区域手动更改为现有的读取区域之一。 可以通过 Azure 门户或[以编程方式](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)启动手动故障转移。 

手动故障转移可确保零收据丢失和零可用性丢失，并可针对指定的 Cosmos DB 帐户，将写入状态从旧写入区域恰当地转移到新写入区域。 与自动故障转移一样，在手动故障转移过程中，Cosmos DB SDK 可以自动处理写入区域更改，确保将调用自动重定向到新的写入区域。 管理故障转移不需在应用程序中更改代码或配置。 

![Azure Cosmos DB 中的手动故障转移](./media/regional-failover/manual-failovers.png)

下面是一些可以使用手动故障转移的常见情形：

**遵循时钟模型**：如果应用程序的流量模式可以根据一天的时间进行预测，则可根据一天的时间定期将写入状态转移到最活动的地理区域。

**服务更新**：某些全球分布式应用程序的部署可能需要在计划内服务更新期间通过流量管理器将流量重新路由到其他区域。 现在，此类应用程序部署可以使用手动故障转移，将写入状态保留给在服务更新时段会有活动流量的区域。

业务连续性和灾难恢复 (BCDR) 以及高可用性和灾难恢复 (HADR) 演练：大多数企业应用程序在开发和发布过程中都会进行业务连续性测试。 BCDR 和 HADR 测试通常是进行符合性认证的重要一步，并可确保在发生区域性中断时服务的可用性。 可以对使用 Cosmos DB 进行存储的应用程序实施 BCDR 就绪性测试，方法是：触发对 Cosmos DB 帐户的手动故障转移，以及/或者动态添加和删除某个区域。

本文探讨了如何在 Cosmos DB 中使用手动和自动故障转移，以及如何配置 Cosmos DB 帐户和应用程序才能让其全球可用。 可以利用 Cosmos DB 的全局复制支持改善端到端延迟情况，确保即使在发生区域故障的情况下，也可以顺利使用这些帐户和应用程序。 

## <a id="NextSteps"></a>后续步骤
* 了解 Cosmos DB 如何支持[全球分布](distribute-data-globally.md)
* 了解 [Azure Cosmos DB 的全局一致性](consistency-levels.md)
* 使用 Azure Cosmos DB 的 [SQL API](tutorial-global-distribution-sql-api.md) 进行多个区域开发
* 了解如何通过 Azure Cosmos DB 构建[多区域写入器体系结构](multi-region-writers.md)

