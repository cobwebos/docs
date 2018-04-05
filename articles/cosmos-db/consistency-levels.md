---
title: Azure Cosmos DB 中的一致性级别 | Microsoft Docs
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
keywords: 最终一致性, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: ''
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b0e46eb001e0b100ad1e181b02c18cfe67648f9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的可优化数据一致性级别
Azure Cosmos DB 是从无到有开发出来的，其设计考虑到了适合每个数据模型的全局分发。 它旨在提供可预测的低延迟保证以及多个完善定义的宽松一致性模型。 Azure Cosmos DB 当前提供 5 种一致性级别：非常、有限过期、会话、一致前缀和最终级别。 有限过期、会话、一致性前缀和最终级别称为“宽松一致性模型”，因为它们提供的一致性比非常一致性更差，后者是可用的最高一致性模型。 

除了分布式数据库提供的**非常一致性**和**最终一致性**模型以外，Azure Cosmos DB 还提供三个谨慎代码化和操作化的一致性模型：**有限过期**、**会话**和**一致性前缀**。 其中每个一致性级别的有效性已根据真实用例进行验证。 总而言之，这五个一致性级别可让你在一致性、可用性和延迟之间做出合理的取舍。 

在下面的视频中，Azure Cosmos DB 项目经理 Andrew Liu 演示了统包全球分布功能。

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>分布式数据库和一致性
商用分布式数据库分为两类：根本不提供妥善定义的可证明一致性选择的数据库，以及提供两种极端可编程性选择（非常一致性与最终一致性）的数据库。 

