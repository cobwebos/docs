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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647503"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure 宇宙 DB 作为关键值存储 – 成本概述

Azure Cosmos DB 是全球分布式多模型数据库服务，用于轻松构建高度可用的大规模应用程序。 默认情况下，Azure Cosmos DB 会自动并高效地为其引入的所有数据编制索引。 因此，可针对数据执行快速一致的 [SQL](how-to-sql-query.md)（和 [JavaScript](stored-procedures-triggers-udfs.md)）查询。 

本文介绍使用 Azure Cosmos DB 作为键/值存储执行简单写入和读取操作时产生的费用。 写入操作包括数据项的插入、替换、删除和更新插入。 除了保证所有多区域帐户 99.999% 的可用性 SLA 外，Azure Cosmos DB 还保证在 99% 的情况下读取和（索引）写入延迟小于 10 毫秒。 

## <a name="why-we-use-request-units-rus"></a>为何使用请求单位 (RU)

Azure Cosmos DB 性能基于以[请求单位](request-units.md)（RU/秒）表示的预配吞吐量的数量。 预配按另一种粒度，根据每秒 RU 数（[请不要与每小时计费相混淆](https://azure.microsoft.com/pricing/details/cosmos-db/)）购买。 应将 RU 视为一种逻辑抽象（一种货币），用于简化应用程序所需吞吐量的预配过程。 用户不必考虑区分读取和写入吞吐量。 RU 的单一货币模型能够有效地在读取和写入之间分享预配的容量。 此预配容量模型使服务能够提供**可预测和一致的吞吐量、保证的低延迟和高可用性**。 最后，虽然 RU 模型用于描述吞吐量，但每个预配的 RU 也有一定的资源量（例如，内存、核心数/CPU 和 IOPS）。

作为全球分布式数据库系统，Cosmos DB 是唯一提供涵盖延迟、吞吐量、一致性和高可用性的全面 SL 的服务。 预配的吞吐量将应用到与 Cosmos 帐户关联的每个区域。 对于读取，Cosmos DB 提供多个妥善定义的[一致性级别](consistency-levels.md)供用户选择。 

下表显示了基于大小为 1 KB 的数据项和关闭默认自动索引的 100 KB 的数据项执行读取和写入操作所需的 R 百分比数。 

|项大小|1 次读取|1 次写入|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>读取和写入成本

如果您提供 1，000 RU/s，则相当于 360 万 RU/小时，每小时费用为 0.08 美元（在美国和欧洲）。 对于 1 KB 大小的数据项，这意味着可以使用预配的吞吐量消费 360 万次读取或 72 万次写入（360 万 RU / 5）。 标准化为百万读取和写入，成本为 0.022 /000 读取（0.08 美元 / 3.6 美元）和 0.111 美元/百万写入（0.08 美元/ 0.72 美元）。 如下表中所示，百万次读写费用极低。

|项大小|1 百万次读取的费用|1 百万次写入的费用|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


大多数基本 Blob 或对象存储针对百万次读取事务收取 $0.40，针对百万次写入事务收取 $5。 如果以最佳方式使用，Cosmos DB 可以比这些其他解决方案（对于 1 KB 事务）便宜 98%。

## <a name="next-steps"></a>后续步骤

* 使用 [RU 计算器](https://cosmos.azure.com/capacitycalculator/)估算工作负荷的吞吐量。

