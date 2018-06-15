---
title: Azure Cosmos DB BulkExecutor 库概述 | Microsoft Docs
description: 了解 Azure Cosmos DB BulkExecutor 库、使用库的优势以及库的体系结构。
keywords: Java 批量执行程序
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 5f1987009d2277590e32136336340aa1b3be07fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610325"
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Azure Cosmos DB BulkExecutor 库概述
 
Azure Cosmos DB 是一种快速且灵活的全局分布式数据库服务，旨在通过弹性的横向扩展来支持以下功能： 

* 大规模的读写吞吐量（每秒数百万操作）。  
* 存储大量（数百 TB 甚至更多）事务性和操作性数据，延迟低至毫秒级且可预测。  

BulkExecutor 库有助于利用此大规模的吞吐量和存储。可以使用 BulkExecutor 库，通过批量导入和批量更新 API 在 Azure Cosmos DB 中执行批量操作。 可在以下部分详细了解 BulkExecutor 库的功能。 

> [!NOTE] 
> 目前，Bulkxecutor 库支持导入和更新操作，但该库仅受 Azure Cosmos DB SQL API 帐户支持。 有关该库的任何更新，请参阅 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 的发行说明。
 
## <a name="key-features-of-the-bulkexecutor-library"></a>BulkExecutor 库的主要功能  
 
* 它可以显著减少使分配给容器的吞吐量达到饱和所需的客户端计算资源。 在客户端计算机的 CPU 已饱和的情况下，使用批量导入 API 来写入数据的单线程应用程序实现的写入吞吐量是以并行方式写入数据的多线程应用程序的 10 倍。  

* 它可以在库中高效地处理各种繁冗的应用程序逻辑编写任务，因此不需用户处理请求限制、请求超时以及其他暂时性异常。  

* 它为执行批量操作的应用程序提供简化的横向扩展机制。单个运行在 Azure VM 上的 BulkExecutor 实例的消耗可以大于 50 万 RU/秒，并且可以通过在单个客户端 VM 上添加更多的实例来实现更高的吞吐率。  
 
* 它可以通过横向扩展体系结构在一小时内批量导入 1 TB 以上的数据。  

* 它可以作为修补程序批量更新 Azure Cosmos DB 容器中的现有数据。 
 
## <a name="how-does-the-bulk-executor-operate"></a>批量执行程序如何进行操作？ 

对一批实体触发用于导入或更新文档的批量操作时，这些实体一开始会随机分布到与其 Azure Cosmos DB 分区键范围相对应的 Bucket 中。 在每个与分区键范围相对应的 Bucket 中，这些实体会细分成迷你批，每个迷你批充当一个可以在服务器端提交的有效负载。 BulkExecutor 库在分区键范围的内外对并发执行这些迷你批进行了内置的优化。 下图演示了 BulkExecutory 如何将数据按批分成不同的分区键：  

![批量执行程序体系结构](./media/bulk-executor-overview/bulk-executor-architecture.png)

批量执行程序库可确保最大程度地利用分配给集合的吞吐量。 它使用适用于每个 Azure Cosmos DB 分区键范围的  [AIMD 样式拥塞控制机制](https://tools.ietf.org/html/rfc5681)，可以有效地处理限制和超时。 

## <a name="next-steps"></a>后续步骤 
  
* 若要进行详细了解，请试用那些在 [.NET](bulk-executor-dot-net.md) 和 [Java](bulk-executor-java.md) 中使用批量执行程序库的示例应用程序。  
* 在 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 中查看 BulkExecutor SDK 信息和发行说明。
* 批量执行程序库已集成到 Cosmos DB Spark 连接器中。若要进行详细的了解，请参阅 [Azure Cosmos DB Spark 连接器](spark-connector.md)一文。  
* BulkExecutor 库也已集成到新版 [Azure Cosmos DB 连接器](https://aka.ms/bulkexecutor-adf-v2)中，可供 Azure 数据工厂复制数据。
