---
title: 使用 Azure Cosmos DB 全局分发数据 | Microsoft Docs
description: 了解如何通过 Azure Cosmos DB（一种全局分布式多模型数据库服务），使用全局数据库进行全球范围的异地复制、多主数据库、故障转移和数据恢复。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408889"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 全局分配数据

Azure 无处不在 - 它的足迹遍布全球 50 多个地理区域，并且还在不断扩展。 凭借其全球影响力和多主数据库支持，Azure 为其开发人员提供的差异化功能之一是能够轻松构建、部署和管理全球分布式应用程序。

[Azure Cosmos DB](../cosmos-db/introduction.md) 是 Microsoft 针对任务关键型应用程序提供的全球分布式多模型数据库服务。 Azure Cosmos DB 在全球范围内提供统包全局分发、[吞吐量和存储空间弹性缩放](../cosmos-db/partition-data.md)、99% 情况下低至个位数的毫秒级读写延迟、[妥善定义的一致性模型](consistency-levels.md)，以及得到保证的高可用性，所有这些均由[行业领先的综合 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 提供支持。 Azure Cosmos DB [自动为所有数据编制索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，无需客户管理架构或索引。

## <a name="global-distribution-with-multi-master"></a>通过多主数据库功能实现全局分发

作为云服务，Azure Cosmos DB 经过精心设计，可支持将多租户、全局分发和多主数据库功能用于文档、键值、图形和列系列数据模型。

![跨 3 个区域进行分区和分布的 Azure Cosmos DB 容器](./media/distribute-data-globally/global-apps.png)

**跨多个 Azure 区域进行分区和分布的一个 Azure Cosmos DB 容器**

我们在构建 Azure Cosmos DB 时就已经知道，全球分发功能不能事后添加。 不能将其“直接附加”到“单一站点”数据库系统之上。 全局分布式数据库提供的功能超越了“单一站点”数据库提供的传统地理灾难恢复 (GEO-DR) 提供的功能。 提供 GEO-DR 功能的单一站点数据库是全局分布式数据库的严格子集。

借助 Azure Cosmos DB 的统包全局分发，开发人员无需通过对数据库日志采用 Lambda 模式或跨多个区域执行“双写操作”来生成自己的复制基架。 不建议采用这些方法，因为无法确保此类方法的正确性并提供合理正确的 SLA。

## <a id="Next Steps"></a>后续步骤

* [Azure Cosmos DB 如何启用统包全局分布](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB 全局分布主要优点](distribute-data-globally-benefits.md)

* [如何配置 Azure Cosmos DB 全局数据库复制](tutorial-global-distribution-sql-api.md)

* [如何为 Azure Cosmos DB 帐户启用多主数据库](enable-multi-master.md)

* [Azure Cosmos DB 中的自动和手动故障转移的工作原理](regional-failover.md)

* [了解 Azure Cosmos DB 中的冲突解决](multi-master-conflict-resolution.md)

* [将多主数据库与开放源代码 NoSQL 数据库结合使用](multi-master-oss-nosql.md)
