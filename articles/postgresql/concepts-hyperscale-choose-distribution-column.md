---
title: 在 Azure Database for PostgreSQL 中选择分布列–超大规模（Citus）
description: 了解如何在 Azure Database for PostgreSQL 中选择常见超大规模方案中的分布列。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 0b29567dcd22c79c30e70594066f7ff87c18fdb0
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947594"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>在 Azure Database for PostgreSQL 中选择分布列–超大规模（Citus）

选择每个表的分布列是您将进行的最重要的建模决策之一。 Azure Database for PostgreSQL –超大规模（Citus）预览版基于行 "分布" 列的值将行存储在分片中。

正确的选择将相关数据组合在同一物理节点上，以便快速查询并添加对所有 SQL 功能的支持。 如果选择不正确，则系统会运行缓慢，并且不支持跨节点的所有 SQL 功能。

本文提供了两个最常见的超大规模（Citus）方案的分布列提示。

### <a name="multi-tenant-apps"></a>多租户应用

多租户体系结构使用一种分层数据库建模形式跨服务器组中的节点分布查询。 数据层次结构的顶部称为*租户 ID* ，需要存储在每个表的列中。

超大规模（Citus）检查查询以查看其所涉及的租户 ID，并找到匹配表分片。 它将查询路由到包含分片的单个工作节点。 在同一节点上运行包含所有相关数据的查询称为归置。

下图说明了多租户数据模型中的归置。 它包含两个表：帐户和活动，每个表由 `account_id` 分发。 带阴影的框代表分片。 绿色分片存储在一个工作节点上，而蓝色分片存储在另一个辅助节点上。 请注意，当两个表限制为同一个帐户 @ no__t-0id 时，帐户和市场活动之间的联接查询如何将所有必要的数据同时包含在一个节点上。

![多租户归置](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

若要在自己的架构中应用此设计，请确定在应用程序中构成租户的内容。 常见实例包括公司、帐户、组织或客户。 列名称将为 `company_id` 或 `customer_id` 之类的内容。 检查每个查询并问自己，如果它有其他 WHERE 子句来将涉及的所有表限制为具有相同租户 ID 的行，它是否起作用？
多租户模型中的查询的作用域限定为租户。 例如，针对销售或库存的查询在特定商店内的范围内。

#### <a name="best-practices"></a>最佳实践

-   **按常见租户 @ no__t-1id 列对分布式表进行分区。** 例如，在租户为公司的 SaaS 应用程序中，租户 @ no__t 0id 可能是公司 @ no__t-1id。
-   **将小型跨租户表转换为引用表。** 当多个租户共享一个较小的信息表时，将它作为引用表进行分发。
-   **限制租户 @ no__t-1id 筛选所有应用程序查询。** 每个查询一次应请求一个租户的信息。

有关如何生成此类应用程序的示例，请参阅[多租户教程](./tutorial-design-database-hyperscale-multi-tenant.md)。

### <a name="real-time-apps"></a>实时应用

多租户体系结构引入了一个层次结构，并使用数据归置来路由每个租户的查询。 与此相反，实时结构依赖于其数据的特定分布属性来实现高度并行处理。

我们在实时模型中使用 "实体 ID" 作为分布列的术语。 典型实体是用户、主机或设备。

实时查询通常要求按日期或类别分组的数值聚合。 超大规模（Citus）将这些查询发送到部分结果的每个分片，并将最终答案汇编到协调器节点。 当尽可能多的节点，并且没有单个节点必须执行不相称的工作量时，查询运行速度最快。

#### <a name="best-practices"></a>最佳实践

-   **选择一个具有高基数的列作为分布列。** 为了进行比较，具有 "新"、"已支付" 和 "发货" 值的 "订单" 表的状态字段不是分布列的选择。 它仅假定这几个值，它们限制了可容纳数据的分片的数目，以及可处理数据的节点数。 在具有高基数的列中，选择那些经常在 group by 子句中使用的列或联接键也是不错的选择。
-   **选择包含 "分布" 的列。** 如果在与特定公用值歪斜的列上分发表，则表中的数据往往会在某些分片中累积。 保存这些分片的节点最终比其他节点更多。
-   **将事实数据表和维度表分布在其通用列上。**
    事实数据表只能有一个分发键。 联接其他键的表将不会与事实数据表相连接。 根据联接的频率和联接行的大小，选择要归置的一个维度。
-   **将一些维度表更改为引用表。** 如果维度表不能与事实数据表一起使用，则可以通过将维度表的副本分发到引用表的所有节点，从而提高查询性能。

有关如何生成此类应用程序的示例，请阅读[实时仪表板教程](./tutorial-design-database-hyperscale-realtime.md)。

### <a name="time-series-data"></a>时序数据

在时序工作负荷中，应用程序会在存档旧信息时查询最新信息。

在超大规模（Citus）中对时间系列信息进行建模时，最常见的错误是使用时间戳本身作为分布列。 基于时间分布的哈希分布时间似乎随机进入不同的分片，而不是在分片中同时保留时间范围。 涉及时间的查询通常引用时间范围，例如，最新的数据。 这种类型的哈希分布会导致网络开销。

#### <a name="best-practices"></a>最佳实践

-   **不要选择时间戳作为分布列。** 选择其他分布列。 在多租户应用中，使用租户 ID，或在实时应用中使用实体 ID。
-   **改为使用 PostgreSQL 表分区。** 使用表分区可将大量有时间排序的数据分解成多个继承表，其中每个表都包含不同的时间范围。 在超大规模（Citus）中分发 Postgres 分区的表会为继承的表创建分片。

有关如何生成此类应用程序的示例，请阅读时序[教程](https://aka.ms/hyperscale-tutorial-timeseries)。

## <a name="next-steps"></a>后续步骤
- 了解分布式数据之间的[归置](concepts-hyperscale-colocation.md)如何帮助查询快速运行。
