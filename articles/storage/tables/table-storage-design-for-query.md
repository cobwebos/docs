---
title: 针对查询设计 Azure 存储表 | Microsoft Docs
description: 针对 Azure 表存储中的查询设计表。
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 8b4ae066edc1c62c25762b5c6feebce1ecfff5a2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521534"
---
# <a name="design-for-querying"></a>针对查询的设计
表服务解决方案可能需要进行大量读取操作和/或大量写入操作。 本文重点介绍在将表服务设计为支持高效读取操作时需要牢记的事项。 通常，支持高效读取操作的设计对于写入操作来说也是高效的。 但是，在进行支持写入操作的设计时，还需牢记一些额外注意事项，具体请参阅文章[针对数据修改的设计](table-storage-design-for-modification.md)。

将表服务解决方案设计为能够高效读取数据的良好起点是问“我的应用程序将需要执行哪些查询来从表服务中检索它所需的数据？”  

> [!NOTE]
> 使用表服务时，预先将设计做正确很重要，因为以后进行更改会很难并且开销会很高。 例如，在关系数据库中，通常只需通过向现有数据库添加索引就可以解决性能问题：而这不是表服务的选项。  
> 
> 

本部分重点介绍在设计要查询的表时必须解决的关键问题。 本部分中涵盖的主题包括：

