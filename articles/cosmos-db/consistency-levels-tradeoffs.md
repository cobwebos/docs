---
title: Azure Cosmos DB 一致性、可用性和性能折衷
description: Azure Cosmos DB 中各种一致性级别的可用性和性能利弊。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a16acfc8f9be820e9cc9b3bd59d6675b7f75d2ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445551"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>一致性、可用性和性能权衡 

依赖复制以实现高可用性和/或低延迟的分布式数据库必须做出权衡。 在读取一致性与可用性、延迟和吞吐量之间做出权衡。

Azure Cosmos DB 通过某种选择范围来实现数据一致性。 此方法包含多个选项，而不会走非常一致性和最终一致性这两种极端。 可以从一致性范畴的五个定义完善的模型中进行选择。 按最强到最弱的顺序，模型分别为：

- *非常*
- *有限过期*
- *会话*
- *一致前缀*
- *最终*

每个模型都提供可用性和性能的折衷，并由全面的 Sla 提供支持。

## <a name="consistency-levels-and-latency"></a>一致性级别和延迟

所有一致性级别的读取延迟始终保证在第 99 百分位小于 10 毫秒。 此读取延迟由 SLA 提供保障。 平均（在第 50 百分位）读取延迟通常不超过 2 毫秒。 跨多个区域且使用非常一致性配置的 Azure Cosmos 帐户不在此保障内。

所有一致性级别的写入延迟始终保证低于10毫秒，99% 的百分点。 此写入延迟由 SLA 提供保障。 平均（在第 50 百分位）写入延迟通常不超过 5 毫秒。

对于配置了具有多个区域的强一致性的 Azure Cosmos 帐户，可保证写入延迟小于两个最远的区域之间的往返时间（RTT），再加上10毫秒的99% 百分位。

确切的 RTT 延迟取决于光速距离和 Azure 网络拓扑。 Azure 网络不会针对任意两个 Azure 区域之间的 RTT 提供任何延迟 SLA。 对于你的 Azure Cosmos 帐户，将在 Azure 门户中显示复制延迟。 你可以使用 Azure 门户（请参阅 "指标" 边栏选项卡）来监视与 Azure Cosmos 帐户关联的不同区域之间的复制延迟。

## <a name="consistency-levels-and-throughput"></a>一致性级别和吞吐量

- 对于相同数量的请求单元，会话、一致前缀和最终一致性级别提供的读取吞吐量大约是非常一致性和有限过期一致性的 2 倍。

- 对于给定类型的写入操作（例如插入、替换、更新插入和删除），所有一致性级别为请求单元提供的写入吞吐量是相同的。

## <a id="rto"></a>一致性级别和数据持续性

在全球分布式数据库环境中，当发生区域范围的服务中断时，一致性级别与数据持续性之间存在直接关系。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为**恢复时间目标**（**RTO**）。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可能会丢失的更新时间段称为**恢复点目标**（**RPO**）。

下表定义了一致性模型与存在区域范围中断的数据持续性之间的关系。 需要注意的是，在分布式系统中，即使使用强一致性，分布式数据库也不可能具有 RPO，而 RTO 为零，因为 CAP 定理。 若要详细了解原因，请参阅[Azure Cosmos DB 中的一致性级别](consistency-levels.md)。

|**区域**|**复制模式**|**一致性级别**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|第|单主或多主数据库|任何一致性级别|< 240 分钟|<1 周|
|>1|单主数据库|会话、一致的前缀或最终|< 15 分钟|< 15 分钟|
|>1|单主数据库|有限过期|*K* & *T*|< 15 分钟|
|>1|单主数据库|Strong|0|< 15 分钟|
|>1|多主数据库|会话、一致的前缀或最终|< 15 分钟|0|
|>1|多主数据库|有限过期|*K* & *T*|0|

*K* = 项的 *"K"* 版本（即更新）的数目。

*T* = 自上次更新后的时间间隔 *"T"* 。

## <a name="strong-consistency-and-multi-master"></a>强一致性和多主机

为多主机配置的 Cosmos 帐户不能配置为具有强一致性，因为分布式系统不可能提供零的 RPO 和 RTO 为零。 此外，如果必须复制任何区域中的所有已配置区域并将其提交到该帐户中的所有已配置区域，则对多主机使用强一致性没有写入延迟的好处。 这将产生与单个主帐户相同的写入延迟。

## <a name="next-steps"></a>后续步骤

详细了解全局分布，以及分布式系统中的常规一致性利弊。 请参阅以下文章：

- [现代分布式数据库系统设计中的一致性利弊](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [高可用性](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
