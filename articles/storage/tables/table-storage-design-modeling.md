---
title: 在 Azure 存储表设计中对关系建模 | Microsoft Docs
description: 了解设计桌面存储解决方案时的建模流程。
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: ed4399e2d58924f89d4201979ffe1fb903a05d0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522622"
---
# <a name="modeling-relationships"></a>为关系建模
本文讨论可帮助设计 Azure 表存储解决方案的建模流程。

构建域模型是复杂系统设计中的一个关键步骤。 通常情况下，使用建模流程确定实体及实体之间的关系，并以此作为了解业务域及获取系统设计信息的方式。 本部分重点介绍如何将域模型中找到的一些常见关系类型转换为表服务的设计。 从逻辑数据模型映射到基于 NoSQL 的物理数据模型的过程与在设计关系数据库时使用的过程不同。 关系数据库设计通常采用数据规范化过程（针对最大限度减少冗余进行优化）和声明性查询功能（提取了数据库工作原理的实现方式）。  

## <a name="one-to-many-relationships"></a>一对多关系
业务域对象之间的一对多关系频繁发生：例如，一个部门拥有许多员工。 有多种方法可在表服务中实现一对多关系，每种方法都有与特定方案相关的利弊。  

请考虑这样一个示例：一个具有数万个部门和员工实体的大型跨国公司，其中每个部门都有许多员工，每个员工都与一个特定部门相关联。 一种方法是存储不同的部门和员工实体，如下所示：  


![存储不同的部门和员工实体](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

此示例展示了类型之间基于 **PartitionKey** 值的隐式一对多关系。 每个部门可以有许多员工。  

此示例还显示了同一个分区中的部门实体及其相关的员工实体。 可以选择对不同实体类型使用不同分区、表或甚至不同存储帐户。  

另一种方法是使数据非规范化，并只存储具有非规范化部门数据的员工实体，如下面的示例所示。 在此特定方案中，如果要求能够更改部门经理的详细信息，则此非规范化方法可能不是最佳方法，因为要这样做，需要更新部门中的每个员工。  

![员工实体](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

有关详细信息，请参阅本指南后面的[反规范化模式](table-storage-design-patterns.md#denormalization-pattern)。  

下表总结了上述每种方法对于存储具有一对多关系的员工和部门的优缺点。 还应考虑希望执行各种操作的频率：如果设计中包含的代价高昂的操作很少发生，则这可能是可以接受的。  

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
<li>可能需要针对一些客户端活动同时检索员工和部门实体。</li>
<li>存储操作发生在同一个分区。 在事务量很大时，这可能会产生热点。</li>
<li>不能使用 EGT 将员工移到新部门。</li>
</ul>
</td>
</tr>
<tr>
<td>不同的实体类型、不同的分区或表或存储帐户</td>
<td>
<ul>
<li>可以使用单个操作更新部门实体或员工实体。</li>
<li>在事务量很高时，这可能有助于将负载分布到多个分区。</li>
</ul>
</td>
<td>
<ul>
<li>可能需要针对一些客户端活动同时检索员工和部门实体。</li>
<li>在更新/插入/删除员工以及更新部门时，不能使用 EGT 来保持一致性。 例如，更新部门实体中的员工计数。</li>
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
<li>如果需要更新部门信息（这会需要更新部门中的所有员工），则保持一致性的开销会很大。</li>
</ul>
</td>
</tr>
</table>

如何在这些选项中进行选择，以及哪些优点和缺点最重要，取决于特定应用程序方案。 例如，修改部门实体的频率；所有员工查询是否都需要附加部门信息；有多接近对分区或存储帐户的伸缩性限制？  

## <a name="one-to-one-relationships"></a>一对一关系
域模型可能包括实体之间的一对一关系。 如果需要在表服务中实现一对一关系，还必须选择在需要检索两个相关的实体时如何链接这两个实体。 此链接可为隐式或显式，前者基于键值中的约定，后者在每个实体中按 **PartitionKey** and **RowKey** 值的形式存储指向其相关实体的链接。 若要了解是否应在同一分区存储相关实体，请参阅[一对多关系](#one-to-many-relationships)部分。  

还有可能引导在表服务中实现一对一关系的实现注意事项：  

* 处理大型实体，详细信息请参阅[大实体模式](table-storage-design-patterns.md#large-entities-pattern)。  
* 实施访问控制，详细信息请参阅[使用共享访问签名控制访问权限](#controlling-access-with-shared-access-signatures)。  

## <a name="join-in-the-client"></a>在客户端中联接
尽管可通过多种方式在表服务中为关系建模，但不能忘记使用表服务的两个主要原因是伸缩性和性能。 如果发现要建模的多个关系损害解决方案的性能和可伸缩性，则应问自己是否有必要在表设计中构建所有数据关系。 如果让客户端应用程序执行任何必要的联接，可能能够简化设计并改进解决方案的伸缩性和性能。  

例如，如果小型表包含不经常更改的数据，则可以检索一次此类数据并将其缓存到客户端。 这可以避免为检索相同数据而进行的重复往返操作。 在本指南中我们已查看过的示例中，小型组织中的部门集可能很小并且不经常更改，使它成为合适的数据候选项，客户端应用程序可以下载一次该数据并将其缓存为查找数据。  

## <a name="inheritance-relationships"></a>继承关系
如果客户端应用程序使用一组构成继承关系的的类来表示业务实体，则可以轻松地在表服务中持久保存这些实体。 例如，客户端应用程序中可能定义了以下一组类，其中 **Person** 是一个抽象类。

![抽象 Person 类](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

可以使用单个 Person 表通过如下代码中的实体在表服务中持久保存两个具体类的实例：  

![Person 表](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

若要深入了解如何在客户端代码中处理同张表中的多个实体类型，请参阅本指南后面的[处理异类实体类型](#working-with-heterogeneous-entity-types)部分。 此部分提供了如何在客户端代码中识别实体类型的示例。  


## <a name="next-steps"></a>后续步骤

- [表设计模式](table-storage-design-patterns.md)
- [针对查询的设计](table-storage-design-for-query.md)
- [对表数据进行加密](table-storage-design-encrypt-data.md)
- [针对数据修改的设计](table-storage-design-for-modification.md)