* [所选的 PartitionKey 和 RowKey 如何影响查询性能](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [选择适当的 PartitionKey](#choosing-an-appropriate-partitionkey)
* [针对表服务优化查询](#optimizing-queries-for-the-table-service)
* [对表服务中的数据进行排序](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>所选的 PartitionKey 和 RowKey 如何影响查询性能
下述示例假定表服务要使用以下结构存储员工实体（为清楚起见，大多数示例将省略 **Timestamp** 属性）：  

| *列名* | *数据类型* |
| --- | --- |
| **PartitionKey**（部门名称） |String |
| **RowKey**（员工 ID） |String |
| **名字** |String |
| **姓氏** |String |
| **年龄** |Integer |
| **电子邮件地址** |String |

有关一些直接影响查询设计的主要 Azure 表服务功能，请参阅文章：[Azure 表存储概述](table-storage-overview.md)。 这些功能产生了以下设计表服务查询的通用准则。 请注意，下述示例中所用的筛选器语法源自表服务 REST API，详细信息请参阅 [Query Entities](https://docs.microsoft.com/rest/api/storageservices/Query-Entities)（查询实体）。  

* ***点查询***是最高效的一种查找方式，可用于且建议用于大容量查找或要求最低延迟的查找。 通过指定 **PartitionKey** 和 **RowKey** 值，此类查询可极为高效地利用索引查找单个实体。 例如：$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')  
* 其次是***范围查询***，它使用 **PartitionKey**并筛选一系列 **RowKey** 值，从而返回多个实体。 **PartitionKey** 值确定特定分区，**RowKey** 值确定该分区中的实体子集。 例如：$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'  
* 然后是***分区扫描***，它使用 **PartitionKey** 并根据另一个非键属性进行筛选，可能会返回多个实体。 **PartitionKey** 值确定特定分区，而属性值将选择该分区中的实体子集。 例如：$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'  
* ***表扫描***不包括 **PartitionKey** 且效率极低，因为它会依次搜索构成表的所有分区，查找所有匹配的实体。 无论筛选器是否使用 **RowKey**它都将执行表扫描。 例如：$filter=LastName eq 'Jones'  
* 返回多个实体的查询将按 **PartitionKey** 和 **RowKey** 顺序返回实体。 若要避免对客户端中的实体重新排序，请选择定义最常见排序顺序的 **RowKey**。  

请注意，使用“**or**”指定基于 **RowKey** 值的筛选器将导致分区扫描，而不会视为范围查询。 因此，应避免使用筛选器 （如查询：$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')  

有关使用存储客户端库执行高效查询的客户端代码的示例，请参阅：  

* [使用存储客户端库执行点查询](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [使用 LINQ 检索多个实体](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [服务器端投影](table-storage-design-patterns.md#server-side-projection)  

有关可以处理存储在同一个表中的多个实体类型的客户端代码的示例，请参阅：  

* [处理异类实体类型](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>选择适当的 PartitionKey
所选的 **PartitionKey** 应权衡不同需求，决定是使用 EGT 确保一致性还是将实体分布到多个分区中来确保可伸缩的解决方案。  

一种极端做法是，可以将所有实体都存储在单个分区，但这可能会限制解决方案的伸缩性，并且会使表服务无法实现请求的负载均衡。 另一种极端做法是，可以每个分区存储一个实体，这样可以获得高伸缩性，并且使表服务能够对请求进行负载均衡，但无法使用实体组事务。  

理想的 **PartitionKey** 可实现高效查询，具有足够多的分区来确保解决方案可伸缩。 通常，会发现实体将具有合适的属性，可以将实体分布到足够多的分区。

> [!NOTE]
> 例如，在存储有关用户或员工的信息的系统中，UserID 可以是一个好的 PartitionKey。 可拥有多个使用某个给定 UserID 作为分区键的实体。 存储有关用户数据的每个实体分组到单个分区，因此通过实体组事务可访问这些实体，同时仍具有高度可伸缩性。
> 
> 

在选择与实体插入、更新和删除方式相关的 **PartitionKey** 时，还要注意一些额外的事项。 有关详细信息，请参阅[针对数据修改设计表](table-storage-design-for-modification.md)。  

## <a name="optimizing-queries-for-the-table-service"></a>针对表服务优化查询
表服务将使用单个聚集索引中的 **PartitionKey** 和 **RowKey** 值自动编制实体的索引，因此使点查询成为了最高效的方式。 但是，只有 **PartitionKey** 和 **RowKey** 的群集索引上具有索引。

许多设计必须满足要求，才能允许根据多个条件查找实体。 例如，根据电子邮件、员工 ID 或姓氏查找员工实体。 [表设计模式](table-storage-design-patterns.md)中所述的模式解决了这些类型的要求，并介绍了相关方式来处理表服务不提供辅助索引的问题：  

* [内分区的第二索引模式](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - 利用同一分区中的 **RowKey** 值存储每个实体的多个副本，实现快速、高效的查询，并借助不同的 **RowKey** 值替换排序顺序。  
* [内分区的第二索引模式](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 在单独分区/表格中利用不同 RowKey 值存储每个实体的多个副本，实现快速高效的查找，并借助 RowKey 值替换排序顺序。  
* [索引实体模式](table-storage-design-patterns.md#index-entities-pattern) - 维护索引实体，实现返回实体列表的高效搜索。  

## <a name="sorting-data-in-the-table-service"></a>对表服务中的数据进行排序
表服务依次按 **PartitionKey** 和 **RowKey** 以升序排序返回实体。 这些键是字符串值，以确保数字值正确排序，应将值转换为固定长度并使用零进行填充。 例如，如果用作 **RowKey** 的员工 ID 值是个整数值，则应将员工 ID **123** 转换为 **00000123**。  

许多应用程序要求使用按不同顺序排序的数据：例如，按名称或按加入日期对员工进行排序。 以下模式解决了如何替换实体的排序顺序的问题：  

* [内分区的第二索引模式](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - 利用同一分区中的 RowKey 值存储每个实体的多个副本，实现快速、高效的查询并借助不同的 RowKey 值替换排序顺序。  
* [内分区的第二索引模式](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 在单独分区/表格中利用不同 RowKey 值存储每个实体的多个副本，实现快速高效的查找，并借助 RowKey 值替换排序顺序。
* [日志结尾模式](table-storage-design-patterns.md#log-tail-pattern) - 利用按日期和时间倒序方式排序的 **RowKey** 值，检索最近添加到分区中的 *n* 个实体。  

## <a name="next-steps"></a>后续步骤

- [表设计模式](table-storage-design-patterns.md)
- [为关系建模](table-storage-design-modeling.md)
- [对表数据进行加密](table-storage-design-encrypt-data.md)
- [针对数据修改的设计](table-storage-design-for-modification.md)