前者使应用程序开发人员为复制协议的细节所累，需要他们在一致性、可用性、延迟和吞吐量之间做出困难的权衡。 后者则迫使用户选择两个极端之一。 尽管有了 50 个以上的一致性模型的大量研究和建议，分布式数据库社区尚未能够将非常一致性和最终一致性之外的一致性级别商业化。 开发人员使用 Cosmos DB 可以沿一致性系列在五个妥善定义的一致性模型（非常、有限过期、[会话](http://dl.acm.org/citation.cfm?id=383631)、一致前缀和最终）之间进行选择。 

![Azure Cosmos DB 提供多个妥善定义的（宽松）一致性模型供选择](./media/consistency-levels/five-consistency-levels.png)

下表说明了每个一致性级别提供的特定保证。
 
**一致性级别和保证**

| 一致性级别 | 保证 |
| --- | --- |
| 非常 | 可线性化。 保证读取操作返回项的最新版本。|
| 有限过期 | 一致前缀。 读取操作落后写入操作 k 个前缀或 t 时间间隔 |
| 会话   | 一致前缀。 单调读取、单调写入、读取写入、写入跟随读取 |
| 一致前缀 | 返回的更新是全部更新的某些前缀，不带间隔 |
| 最终  | 失序读取 |

可以在 Cosmos DB 帐户上配置默认一致性级别（并且可在以后对特定读取请求重写一致性）。 在内部，默认一致性级别适用于可能跨区域的分区集内的数据。 大约 73% 的 Azure Cosmos DB 租户使用会话一致性，20% 的偏好有限过期。 大约 3% 的 Azure Cosmos DB 客户一开始会试用各种一致性级别，然后才会选定适合其应用程序的特定一致性。 只有 2% 的 Azure Cosmos DB 租户会请求重写一致性级别。 

在 Cosmos DB 中，读取在会话中提供，一致性前缀和最终一致性在开销上是用于非常一致性或有限过期一致性的读取的一半。 Cosmos DB 提供行业领先且涵盖范围广泛的 SLA，包括一致性保证，以及可用性、吞吐量和延迟保证。 Azure Cosmos DB 采用[可线性化检查器](http://dl.acm.org/citation.cfm?id=1806634)来持续处理服务遥测并公开报告任何一致性违规。 对于有限过期，Azure Cosmos DB会监视和报告任何 k 边界和 t 边界违规。 对于所有五种松散的一致性级别，Azure Cosmos DB 还会直接报告[概率限制的有限过期指标](http://dl.acm.org/citation.cfm?id=2212359)。  

## <a name="service-level-agreements"></a>服务级别协议

Azure Cosmos 数据库提供 99.99% 的全面 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)，保证吞吐量、一致性、可用性和延迟 Azure Cosmos DB 数据库范围限定为单个 Azure 区域配置与任意五个一致性的帐户级别或跨越多个 Azure 区域，使用的四个宽松一致性级别配置的数据库帐户。 此外，不管选择哪个一致性级别，Azure Cosmos DB 都提供跨两个或更多个 Azure 区域的数据库帐户的读取可用性为 99.999% 的 SLA。

## <a name="scope-of-consistency"></a>一致性的范围
一致性的粒度归并为单个用户请求。 写入请求可能对应于插入、替换、upsert 或删除事务。 与写入操作一样，读取/查询事务也归并为单个用户请求。 用户可能需要在跨越多个分区的大型结果集中分页，但每个读取事务归并为单个页面，并从单个分区内部进行。

## <a name="consistency-levels"></a>一致性级别
可以配置数据库帐户的默认一致性级别，将其应用于 Cosmos DB 帐户下的所有集合（和数据库）。 所有对用户定义的资源发出的读取和查询，默认都使用数据库帐户上指定的默认一致性级别。 可以放松在每个支持的 API 中使用的特定读取/查询请求的一致性级别。 如本部分所述，Azure Cosmos DB 复制协议支持五种类型的一致性级别，这些级别可在特定的一致性保证与性能之间提供明确的折衷。

<a id="strong"></a>
**强**： 

* 非常一致性提供[可线化](https://aphyr.com/posts/313-strong-consistency-models)保证，即保证读取操作返回项的最新版本。 
* 非常一致性保证写入只有在副本的多数仲裁一直提交的情况下才可见。 写入要么由主要副本和辅助副本仲裁一直同步提交，要么被中止。 读取始终由多数读取仲裁确认；客户端绝不会看到未提交或不完整的写入，而且始终保证读取最新确认的写入。 
* 配置为使用非常一致性的 Azure Cosmos DB 帐户不能将多个 Azure 区域与其 Azure Cosmos DB 帐户相关联。  
* 具有非常一致性的读取操作的开销（从消耗的[请求单位数](request-units.md)来讲）高于会话一致性和最终一致性，但与受限停滞一致性相同。

<a id="bounded-staleness"></a>
**有限过期性**： 

* 有限过期一致性保证读取操作滞后于写入操作最多 *K* 个项版本或前缀或 *t* 时间间隔。 
* 因此，在选择有限过期时，可以通过两种方式来配置“过期”：项的版本 *K* 的数目，读取以这种方式落后于写入，以及时间间隔 *t* 
* 有限过期提供全局整体顺序，但在“过期窗口”中除外。 “过期窗口”内部和外部的区域中提供单调读取保证。 
* 与会话、一致性前缀或最终一致性相比，有限过期一致性提供更强的一致性保证。 对于全球分布式应用程序，如果想要获得非常一致性，同时希望获得 99.99% 的可用性和低延迟，则我们建议使用受限停滞。   
* 配置了有限过期一致性的 Azure Cosmos DB 帐户可将任意数量的 Azure 区域与其 Azure Cosmos DB 帐户相关联。 
* 具有受限停滞一致性的读取操作的开销（从消耗的 RU 来讲）高于会话一致性和最终一致性，但与非常一致性相同。

<a id="session"></a>
**会话**： 

* 与非常一致性和受限停滞一致性级别所提供的全局一致性模型不同，会话一致性归并为客户端会话。 
* 对于涉及到设备或用户会话的所有方案，会话一致性非常合适，因为它提供单调读取、单调写入和读取自己的写入 (RYW) 保证。 
* 会话一致性为会话提供可预测的一致性和最大的读取吞吐量，同时提供最低延迟的写入和读取。 
* 配置了会话一致性的 Azure Cosmos DB 帐户可将任意数量的 Azure 区域与其 Azure Cosmos DB 帐户相关联。 
* 具有会话一致性级别的读取操作的开销（从消耗的 RU 来讲）小于非常一致性和有限过期一致性，但大于最终一致性。

<a id="consistent-prefix"></a>
**一致前缀**： 

* 一致前缀保证在没有再收到任何写入的情况下，组中的副本最终只剩一个。 
* 一致前缀保证读取永远不会看到无序写入。 如果按 `A, B, C` 顺序执行写入，则客户端会看到 `A`、`A,B` 或 `A,B,C`，但不会看到 `A,C` 或 `B,A,C` 这样的混乱顺序。
* 配置了“一致前缀”一致性的 Azure Cosmos DB 帐户可将任意数量的 Azure 区域与其 Azure Cosmos DB 帐户相关联。 

<a id="eventual"></a>
**最终**： 

* 最终一致性保证在没有再收到任何写入的情况下，组中的副本最终只剩一个。 
* 最终一致性是最弱的一致性形式，客户端可能会获取比之前看到的值还要旧的值。
* 最终一致性提供最差的读取一致性，但是读取和写入的延迟均为最低。
* 配置了“最终”一致性的 Azure Cosmos DB 帐户可将任意数量的 Azure 区域与其 Azure Cosmos DB 帐户相关联。 
* 具有最终一致性级别的读取操作的开销（从消耗的 RU 来讲）在所有 Azure Cosmos DB 一致性级别中是最低的。

## <a name="configuring-the-default-consistency-level"></a>配置默认的一致性级别
1. 在 [Azure 门户](https://portal.azure.com/)的跳转栏中，单击“Azure Cosmos DB”。
2. 在“Azure Cosmos DB”页中，选择要修改的数据库帐户。
3. 在帐户页中，单击“默认一致性”。
4. 在“默认一致性”页中，选择新的一致性级别并单击“保存”。
   
    ![屏幕截图：突出显示“设置”图标和默认一致性条目](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>查询的一致性级别
默认情况下，对于用户定义的资源，查询的一致性级别与读取的一致性级别相同。 默认情况下，每次在 Cosmos DB 容器中插入、替换或删除项时同步更新索引。 这个行为让查询能够使用与点读取相同的一致性级别。 虽然 Azure Cosmos DB 针对写入进行了优化，且支持持续大量写入，以及同步索引维护和提供一致的查询服务，但也可以配置某些集合，使其索引延迟更新。 延迟索引编制可大大提高写入性能，非常适合工作负荷主要具有大量读取操作的批量引入方案。  

| 索引模式 | 读取 | 查询 |
| --- | --- | --- |
| 一致（默认值） |从非常、有限过期、会话、一致前缀或最终一致性级别中选择 |从非常、受限停滞、会话或最终一致性级别中选择 |
| 延迟 |从非常、有限过期、会话、一致前缀或最终一致性级别中选择 |最终 |
| 无 |从非常、有限过期、会话、一致前缀或最终一致性级别中选择 |不适用 |

与读取请求一样，可以在每个 API 中降低特定查询请求的一致性级别。

## <a name="consistency-levels-for-the-mongodb-api"></a>MongoDB API 的一致性级别

Azure Cosmos DB 当前实现了 MongoDB 3.4 版，其中具有两个一致性设置：强一致性和最终一致性。 由于 Azure Cosmos DB 是多 api，因此一致性设置在帐户级别适用，并由每个 API 控制一致性的实施。  在 MongoDB 3.6 以前，没有会话一致性的概念，因此如果将 MongoDB API 帐户设置为使用会话一致性，则使用 MongoDB API 时，该一致性会降级为最终一致性。 如果需要向某个 MongoDB API 帐户提供“读取自己的写入”保证，该帐户的默认一致性级别应设置为“强”或“有限过期”。

## <a name="next-steps"></a>后续步骤
如果想详细了解一致性级别和平衡方案，建议参阅下列资源：

* [Doug Terry 借助棒球阐释复制数据一致性（视频）](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Doug Terry 借助棒球阐释复制数据一致性（白皮书）](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [弱一致性重复数据的会话保证](http://dl.acm.org/citation.cfm?id=383631)
* [现代分布式数据库系统设计中的一致性平衡方案：CAP 只是冰山一角](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)（实用部分仲裁的概率有限过期性 (PBS)）
* [最终一致性 - 再探](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [The Load, Capacity, and Availability of Quorum Systems, SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)（仲裁系统的负载、容量和可用性, SIAM Journal on Computing）
* [Line-up: a complete and automatic linearizability checker, Proceedings of the 2010 ACM SIGPLAN conference on Programming language design and implementation](http://dl.acm.org/citation.cfm?id=1806634)（Line-up：完整而自动的可线性化检查器, 2010 ACM SIGPLAN 编程语言设计和实现大会会议记录）
* [Probabilistic bounded staleness (PBS) for practical partial quorums](http://dl.acm.org/citation.cfm?id=2212359)（实用部分仲裁的概率有限过期性 (PBS)）
