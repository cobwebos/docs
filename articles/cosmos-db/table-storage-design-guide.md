---
title: 设计用于缩放和性能的 Azure Cosmos DB 表
description: Azure 表存储设计指南： Azure Cosmos DB 和 Azure 表存储中可缩放的高性能表
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 74bd22de81e385a4fbd9129a70616e24b594b0b4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441318"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Azure 表存储表设计指南：可缩放的高性能表

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

若要设计可缩放的高性能表，必须考虑到各种因素，包括费用。 如果你以前已为关系数据库设计了架构，则这些注意事项将非常熟悉。 但尽管 Azure 表存储与关系模型之间有一些相似之处，但也存在许多重要的差异。 这些差异通常会导致不同的设计，这些设计对于熟悉关系数据库的人来说可能是非常直观或错误的，但如果您正在设计 NoSQL 键/值存储（如表存储），则这种做法很有意义。

表存储的设计目的是支持云规模的应用程序，这些应用程序可包含数十亿个实体（关系数据库术语中的 "行"）数据，或者用于必须支持高事务量的数据集。 因此，需要以不同方式考虑如何存储数据，并了解表存储的工作方式。 设计良好的 NoSQL 数据存储可以使解决方案比使用关系数据库的解决方案更进一步（并且成本更低）。 本指南中介绍这些主题。  

## <a name="about-azure-table-storage"></a>关于 Azure 表存储
本部分重点介绍表存储的一些主要功能，这些功能尤其与设计性能和可伸缩性相关。 如果不熟悉 Azure 存储和表存储，请参阅本文[简介](../storage/common/storage-introduction.md)，了解本文的其余部分，了解 Microsoft Azure 存储和[azure 表存储入门](table-storage-how-to-use-dotnet.md)。 尽管本指南的重点是在表存储上，但它确实包括 Azure 队列存储和 Azure Blob 存储的一些讨论，以及如何在解决方案中将其与表存储一起使用。  

表存储使用表格格式来存储数据。 在标准术语中，表的每一行表示一个实体，而列存储该实体的各种属性。 每个实体都有一对用于唯一标识该实体的键，以及一个时间戳列，表存储使用该时间戳列来跟踪上次更新实体的时间。 时间戳字段是自动添加的，不能使用任意值手动覆盖时间戳。 表存储使用此最后修改的时间戳（LMT）来管理开放式并发。  

> [!NOTE]
> 表存储 REST API 操作还返回一个从 LMT 派生的 `ETag` 值。 在本文档中，术语 ETag 和 LMT 可互换使用，因为它们引用相同的基础数据。  
> 
> 

下面的示例演示了一个简单的表设计，该表用于存储员工和部门实体。 本指南后面所示的许多示例都基于此简单设计。  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>营销</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>营销</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td>六月</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>营销</td>
<td>部门</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>营销</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>销售热线</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


