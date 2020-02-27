---
title: 作为键/值存储的 Azure Cosmos DB 的请求单位费用
description: 了解 Azure Cosmos DB 在用作键/值存储时执行简单写入和读取操作的请求单位费用。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647503"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>作为键值存储 Azure Cosmos DB-费用概述

Azure Cosmos DB 是全球分布式多模型数据库服务，用于轻松构建高度可用的大规模应用程序。 默认情况下，Azure Cosmos DB 自动为其引入的所有数据编制索引。 这可以对数据进行快速一致的[SQL](how-to-sql-query.md) （和[JavaScript](stored-procedures-triggers-udfs.md)）查询。 

本文介绍使用 Azure Cosmos DB 作为键/值存储执行简单写入和读取操作时产生的费用。 写入操作包括数据项的插入、替换、删除和 upsert。 除了为所有多区域帐户保证99.999% 的可用性 SLA 外，Azure Cosmos DB 保证在99% 的100% 的时间内进行读取和（索引）写入操作的 < 10 毫秒的延迟。 

## <a name="why-we-use-request-units-rus"></a>为何使用请求单位 (RU)

Azure Cosmos DB 性能基于按[请求单位](request-units.md)（RU/秒）表示的预配吞吐量。 预配的粒度为第二个，并以 RU/s 的方式购买（[不要与每小时计费相混淆](https://azure.microsoft.com/pricing/details/cosmos-db/)）。 应将 ru 视为逻辑抽象（一种货币），以便简化应用程序的所需吞吐量的预配。 用户无需考虑在读取和写入吞吐量之间进行区分。 RU 的单一货币模型能够有效地在读取和写入之间分享预配的容量。 此预配的容量模型使服务能够提供**可预测且一致的吞吐量，保证低延迟和高可用性**。 最后，虽然 RU 模型用于描述吞吐量，但每个预配的 RU 还具有定义数量的资源（例如内存、核心/CPU 和 IOPS）。

作为一种全球分布式数据库系统，Cosmos DB 是唯一的 Azure 服务，提供涵盖延迟、吞吐量、一致性和高可用性的综合 Sla。 预配的吞吐量将应用到与 Cosmos 帐户关联的每个区域。 对于读取，Cosmos DB 提供多个妥善定义的[一致性级别](consistency-levels.md)供用户选择。 

下表显示了根据大小为 1 KB 的数据项和 100 Kb （默认情况下自动索引关闭）执行读写操作所需的 ru 数。 

|项大小|1 次读取|1 次写入|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>读取和写入成本

如果你预配 1000 RU/s，则这些金额将为 3600000 RU/小时，将会在美国和欧洲为该小时 $0.08 收费。 对于 1 KB 大小的数据项，这意味着可以使用预配的吞吐量来使用3600000读取或720000写入（3600000 RU/5）。 规范化为百万次读取和写入，开销为 $0.022/百万（$0.08/3.6）和 $ 0.111/百万写入（$0.08/0.72）。 如下表中所示，百万次读写费用极低。

|项大小|1000000读取成本|1000000写入成本|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


大多数基本 Blob 或对象存储针对百万次读取事务收取 $0.40，针对百万次写入事务收取 $5。 如果以最佳方式使用，Cosmos DB 可能比其他解决方案要便宜98% （适用于 1 KB 事务）。

## <a name="next-steps"></a>后续步骤

* 使用[RU 计算器](https://cosmos.azure.com/capacitycalculator/)来估计工作负荷的吞吐量。

