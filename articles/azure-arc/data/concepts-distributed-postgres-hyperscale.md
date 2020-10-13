---
title: 在启用了 Arc 的情况 PostgreSQL 超大规模服务器组中分发数据和横向扩展的概念
titleSuffix: Azure Arc enabled data services
description: PostgreSQL 超大规模服务器组的启用了 Arc 的数据的分发概念
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934572"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL 超大规模服务器组的启用了 Arc 的数据的分发概念

本文介绍了重要的概念，这些概念对于从 Azure Arc 启用的 PostgreSQL 超大规模中获益最大。
下面链接的文章指向 Azure Database for PostgreSQL 超大规模 (Citus) 中介绍的概念。 它与启用了 Azure Arc 的 PostgreSQL 超大规模的技术相同，因此也适用相同的概念和角度。

**它们之间的区别是什么？**
- _Azure Database for PostgreSQL 超大规模 (Citus)_

这是 Azure (PaaS) 中作为数据库即服务提供的 Postgres 数据库引擎的超大规模外形规格。 它由启用超大规模体验的 Citus 扩展提供支持。 采用这种方式时，服务在 Microsoft 数据中心内运行，由 Microsoft 运营。

- _已启用 Azure Arc PostgreSQL 超大规模_

这是提供支持 Azure Arc 的数据服务的 Postgres 数据库引擎的超大规模外形规格。 按照此外观，我们的客户提供了托管系统和运行系统的基础结构。

下面总结了有关 Azure Arc enabled PostgreSQL 超大规模的关键概念：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>节点和表
了解以下概念非常重要，可以充分利用 Azure Arc enabled Postgres 超大规模：
- Azure Arc 中的专用 Postgres 节点已启用 PostgreSQL 超大规模：协调器和工作人员
- 表的类型：分布式表、引用表和本地表
- 分片

有关详细信息，请参阅 [Azure Database for PostgreSQL-超大规模 (Citus) 中的节点和表 ](../../postgresql/concepts-hyperscale-nodes.md)。 

## <a name="determine-the-application-type"></a>确定应用程序类型
清楚地确定要生成的应用程序的类型非常重要。 为什么？ 由于在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组上运行高效查询，因此需要在服务器之间正确分配表。 建议的分发因应用程序的类型和查询模式而异。 在 Azure Arc 上，已启用的应用程序分为两种： Postgres 超大规模：
- 多租户应用程序
- Real-Time 应用程序

数据建模的第一步是确定与您的应用程序更密切的数据建模。

有关 [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)的详细信息，请参阅。


## <a name="choose-a-distribution-column"></a>选择分布列
为什么选择分布式列？

这是你将进行的最重要的建模决策之一。 启用 Azure Arc 后，PostgreSQL 超大规模根据行的 "分布" 列的值将行存储在分片中。 正确的选择将相关数据组合在同一物理节点上，以便快速查询并添加对所有 SQL 功能的支持。 如果选择不正确，则系统会运行缓慢，并且不支持跨节点的所有 SQL 功能。 本文为两个最常见的超大规模方案提供分布列提示。

请参阅 [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)中的详细信息。


## <a name="table-colocation"></a>表共置

归置是指将相关信息一起存储在相同的节点上。 当所有必要的数据都可用且没有任何网络流量时，查询的速度会加快。 不同节点上的归置相关数据允许查询在每个节点上并行运行。

请参阅 [表归置](../../postgresql/concepts-hyperscale-colocation.md)的详细信息。


## <a name="next-steps"></a>后续步骤
- [了解如何创建启用 Azure Arc 的 PostgreSQL 超大规模](create-postgresql-hyperscale-server-group.md)
- [了解如何扩展启用了 Azure Arc 的 PostgreSQL 超大规模服务器组。](scale-out-postgresql-hyperscale-server-group.md)
- [阅读有关启用了 Azure Arc 的数据服务的信息](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [了解 Azure Arc](https://aka.ms/azurearc)

