---
title: "用作键值存储的 Azure DocumentDB – 费用概述 | Microsoft 文档"
description: "了解使用 Azure DocumentDB 作为键值存储带来的经济性。"
keywords: "键值存储"
services: documentdb
author: ArnoMicrosoft
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: acomet
translationtype: Human Translation
ms.sourcegitcommit: 6fc5dfd3fbdd2e0690b60f4b232e624c34bf53b6
ms.openlocfilehash: c1022a127266d28d3b59bfebd0543a840fe11e3a


---

# <a name="documentdb-as-a-key-value-store--cost-overview"></a>用作键值存储的 DocumentDB – 费用概述

Azure DocumentDB 是完全托管的全球分布式 NoSQL 数据库服务，用于轻松构建高度可用的大规模[全球分布式](documentdb-distribute-data-globally.md)应用程序。 默认情况下，DocumentDB 将有效地为它引入的所有数据编制索引。 因此，可针对任何类型的数据执行快速一致的 [SQL](documentdb-sql-query.md)（和 [JavaScript](documentdb-programming.md)）查询。 

本文介绍使用 DocumentDB 作为键/值存储执行简单写入和读取操作时产生的费用。 写入操作包括文档的插入、替换、删除和更新插入。 除了保证 99.99% 的高可用性以外，DocumentDB 还保证读取延迟小于 10 毫秒，（索引）写入延迟小于 15 毫秒，SLA 高达 99%。 

## <a name="why-we-use-request-units-rus"></a>为何使用请求单位 (RU)

DocumentDB 的性能基于分区的预配[请求单位](documentdb-programming.md) (RU) 数量。 预配属于另一种粒度，根据每秒 RU 数（[请不要与每小时计费相混淆](https://azure.microsoft.com/pricing/details/documentdb/)）购买。 应该将 RU 视为一种货币，它简化了应用程序所需吞吐量的预配。 客户无需考虑读取和写入容量单位之间的差异。 RU 的单一货币模型能够有效地在读取和写入之间分享预配的容量。 这种预配的容量模型使服务能够提供可预测且一致的吞吐量，保证低延迟、高可用性。 最后，我们使用 RU 来为吞吐量建模，但每个预配的 RU 还具有定义数量的资源（内存、核心）。 每秒 RU 数不仅仅是 IOPS。

作为一种全球分布式数据库系统，DocumentDB 是仅限 Azure 的服务，除了高可用性以外，还在延迟、吞吐量和一致性方面提供 SLA。 预配的吞吐量将应用到与 DocumentDB 数据库帐户关联的每个区域。 对于读取，DocumentDB 提供多个妥善定义的[一致性级别](documentdb-consistency-levels.md)供用户选择。 

下表显示了基于某些文档大小执行读取和写入事务所需的 RU 数量。 

|文档大小|1 次读取|1 次写入|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|5 KB|5 RU|25 RU|
|10 KB|10 RU|50 RU|
|50 KB|50 RU|250 RU|
|100 KB|100 RU|500 RU|

## <a name="cost-of-running-documentdb-in-readwrite-mode-without-indexing"></a>在不使用索引的情况下以读取/写入模式运行 DocumentDB 的费用

如果预配 1,000 RU/秒，则相当于 360 万 RU/小时，因此，每小时费用为 $0.08（美国和欧洲）。 对于 1KB 大小的文档，这意味着可以使用预配的吞吐量消费 360 万次读取或 72 万次写入（360 万 RU / 5）。 规范化为百万次读取和写入后，费用将是 $0.022/百万次读取 ($0.08 / 3.6) 和 $0.111/百万次写入 ($0.08 / 0.72)。 如下表中所示，百万次读写费用极低。

|文档大小|100 万次读取|100 万次写入|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|5 KB|$0.111|$0.556|
|10 KB|$0.222|$1.111|
|50 KB|$1.111|$5.556|
|100 KB|$2.222|$11.111|

大多数基本 Blob 或对象存储（例如 AWS S3 或 Azure Blob 存储服务）针对百万次读取事务收取 $0.40，针对百万次写入事务收取 $5。 如果以最佳的方式使用，DocumentDB 可能比其他解决方案要便宜 98%（适用于 1KB 事务）。

## <a name="next-steps"></a>后续步骤

请持续关注有关优化 DocumentDB 资源预配的新文章。 同时，欢迎使用我们的 [RU 计算器](https://www.documentdb.com/capacityplanner)。



<!--HONumber=Feb17_HO1-->


