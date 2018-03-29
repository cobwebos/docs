---
title: Azure Cosmos DB 简介 | Microsoft Docs
description: 了解 Azure Cosmos DB。 此全球分布式多模型数据库是为了实现低延迟、弹性的可伸缩性和高可用性而构建的。
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: e78480ceb47e472029795848739b4416e0c4256e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="welcome-to-azure-cosmos-db"></a>欢迎使用 Azure Cosmos DB

Azure Cosmos DB 由 Microsoft 提供，是全球分布式多模型数据库。 只需单击一个按钮，即可通过 Azure Cosmos DB 跨任意数量的 Azure 地理区域弹性且独立地缩放吞吐量和存储。 它通过综合[服务级别协议](https://aka.ms/acdbsla) (SLA) 提供吞吐量、延迟、可用性和一致性保证，这是其他数据库服务无法提供的。 无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB 是 Microsoft 的全球分布式数据库服务，可以弹性扩展，已保证低延迟，有五个一致性模型，并提供全面 SLA 的保证](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>关键功能
作为一种全局分布式多模型数据库服务，Azure Cosmos DB 可以轻松地生成可缩放且响应迅速的全局规模应用程序：

* **统包式全局分配**
    * [单击按钮](tutorial-global-distribution-sql-api.md)即可将[数据分配](distribute-data-globally.md)到任意数目的 [Azure 区域](https://azure.microsoft.com/regions/)。 这样就可以将数据置于用户所在的位置，确保将客户可能会遇到到的延迟降至最低。 
    * 使用 Azure Cosmos DB 的多宿主 API，应用始终能够知道最近的区域在哪里，于是就会将请求发送到最近的数据中心。 所有这些功能不需更改配置即可实现， 只需设置写入区域并根据需要设置尽可能多的读取区域即可。
    * 有了多宿主 API 功能，在添加和删除 Cosmos DB 数据库的区域时，不需重新部署应用程序即可让其保持高可用性。

* 多个数据模型和用于访问及查询数据的常用 API
    * Azure Cosmos DB 本身所基于的数据模型以 Atom 记录序列 (ARS) 为基础，该模型支持多个数据模型，包括但不限于文档、图形、键-值、表以及列系列数据模型。
    * 多种语言的 SDK 均支持以下数据模型的 API：
        * [SQL API](sql-api-introduction.md)：使用丰富的 SQL 查询功能的无架构 JSON 数据库引擎。
        * [MongoDB API](mongodb-introduction.md)：一种可大规模缩放的服务型 MongoDB，由 Azure Cosmos DB 平台提供支持。 与现有 MongoDB 库、驱动程序、工具和应用程序兼容。
        * [Cassandra API](cassandra-introduction.md)：一种可全局分发的服务型 Cassandra，由 Azure Cosmos DB 平台提供支持。 与现有 [Apache Cassandra](https://cassandra.apache.org/) 库、驱动程序、工具和应用程序兼容。
        * [图形 (Gremlin) API](graph-introduction.md)：一种完全托管的、可横向缩放的图形数据库服务，可以轻松地用于生成和运行特定的应用程序，这些应用程序适用于支持开放式图形 API（基于 [Apache TinkerPop 规范](http://tinkerpop.apache.org/)：Apache Gremlin）且存在多个连接的数据库。
        * [表 API](table-introduction.md)：一种键值对型数据库服务，可以在不进行应用更改的情况下，为现有的 Azure 表存储应用程序提供高级功能（例如，自动索引、低延迟保证、全局分发）。
        * 即将推出其他数据模型！

* **在全球范围内按需求灵活且独立地缩放吞吐量和存储**
    * 以[秒](request-units.md)为时间粒度轻松缩放数据库吞吐量，并随时对其进行更改。 
    * [透明且自动地](partition-data.md)缩放存储大小，以满足现在和将来对大小的要求。

* 构建快速响应的任务关键型应用程序
    * Azure Cosmos DB 向其客户保证了 99% 的情况下端到端的低延迟。 
    * 对于典型的 1KB 项，Cosmos DB 保证，在 99% 的情况下，同一 Azure 区域内读取操作的端到端延迟和已索引写入操作的端到端延迟分别低于 10 毫秒和 15 毫秒。 中间延迟明显更低（低于 5 毫秒）。

* 确保提供“始终可用”的可用性
    * 为所有单区域数据库帐户提供 99.99% 的可用性 SLA，为所有多区域数据库帐户提供 99.999% 的读取可用性。
    * 部署到任意数量的 [Azure 区域](https://azure.microsoft.com/regions)，在提高可用性的同时改进性能。
    * 动态设置区域的优先级，并在保证不丢失数据的情况下，对一个或多个区域进行[故障模拟](regional-failover.md)，测试整个应用的端到端可用性（超出单纯数据库的范围）。 

* 以正确方式编写全球发布式应用程序
    * 五个定义完善、实用且直观的[一致性模型](consistency-levels.md)提供了一系列的一致性：从类似于 SQL 的“强”一致性到类似于 NoSQL 的“宽松”终极一致性，以及介于二者之间的其他一致性。 
  
* 退款保证
    * 针对业务关键型数据提供的有关可用性、延迟、吞吐量和一致性的[服务级别协议](https://aka.ms/acdbsla)具有行业领先、财务支持充分、内容全面的特点。 

* 无数据库架构/索引管理
    * 快速地循环访问应用程序的架构，不需担心数据库架构和/或索引管理。
    * Azure Cosmos DB 的数据库引擎完全与架构无关 – 它自动索引所引入的所有数据而无需任何架构或索引，并提供速度超快的快速查询。 

* 拥有成本低廉
    * [成本效益](https://aka.ms/cosmos-db-tco-paper)是非托管解决方案或本地 NoSQL 解决方案的 5 到 10 倍。
    * 是 AWS DynamoDB 或 Google Spanner 价格的三分之一。

## <a name="capability-comparison"></a>功能比较

Azure Cosmos DB 提供关系数据库和非关系数据库的最佳功能。

| 功能 | 关系数据库   | 非关系 (NoSQL) 数据库 |    Azure Cosmos DB |
| --- | --- | --- | --- |
| 全球分布 | 否 | 否 | 是的，可以在 30 多个区域通过多宿主 API 实现统包式分发|
| 横向缩放 | 否 | 是 | 是的，可以独立缩放存储和吞吐量 | 
| 延迟保证 | 否 | 是 | 是的，99% 的情况下可以确保读取延迟低于 10 毫秒，写入延迟低于 15 毫秒 | 
| 高可用性 | 否 | 是 | 是的，Azure Cosmos DB 始终可用，实施定义完善的 PACELC 权衡，并提供自动和手动故障转移选项|
| 数据模型 + API | 关系 + SQL | 多模型 + OSS API | 多模型 + SQL + OSS API（即将推出更多） |
| SLA | 是 | 否 | 是的，综合性 SLA 的内容涵盖延迟、吞吐量、一致性和可用性 |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>受益于 Azure Cosmos DB 的解决方案

任何 [Web、移动、游戏和 IoT 应用程序](use-cases.md)，只要其需要处理大量的数据和[全局](distribute-data-globally.md)规模的读写操作，各种数据的响应时间接近实时，就可以充分利用 Azure Cosmos DB 所[保证的](https://azure.microsoft.com/support/legal/sla/cosmos-db/)高可用性、高吞吐量、低延迟以及可调的一致性。 了解如何将 Azure Cosmos DB 应用于 [IoT 和 远程信息处理](use-cases.md#iot-and-telematics)、[零售和营销](use-cases.md#retail-and-marketing)、[游戏](use-cases.md#gaming)以及 [Web 和移动应用程序](use-cases.md#web-and-mobile-applications)。

## <a name="next-steps"></a>后续步骤
请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB MongoDB API 入门](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](create-cassandra-dotnet.md)
* [Azure Cosmos DB 图形 API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
