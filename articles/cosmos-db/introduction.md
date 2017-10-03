---
title: "Azure Cosmos DB 简介 | Microsoft Docs"
description: "了解 Azure Cosmos DB。 此全球分布式多模型数据库是为了实现低延迟、弹性的可伸缩性和高可用性而构建的。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/14/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: fe4c79f4e71d885e22bbcf31869b809f5d7fe1a4
ms.contentlocale: zh-cn
ms.lasthandoff: 09/21/2017

---

# <a name="welcome-to-azure-cosmos-db"></a>欢迎使用 Azure Cosmos DB

Azure Cosmos DB 由 Microsoft 提供，是全球分布式多模型数据库。 只需单击一个按钮，即可通过 Azure Cosmos DB 跨任意数量的 Azure 地理区域弹性且独立地缩放吞吐量和存储。 它通过综合[服务级别协议](https://aka.ms/acdbsla) (SLA) 提供吞吐量、延迟、可用性和一致性保证，这是其他数据库服务无法提供的。

![Azure Cosmos DB 是 Microsoft 的全球分布式数据库服务，可以弹性扩展，已保证低延迟，有五个一致性模型，并且已保证满足综合 SLA](./media/introduction/azure-cosmos-db.png)

无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>受益于 Azure Cosmos DB 的解决方案

任何 [Web、移动、游戏和 IoT 应用程序](use-cases.md)，只要其需要处理大量的[全局](distribute-data-globally.md)规模的读写操作，需要缩短各种数据的响应时间，就可以充分利用 Azure Cosmos DB 所[保证的](https://azure.microsoft.com/support/legal/sla/cosmos-db/)可用性、高吞吐量、低延迟以及可调的一致性。

## <a name="key-capabilities"></a>关键功能
作为一种全球分布式数据库服务，Azure Cosmos DB 提供以下功能，帮助构建可缩放且响应迅速的应用程序：

* **统包式全局分配**
    * [单击按钮](tutorial-global-distribution-documentdb.md)即可将[数据分配](distribute-data-globally.md)到任意数目的 [Azure 区域](https://azure.microsoft.com/regions/)。 这样就可以将数据置于用户所在的位置，确保将客户可能会遇到到的延迟降至最低。 
    * 使用 Azure Cosmos DB 的多宿主 API，应用始终能够知道最近的区域在哪里，于是就会将请求发送到最近的数据中心。 所有这些功能不需更改配置即可实现，只需设置写入区域并根据需要设置尽可能多的读取区域即可。

* 多个数据模型和用于访问及查询数据的常用 API
    * Azure Cosmos DB 本身所基于的数据模型以 Atom 记录序列 (ARS) 为基础，该模型支持多个数据模型，包括但不限于文档、图形、键-值、表以及分栏式数据模型。
    * 多种语言的 SDK 均支持以下数据模型的 API：
        * [DocumentDB API](documentdb-introduction.md)
        * [MongoDB API](mongodb-introduction.md)
        * [表 API](table-introduction.md)
        * [图形 (Gremlin) API](graph-introduction.md)
        * 即将推出其他数据模型 

* 在全球范围内按需求灵活缩放吞吐量和存储
    * 以[秒](request-units.md)为时间粒度轻松缩放数据库吞吐量，并随时对其进行更改。 
    * [透明且自动地](partition-data.md)缩放存储大小，以满足现在和将来对大小的要求。

* 构建快速响应的任务关键型应用程序
    * Azure Cosmos DB 向其客户保证了 99% 的情况下端到端的低延迟。 
    * 对于典型的 1 KB 项，Cosmos DB 保证，在 99% 的情况下，同一 Azure 区域内读取操作的端到端延迟和已索引写入操作的端到端延迟分别低于 10 毫秒和 15 毫秒。 中间延迟明显更低（低于 5 毫秒）。

* 确保提供“始终可用”的可用性
    * 在单个区域内可用性为 99.99%。
    * 部署到任意数量的 [Azure 区域](https://azure.microsoft.com/regions)以提高可用性。
    * [模拟一个或多个区域的故障](regional-failover.md)而保证不丢失任何数据。 

* 以正确方式编写全球发布式应用程序
    * 五个[一致性级别](consistency-levels.md)模型提供了一系列的一致性：从类似于 SQL 的“强”一致性到类似于 NoSQL 的“终极”一致性，以及介于二者之间的其他一致性。 
  
* 退款保证
    * 要么数据快速到达，要么退款。 
    * 有关可用性、延迟、吞吐量和一致性的[服务级别协议](https://aka.ms/acdbsla)。 

* 无数据库架构/索引管理
    * 无需担心将数据库架构和索引与应用程序架构保持同步的问题。 我们免架构。 
    * Azure Cosmos DB 的数据库引擎完全与架构无关 – 它自动索引所引入的所有数据而无需任何架构或索引，并提供速度超快的快速查询。 

* 拥有成本低廉
    * 比非托管解决方案的[成本效益](https://aka.ms/cosmos-db-tco-paper)高 5 到 10 倍。
    * 比 DynamoDB 便宜 3 倍。

## <a name="capability-comparison"></a>功能比较

Azure Cosmos DB 提供关系数据库和非关系数据库的最佳功能。

| 功能 | 关系数据库   | 非关系 (NoSQL) 数据库 |    Azure Cosmos DB |
| --- | --- | --- | --- |
| 全局分发 | 否 | 否 | 是的，可以在 30 多个区域通过多宿主 API 实现统包式分发|
| 横向缩放 | 否 | 是 | 是的，可以独立缩放存储和吞吐量 | 
| 延迟保证 | 否 | 是 | 是的，99% 的情况下可以确保读取延迟低于 10 毫秒，写入延迟低于 15 毫秒 | 
| 高可用性 | 否 | 是 | 是的，Cosmos DB 始终可用，实施 PACELC 权衡，并提供自动和手动故障转移选项|
| 数据模型 + API | 关系 + SQL | 多模型 + OSS API | 多模型 + SQL + OSS API（即将推出更多） |
| SLA | 是 | 否 | 是的，综合性 SLA 的内容涵盖延迟、吞吐量、一致性和可用性 |


## <a name="next-steps"></a>后续步骤
请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

* [开始使用 Azure Cosmos DB 的 DocumentDB API](create-documentdb-dotnet.md)
* [开始使用 Azure Cosmos DB 的 MongoDB API](create-mongodb-nodejs.md)
* [开始使用 Azure Cosmos DB 的图形 API](create-graph-dotnet.md)
* [开始使用 Azure Cosmos DB 的表 API](create-table-dotnet.md)

