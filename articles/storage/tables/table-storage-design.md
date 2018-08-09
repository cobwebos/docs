---
title: 在 Azure 表存储中设计可伸缩的高性能表。 | Microsoft Docs
description: 在 Azure 表存储中设计可伸缩的高性能表。
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 783522997a752c4eac575316983bc6ef853c3f43
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526906"
---
# <a name="design-scalable-and-performant-tables"></a>设计可伸缩的高性能表

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

要设计可伸缩的高性能表，必须考虑性能、可伸缩性和成本等诸多因素。 如果你以前为关系数据库设计过架构，则应当很熟悉这些注意事项，尽管 Azure 表服务存储模型与关系模型之间有一些相似之处，但也存在重大差异。 这些差异通常会导致不同的设计，这些设计对于熟悉关系数据库的人来说可能看起来不直观或是错误的，但如果正在设计 Azure 表服务等 NoSQL 键/值存储，就会体会到这些设计是合理的。 许多设计差异将反映这样一个事实：表服务旨在支持云级别应用程序，这些应用程序可包含数十亿个实体（或关系数据库术语所称的行）的数据，或者用于必须支持高事务量的数据集。 因此，需要以不同方式考虑如何存储数据，并了解表服务的工作原理。 相对于使用关系数据库的解决方案而言，设计良好的 NoSQL 数据存储可以使解决方案以更低的成本更进一步扩展。 本指南中介绍这些主题。  

## <a name="about-the-azure-table-service"></a>关于 Azure 表服务
本部分重点介绍表服务的一些主要功能，这些功能尤其与设计性能和可伸缩性相关。 如果不熟悉 Azure 存储和表服务，请在阅读本文的其他部分之前，先阅读 [Microsoft Azure 存储简介](../../storage/common/storage-introduction.md)和[通过 .NET 实现 Azure 表存储入门](../../cosmos-db/table-storage-how-to-use-dotnet.md)。 尽管本指南的重点是介绍表服务，但它也包括对 Azure 队列和 Blob 服务的论述，并介绍了如何将它们与表服务一起使用。  

什么是表服务？ 从名称可以推测出，表服务将使用表格格式来存储数据。 在标准术语中，表的每一行表示一个实体，而列存储该实体的各种属性。 每个实体都有唯一地标识它的一对键，还有一个时间戳列，表服务使用该列来跟踪实体的最后更新时间。 时间戳是自动应用的，无法使用任意值手动覆盖它。 表服务使用此上次修改时间戳 (LMT) 来管理开放式并发。  

> [!NOTE]
> 表服务 REST API 操作还会返回它从 LMT 推导出的 **ETag** 值。 本文档互换使用术语 ETag 和 LMT，因为它们指的是同一基础数据。  
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
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
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
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>电子邮件</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>部门</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
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


