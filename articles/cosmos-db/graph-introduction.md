---
title: Azure Cosmos DB Gremlin API 简介
description: 了解如何在 Azure Cosmos DB 中通过使用 Apache TinkerPop 的 Gremlin 图形查询语言以较低的延迟存储、查询和遍历大量图形。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502797"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB 简介：Gremlin API

[Azure Cosmos DB](introduction.md)  是 Microsoft 针对任务关键型应用程序提供的全球分布的多模型数据库服务。 它是一个多模型数据库，支持文档、键-值、图形和纵栏表数据模型。 Azure Cosmos DB Gremlin API 用于在任何规模的完全托管数据库环境中存储图形数据以及对其进行操作。  

![Azure Cosmos DB 图形体系结构](./media/graph-introduction/cosmosdb-graph-architecture.png)

本文提供 Azure Cosmos DB Gremlin API 概述，并说明如何使用它存储具有数十亿顶点和边缘的大量图形。 可在毫秒级延迟的情况下查询图形，并轻松改进图结构。 Azure Cosmos DB 的 Gremlin API 基于 [Apache TinkerPop](https://tinkerpop.apache.org)  图形数据库标准，并使用 Gremlin 查询语言。 

Azure Cosmos DB 的 Gremlin API 将强大的图形数据库算法与高度可扩展的托管基础结构相结合，为缺乏灵活性和关系型方法的大多数常见数据问题提供独特、灵活的解决方案。 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Azure Cosmos DB 图形数据库的功能
 
Azure Cosmos DB 是一个完全托管的图形数据库，提供全局分发、存储和吞吐量弹性缩放、自动索引编制与查询、可优化的一致性级别，并支持 TinkerPop 标准。 

以下是 Azure Cosmos DB Gremlin API 提供的差异化功能：

* **可灵活缩放吞吐量和存储**

  现实世界中的图形需要扩展到超越单个服务器的容量。 Azure Cosmos DB 支持横向可缩放的图形数据库，这些数据库在预配置吞吐量方面具有几乎无限制的大小。 随着图形数据库规模的增长，数据将使用[图形分区](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)自动分发。

* **多区域复制**

  Azure Cosmos DB 可以自动将图形数据复制到任何 Azure 区域。 复制简化了需要全球访问数据的应用程序的开发过程。 Azure Cosmos DB 除了最大限度地减少读取延迟之外，还提供了一种区域故障转移机制，可以在某区域服务中断（这种情况很少见）的情况下确保应用程序持续可用。 

* **使用最广泛采用的图形查询标准进行快速查询和遍历**

  存储异类顶点和边缘，并通过熟悉的 Gremlin 语法查询这些文档。 Gremlin 是一种命令式函数查询语言，提供了丰富的接口来实现常见的图形算法。 
  
  Azure Cosmos DB 支持丰富的实时查询和遍历，而无需指定架构提示、二级索引或视图。 在[通过使用 Gremlin 查询图形](gremlin-support.md)中了解详细信息。

* **完全托管的图形数据库**

  通过 Azure Cosmos DB 无需管理数据库和计算机资源。 大多数现有的图形数据库平台受其基础结构的限制，并且通常需要进行高度维护才能确保其运营。 
  
  作为一种完全托管的 Microsoft Azure 服务，无需管理虚拟机、更新运行时软件、管理分片或复制或者处理复杂的数据层升级。 每个图形会自动备份，以防受到区域故障的影响。 这些保证使得开发人员能够专注于提供应用程序价值，而不是专注于操作和管理其数据库。 

* **自动编制索引**

  默认情况下，Azure Cosmos DB 自动为图形中的节点和边缘包含的所有属性编制索引，无需任何架构或创建二级索引。 深入了解 [Azure Cosmos DB 中的索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)。 

* **与 Apache TinkerPop 兼容**

  Azure Cosmos DB 支持[开放源代码 Apache TinkerPop 标准](http://tinkerpop.apache.org/)。 Tinkerpop 标准拥有丰富的应用程序和库生态系统，它们可以轻松地与 Azure Cosmos DB 的 Gremlin API 集成。 

* **可优化的一致性级别**

  从五个妥善定义的一致性级别中选择，实现一致性与性能之间的最佳平衡。 对于查询和读取操作，Azure Cosmos DB 提供五种不同的一致性级别：强、有限过时、会话、一致前缀和最终。 通过这些细化的妥善定义的一致性级别，可以在一致性、可用性与延迟之间实现合理的平衡。 有关详细信息，请参阅 [Azure Cosmos DB 中的可优化数据一致性级别](consistency-levels.md)。

## <a name="scenarios-that-can-use-gremlin-api"></a>可以使用 Gremlin API 的场合
下面是可以使用 Azure Cosmos DB 图形支持的一些场合：

* 社交网络

  通过合并有关客户及其与其他人的互动的数据，可以开发个性化的体验、预测客户行为，或者将某些人员与其他具有类似兴趣的人员相连接。 使用 Azure Cosmos DB 可以管理社交网络以及跟踪客户的喜好与数据。

* 推荐引擎

  此场合通常用于零售行业。 通过合并有关产品、用户和用户互动（例如购买、浏览某件商品或者为商品评分）的信息，可以生成自定义的推荐内容。 Azure Cosmos DB 的低延迟、弹性缩放和原生图形支持是为这些互动建模的理想选择。

* 地理空间

  电信、物流和旅行规划行业中的许多应用程序需要在某个区域中查找兴趣点，或者查找两个地点之间最短/最佳的路线。 Azure Cosmos DB 天生就很适合解决这些问题。

* 物联网

  当 IoT 设备之间的网络和连接建模为图形时，可以更好地理解设备和资产的状态。 还可以了解网络一个部分的更改可能对另一个部分造成的影响。

## <a name="introduction-to-graph-databases"></a>图形数据库简介
现实世界中的数据存在必然的联系。 传统数据建模侧重于单独定义实体并在运行时计算它们之间的关系。 虽然这种模型有其自身的优点，但高度连接的数据在其约束下可能难以进行管理。  

而图形数据库方法依赖于存储层中的持久关系，这使得图形检索操作非常高效。 Azure Cosmos DB 的 Gremlin API 支持[属性图形模型](https://tinkerpop.apache.org/docs/current/reference/#intro)。

### <a name="property-graph-objects"></a>属性图形对象

属性[图形](http://mathworld.wolfram.com/Graph.html)是由[顶点](http://mathworld.wolfram.com/GraphVertex.html)和[边缘](http://mathworld.wolfram.com/GraphEdge.html)组成的结构。 两个对象都可以作为属性包含任意数量的键值对。 

* **顶点** - 顶点表示人员、地点或事件等离散实体。

* **边** - 边表示顶点之间的关系。 例如，一个人可能认识其他人、涉及到某个事件以及最近处于某个位置。 

* **属性** - 属性表示有关顶点和边的信息。 顶点或边缘中可以包含任意数量的属性，并且这些属性可用于描述和筛选查询中的对象。 属性示例包括具有姓名和年龄的顶点，或具有时间戳和/或体重的边缘。 

因为架构或受约束的数据模型中不存在依赖项，因此图形数据库通常包含在 NoSQL 或非关系数据库类别中。 由于缺乏架构，因此可以自然且高效地建模和存储连接的结构。 

### <a name="gremlin-by-example"></a>举例介绍 Gremlin
我们使用一个示例图形来了解如何在 Gremlin 中表示查询。 下图显示了一个商业应用程序，该应用程序管理以图形形式呈现的有关用户、兴趣和设备的数据。  

![显示人员、设备和兴趣的示例数据库](./media/gremlin-support/sample-graph.png) 

此图形使用以下顶点类型（在 Gremlin 中称为“标签”）：

- 人员：图形中包含三名人员：Robin、Thomas 和 Ben
- 兴趣：在此示例中，人员的兴趣为足球比赛
- 设备：人员使用的设备
- 操作系统：设备在其上运行的操作系统

我们通过以下边缘类型/标签表示这些实体之间的关系：

- 认识：例如，“Thomas 认识 Robin”
- 感兴趣的内容：在图形中表示人员的兴趣，例如，“Ben 对足球感兴趣”
- RunsOS：运行 Windows OS 的笔记本电脑
- 使用：表示人员使用哪种设备。 例如，Robin uses a Motorola phone with serial number 77

让我们使用 [Gremlin 控制台](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console)对此图形运行一些操作。 也可以所选的平台（Java、Node.js、Python 或 .NET）中使用 Gremlin 驱动程序执行这些操作。  在了解 Azure Cosmos DB 支持的功能之前，我们先通过几个示例来熟悉语法。

首先，让我们了解 CRUD。 以下 Gremlin 语句在图形中插入“Thomas”顶点：

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

接下来，以下 Gremlin 语句在 Thomas 与 Robin 之间插入“knows”边缘。

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

以下查询按人员名字的降序返回“person”顶点：
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

如果需要回答类似于“Thomas 的朋友使用哪些操作系统？”的问题，图形可以提供很大的方便。 可以运行此 Gremlin 遍历从图形中获取该信息：

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
现在，让我们看看 Azure Cosmos DB 为 Gremlin 开发人员提供哪些信息。

## <a name="next-steps"></a>后续步骤
若要详细了解 Azure Cosmos DB 中的图形支持，请参阅：

* [Azure Cosmos DB 图形入门教程](create-graph-dotnet.md)。
* 了解如何[通过使用 Gremlin 在 Azure Cosmos DB 中查询图形](gremlin-support.md)。
