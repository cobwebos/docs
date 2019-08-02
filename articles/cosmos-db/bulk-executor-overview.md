---
title: 使用批量执行程序库在 Azure Cosmos DB 中批量导入和更新数据
description: 通过批量执行程序库提供的批量导入和批量更新 API 功能，在 Azure Cosmos DB 中执行批量操作。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 1716bd64286f1882b9fc224712d227967d78058a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637790"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB 批量执行程序库概述
 
Azure Cosmos DB 是一种快速且灵活的全局分布式数据库服务，旨在通过弹性的横向扩展来支持以下功能： 

* 大规模的读写吞吐量（每秒数百万操作）。  
* 存储大量（数百 TB 甚至更多）事务性和操作性数据，延迟低至毫秒级且可预测。  

批量执行程序库可助你利用这个极大的吞吐量和存储。 批量执行程序库允许通过批量导入和批量更新 API 在 Azure Cosmos DB 中执行批量操作。 可在以下部分中详细了解批量执行程序库的功能。 

> [!NOTE] 
> 目前，批量执行程序库支持导入和更新操作，但该库仅受 Azure Cosmos DB SQL API 和 Gremlin API 帐户支持。
 
## <a name="key-features-of-the-bulk-executor-library"></a>批量执行程序库的主要功能  
 
* 它可以显著减少使分配给容器的吞吐量达到饱和所需的客户端计算资源。 在客户端计算机的 CPU 已饱和的情况下，使用批量导入 API 来写入数据的单线程应用程序实现的写入吞吐量是以并行方式写入数据的多线程应用程序的 10 倍。  

* 它可以在库中高效地处理请求限制、请求超时以及其他暂时性异常，因此用户不需要执行繁冗的编写应用程序逻辑任务来处理这些内容。  

* 它为执行批量操作的应用程序提供简化的横向扩展机制。单个运行在 Azure VM 上的批量执行程序实例的消耗可以大于 50 万 RU/秒，并且可以通过在单个客户端 VM 上添加更多的实例来实现更高的吞吐率。  
 
* 它可以通过横向扩展体系结构在一小时内批量导入 1 TB 以上的数据。  

* 它可以作为修补程序批量更新 Azure Cosmos DB 容器中的现有数据。 
 
## <a name="how-does-the-bulk-executor-operate"></a>大容量执行程序的工作原理是什么？ 

对一批实体触发用于导入或更新文档的批量操作时，这些实体一开始会随机分布到与其 Azure Cosmos DB 分区键范围相对应的 Bucket 中。 在每个与分区键范围相对应的 Bucket 中，这些实体会细分成迷你批，每个迷你批充当一个可以在服务器端提交的有效负载。 批量执行程序库在分区键范围的内外对并发执行这些迷你批进行了内置的优化。 下图演示了批量执行程序如何将数据按批分成不同的分区键：  

![批量执行程序体系结构](./media/bulk-executor-overview/bulk-executor-architecture.png)

大容量执行器库确保最大化利用分配给集合的吞吐量。 它使用适用于每个 Azure Cosmos DB 分区键范围的  [AIMD 样式拥塞控制机制](https://tools.ietf.org/html/rfc5681)，可以有效地处理速率限制和超时。 

## <a name="next-steps"></a>后续步骤 
  
* 通过尝试使用[.net](bulk-executor-dot-net.md)和[Java](bulk-executor-java.md)中的批量执行程序库的示例应用程序, 了解详细信息。  
* 在 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 中查看批量执行程序 SDK 信息和发行说明。
* 大容量执行程序库已集成到 Cosmos DB Spark 连接器, 若要了解详细信息, 请参阅[Azure Cosmos DB Spark 连接器](spark-connector.md)一文。  
* 批量执行程序库也已集成到新版 [Azure Cosmos DB 连接器](https://aka.ms/bulkexecutor-adf-v2)中，可供 Azure 数据工厂复制数据。