到目前为止，此数据看起来非常类似于关系数据库中的表，主要区别是有必需的列，以及能够在同一个表中存储多种实体类型。 此外，**FirstName** 或 **Age** 等用户定义的每个属性还具有数据类型（如 integer 或 string），就像关系数据库中的列一样。 虽然与关系数据库中不同，表服务的架构灵活性质意味着每个实体的属性不需要具有相同的数据类型。 若要在单个属性中存储复杂数据类型，必须使用序列化格式（例如，JSON 或 XML）。 若要深入了解表服务（例如支持的数据类型、支持的日期范围、命名规则和大小限制），请参阅 [Understanding the Table Service Data Model](http://msdn.microsoft.com/library/azure/dd179338.aspx)（了解表服务数据模型）。

对 **PartitionKey** 和 **RowKey** 的选择是实现良好的表设计的基础。 表中存储的每个实体都必须具有唯一的 **PartitionKey** 和 **RowKey**。 与关系数据库表中的键一样，将为 **PartitionKey** 和 **RowKey** 值编制索引来创建聚集索引以便快速地进行查找。 但是，表服务不创建任何辅助索引，因此，**PartitionKey** 和 **RowKey** 是唯一具有索引的属性。 [表设计模式](table-storage-design-patterns.md)中介绍的一些模式展示了可以如何解决此明显的限制。  

一个表包含一个或多个分区，为优化解决方案，所做的很多设计决策都将围绕选取合适的 **PartitionKey** 和 **RowKey** 而展开。 一个解决方案可以仅包含单个表，该表包含组织为分区的所有实体，但通常一个解决方案具有多个表。 表可帮助你在逻辑上组织实体，帮助你使用访问控制列表管理对数据的访问，并且可以使用单个存储操作删除整个表。  

## <a name="table-partitions"></a>表分区
帐户名称、表名称和 **PartitionKey** 共同标识存储服务中表服务用于存储实体的分区。 作为实体寻址方案的一部分，分区定义事务的作用域（详见下方的[实体组事务](#entity-group-transactions)），并构成表服务缩放方式的基础。 有关分区的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)。  

在表服务中，单个节点为一个或多个完整的分区提供服务，并且该服务可通过对节点上的分区进行动态负载均衡来进行缩放。 如果某节点负载过轻，表服务将该节点针对的分区范围*拆分*为不同节点；流量下降时，该服务可将无操作的节点的分区范围*合并*为单个节点。  

若要深入了解表服务的内部细节（特别是服务管理分区的方式），请参阅文章 [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（Microsoft Azure 存储：具有高度一致性的高可用云存储服务）。  

## <a name="entity-group-transactions"></a>实体组事务
在表服务中，实体组事务 (EGT) 是唯一内置机制，用于对多个实体执行原子更新。 EGT 有时也被称为“批处理事务”。 EGT 只能对存储在同一分区中的实体（也就是说，在给定的表中共享同一分区键）执行操作。 因此，任何时候需要实现跨多个实体的原子事务行为时，必须确保那些实体位于同一分区中。 这通常是将多个实体类型保存在同一个表（和分区）中，而不是对不同实体类型使用多个表的原因。 单个 EGT 最多可应用于 100 个实体。  若要提交多个并发 EGT 进行处理，请务必确保不在 EGT 共用实体上操作这些 EGT，否则会造成延迟处理。

EGT 还引入了一个在设计时需要评估的潜在权衡。 那就是，使用更多分区会提高应用程序的可伸缩性，因为 Azure 可以有更多的机会在各个节点之间对请求进行负载均衡。 但是，使用更多分区可能会限制应用程序执行原子事务以及保持数据的强一致性的能力。 而且，在分区级别还有特定的可伸缩性目标，这些目标可能会限制预期单个节点可以实现的事务吞吐量。 有关 Azure 存储帐户和表服务的可伸缩性目标的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)。   

## <a name="capacity-considerations"></a>容量注意事项
下表介绍了设计表服务解决方案时需要注意的一些关键值：  

| Azure 存储帐户的总容量 | 500 TB |
| --- | --- |
| Azure 存储帐户中表的个数 |仅受存储帐户的容量限制 |
| 表中的分区个数 |仅受存储帐户的容量限制 |
| 分区中实体的个数 |仅受存储帐户的容量限制 |
| 单个实体的大小 |最大 1 MB，最多 255 个属性（包括 **PartitionKey**、**RowKey** 和 **Timestamp**） |
| **PartitionKey** 的大小 |大小最大为 1 KB 的字符串 |
| **RowKey** 的大小 |大小最大为 1 KB 的字符串 |
| 实体组事务的大小 |一个事务最多可包含 100 个实体，并且负载大小必须小于 4 MB。 EGT 只能更新一次实体。 |

有关详细信息，请参阅 [Understanding the Table Service Data Model](http://msdn.microsoft.com/library/azure/dd179338.aspx)（了解表服务数据模型）。  

## <a name="cost-considerations"></a>成本注意事项
表存储的价格相对便宜，但在评估任何表服务解决方案时，应同时针对容量使用情况和事务数量进行成本估算。 但是，在许多情况下，为提高解决方案的性能或可伸缩性，存储非规范化或重复的数据是一种有效方法。 有关定价的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。  

## <a name="next-steps"></a>后续步骤

- [表设计模式](table-storage-design-patterns.md)
- [为关系建模](table-storage-design-modeling.md)
- [针对查询进行设计](table-storage-design-for-query.md)
- [对表数据进行加密](table-storage-design-encrypt-data.md)
- [针对数据修改进行设计](table-storage-design-for-modification.md)