到目前为止，此设计类似于关系数据库中的表。 主要区别是必需的列，以及将多个实体类型存储在同一个表中的功能。 此外，每个用户定义的属性（如**FirstName**或**Age**）都具有数据类型（如 integer 或 string），就像关系数据库中的列一样。 但与在关系数据库中不同，表存储的无架构特性意味着每个实体的属性不需要具有相同的数据类型。 若要在单个属性中存储复杂数据类型，必须使用序列化格式（例如，JSON 或 XML）。 有关详细信息，请参阅[了解表存储数据模型](https://msdn.microsoft.com/library/azure/dd179338.aspx)。

您选择的 `PartitionKey` 和 `RowKey` 是良好的表设计的基础。 表中存储的每个实体都必须具有唯一的 `PartitionKey` 和 `RowKey`组合。 与关系数据库表中的键一样，将对 `PartitionKey` 和 `RowKey` 值进行索引，以创建支持快速查找的聚集索引。 但是，表存储不创建任何辅助索引，因此它们是两个唯一的索引属性（后面所述的一些模式会显示如何解决这种明显的限制）。  

表由一个或多个分区组成，你所做的很多设计决策都将围绕选择合适的 `PartitionKey`，并 `RowKey` 优化解决方案。 一个解决方案可以仅包含一个表，其中包含组织到分区中的所有实体，但通常一个解决方案包含多个表。 表可帮助你以逻辑方式组织实体，帮助你使用访问控制列表管理对数据的访问。 您可以使用单个存储操作删除整个表。  

### <a name="table-partitions"></a>表分区
帐户名称、表名称和 `PartitionKey` 共同标识存储服务中表存储存储实体的分区。 对于实体的寻址方案，分区定义事务的作用域（请参阅本文后面的 "[实体组事务](#entity-group-transactions)" 一节），并构成表存储的缩放方式。 有关表分区的详细信息，请参阅[表存储的性能和可伸缩性清单](../storage/tables/storage-performance-checklist.md)。  

在表存储中，单个节点为一个或多个完整的分区服务，该服务通过跨节点动态负载平衡分区进行缩放。 如果某个节点正在负载下，则表存储可将该节点提供服务的分区范围拆分到不同的节点上。 当流量下降时，表存储可以将每个分区范围从 quiet 节点合并回单个节点。  

有关表存储的内部详细信息以及它如何管理分区的详细信息，请参阅[Microsoft Azure 存储：具有高度一致性的高可用性云存储服务](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。  

### <a name="entity-group-transactions"></a>实体组事务
在表存储中，实体组事务（Egt）是唯一的内置机制，用于跨多个实体执行原子更新。 Egt 也称为*批处理事务*。 Egt 只能对存储在同一分区中的实体进行操作（在特定表中共享相同的分区键），因此，任何时候需要跨多个实体的原子事务行为时，都要确保这些实体位于同一分区中。 这通常是将多个实体类型保存在同一个表（和分区）中，而不是对不同实体类型使用多个表的原因。 单个 EGT 最多可应用于 100 个实体。  如果提交多个并发 Egt 进行处理，则必须确保这些 Egt 不会对跨 Egt 的实体进行操作。 否则，可能会延迟处理。

EGT 还引入了一个在设计时需要评估的潜在权衡。 使用更多分区会提高应用程序的可伸缩性，因为 Azure 有更多机会跨节点进行负载均衡请求。 但这可能会限制应用程序执行原子事务的能力，并保持数据的高度一致性。 而且，在分区级别还有特定的可伸缩性目标，这些目标可能会限制预期单个节点可以实现的事务吞吐量。

有关 Azure 存储帐户的可伸缩性目标的详细信息，请参阅[标准存储帐户的可伸缩性目标](../storage/common/scalability-targets-standard-account.md)。 有关表存储的可伸缩性目标的详细信息，请参阅[表存储的可伸缩性和性能目标](../storage/tables/scalability-targets.md)。 请参阅本指南的后面部分，了解帮助管理此类权衡的各种设计策略，并了解如何根据客户端应用程序的特定要求以最佳方式选择分区键。  

### <a name="capacity-considerations"></a>容量注意事项
下表包括设计表存储解决方案时要注意的一些关键值：  

| Azure 存储帐户的总容量 | 500 TB |
| --- | --- |
| Azure 存储帐户中表的个数 |仅受存储帐户的容量限制。 |
| 表中的分区个数 |仅受存储帐户的容量限制。 |
| 分区中实体的个数 |仅受存储帐户的容量限制。 |
| 单个实体的大小 |最大为 1 MB，最大值为255（包括 `PartitionKey`、`RowKey`和 `Timestamp`）。 |
| `PartitionKey` 的大小 |大小最大为 1 KB 的字符串。 |
| `RowKey` 的大小 |大小最大为 1 KB 的字符串。 |
| 实体组事务的大小 |一个事务最多可包含100个实体，并且有效负载的大小必须小于 4 MB。 EGT 只能更新一次实体。 |

有关详细信息，请参阅[了解表服务数据模型](https://msdn.microsoft.com/library/azure/dd179338.aspx)。  

### <a name="cost-considerations"></a>成本注意事项
表存储的价格相对便宜，但在评估使用表存储的任何解决方案时，您应将容量使用情况和事务数量作为计算的一部分包括在内。 然而，在许多情况下，为了提高解决方案的性能或可伸缩性，存储非规范化或重复的数据是一种有效的方法。 有关定价的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。  

## <a name="guidelines-for-table-design"></a>表设计准则
这些列表汇总了在设计表时应注意的一些关键准则。 本指南稍后将详细介绍它们。 这些准则不同于你通常用于关系数据库设计的准则。  

设计表存储以使其*更高效：*

* **专用于查询读取操作繁重的应用程序。** 设计表时，请考虑要在考虑如何更新实体之前运行的查询（特别是延迟敏感的查询）。 这通常会产生一个高效且高性能的解决方案。  
* **在查询中同时指定 `PartitionKey` 和 `RowKey`。** 例如，*点查询*是最高效的表存储查询。  
* **请考虑存储实体的重复副本。** 表存储开销较低，因此请考虑多次存储同一实体（使用不同键）以实现更高效的查询。  
* **请考虑反规范化处理数据。** 表存储比较便宜，因此请考虑非规范化数据。 例如，存储摘要实体，以便对聚合数据的查询只需访问单个实体。  
* **使用复合键值。** 你具有的唯一键是 `PartitionKey` 和 `RowKey`。 例如，使用复合键值来启用对实体的备用键控访问路径。  
* **使用查询投影。** 可使用仅选择所需字段的查询，减少网络上传输的数据量。  

将表存储设计为有效*编写*：  

* **不要创建热分区。** 选择在任何时间点都能够将请求散布到多个分区的键。  
* **避免出现流量高峰。** 在合理的时间段内分发流量，并避免流量高峰。
* **无需为每种实体类型创建一个单独的表。** 当需要对多种实体类型执行原子事务操作时，可以将这些实体类型存储在同一个表的同一个分区中。
* **请考虑必须实现的最大吞吐量。** 你必须了解表存储的可伸缩性目标，并确保你的设计不会使你超出这些目标。  

在本指南的后面部分中，你将看到将所有这些原则投入实践的示例。  

## <a name="design-for-querying"></a>针对查询的设计
表存储的读取和写入工作量可能会很大，也可能会很大。 本部分介绍如何有效地支持读取操作。 通常，支持高效读取操作的设计对于写入操作来说也是高效的。 但是，在设计以支持写入操作时还有一些其他注意事项。 下一节介绍了[数据修改的设计](#design-for-data-modification)。

使你能够高效读取数据的良好起点是问 "我的应用程序需要运行哪些查询来检索所需的数据？"  

> [!NOTE]
> 在表存储中，最好先将设计正确地进行，因为稍后对其进行更改非常困难且成本高昂。 例如，在关系数据库中，通常只需通过向现有数据库添加索引就可以解决性能问题。 这不是使用表存储的选项。  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>您选择的 `PartitionKey` 和 `RowKey` 如何影响查询性能
下面的示例假定表存储以以下结构存储员工实体（为清楚起见，大多数示例将省略 `Timestamp` 属性）：  

| 列名称 | 数据类型 |
| --- | --- |
| `PartitionKey` （部门名称） |String |
| `RowKey` （员工 ID） |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |Integer |
| `EmailAddress` |String |

下面是一些用于设计表存储查询的一般准则。 以下示例中使用的筛选器语法源自表存储 REST API。 有关详细信息，请参阅[查询实体](https://msdn.microsoft.com/library/azure/dd179421.aspx)。  

* *点查询*是最高效的查找，建议用于大容量查找或要求最低延迟的查找。 此类查询可以通过指定 `PartitionKey` 和 `RowKey` 值来使用索引来有效地查找单个实体。 例如：`$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`。  
* 第二个最佳做法是*范围查询*。 它使用 `PartitionKey`，并筛选一系列 `RowKey` 值以返回多个实体。 `PartitionKey` 值标识特定分区，`RowKey` 值标识该分区中的实体子集。 例如：`$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`。  
* 第三种是*分区扫描*。 它使用 `PartitionKey`，并筛选另一个非键属性，并可能返回多个实体。 `PartitionKey` 值标识特定分区，而属性值则选择该分区中的实体子集。 例如：`$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`。  
* *表扫描*不包含 `PartitionKey`，而且效率低下，因为它会搜索构成表的所有匹配实体的所有分区。 它将执行表扫描，无论筛选器是否使用 `RowKey`。 例如：`$filter=LastName eq 'Jones'`。  
* 返回多个实体的 Azure 表存储查询将按 `PartitionKey` 和 `RowKey` 顺序对它们进行排序。 若要避免对客户端中的实体进行排序，请选择定义最常见排序顺序的 `RowKey`。 Azure Cosmos DB 中的 Azure 表 API 返回的查询结果未按分区键或行键进行排序。 有关功能差异详细列表的信息，请参阅 [Azure Cosmos DB 和 Azure 表存储中的表 API 之间的差异](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)。

使用 "**or**" 指定基于 `RowKey` 值的筛选器将导致分区扫描，而不会视为范围查询。 因此，请避免使用筛选器的查询，例如： `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`。  

有关使用存储客户端库来运行高效查询的客户端代码的示例，请参阅：  

* [使用存储客户端库运行点查询](#run-a-point-query-by-using-the-storage-client-library)
* [使用 LINQ 检索多个实体](#retrieve-multiple-entities-by-using-linq)
* [服务器端投影](#server-side-projection)  

有关可以处理存储在同一个表中的多个实体类型的客户端代码的示例，请参阅：  

* [处理异类实体类型](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>选择适当的 `PartitionKey`
您选择的 `PartitionKey` 应权衡允许使用 Egt （以确保一致性）的需求，以确保将实体分布到多个分区（以确保可缩放的解决方案）的需求。  

在一个极端情况下，可以将所有实体都存储在单个分区中。 但这可能会限制解决方案的可伸缩性，并使表存储无法对请求进行负载均衡。 另一种极端情况是，您可以为每个分区存储一个实体。 这是高度可缩放的，使表存储能够对请求进行负载均衡，但会阻止你使用实体组事务。  

理想的 `PartitionKey` 使你可以使用高效查询，并具有足够的分区以确保解决方案是可缩放的。 通常情况下，你会发现实体将具有合适的属性，可将实体分布到足够多的分区。

> [!NOTE]
> 例如，在存储有关用户或员工的信息的系统中，`UserID` 可能是良好 `PartitionKey`。 可能有多个使用特定 `UserID` 作为分区键的实体。 存储有关用户数据的每个实体都分组到单个分区中。 可以通过 Egt 访问这些实体，同时仍具有高度可伸缩性。
> 
> 

选择与插入、更新和删除实体相关的 `PartitionKey` 时，还有其他注意事项。 有关详细信息，请参阅本文后面的[数据修改设计](#design-for-data-modification)。  

### <a name="optimize-queries-for-table-storage"></a>优化表存储的查询
表存储使用单个聚集索引中的 `PartitionKey` 和 `RowKey` 值自动为你的实体编制索引。 这就是最有效地使用点查询的原因。 不过，`PartitionKey` 和 `RowKey`上的聚集索引中不存在索引。

许多设计必须满足要求，才能允许根据多个条件查找实体。 例如，根据电子邮件、员工 ID 或姓氏查找员工实体。 [表设计模式](#table-design-patterns)部分中的以下模式满足这些类型的要求。 这些模式还介绍了解决表存储不提供辅助索引这一事实的方法。  

* [分区内辅助索引模式](#intra-partition-secondary-index-pattern)：使用不同的 `RowKey` 值（在同一分区中）存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。  
* [分区间辅助索引模式](#inter-partition-secondary-index-pattern)：通过在单独的分区或单独的表中使用不同的 `RowKey` 值存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。  
* [索引实体模式](#index-entities-pattern)：维护索引实体以启用返回实体列表的高效搜索。  

### <a name="sort-data-in-table-storage"></a>对表存储中的数据进行排序

表存储按照 `PartitionKey` 然后 `RowKey`，返回按升序排序的查询结果。

> [!NOTE]
> Azure Cosmos DB 中的 Azure 表 API 返回的查询结果未按分区键或行键进行排序。 有关功能差异详细列表的信息，请参阅 [Azure Cosmos DB 和 Azure 表存储中的表 API 之间的差异](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)。

表存储中的键是字符串值。 若要确保数字值正确排序，应将它们转换为固定长度并使用零填充它们。 例如，如果用作 `RowKey` 的雇员 ID 值是整数值，则应将员工 ID **123**转换为**00000123**。 

许多应用程序要求使用按不同顺序排序的数据：例如，按名称或按加入日期对员工进行排序。 [表设计模式](#table-design-patterns)部分中的以下模式解决了如何替换实体的排序顺序：  

* [分区内辅助索引模式](#intra-partition-secondary-index-pattern)：使用不同的 `RowKey` 值（在同一分区中）存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。  
* [分区间辅助索引模式](#inter-partition-secondary-index-pattern)：通过在单独的表中使用不同的 `RowKey` 值在不同的分区中存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。
* [日志尾部模式](#log-tail-pattern)：通过使用按日期和时间倒序排序的 `RowKey` 值检索最近添加到分区中的*n*个实体。  

## <a name="design-for-data-modification"></a>针对数据修改的设计
本部分重点介绍优化插入、更新和删除的设计注意事项。 在某些情况下，需要在针对查询优化的设计与针对数据修改优化的设计之间进行权衡。 此评估与您在设计关系数据库时所做的那样（尽管在关系数据库中，管理设计权衡的方法有所不同）。 [表设计模式](#table-design-patterns)部分介绍了表存储的一些详细设计模式，并着重介绍了其中一些利弊。 在实践中，您会发现，许多针对查询实体优化的设计也可以很好地用于修改实体。  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>优化插入、更新和删除操作的性能
若要更新或删除某个实体，必须能够通过使用 `PartitionKey` 和 `RowKey` 值来识别它。 在这一点上，你选择的用于修改实体的 `PartitionKey` 和 `RowKey` 应遵循为支持点查询而选择的类似条件。 您希望尽可能高效地标识实体。 您不希望使用效率低下的分区或表扫描来查找实体，以便发现所需的 `PartitionKey` 和 `RowKey` 值。  

[表设计模式](#table-design-patterns)部分中的以下模式解决了您的插入、更新和删除操作的性能：  

* [高容量删除模式](#high-volume-delete-pattern)：通过将所有实体都存储在它们自己的单独表中，启用删除大量实体。 您可以通过删除表来删除这些实体。  
* [数据序列模式](#data-series-pattern)：将完整的数据系列存储在单个实体中，以最大程度地减少发出的请求数。  
* [宽实体模式](#wide-entities-pattern)：使用多个物理实体来存储具有超过252个属性的逻辑实体。  
* [大实体模式](#large-entities-pattern)：使用 blob 存储来存储大属性值。  

### <a name="ensure-consistency-in-your-stored-entities"></a>确保存储实体中的一致性
影响你选择用于优化数据修改的键的其他关键因素是如何通过使用原子事务来确保一致性。 只能使用 EGT 作用于存储在同一个分区中的实体。  

[表设计模式](#table-design-patterns)部分中的以下模式解决了管理一致性：  

* [分区内辅助索引模式](#intra-partition-secondary-index-pattern)：使用不同的 `RowKey` 值（在同一分区中）存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。  
* [分区间辅助索引模式](#inter-partition-secondary-index-pattern)：通过在单独的分区或单独的表中使用不同的 `RowKey` 值存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。  
* [最终一致性事务模式](#eventually-consistent-transactions-pattern)：通过使用 Azure 队列，在分区边界或存储系统边界之间实现最终一致的行为。
* [索引实体模式](#index-entities-pattern)：维护索引实体以启用返回实体列表的高效搜索。  
* 非[规范化模式](#denormalization-pattern)：将相关数据组合在一起的单个实体中，使你能够使用单个点查询检索所有所需数据。  
* [数据序列模式](#data-series-pattern)：将完整的数据系列存储在单个实体中，以最大程度地减少发出的请求数。  

有关详细信息，请参阅本文后面的[实体组事务](#entity-group-transactions)。  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>确保用于高效修改的设计便于高效查询
在许多情况下，用于高效查询的设计会产生高效修改的效果，但你应始终评估这是否适用于特定方案。 [表设计模式](#table-design-patterns)部分中的某些模式显式评估了查询实体和修改实体之间的权衡，应始终考虑每种类型的操作的数目。  

[表设计模式](#table-design-patterns)部分中的以下模式解决了为高效查询设计和设计高效数据修改之间的权衡：  

* [复合键模式](#compound-key-pattern)：使用复合 `RowKey` 值可让客户端使用单个点查询查找相关数据。  
* [日志尾部模式](#log-tail-pattern)：通过使用按日期和时间倒序排序的 `RowKey` 值检索最近添加到分区中的*n*个实体。  

## <a name="encrypt-table-data"></a>对表数据进行加密
.NET Azure 存储客户端库支持对插入和替换操作的字符串实体属性进行加密。 加密的字符串作为二进制属性存储在服务中，并在解密之后转换回字符串。    

对于表，除了加密策略以外，用户还必须指定要加密的属性。 请指定 `EncryptProperty` 特性（适用于从 `TableEntity`派生的 POCO 实体），或在请求选项中指定加密解析程序。 加密解析程序是一个委托，该委托采用分区键、行键和属性名称，并返回一个布尔值，指示是否应加密该属性。 在加密过程中，客户端库将使用此信息来确定是否应在写入到网络时加密属性。 该委托还可以围绕如何加密属性来实现逻辑的可能性。 （例如，如果 X，则加密属性 A，否则加密属性 A 和 B。）在读取或查询实体时，不需要提供此信息。

当前不支持合并。 由于属性的子集可能以前已使用不同的密钥进行了加密，因此只合并新属性和更新元数据将导致数据丢失。 合并需要进行额外的服务调用以从服务中读取预先存在的实体，或者对每个属性使用新的密钥。 出于性能原因，这两种情况均不适用。     

有关对表数据进行加密的信息，请参阅[Microsoft Azure 存储的客户端加密和 Azure Key Vault](../storage/common/storage-client-side-encryption.md)。  

## <a name="model-relationships"></a>模型关系
构建域模型是复杂系统设计中的一个关键步骤。 通常，使用建模过程来确定实体和它们之间的关系，作为了解业务域并通知系统设计的一种方法。 本部分重点介绍如何将域模型中找到的一些常见关系类型转换为表存储的设计。 从逻辑数据模型映射到基于 NoSQL 的物理数据模型的过程与在设计关系数据库时使用的过程不同。 关系数据库设计通常假设数据规范化过程经过优化，以将冗余降到最低。 此类设计还假设有一个声明性查询功能，该功能可提取数据库工作原理的实现方式。  

### <a name="one-to-many-relationships"></a>一对多关系
业务域对象之间的一对多关系频繁发生：例如，一个部门拥有许多员工。 可以通过多种方式实现表存储中的一对多关系，每种方法都有与特定方案相关的优缺点。  

假设有成千上万个部门和员工实体的大型跨国公司的示例。 每个部门都有许多员工，每个员工都与一个特定部门相关联。 一种方法是存储不同的部门和员工实体，如下所示：  

![显示部门实体和员工实体的图形][1]

此示例根据 `PartitionKey` 值显示类型之间的隐式一对多关系。 每个部门可以有许多员工。  

此示例还显示了同一个分区中的部门实体及其相关的员工实体。 您可以选择对不同实体类型使用不同分区、表甚至存储帐户。  

另一种方法是非规范化数据，并只存储具有非规范化部门数据的员工实体，如下面的示例中所示。 在此特定方案中，如果您要求能够更改部门经理的详细信息，则此非规范化方法可能不是最佳方法。 为此，需要更新部门中的每个员工。  

![员工实体的图形][2]

有关详细信息，请参阅本指南后面的[反规范化模式](#denormalization-pattern)。  

下表总结了用于存储具有一对多关系的员工和部门实体的每种方法的优缺点。 你还应考虑你希望执行各种操作的频率。 如果该操作只发生了很少发生的情况，则必须具有一种包含昂贵操作的设计。  

<table>
<tr>
<th>方法</th>
<th>优点</th>
<th>缺点</th>
</tr>
<tr>
<td>不同的实体类型、同一分区、同一个表</td>
<td>
<ul>
<li>可以使用单个操作来更新部门实体。</li>
<li>如果需要在更新/插入/删除员工实体时修改部门实体，则可以使用 EGT 来保持一致性。 例如，如果维护每个部门的部门员工计数。</li>
</ul>
</td>
<td>
<ul>
<li>你可能需要为一些客户端活动同时检索员工和部门实体。</li>
<li>存储操作发生在同一个分区。 在事务量很高时，这可能导致热点。</li>
<li>不能使用 EGT 将员工移到新部门。</li>
</ul>
</td>
</tr>
<tr>
<td>不同的实体类型、不同的分区或表或存储帐户</td>
<td>
<ul>
<li>可以使用单个操作更新部门实体或员工实体。</li>
<li>在事务量很高时，这有助于将负载分布到多个分区。</li>
</ul>
</td>
<td>
<ul>
<li>你可能需要为一些客户端活动同时检索员工和部门实体。</li>
<li>更新/插入/删除员工以及更新部门时，不能使用 Egt 来保持一致性。 例如，更新部门实体中的员工计数。</li>
<li>不能使用 EGT 将员工移到新部门。</li>
</ul>
</td>
</tr>
<tr>
<td>非规范化为单个实体类型</td>
<td>
<ul>
<li>使用单个请求即可检索所需的所有信息。</li>
</ul>
</td>
<td>
<ul>
<li>如果需要更新部门信息（这需要更新部门中的所有员工），则保持一致性可能会很昂贵。</li>
</ul>
</td>
</tr>
</table>

如何在这些选项中进行选择，以及哪些优点和缺点最重要，取决于具体的应用程序方案。 例如，修改部门实体的频率如何？ 所有员工查询是否都需要额外的部门信息？ 你需要接近分区或存储帐户的可伸缩性限制？  

### <a name="one-to-one-relationships"></a>一对一关系
域模型可能包括实体之间的一对一关系。 如果需要在表存储中实现一对一关系，还必须选择在需要检索两个相关实体时如何链接这两个实体。 此链接可以是隐式的（基于键值中的约定），也可以是显式的，方法是将每个实体中的 `PartitionKey` 和 `RowKey` 值的形式存储到其相关实体。 若要了解是否应在同一分区存储相关实体，请参阅[一对多关系](#one-to-many-relationships)部分。  

还有一些实现注意事项，这些注意事项可能会导致在表存储中实现一对一关系：  

* 处理大型实体（有关详细信息，请参阅[大实体模式](#large-entities-pattern)）。  
* 实现访问控制（有关详细信息，请参阅[使用共享访问签名控制访问权限](#control-access-with-shared-access-signatures)）。  

### <a name="join-in-the-client"></a>在客户端中联接
尽管有多种方法可以在表存储中建立关系模型，但请不要忘记，使用表存储的两个主要原因是可伸缩性和性能。 如果你发现正在对许多关系进行建模，而这些关系会损害解决方案的性能和可伸缩性，则应问自己是否需要在表设计中构建所有数据关系。 如果让客户端应用程序执行任何必要的联接，则可能能够简化设计并改进解决方案的可伸缩性和性能。  

例如，如果你的小型表包含不经常更改的数据，则可以检索一次此数据，并将其缓存在客户端上。 这可以避免为检索相同数据而进行的重复往返操作。 在本指南中我们已讨论过的示例中，小型组织中的部门集可能很小并且不经常更改。 这使它成为了客户端应用程序可以下载一次并缓存为查找数据的数据的良好候选项。  

### <a name="inheritance-relationships"></a>继承关系
如果客户端应用程序使用一组构成继承关系的一部分的类来表示业务实体，则可以轻松地在表存储中持久保存这些实体。 例如，你可能在客户端应用程序中定义了以下一组类，其中 `Person` 是一个抽象类。

![继承关系的关系图][3]

您可以使用单个 `Person` 表在表存储中持久保存两个具体类的实例。 使用如下所示的实体：  

![显示客户实体和员工实体的图形][4]

有关在客户端代码中处理同一个表中的多个实体类型的详细信息，请参阅本指南后面的[使用异类实体类型](#work-with-heterogeneous-entity-types)。 此部分提供了如何在客户端代码中识别实体类型的示例。  

## <a name="table-design-patterns"></a>表设计模式
在前面的部分中，您已了解如何优化表设计，以便使用查询来检索实体数据，以及插入、更新和删除实体数据。 本部分介绍适用于表存储的一些模式。 此外，你将了解如何实际解决先前在本指南中提出的一些问题和利弊。 下图汇总了不同模式之间的关系：  

![表设计模式示意图][5]

模式图突出显示了本指南中介绍的模式（蓝色）和反模式（橙色）之间的某些关系。 当然，还有许多其他值得考虑的模式。 例如，表存储的关键方案之一是使用[命令查询责任分离](https://msdn.microsoft.com/library/azure/jj554200.aspx)模式中的[具体化视图模式](https://msdn.microsoft.com/library/azure/dn589782.aspx)。  

### <a name="intra-partition-secondary-index-pattern"></a>分区内辅助索引模式
使用不同 `RowKey` 值（在同一分区中）存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。 可以使用 Egt 使副本之间的更新保持一致。  

#### <a name="context-and-problem"></a>上下文和问题
表存储使用 `PartitionKey` 和 `RowKey` 值自动索引实体。 这使客户端应用程序可以使用这些值高效地检索实体。 例如，通过使用下表结构，客户端应用程序可以使用点查询，通过部门名称和员工 ID （`PartitionKey` 和 `RowKey` 值）来检索单个员工实体。 客户端还可以在每个部门内检索按员工 ID 排序的实体。

![员工实体的图形][6]

如果还想要基于另一个属性（如电子邮件地址）的值查找员工实体，则必须使用效率较低的分区扫描来查找匹配项。 这是因为表存储不提供辅助索引。 此外，没有任何选项可请求按与 `RowKey` 顺序不同的顺序排序的员工列表。  

#### <a name="solution"></a>解决方案
若要解决缺少辅助索引的情况，可以存储每个实体的多个副本，每个副本使用不同 `RowKey` 值。 如果存储具有以下结构的实体，则可以基于电子邮件地址或员工 ID 有效地检索员工实体。 使用 `RowKey`、`empid_`和 `email_` 的前缀值，你可以通过使用一系列电子邮件地址或员工 Id 查询单个员工或某一范围的员工。  

![显示具有不同 RowKey 值的 employee 实体的图形][7]

以下两个筛选条件（一个按员工 ID 查找，一个按电子邮件地址查找）都指定点查询：  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'email_jonesj@contoso.com')  

如果查询一组员工实体，则可以指定按员工 ID 顺序排序的范围或按电子邮件地址顺序排序的范围。 在 `RowKey`中查询具有相应前缀的实体。  

* 若要查找销售部门中员工 ID 介于000100到000199之间的所有员工，请使用： $filter = （PartitionKey eq ' Sales '） and （RowKey ge ' empid_000100 '） and （RowKey le ' empid_000199 '）  
* 若要查找销售部门中以字母 "a" 开头的电子邮件地址的所有员工，请使用： $filter = （PartitionKey eq ' Sales '） and （RowKey ge ' email_a '） and （RowKey lt ' email_b '）  
  
前面的示例中使用的筛选器语法来自表存储 REST API。 有关详细信息，请参阅[查询实体](https://msdn.microsoft.com/library/azure/dd179421.aspx)。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 表存储使用相对便宜，因此存储重复数据的成本开销不应是主要考虑因素。 但是，应始终根据预期的存储要求来评估设计成本，并且只添加重复的实体来支持客户端应用程序将运行的查询。  
* 由于辅助索引实体与原始实体存储在同一分区中，因此请确保不会超过单个分区的可伸缩性目标。  
* 可以通过使用 EGT 以原子方式更新实体的两个副本，来使重复的实体彼此保持一致。 这意味着应将实体的所有副本都存储在同一个分区。 有关详细信息，请参阅[使用实体组事务](#entity-group-transactions)。  
* 对于每个实体，用于 `RowKey` 的值必须是唯一的。 请考虑使用复合键值。  
* 在 `RowKey` 中填充数字值（例如，员工 ID 000223）可基于上限和下限启用正确的排序和筛选。  
* 不一定需要重复实体的所有属性。 例如，如果使用 `RowKey` 中的电子邮件地址查找实体的查询永远不需要员工的年龄，则这些实体可以具有以下结构：

  ![员工实体的图形][8]

* 通常，最好存储重复数据并确保可以使用单个查询检索所有所需数据，而不是使用一个查询来查找实体，使用另一个查询来查找所需数据。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
在以下情况下使用此模式：

- 客户端应用程序需要使用各种不同的键检索实体。
- 你的客户端需要以不同排序顺序检索实体。
- 您可以使用各种唯一值标识每个实体。

但是，在使用不同 `RowKey` 值执行实体查找时，请确保不会超过分区可伸缩性限制。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [分区间辅助索引模式](#inter-partition-secondary-index-pattern)
* [复合键模式](#compound-key-pattern)
* [实体组事务](#entity-group-transactions)
* [处理异类实体类型](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>分区间辅助索引模式
通过在单独的分区或单独的表中使用不同 `RowKey` 值存储每个实体的多个副本。 这将实现快速高效的查找，并通过使用不同的 `RowKey` 值来替换排序顺序。  

#### <a name="context-and-problem"></a>上下文和问题
表存储使用 `PartitionKey` 和 `RowKey` 值自动索引实体。 这使客户端应用程序可以使用这些值高效地检索实体。 例如，通过使用下表结构，客户端应用程序可以使用点查询，通过部门名称和员工 ID （`PartitionKey` 和 `RowKey` 值）来检索单个员工实体。 客户端还可以在每个部门内检索按员工 ID 排序的实体。  

![员工实体的图形][9]

如果还要能够基于另一个属性（例如，电子邮件地址）的值查找员工实体，则必须使用效率较低的分区扫描来查找匹配项。 这是因为表存储不提供辅助索引。 此外，没有任何选项可请求按与 `RowKey` 顺序不同的顺序排序的员工列表。  

对于这些实体，要预测的事务量很大，并且想要将表存储速率限制客户端的风险降到最低。  

#### <a name="solution"></a>解决方案
若要解决缺少辅助索引的情况，可以存储每个实体的多个副本，每个副本使用不同 `PartitionKey` 和 `RowKey` 值。 如果存储具有以下结构的实体，则可以基于电子邮件地址或员工 ID 有效地检索员工实体。 `PartitionKey`、`empid_`和 `email_` 的前缀值使您能够确定要用于查询的索引。  

![显示具有主索引的员工实体和具有辅助索引的员工实体的图形][10]

以下两个筛选条件（一个按员工 ID 查找，一个按电子邮件地址查找）都指定点查询：  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') and (RowKey eq 'jonesj@contoso.com')  

如果查询一组员工实体，则可以指定按员工 ID 顺序排序的范围或按电子邮件地址顺序排序的范围。 在 `RowKey`中查询具有相应前缀的实体。  

* 若要查找销售部门中员工 ID 在**000100**到**000199**范围内的所有员工，按员工 id 顺序排序，请使用： $filter = （PartitionKey eq ' empid_Sales '） and （RowKey ge ' 000100 '） and （RowKey le ' 000199 '）  
* 若要查找销售部门中的所有员工，其电子邮件地址以 "a" 开头，按电子邮件地址顺序排序，请使用： $filter = （PartitionKey eq ' email_Sales '） and （RowKey ge ' a '） and （RowKey lt ' b '）  

请注意，上述示例中使用的筛选器语法来自表存储 REST API。 有关详细信息，请参阅[查询实体](https://msdn.microsoft.com/library/azure/dd179421.aspx)。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 可使用[最终一致的事务模式](#eventually-consistent-transactions-pattern)维护主辅索引实体，保证重复实体彼此间的最终一致性。  
* 表存储使用相对便宜，因此存储重复数据的成本开销不应是主要考虑因素。 但是，应始终根据预期的存储要求来评估设计成本，并且只添加重复的实体来支持客户端应用程序将运行的查询。  
* 对于每个实体，用于 `RowKey` 的值必须是唯一的。 请考虑使用复合键值。  
* 在 `RowKey` 中填充数字值（例如，员工 ID 000223）可基于上限和下限启用正确的排序和筛选。  
* 不一定需要重复实体的所有属性。 例如，如果使用 `RowKey` 中的电子邮件地址查找实体的查询永远不需要员工的年龄，则这些实体可以具有以下结构：
  
  ![显示具有辅助索引的员工实体的图形][11]
* 通常，最好存储重复数据并确保可以使用单个查询检索所有所需数据，而不是使用一个查询通过辅助索引找到实体，使用另一个查询在主索引中查找所需数据。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
在以下情况下使用此模式：

- 客户端应用程序需要使用各种不同的键检索实体。
- 你的客户端需要以不同排序顺序检索实体。
- 您可以使用各种唯一值标识每个实体。

如果要在使用不同 `RowKey` 值执行实体查找时避免超过分区可伸缩性限制，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [最终一致的事务模式](#eventually-consistent-transactions-pattern)  
* [分区内辅助索引模式](#intra-partition-secondary-index-pattern)  
* [复合键模式](#compound-key-pattern)  
* [实体组事务](#entity-group-transactions)  
* [处理异类实体类型](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>最终一致的事务模式
通过使用 Azure 队列跨分区边界或存储系统边界启用最终一致的行为。  

#### <a name="context-and-problem"></a>上下文和问题
EGT 在多个共享同一分区键的实体之间启用原子事务。 由于性能和伸缩性原因，你可能会决定在单独的分区或单独的存储系统中存储具有一致性要求的实体。 在这种情况下，不能使用 Egt 来保持一致性。 例如，可能需要保持以下对象之间的最终一致性：  

* 存储在同一个表的两个不同分区中的实体、存储在不同表中的实体，或存储在不同存储帐户中的实体。  
* 存储在表存储中的实体和存储在 Blob 存储中的 blob。  
* 存储在表存储中的实体和文件系统中的文件。  
* 存储在表存储中的实体，但使用 Azure 认知搜索进行了索引。  

#### <a name="solution"></a>解决方案
通过使用 Azure 队列，可以实现一种解决方案，用于在两个或更多个分区或存储系统之间提供最终一致性。

为了说明此方法，假定你需要能够将以前的员工实体存档。 以前的员工实体很少进行查询，并应从处理当前员工的任何活动中排除。 若要实现此要求，请将活动员工存储在**当前**表中，并将以前的员工存储在**Archive**表中。 存档员工需要从**当前**表中删除实体，然后将该实体添加到**Archive**表中。

但不能使用 EGT 执行这两个操作。 若要避免故障导致实体同时出现在这两个表中或未出现在任一表中的风险，存档操作必须确保最终一致性。 下面的序列图概述了此操作中的步骤。  

![最终一致性的解决方案图示][12]

客户端通过在 Azure 队列中放置一条消息来启动存档操作（在本示例中，将员工存档 #456）。 辅助角色会在队列中轮询新消息；当它找到一个新消息时，将读取该消息，并在队列上保留一个隐藏的副本。 接下来，辅助角色从 **Current** 表中获取实体的副本，将该副本插入 **Archive** 表中，然后删除 **Current** 表中的原始实体。 最后，如果在前面的步骤中没有出现错误，辅助角色将从队列中删除隐藏的消息。  

在此示例中，关系图中的步骤4将该员工插入到**Archive**表中。 它可以将该员工添加到 Blob 存储中的 blob 或文件系统中的文件。  

#### <a name="recover-from-failures"></a>从故障中恢复
如果辅助角色需要重新启动存档操作，则关系图的步骤4-5 中的操作必须是*幂等*的。 如果使用的是表存储，则在步骤4中应使用 "插入或替换" 操作;对于步骤5，应在所使用的客户端库中使用 "如果存在，则删除" 操作。 如果使用的是其他存储系统，则必须使用相应的幂等操作。  

如果辅助角色永远不会在关系图中完成步骤6，则在超时后，该消息将重新出现在队列中，以供辅助角色尝试重新处理它。 辅助角色可以检查已读取队列中的消息的次数，如有必要，可通过将消息发送到单独的队列来将其标记为 "病毒" 消息进行调查。 有关读取队列消息和检查取消排队计数的详细信息，请参阅[获取消息](https://msdn.microsoft.com/library/azure/dd179474.aspx)。  

表存储和队列存储中的一些错误是暂时性错误，客户端应用程序应包括适当的重试逻辑来处理这些错误。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 此解决方案不提供事务隔离。 例如，当辅助角色在关系图中的步骤4-5 之间时，客户端可以读取**Current**和**Archive**表，并查看数据的不一致视图。 数据将最终保持一致。  
* 必须确保步骤4-5 是幂等的，才能确保最终一致性。  
* 可以通过使用多个队列和辅助角色实例来扩展此解决方案。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当需要保证不同分区或表中存在的实体之间的最终一致性时，请使用此模式。 你可以扩展此模式，以确保跨表存储和 Blob 存储以及其他非 Azure 存储数据源（如数据库或文件系统）的操作的最终一致性。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [实体组事务](#entity-group-transactions)  
* [合并或替换](#merge-or-replace)  

> [!NOTE]
> 如果事务隔离对解决方案很重要，请考虑重新设计表以使你可以使用 Egt。  
> 
> 

### <a name="index-entities-pattern"></a>索引实体模式
维护索引实体以启用返回实体列表的高效搜索。  

#### <a name="context-and-problem"></a>上下文和问题
表存储使用 `PartitionKey` 和 `RowKey` 值自动索引实体。 这样，客户端应用程序便可以使用点查询高效地检索实体。 例如，使用下表结构，客户端应用程序可以通过使用部门名称和员工 ID （`PartitionKey` 和 `RowKey`）来高效地检索单个员工实体。  

![员工实体的图形][13]

如果还希望能够根据另一个非唯一的属性（如姓氏）的值检索员工实体的列表，则必须使用效率较低的分区扫描。 此扫描找到匹配项，而不是使用索引来直接查找。 这是因为表存储不提供辅助索引。  

#### <a name="solution"></a>解决方案
若要启用按姓氏与前面的实体结构的查找，必须维护员工 Id 列表。 如果要检索具有特定姓氏（例如 ""）的员工实体，则必须首先查找员工的雇员 Id 列表作为其姓氏，然后检索这些员工实体。 有三个主要选项用于存储员工 Id 列表：  

* 使用 Blob 存储。  
* 在员工实体所在的同一分区中创建索引实体。  
* 在不同分区或表中创建索引实体。  

选项1：使用 Blob 存储  

为每个唯一的姓氏创建一个 blob，并在每个 blob 中存储具有该姓氏的员工的 `PartitionKey` （部门）和 `RowKey` （员工 ID）值的列表。 在添加或删除员工时，请确保相关 blob 的内容与员工实体最终一致。  

选项2：在同一分区中创建索引实体  

使用存储以下数据的索引实体：  

![显示 employee 实体的图形，包含具有相同姓氏的雇员 Id 列表的字符串][14]

`EmployeeIDs` 属性包含存储在 `RowKey`中的雇员的雇员 Id 列表。  

以下步骤概述了在添加新员工时应遵循的过程。 在此示例中，我们将在销售部门添加 ID 为000152和姓氏为：  

1. 检索 `PartitionKey` 值为 "Sales" 的索引实体和 `RowKey` 值 "加"。 保存此实体的 ETag 以便在步骤 2 中使用。  
2. 创建实体组事务（即批量操作），用于插入新的员工实体（`PartitionKey` 值 "Sales" 和 `RowKey` 值 "000152"），并更新索引实体（`PartitionKey` 值 "Sales" 和 `RowKey` 值 " EGT 通过将新的员工 ID 添加到 EmployeeIDs 字段的列表来实现此功能。 有关 Egt 的详细信息，请参阅[实体组事务](#entity-group-transactions)。  
3. 如果 EGT 由于乐观并发错误而失败（即，其他人修改了索引实体），则需要从步骤1开始。  

如果使用第二个选项，则可以使用类似的方法删除员工。 更改员工的姓氏会稍微复杂一些，因为需要运行更新三个实体的 EGT： employee 实体、旧姓氏的索引实体和新姓氏的索引实体。 必须在进行任何更改之前检索每个实体，才能检索 ETag 值，然后可以使用该值通过乐观并发来执行更新。  

以下步骤概述了在需要查找部门中具有特定姓氏的所有员工时应遵循的过程。 在此示例中，我们将在销售部门查找姓氏为的所有雇员：  

1. 检索 `PartitionKey` 值为 "Sales" 的索引实体和 `RowKey` 值 "加"。  
2. 分析 "`EmployeeIDs`" 字段中的员工 Id 列表。  
3. 如果你需要有关其中每个员工的其他信息（例如其电子邮件地址），请使用 `PartitionKey` 值 "Sales" 检索每个员工实体，并从你在步骤2中获得的员工列表中 `RowKey` 值。  

选项3：在单独的分区或表中创建索引实体  

对于此选项，请使用存储以下数据的索引实体：  

![显示 employee 实体的图形，包含具有相同姓氏的雇员 Id 列表的字符串][15]

`EmployeeIDs` 属性包含存储在 `RowKey`中的雇员的雇员 Id 列表。  

不能使用 Egt 来保持一致性，因为索引实体位于与员工实体不同的分区中。 确保索引实体与员工实体最终一致。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 此解决方案至少需要两个查询来检索匹配的实体：一个查询用于查询索引实体以获取 `RowKey` 值的列表，然后查询用于检索列表中的每个实体。  
* 由于单个实体的最大大小为 1 MB，因此，解决方案中的选项2和选项3假定任何特定姓氏的员工 Id 列表永远不会超过 1 MB。 如果员工 Id 列表的大小可能大于 1 MB，请使用选项1，并将索引数据存储在 Blob 存储中。  
* 如果使用选项2（使用 Egt 处理添加和删除员工，以及更改员工的姓氏），则必须评估事务量是否将接近特定分区的可伸缩性限制。 如果是这种情况，应考虑使用最终一致的解决方案（选项1或选项3）。 这些队列使用队列来处理更新请求，并使你能够将索引实体存储在与员工实体不同的分区中。  
* 此解决方案中的选项2假设你想要在某个部门内按姓氏查找。 例如，你想要检索销售部门中姓氏为个雇员的雇员列表。 如果希望能够在整个组织内查找姓氏为所有的雇员，请使用选项1或选项3。
* 可实现基于队列的解决方案，以提供最终一致性。 有关更多详细信息，请参阅[最终一致的事务模式](#eventually-consistent-transactions-pattern)。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当您想要查找所有共享公共属性值的实体集时，请使用此模式，例如，姓氏为所有的雇员。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [复合键模式](#compound-key-pattern)  
* [最终一致的事务模式](#eventually-consistent-transactions-pattern)  
* [实体组事务](#entity-group-transactions)  
* [处理异类实体类型](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>非规范化模式
将相关数据组合在一起放置在单个实体中，使你能够使用单个点查询检索所有所需的数据。  

#### <a name="context-and-problem"></a>上下文和问题
在关系数据库中，通常会规范化数据，以删除在查询从多个表中检索数据时发生的重复。 如果规范化 Azure 表中的数据，则必须从客户端到服务器进行多次往返才能检索相关数据。 例如，使用下表结构时，需要两次往返行程来检索部门的详细信息。 一次行程会提取包含经理 ID 的部门实体，第二次行程会在 employee 实体中获取经理的详细信息。  

![部门实体和员工实体的图形][16]

#### <a name="solution"></a>解决方案
不是将数据存储在两个不同的实体中，而是对数据进行反规范化，并在部门实体中保留经理详细信息的副本。 例如：  

![不规范和组合的部门实体的图形][17]

通过使用这些属性存储部门实体，你现在可以使用点查询检索有关某个部门的所有所需详细信息。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 将一些数据存储两次会有一些相关的成本开销。 较少的表存储请求导致的性能好处通常是存储成本的边际增长。 此外，此成本通过减少获取部门详细信息所需的事务数部分偏移。  
* 必须维护存储经理相关信息的两个实体的一致性。 可以通过使用 Egt 在单个原子事务中更新多个实体来处理一致性问题。 在这种情况下，部门经理的部门实体和员工实体存储在同一个分区中。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
经常需要查找相关信息时，请使用此模式。 此模式减少了客户端要检索它所需的数据必须执行的查询数。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [复合键模式](#compound-key-pattern)  
* [实体组事务](#entity-group-transactions)  
* [处理异类实体类型](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>复合键模式
使用复合 `RowKey` 值可让客户端使用单个点查询查找相关数据。  

#### <a name="context-and-problem"></a>上下文和问题
在关系数据库中，在查询中使用联接来在单个查询中向客户端返回相关的数据片段是自然的。 例如，可能会使用员工 ID 来查找包含该员工的绩效和评价数据的相关实体的列表。  

假设你要使用以下结构在表存储中存储员工实体：  

![员工实体的图形][18]

还需要存储有关员工为组织工作的每年的评审和绩效的历史数据，并且需要能够按年份访问此信息。 一种选择是创建另一个表，该表存储具有以下结构的实体：  

![员工评论实体的图形][19]

使用此方法时，你可能会决定在新实体中重复一些信息（如名字和姓氏），以便可以使用单个请求检索数据。 但是，无法保持强一致性，因为不能使用 EGT 以原子方式更新这两个实体。  

#### <a name="solution"></a>解决方案
通过使用具有以下结构的实体，将新的实体类型存储到原始表中：  

![具有复合键的 employee 实体的图形][20]

请注意，`RowKey` 现在是复合键，由雇员 ID 和评审数据的年份组成。 这使您可以使用单个实体的单个请求检索员工的绩效和查看数据。  

下面的示例概述了如何检索特定员工的所有评价数据（如 Sales 部门中的员工 000123）：  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 应使用适当的分隔符，以便轻松分析 `RowKey` 值：例如， **000123_2012**。  
* 你也将此实体存储在与包含同一员工的相关数据的其他实体相同的分区中。 这意味着可以使用 Egt 来维护强一致性。
* 你应考虑查询数据的频率，以确定此模式是否合适。 例如，如果不经常访问查看数据，并且经常访问主要员工数据，则应将这些数据保留为单独的实体。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当需要存储一个或多个经常查询的相关实体时，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [实体组事务](#entity-group-transactions)  
* [处理异类实体类型](#work-with-heterogeneous-entity-types)  
* [最终一致的事务模式](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>记录结尾模式
使用按日期和时间倒序排序的 `RowKey` 值检索最近添加到分区中的*n*个实体。  

> [!NOTE]
> Azure Cosmos DB 中的 Azure 表 API 返回的查询结果未按分区键或行键进行排序。 因此，虽然此模式适用于表存储，但它并不适用于 Azure Cosmos DB。 有关功能差异详细列表的信息，请参阅 [Azure Cosmos DB 和 Azure 表存储中的表 API 之间的差异](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)。

#### <a name="context-and-problem"></a>上下文和问题
一个常见的需求是能够检索最近创建的实体，例如某个员工提交的最近 10 个费用报销单。 表查询支持 `$top` 查询操作，以返回集中的前*n*个实体。 没有等效的查询操作可返回集中的最后*n*个实体。  

#### <a name="solution"></a>解决方案
使用以反向日期/时间顺序自然排序的 `RowKey` 存储实体，因此，最新的条目始终是表中的第一个条目。  

例如，若要能够检索某个员工提交的最近十个费用报销单，可以使用从当前日期/时间派生的反向时点值。 下面C#的代码示例演示了一种为从最新到最旧的 `RowKey` 创建合适的 "反向计时周期" 值的方法：  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

可以通过使用以下代码来返回到日期/时间值：  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

表查询如下所示：  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 必须用前导零填充反向刻度值，以确保字符串值按预期方式排序。  
* 必须要注意分区级别的可伸缩性目标。 请注意不要创建热点分区。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当需要按反向日期/时间顺序访问实体或需要访问最近添加的实体时，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [前置/后置反模式](#prepend-append-anti-pattern)  
* [检索实体](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>大量删除模式
通过将所有实体都存储在它们自己的单独表中来实现删除大量实体。 您可以通过删除表来删除这些实体。  

#### <a name="context-and-problem"></a>上下文和问题
许多应用程序会删除不再需要向客户端应用程序提供或已归档到其他存储介质的旧数据。 通常按日期标识此类数据。 例如，你需要删除超过60天以前的所有登录请求的记录。  

一个可能的设计是使用 `RowKey`中登录请求的日期和时间：  

![登录尝试实体的图形][21]

此方法可避免分区热点，因为应用程序可以在单独的分区中插入和删除每个用户的登录实体。 但是，如果有大量实体，此方法可能成本高昂且非常耗时。 首先，您需要执行表扫描以便确定所有要删除的实体，然后必须删除每个旧实体。 可以通过在 EGT 中成批处理多个删除请求来减少到服务器的往返次数。  

#### <a name="solution"></a>解决方案
对每天的登录尝试使用一个单独的表。 在插入实体时，可以使用上述实体设计来避免热点。 删除旧实体现在只是一种删除一个表的问题（单个存储操作），而不是每天查找并删除成百上千个单个登录实体。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 你的设计是否支持应用程序将使用数据的其他方法（例如查找特定实体、与其他数据链接或生成聚合信息）？  
* 插入新实体时，设计会避免产生热点吗？  
* 如果要在删除某个表后重用同一表名，应会出现延迟。 最好始终使用唯一表名。  
* 首次使用新表时，预期会出现一定速率限制，而表存储则了解访问模式并跨节点分散分区。 应考虑需要创建新表的频率。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
有大量必须同时删除的实体时，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [实体组事务](#entity-group-transactions)
* [修改实体](#modify-entities)  

### <a name="data-series-pattern"></a>数据系列模式
将完整的数据系列存储在单个实体中，以最大限度地减少发出请求的次数。  

#### <a name="context-and-problem"></a>上下文和问题
一个常见方案用于要存储一系列数据的应用程序，该应用程序通常需要一次检索所有这些数据。 例如，应用程序可能会记录每个员工每小时发送的 IM 消息数，并使用此信息来绘制每个用户在过去 24 小时内发送的消息数。 一个设计可以是为每个员工存储 24 个实体：  

![消息统计信息实体的图形][22]

利用此设计，在应用程序需要更新消息计数值时，可以方便地找到并更新要为每个员工更新的实体。 但是，为了检索信息以绘制过去 24 小时的活动图，必须检索 24 个实体。  

#### <a name="solution"></a>解决方案
使用以下设计，并使用单独的属性来存储每小时的消息计数：  

![显示包含分隔属性的消息统计信息实体的图形][23]

利用此设计，可以使用合并操作来更新某个员工在特定小时内的消息计数。 现在，您可以使用对单个实体的请求检索绘制图表所需的所有信息。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 如果您的完整数据序列不能容纳在单个实体中（一个实体最多可以有252个属性），请使用其他数据存储（如 blob）。  
* 如果有多个客户端同时更新实体，请使用**ETag**来实现开放式并发。 如果有多个客户端，则可能会遇到高争用情况。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当需要更新和检索与单个实体关联的数据序列时，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [大实体模式](#large-entities-pattern)  
* [合并或替换](#merge-or-replace)  
* [最终一致的事务模式](#eventually-consistent-transactions-pattern)（如果要将数据序列存储在 blob 中）  

### <a name="wide-entities-pattern"></a>宽实体模式
使用多个物理实体来存储具有多于 252 个属性的逻辑实体。  

#### <a name="context-and-problem"></a>上下文和问题
单个实体不能包含252个以上的属性（不包括必需的系统属性），并且总共不能存储超过 1 MB 的数据。 在关系数据库中，您通常会通过添加一个新表并在它们之间强制实施1对1关系，来避免对行大小的任何限制。  

#### <a name="solution"></a>解决方案
通过使用表存储，可以存储多个实体来表示具有252多个属性的单个大型业务对象。 例如，如果你想要存储每个员工在过去365天内发送的 IM 消息数的计数，则可以使用以下设计（该设计使用两个具有不同架构的实体）：  

![显示消息统计信息实体的图形，该实体具有 Rowkey 01，消息统计信息实体与 Rowkey 02][24]

如果需要进行的更改需要更新这两个实体以使它们保持彼此同步，则可以使用 EGT。 否则，可以使用单个合并操作来更新特定天的消息计数。 若要检索单个员工的所有数据，必须检索这两个实体。 您可以使用两个同时使用 `PartitionKey` 和 `RowKey` 值的有效请求来执行此操作。  

#### <a name="issues-and-considerations"></a>问题和注意事项
决定如何实现此模式时，请注意以下事项：  

* 检索完整的逻辑实体至少涉及两个存储事务：其中一个用于检索每个物理实体。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当需要存储的实体的大小或属性数超出表存储中单个实体的限制时，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [实体组事务](#entity-group-transactions)
* [合并或替换](#merge-or-replace)

### <a name="large-entities-pattern"></a>大实体模式
使用 Blob 存储来存储大属性值。  

#### <a name="context-and-problem"></a>上下文和问题
单个实体不能存储总共超过 1 MB 的数据。 如果一个或多个属性存储的值导致实体的总大小超出此值，则不能在表存储中存储整个实体。  

#### <a name="solution"></a>解决方案
如果由于一个或多个属性包含大量数据而导致实体的大小超过 1 MB，可以将数据存储在 Blob 存储中，然后将 blob 的地址存储在实体的属性中。 例如，你可以在 Blob 存储中存储员工的照片，并在员工实体的 `Photo` 属性中存储指向照片的链接：  

![显示指向 Blob 存储的照片的员工实体的图形][25]

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 若要保持表存储中的实体和 Blob 存储中的数据之间的最终一致性，请使用[最终一致的事务模式](#eventually-consistent-transactions-pattern)来维护你的实体。
* 检索完整实体至少涉及两个存储事务：一个用于检索实体，另一个用于检索 blob 数据。  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当需要存储的实体的大小超过表存储中单个实体的限制时，请使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [最终一致的事务模式](#eventually-consistent-transactions-pattern)  
* [宽实体模式](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>前置/后置反模式
当有大量插入时，通过将插入分散到多个分区来提高可伸缩性。  

#### <a name="context-and-problem"></a>上下文和问题
将实体前置或后置于存储实体通常会导致应用程序将新实体添加到分区序列中的第一个分区或最后一个分区。 在这种情况下，任何特定时间的所有插入都发生在同一个分区中，从而创建一个热点。 这可以防止表存储跨多个节点对插入进行负载均衡，并可能导致应用程序命中分区的可伸缩性目标。 例如，假设应用程序记录员工对网络和资源的访问。 如果事务量达到单个分区的可伸缩性目标，则如下图所示的实体结构可能会导致当前小时的分区成为热点：  

![员工实体的图形][26]

#### <a name="solution"></a>解决方案
以下替代实体结构在应用程序记录事件时可避免在任何特定分区上出现热点：  

![显示 RowKey 复利年、月、日、小时和事件 ID 的 employee 实体的图形][27]

请注意，在此示例中，`PartitionKey` 和 `RowKey` 都是复合键。 `PartitionKey` 使用部门和员工 ID 将日志记录分布到多个分区。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何实现此模式时，请考虑以下几点：  

* 可避免在高效插入时产生热分区的替代键结构是否支持客户端应用程序进行的查询？  
* 预期的事务量是否意味着可能会达到单个分区的可伸缩性目标，并被表存储限制？  

#### <a name="when-to-use-this-pattern"></a>何时使用此模式
当你访问热分区时，如果事务量可能会导致表存储速率限制，则应避免使用预置/追加反模式。  

#### <a name="related-patterns-and-guidance"></a>相关模式和指南
实施此模式时，可能也会与以下模式和指南相关：  

* [复合键模式](#compound-key-pattern)  
* [日志结尾模式](#log-tail-pattern)  
* [修改实体](#modify-entities)  

### <a name="log-data-anti-pattern"></a>日志数据反模式
通常，应使用 Blob 存储而不是表存储来存储日志数据。  

#### <a name="context-and-problem"></a>上下文和问题
日志数据的一个常见用例是检索针对特定日期/时间范围选择的日志条目。 例如，你想要查找应用程序在特定日期的15:04 和15:06 之间记录的所有错误和关键消息。 您不希望使用日志消息的日期和时间来确定将日志实体保存到的分区。 这会导致热分区，因为在任何特定时间，所有日志实体都将共享相同的 `PartitionKey` 值（请参阅[预置/追加反模式](#prepend-append-anti-pattern)）。 例如，日志消息的以下实体架构会导致热分区，因为应用程序会将所有日志消息写入当前日期和小时的分区中：  

![日志消息实体的图形][28]

在此示例中，`RowKey` 包括日志消息的日期和时间，以确保日志消息按日期/时间顺序排序。 如果多个日志消息共享同一日期和时间，则 `RowKey` 还包括消息 ID。  

另一种方法是使用 `PartitionKey` 确保应用程序在一组分区中写入消息。 例如，如果日志消息的源提供了一种跨多个分区分发消息的方式，则可以使用以下实体架构：  

![日志消息实体的图形][29]

但是，此架构的问题在于，若要检索特定时间跨度内的所有日志消息，必须搜索表中的每个分区。

#### <a name="solution"></a>解决方案
上一部分重点介绍了尝试使用表存储来存储日志条目的问题，并建议了两个不满意的设计。 一种解决方案会导致热分区，并面临在写入日志消息时性能不佳的风险。 由于需要扫描表中的每个分区以检索特定时间跨度的日志消息，因此另一种解决方案导致了查询性能不佳。 Blob 存储为这种类型的方案提供了更好的解决方案，这就是 Azure 存储分析存储它收集的日志数据的方式。  

本部分概述了存储分析如何在 Blob 存储中存储日志数据，并说明了此方法如何存储通常按范围查询的数据。  

Storage analytics 以带分隔符格式将日志消息存储在多个 blob 中。 使用带分隔符的格式，客户端应用程序可以轻松地分析日志消息中的数据。  

存储分析使用 blob 的命名约定，使你可以找到包含要搜索的日志消息的 blob （或 blob）。 例如，名为 "queue/2014/07/31/1800/000001" 的 blob 包含与在18:00 年7月 2014 31 日开始的一小时的队列服务相关的日志消息。 “000001”指示这是此期间的第一个日志文件。 存储分析还会记录存储在文件中的第一条和最后一条日志消息的时间戳作为 blob 的元数据的一部分。 使用 Blob 存储的 API 可以根据名称前缀在容器中查找 blob。 若要查找包含从18:00 开始的一小时的队列日志数据的所有 blob，可以使用前缀 "queue/2014/07/31/1800"。  

存储分析在内部缓存日志消息，然后定期更新相应的 blob 或使用最新一批日志条目创建新的 blob。 这会减少它必须对 Blob 存储执行的写入次数。  

如果要在自己的应用程序中实现类似的解决方案，请考虑如何管理可靠性、成本和可伸缩性之间的权衡。 换句话说，比较了在应用程序中缓冲更新，并分批将它们写入 Blob 存储的情况下，将每个日志条目写入到 Blob 存储的影响。  

#### <a name="issues-and-considerations"></a>问题和注意事项
在决定如何存储日志数据时，请考虑以下几点：  

* 如果创建了可避免潜在热分区的表设计，则可能会发现无法高效地访问日志数据。  
* 若要处理日志数据，客户端通常需要加载多个记录。  
* 虽然日志数据通常结构化，但 Blob 存储可能是更好的解决方案。  

### <a name="implementation-considerations"></a>实现注意事项
本部分讨论在实现前面的部分中所述的模式时，需要牢记的一些注意事项。 本部分的大部分内容使用以 C# 编写的示例，其中使用了存储客户端库（在撰写本文时为版本 4.3.0）。  

### <a name="retrieve-entities"></a>检索实体
如[针对查询的设计](#design-for-querying)部分所述，最高效的查询是点查询。 但是，在某些情况下，可能需要检索多个实体。 本部分介绍使用存储客户端库检索实体的一些常用方法。  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>使用存储客户端库运行点查询
运行点查询的最简单方法是使用**检索**表操作。 如下面C#的代码片段所示，此操作将检索值为 "Sales" 且值为 "212" 的 `RowKey` 的 `PartitionKey` 实体：  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

请注意，此示例需要它所检索到的实体的类型为 `EmployeeEntity`。  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>使用 LINQ 检索多个实体
可以通过将 LINQ 与存储客户端库配合使用，并使用**where**子句指定查询来检索多个实体。 若要避免表扫描，应始终在 where 子句中包含 `PartitionKey` 值，并且如果可能，则应使用 `RowKey` 值以避免表和分区扫描。 表存储支持在 where 子句中使用一组有限的比较运算符（大于、大于等于、小于、小于等于、等于和不等于）。 下面C#的代码段查找销售部门中姓氏以 "B" 开头（假定 `RowKey` 存储姓氏）的所有员工（假定 `PartitionKey` 存储部门名称）：  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

请注意，查询如何指定 `RowKey` 和 `PartitionKey` 以确保更好的性能。  

下面的代码示例通过使用 Fluent API 显示等效功能（有关一般的熟知 Api 的详细信息，请参阅[设计 Fluent API 的最佳实践](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)）：  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> 该示例嵌套了多个 `CombineFilters` 方法以包含三个筛选条件。  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>从查询中检索大量实体
最佳查询根据 `PartitionKey` 值和 `RowKey` 值返回单个实体。 但是，在某些情况下，您可能需要从同一个分区或者甚至从多个分区返回多个实体。 在这种情况下，应始终充分地测试应用程序的性能。  

针对表存储的查询一次最多可以返回1000个实体，并运行最多5秒。 表存储将返回一个继续标记，使客户端应用程序能够请求下一组实体（如果满足以下任一条件）：

- 结果集包含1000个以上的实体。
- 查询未在5秒内完成。
- 查询跨越分区边界。 

有关继续标记如何工作的详细信息，请参阅[查询超时和分页](https://msdn.microsoft.com/library/azure/dd135718.aspx)。  

如果你使用的是存储客户端库，则它可以在返回表存储中的实体时自动处理继续标记。 例如，如果表存储C#在响应中返回继续标记，下面的代码示例会自动处理它们：  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

以下 C# 代码显式处理继续标记：  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

通过显式使用继续标记，可以控制应用程序何时检索下一个数据段。 例如，如果客户端应用程序允许用户翻阅表中存储的实体，用户可能会决定不翻阅查询检索的所有实体。 当用户已完成对当前段中的所有实体的分页时，应用程序才会使用继续标记检索下一段。 此方法具有以下几个优点：  

* 可以限制要从表存储中检索的数据量，以及在网络上移动的数据量。  
* 可以在 .NET 中执行异步 i/o。  
* 可以将继续标记序列化到持久存储，以便在应用程序崩溃时继续进行。  

> [!NOTE]
> 继续标记通常返回包含1000实体的段，尽管它可以包含更少的。 如果使用**Take**返回与查找条件匹配的前 n 个实体来限制查询返回的条目数，也会出现这种情况。 表存储可能会返回包含少于 n 个实体的段和一个继续标记，可用于检索剩余的实体。  
> 
> 

以下 C# 代码演示如何修改段内返回的实体数：  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>服务器端投影
单个实体最多可以具有 255 个属性，并且大小最多可以为 1 MB。 查询表并检索实体时，可能不需要所有属性，并可以避免不必要地传输数据（以帮助减少延迟和降低成本）。 可以使用服务器端投影来只传输需要的属性。 以下示例只检索查询选择的实体中的 `Email` 属性（连同 `PartitionKey`、`RowKey`、`Timestamp`和 `ETag`）。  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

请注意 `RowKey` 值如何可用，即使它未包含在要检索的属性列表中。  

### <a name="modify-entities"></a>修改实体
使用存储客户端库可以通过插入、删除和更新实体修改存储在表存储中的实体。 你可以使用 Egt 将多个插入、更新和删除操作一起批处理，以减少所需的往返次数并提高解决方案的性能。  

当存储客户端库运行 EGT 时引发的异常通常包含导致批处理失败的实体的索引。 如果正在调试使用 EGT 的代码，这非常有用。  

还应考虑设计如何影响客户端应用程序处理并发和更新操作的方式。  

#### <a name="managing-concurrency"></a>管理并发
默认情况下，表存储在单个实体级别实现针对 insert、merge 和 delete 操作的开放式并发检查，尽管客户端可以强制表存储绕过这些检查。 有关详细信息，请参阅[Microsoft Azure 存储中的 "管理并发](../storage/common/storage-concurrency.md)"。  

#### <a name="merge-or-replace"></a>合并或替换
`TableOperation` 类的 `Replace` 方法始终替换表存储中的完整实体。 如果在存储实体中存在某个属性时请求中未包含该属性，则请求将从存储实体中删除该属性。 除非想要从存储实体中显式删除某一属性，否则必须在请求中包含每个属性。  

当要更新实体时，可以使用 `TableOperation` 类的 `Merge` 方法减少发送到表存储的数据量。 `Merge` 方法会将存储实体中的所有属性替换为请求中包含的实体的属性值。 此方法将不包含在请求中的存储实体中的任何属性保留原样。 如果你的实体较大，并且只需要更新请求中的少量属性，这会很有用。  

> [!NOTE]
> 如果该实体不存在，则 `*Replace` 和 `Merge` 方法将失败。 作为替代方法，可以使用 `InsertOrReplace` 和 `InsertOrMerge` 方法来创建新实体（如果该实体不存在）。  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>使用异类实体类型
表存储是无*架构的*表存储。 这意味着单个表可以存储多种类型的实体，从而在设计中提供极大的灵活性。 以下示例说明了同时存储员工实体和部门实体的表：  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

每个实体仍然必须具有 `PartitionKey`、`RowKey`和 `Timestamp` 值，但可以具有任何一组属性。 而且，除非您选择将该信息存储在某个位置，否则不会指示实体的类型。 有两个用于标识实体类型的选项：  

* 将实体类型预置到 `RowKey` （也可能是 `PartitionKey`）。 例如，将 `EMPLOYEE_000123` 或 `DEPARTMENT_SALES` 为 `RowKey` 值。  
* 使用单独的属性来记录实体类型，如下表所示。  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>部门</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>年龄</th>
<th>电子邮件</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

如果有可能两个不同类型的实体可能具有相同的键值，则第一个选项（将实体类型预先计算到 `RowKey`）非常有用。 它还会在分区中将同一类型的实体分组在一起。  

本节中讨论的技术与有关[继承关系](#inheritance-relationships)的讨论特别相关。  

> [!NOTE]
> 请考虑在实体类型值中包含版本号，以使客户端应用程序能够演化 POCO 对象并处理不同版本。  
> 
> 

本部分的剩余部分介绍存储客户端库中便于处理同一表中的多个实体类型的一些功能。  

#### <a name="retrieve-heterogeneous-entity-types"></a>检索异类实体类型
如果使用的是存储客户端库，则可以使用三个选项来处理多个实体类型。  

如果知道用特定 `RowKey` 和 `PartitionKey` 值存储的实体的类型，则在检索实体时可以指定实体类型。 前面两个示例中介绍了这一点，`EmployeeEntity`这些示例[使用存储客户端库来运行点查询](#run-a-point-query-by-using-the-storage-client-library)，并[使用 LINQ 检索多个实体](#retrieve-multiple-entities-by-using-linq)。  

第二种方法是使用 `DynamicTableEntity` 类型（属性包），而不是具体的 POCO 实体类型。 此选项还可以提高性能，因为无需序列化实体并将其反序列化为 .NET 类型。 以下C#代码可能会从表中检索多个不同类型的实体，但会将所有实体作为 `DynamicTableEntity` 实例返回。 然后，它使用 `EntityType` 属性来确定每个实体的类型：  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

若要检索其他属性，必须对 `DynamicTableEntity` 类的 `Properties` 属性使用 `TryGetValue` 方法。  

第三种方法是使用 `DynamicTableEntity` 类型和 `EntityResolver` 实例进行组合。 使用此选项可以在同一查询中解析为多种 POCO 类型。 在此示例中，`EntityResolver` 委托使用 `EntityType` 属性来区分查询返回的两种实体类型。 `Resolve` 方法使用 `resolver` 委托将 `DynamicTableEntity` 实例解析为 `TableEntity` 实例。  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>修改异类实体类型
您无需知道实体的类型就可删除该实体，在插入实体时您始终知道该实体的类型。 但是，你可以使用 `DynamicTableEntity` 类型来更新实体，而无需知道其类型，也不使用 POCO 实体类。 下面的代码示例检索单个实体，并在更新该实体前检查 `EmployeeCount` 属性是否存在。  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>使用共享访问签名控制访问权限
你可以使用共享访问签名（SAS）令牌使客户端应用程序能够直接修改（和查询）表实体，而无需直接使用表存储进行身份验证。 通常情况下，在应用程序中使用 SAS 主要有以下三大优点：  

* 无需将存储帐户密钥分发到不安全的平台（如移动设备），即可允许该设备访问和修改表存储中的实体。  
* 你可以卸载 web 角色和辅助角色在管理实体时执行的一些工作。 你可以卸载到客户端设备（如最终用户计算机和移动设备）。  
* 你可以向客户端分配受约束和限时的权限集（如允许对特定资源进行只读访问）。  

有关对表存储使用 SAS 令牌的详细信息，请参阅[使用共享访问签名（SAS）](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。  

但是，仍必须生成向表存储中的实体授予客户端应用程序的 SAS 令牌。 在能够安全访问存储帐户密钥的环境中执行此操作。 通常，使用 Web 角色或辅助角色生成 SAS 令牌并将其传递给需要访问实体的客户端应用程序。 由于生成 SAS 令牌并将其传递到客户端仍有开销，应考虑如何最有效地减少此开销，尤其是在大容量方案中。  

可以生成一个 SAS 令牌，用于授予对表中实体子集的访问权限。 默认情况下，你为整个表创建 SAS 令牌。 但是，也可以指定 SAS 令牌授予对一系列 `PartitionKey` 值或 `PartitionKey` 和 `RowKey` 值的访问权限。 你可以选择为系统的各个用户生成 SAS 令牌，以便每个用户的 SAS 令牌仅允许其访问表存储中自己的实体。  

### <a name="asynchronous-and-parallel-operations"></a>异步和并行操作
假设将请求分布到多个分区，可以通过使用异步或并行查询来提高吞吐量和客户端响应能力。
例如，可以使用两个或更多个辅助角色实例并行访问表。 可以让单个辅助角色负责特定分区集，也可以只使用多个辅助角色实例，每个辅助角色实例都能访问表中的所有分区。  

在客户端实例中，可以通过以异步方式运行存储操作来提高吞吐量。 使用存储客户端库，可以轻松地编写异步查询和修改。 例如，可以从检索分区中的所有实体的同步方法开始，如以下C#代码所示：  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

可以轻松地修改此代码，使查询以异步方式运行，如下所示：  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

在此异步示例中，可以看到对同步版本进行了以下更改：  

* 方法签名现在包括 `async` 修饰符，并返回一个 `Task` 实例。  
* 此方法现在调用 `ExecuteSegmentedAsync` 方法，而不是调用 `ExecuteSegmented` 方法来检索结果。 方法使用 `await` 修饰符来以异步方式检索结果。  

客户端应用程序可以多次调用此方法，`department` 参数具有不同的值。 每个查询都在单独的线程上运行。  

`TableQuery` 类中没有 `Execute` 方法的异步版本，因为 `IEnumerable` 接口不支持异步枚举。  

此外，还可以用异步方式插入、更新和删除实体。 以下 C# 示例说明了一个简单的同步方法，该方法用于插入或替换员工实体：  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

可以轻松地修改此代码，使更新以异步方式运行，如下所示：  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

在此异步示例中，可以看到对同步版本进行了以下更改：  

* 方法签名现在包括 `async` 修饰符，并返回一个 `Task` 实例。  
* 方法现在调用 `ExecuteAsync` 方法，而不是调用 `Execute` 方法来更新实体。 方法使用 `await` 修饰符来以异步方式检索结果。  

客户端应用程序可以调用多个与此类似的异步方法，每个方法调用都在单独的线程上运行。  


[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

