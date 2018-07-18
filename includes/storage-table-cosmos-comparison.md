---
title: include 文件
description: include 文件
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 76e1a22e02ad6b8161ec18f3806c88ffc54fe123
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "34665372"
---
如果当前使用 Azure 表存储，可以通过移到 Azure Cosmos DB 表 API 获得以下好处：

| | Azure 表存储 | Azure Cosmos DB 表 API |
| --- | --- | --- |
| Latency | 快速，但对延迟没有上限。 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在全球任何位置，对于任何规模，99% 的情况下读取操作的延迟 < 10 毫秒、写入操作的延迟 < 15 毫秒）。 |
| Throughput | 可变吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒。 | 使用 SLA 支持的[每个表专用保留吞吐量](../articles/cosmos-db/request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒。 |
| 全球分布 | 具有一个可选可读辅助读取区域以实现高可用性的单一区域。 不能启动故障转移。 | 从 1 个到 30 多个区域进行[统包式全局分发](../articles/cosmos-db/distribute-data-globally.md)。 支持在全球各地随时[自动和手动故障转移](../articles/cosmos-db/regional-failover.md)。 |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引。 | 自动对所有属性完成编制索引，没有索引管理。 |
| 查询 | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 |
| 一致性 | 在主要区域内实现强一致性。 在辅助区域内实现最终一致性。 | [五个定义完善的一致性级别](../articles/cosmos-db/consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性。 |
| 定价 | 存储优化。 | 吞吐量优化。 |
| SLA | 99.99% 可用性。 | 在公开发布时，为所有单区域帐户和具有松散一致性的所有多区域帐户提供 99.99% 的可用性 SLA，为所有多区域数据库帐户提供 99.999% 的读取可用性方面的[行业领先的综合 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |
