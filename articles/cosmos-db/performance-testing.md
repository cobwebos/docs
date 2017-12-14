---
title: "Azure Cosmos DB 缩放和性能测试 | Microsoft Docs"
description: "了解如何执行 Azure Cosmos DB 缩放和性能测试"
keywords: "性能测试"
services: cosmos-db
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: f72a75f2750d4838566815bece5c2221359ece29
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>执行 Azure Cosmos DB 缩放和性能测试

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

性能和规模测试是应用程序开发过程中的关键步骤。 对许多应用程序而言，数据库层对整体性能和可伸缩性有重大影响。 因此，它是性能测试的关键组成部分。 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 专为弹性缩放和可预测性能而构建。 这些功能使它非常适合需要高性能数据库层的应用程序。 

要对 Azure Cosmos DB 工作负载实施性能测试的开发人员可将本文作为参考。 此外，本文还可用于评估 Azure Cosmos DB 是否适用于高性能应用程序方案。 本文重点演示隔离的数据库性能测试，但也提供适用于生产应用程序的最佳实践。

阅读本文之后，能够回答以下问题： 

* 在哪里可以找到可用于 Azure Cosmos DB 性能测试的示例 .NET 客户端应用程序？ 
* 如何从客户端应用程序实现 Azure Cosmos DB 的高吞吐量级别？

若要开始处理代码，请从 [Azure Cosmos DB 性能测试示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)下载项目。 

> [!NOTE]
> 此应用程序的目标是演示如何使用少量的客户端计算机从 Azure Cosmos DB 中获得更佳性能。 此示例的目标不是实现 Azure Cosmos DB 的峰值吞吐量容量（可以不受限制地缩放）。
> 
> 

如果正在寻找用于提高 Azure Cosmos DB 性能的客户端配置选项，请参阅 [Azure Cosmos DB 性能提示](performance-tips.md)。

## <a name="run-the-performance-testing-application"></a>运行性能测试应用程序
最快的入门方法是根据下列步骤编译并运行 .NET 示例。 也可以查看源代码，然后在自己的客户端应用程序中实施类似的配置。

**步骤 1：**从 [Azure Cosmos DB 性能测试示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)下载项目，或创建 GitHub 存储库分支。

**步骤 2：**在 App.config 中修改 EndpointUrl、AuthorizationKey、CollectionThroughput 和 DocumentTemplate（可选）的设置。

> [!NOTE]
> 为集合预配高吞吐量之前，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)估算每个集合的成本。 Azure Cosmos DB 按小时对存储和吞吐量单独计费。 可在测试完成后，删除或降低 Azure Cosmos DB 集合的吞吐量以节省成本。
> 
> 

**步骤 3：**从命令行编译并运行控制台应用。 应会看到如下输出：

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**步骤 4（如有必要）：**工具报告的吞吐量（RU/秒）应该等于或大于预配的集合吞吐量。 如果情况并非如此，以较小的增量提高 DegreeOfParallelism 可帮助达到该限制。 如果客户端应用的吞吐量达到持平状态，请在其他客户端计算机上启动多个应用实例。 如需此步骤的帮助，请向 askcosmosdb@microsoft.com 发送电子邮件，或通过 [Azure 门户](https://portal.azure.com)开具支持票证。

应用处于运行状态后，可以尝试不同的[编制索引策略](indexing-policies.md)和[一致性级别](consistency-levels.md)，以了解它们对吞吐量和延迟的影响。 也可以查看源代码，然后在自己的测试套件或生产应用程序中实施类似的配置。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 .NET 控制台应用对 Azure Cosmos DB 执行性能和缩放测试。 有关详细信息，请参阅以下文章：

* [Azure Cosmos DB 性能测试示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [用于提高 Azure Cosmos DB 性能的客户端配置选项](performance-tips.md)
* [Azure Cosmos DB 中的服务器端分区](partition-data.md)


