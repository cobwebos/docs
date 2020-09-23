---
title: Azure Cosmos DB 分析存储（预览版）是什么？
description: 了解 Azure Cosmos DB 事务性（基于行）和分析（基于列）存储。 分析存储的优势、对大型工作负荷的性能响应以及自动将事务性存储中的数据同步到分析存储
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: rosouz
ms.openlocfilehash: 17dce45e73a5620db2201534126900d8e571ec45
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900279"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Azure Cosmos DB 分析存储（预览版）是什么？

> [!IMPORTANT]
> Azure Cosmos DB 分析存储目前处于预览状态。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 分析存储是一个完全隔离的列存储，用于针对 Azure Cosmos DB 中的操作数据启用大规模分析，而不会对事务工作负荷产生任何影响。  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>对操作数据进行大型分析面临的挑战

Azure Cosmos DB 容器中的多模型操作数据存储在已编索的基于行的“事务性存储”内部。 行存储格式旨在快速实现事务性读写（以毫秒级的响应时间）和操作查询。 如果数据集增长很大，则复杂的分析查询在预配存储为此格式的数据时的吞吐量方面可能会很大。 预配的吞吐量的高消耗反过来会影响你的实时应用程序和服务所使用的事务工作负荷的性能。

通常，若要分析大量的数据，则从 Azure Cosmos DB 的事务性存储中提取操作数据，并将其存储在单独的数据层中。 例如，采用适当的格式将数据存储在数据仓库或数据湖中。 之后将使用此数据进行大型分析，并使用计算引擎（如 Apache Spark 群集）分析它们。 从操作数据中分离分析存储和计算层会增加延迟，因为运行 ETL（提取、转换、加载）管道的频率会降低，以最大程度减少对事务性工作负荷的潜在影响。

相对于仅处理新引入的操作数据而言，处理操作数据更新时，ETL 管道也会变得复杂。 

## <a name="column-oriented-analytical-store"></a>面向列的分析存储

Azure Cosmos DB 分析存储解决了传统 ETL 管道所具有的复杂和延迟问题。 Azure Cosmos DB 分析存储可以自动将操作数据同步到单独的列存储。 列存储格式适用于按优化方式执行的大规模分析查询，从而提高了此类查询的延迟。

借助 Azure Synapse Link，现在可以直接从 Synapse Analytics 链接到 Azure Cosmos DB 分析存储，生成无 ETL HTAP 解决方案。 借助它，你可以以接近实时的速度对操作数据运行的大型分析。

## <a name="features-of-analytical-store"></a>分析存储的功能 

在 Azure Cosmos DB 容器中启用分析存储时，将基于容器中的操作数据在内部创建新的列存储。 此列存储将与该容器的面向行的事务性存储分开保存。 对操作数据的插入、更新和删除将自动同步到分析存储。 你无需更改源或 ETL，即可同步数据。

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>用于操作数据分析工作负荷的列存储

分析工作负荷通常涉及聚合和按顺序扫描选定字段。 通过按以列为主的顺序存储数据，分析存储可以将每个字段的值组合在一起序列化。 此格式减少了扫描或计算特定字段中统计信息所需的 IOPS。 它极大地改进了扫描大型数据集所需的查询响应时间。 

例如，操作表采用下面的格式时：

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="示例操作表" border="false":::

行存储会采用序列化格式将上面的数据按行保存到磁盘中。 此格式可以加快事务性读写和操作查询，如“返回产品 1 的相关信息”。 不过，随着数据集增大，如果你想要对数据运行复杂的分析查询，它的成本可能会很高。 例如，如果想要获取“‘设备’类别下的某个产品在不同业务单位和月份的销量趋势”，则需要运行复杂查询。 就预配的吞吐量而言，对此数据集执行大型扫描的成本可能会变得很高，这还可能会影响支持实时应用程序和服务的事务性工作负荷的性能。

分析存储是列存储，它更适用于此类查询，因为它会将相似的数据字段一起序列化，减少磁盘 IOPS。

下图说明了 Azure Cosmos DB 中的事务性行存储与分析列存储：

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Azure Cosmos DB 中的事务性行存储与分析列存储" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>分析工作负荷性能已分离

分析查询不会影响事务性工作负荷的性能，因为分析存储已与事务性存储分开。  分析存储无需分配单独的请求单位 (RU)。

### <a name="auto-sync"></a>自动同步

自动同步是指 Azure Cosmos DB 的完全托管功能，对操作数据执行的插入、更新、删除将以接近实时的速度（在 5 分钟内）自动从事务性存储同步到分析存储。

自动同步功能与分析存储一起提供了以下主要优势：

#### <a name="scalability--elasticity"></a>可伸缩性和弹性

通过使用水平分区，Azure Cosmos DB 事务性存储无需停机即可弹性缩放存储和吞吐量。 事务性存储中的水平分区为自动同步提供了可伸缩性和弹性，确保数据以接近实时的速度同步到分析存储。 无论事务性流量吞吐量如何数据同步都会执行（无论是 1000 个操作/秒还是 1 百万个操作/秒），并且它不会影响事务性存储的预配吞吐量。 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>自动处理架构更新

Azure Cosmos DB 事务性存储架构不可知，因此你能够迭代事务性应用程序，而无需处理架构或索引管理。 与此相反，Azure Cosmos DB 分析存储已架构化，以便优化分析查询性能。 利用自动同步功能，Azure Cosmos DB 管理从事务存储中的最新更新的架构推断。  它还管理现成分析存储中的架构表示形式，其中包括处理嵌套数据类型。

随着您的架构不断演变，随着时间的推移添加了新属性，分析存储会自动在事务存储中的所有历史架构中显示联合架构。

##### <a name="schema-constraints"></a>架构约束

以下约束适用于在启用分析存储以自动推断并正确表示架构的 Azure Cosmos DB 中的操作数据：

* 在架构中，最多可以有200个属性，最大嵌套深度为5。
  
  * 顶部具有201属性的项不满足此约束，因此不会在分析存储区中表示。
  * 在架构中具有五个以上嵌套级别的项也不满足此约束，因此不会在分析存储区中表示。 例如，以下项不满足要求：

     `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* 比较 case 区分时，属性名称应是唯一的。 例如，以下各项不满足此约束，因此不会在分析存储中表示：

  `{"Name": "fred"} {"name": "john"}` –以不区分大小写的方式进行比较时，"name" 和 "name" 是相同的。

##### <a name="schema-representation"></a>架构表示形式

在分析存储区中，架构表示形式有两种模式。 这些模式在列式表示形式、处理多态架构和简单的查询体验之间具有折衷：

* 定义完善的架构表示形式
* 完全保真架构表示形式

> [!NOTE]
> 对于 SQL (核心) API 帐户，启用分析存储时，分析存储中的默认架构表示形式是定义完善的。 对于 MongoDB 帐户 Azure Cosmos DB API，分析存储中的默认架构表示形式是一种完全保真架构表示形式。 如果你的方案需要不同于其中每个 Api 的默认产品的架构表示形式，请联系 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com) 以启用它。

**定义完善的架构表示形式**

定义完善的架构表示形式会创建一个简单的表格表示形式，该表示形式是事务性存储区中与架构无关的数据。 定义良好的架构表示形式具有以下注意事项：

* 属性在多个项中始终具有相同的类型。

  * 例如，`{"a":123} {"a": "str"}` 没有完善定义的架构，因为 `"a"` 有时是字符串，有时是数值。 在这种情况下，分析存储将的数据类型注册 `“a”` 为 `“a”` 容器生存期中第一次发生的项的数据类型。 如果项中 `“a”` 的数据类型与众不同，则不会将其包含在分析存储中。
  
    此条件不适用于 null 属性。 例如， `{"a":123} {"a":null}` 仍然定义良好。

* 数组类型必须包含单个重复类型。

  * 例如，不是 `{"a": ["str",12]}` 定义完善的架构，因为数组包含整数和字符串类型的组合。

> [!NOTE]
> 如果 Azure Cosmos DB 分析存储遵循定义良好的架构表示形式，并且上述规范违反了某些项，则这些项将不会包含在分析存储中。

**完全保真架构表示形式**

完全保真架构表示形式旨在处理架构不可知的操作数据中的各种多态架构。 在此架构表示形式中，即使未定义混合数据类型字段的明确定义的架构约束 (与不违反混合数据类型的数组) ，也不会从分析存储区中删除任何项。

这是通过基于属性中值的数据类型将操作数据的叶属性转换为具有不同列的分析存储区来实现的。 叶属性名称在分析存储架构中使用数据类型作为后缀进行扩展，以便它们可以无歧义地进行查询。

例如，让我们在事务存储中使用下面的示例文档：

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

`streetName`嵌套对象中的叶属性 `address` 将在分析存储架构中表示为列 `address.object.streetName.int32` 。 数据类型作为后缀添加到列中。 这样一来，如果将其他文档添加到 "叶" 属性的值 `streetNo` 为 "123" (注意，它是一个字符串) ，则分析存储的架构将自动演变，而不会改变以前写入的列的类型。 添加到分析存储中的新列， `address.object.streetName.string` 其中存储了此值 "123"。

**要映射到后缀的数据类型**

下面是所有属性数据类型及其在分析存储中的后缀表示形式的映射：

|原始数据类型  |Suffix  |示例  |
|---------|---------|---------|
| 双精度 |  "float64" |    24.99|
| 数组 | "array" |    ["a"，"b"]|
|二进制 | "binary" |0|
|Boolean    | "bool"   |True|
|Int32  | "int32"  |123|
|Int64  | "int64"  |255486129307|
|Null   | "null"   | null|
|字符串|    "string" | "ABC"|
|时间戳 |    "timestamp" |  Timestamp (0，0) |
|DateTime   |"date"    | ISODate ( "2020-08-21T07：43： 07.375 Z" ) |
|ObjectId   |"objectId"    | ObjectId ( "5f3f7b59330ec25c132623a2" ) |
|文档   |"object" |    {"a"： "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>以经济高效的方式将历史数据存档

数据分层指在存储基础结构之间分隔数据，以适用于不同的方案， 从而提高端到端数据堆栈的整体性能和经济高效性。 Azure Cosmos DB 借助分析存储，现在支持使用不同的数据布局将事务性存储中的数据自动分层到分析存储。 相对于事务性存储，分析存储在存储成本方面实现了优化，你可以将操作数据保留更长时间，来进行历史分析。

启用分析存储后，你可以根据事务性工作负荷的数据保留需求，配置“事务性存储生存时间(事务性 TTL)”属性，以便超过特定时间后即自动从事务性存储删除记录。 同样，你可以通过“分析存储生存时间(分析 TTL)”管理分析存储（独立于事务性存储）中保留的数据的生命周。 通过启用分析存储并配置 TTL 属性，你可以进行无缝分层，并定义两个存储的数据保留期。

### <a name="global-distribution"></a>全局分发

若你拥有全局分发的 Azure Cosmos DB 帐户，为容器启用分析存储后，它将适用于该帐户的所有区域。  对操作数据的任何更改均将全局复制到所有区域。 你可以高效地对 Azure Cosmos DB 中距离最近的区域的数据副本运行分析查询。

### <a name="security"></a>安全性

对分析存储进行身份验证的方式，与对给定数据库的事务性存储进行身份验证的方式相同。 可以使用主密钥或只读密钥进行身份验证。 可以利用 Synapse Studio 中的链接服务，以防止粘贴 Spark 笔记本中的 Azure Cosmos DB 密钥。 有权访问工作区的任何用户都可以访问此链接服务。

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>支持多个 Azure Synapse Analytics 运行时

分析存储已经过优化，无需依赖计算运行时即可为分析工作负荷提供可伸缩性、弹性和性能。 存储技术是自行管理，无需手动操作即可优化分析工作负荷。

通过将分析存储系统与分析计算系统分离，可以同时从 Azure Synapse Analytics 支持的不同分析运行时中查询 Azure Cosmos DB 分析存储中的数据。 目前，Synapse Analytics 支持 Apache Spark 和 SQL 无服务器使用 Azure Cosmos DB 分析存储。

> [!NOTE]
> 只能使用 Synapse Analytics 运行时读取分析存储。 可以将数据重写入事务性存储，将其作为服务层。

## <a name="pricing"></a><a id="analytical-store-pricing"></a> 定价

分析存储采用基于消耗的定价模型，计费项包括：

* 存储：分析存储每个月保留的数据量，包括分析 TTL 定义的历史数据。

* 分析写入操作：从事务性存储将操作数据更新以完全托管的方式同步到分析存储（自动同步）

* 分析读取操作：针对分析存储执行的、从 Synapse Analytics Spark 和 SQL 无服务器运行时间执行的读取操作。

> [!NOTE]
> Azure Cosmos DB 分析存储当前在公共预览版中免费提供，不收取任何费用。

分析存储定价与事务性存储定价模型不同。 分析存储中没有预配 RU 这一概念。 有关分析存储定价模型的完整详细信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

若要为在 Azure Cosmos DB 容器中启用分析存储进行高级别成本估算，可以使用 [Azure Cosmos DB 容量规划器](https://cosmos.azure.com/capacitycalculator/)，来获得分析存储和写入操作成本的估算值。 分析读取操作成本取决于分析工作负荷特性，但按照高级别估算，扫描分析存储中的 1 TB 的数据通常引发 130,000 个分析读取操作，产生的成本为 0.065 美元。

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> 分析生存时间 (TTL)

分析 TTL 表示应将容器的分析存储中的数据保留多久。 

如果已启用分析存储区，则操作数据的插入、更新、删除操作会自动从事务存储同步到分析存储，而不考虑事务 TTL 配置。 分析存储中操作数据的保留可以由容器级别的分析 TTL 值控制，如下所示：

使用 `AnalyticalStoreTimeToLiveInSeconds` 属性设置容器中的分析 TTL：

* 若将此值设置为“0”、此值缺失（或设置为 null）：将禁用分析存储，不会将任何数据从事务性存储复制到分析存储

* 若此值存在且设置为“-1”：无论事务性存储中数据的保留期是多久，分析存储都将保留所有历史数据。 此设置表示分析存储会无限期保留操作数据

* 若此值存在且设置为某个正数“n”：距在事务性存储中最后一次修改项“n”秒后，项将从分析存储中过期。 如果要在分析存储中将操作数据保留一段有限的时间，则可以利用此设置，而不考虑事务存储中数据的保留期

考虑的要点：

*   使用分析 TTL 值启用分析存储后，可以在以后将其更新为其他有效值。 
*   尽管可在容器或项级别设置事务性 TTL，但目前只能在容器级别设置分析 TTL。
*   将容器级别的分析 TTL 设置为大于或等于事务性 TTL，即可将操作数据存档在分析存储中更长时间。
*   可以通过设置 "分析 TTL = 事务 TTL"，将分析存储镜像到事务性存储区。

在容器上启用分析存储时：

* 在 Azure 门户中，分析 TTL 选项设置为默认值-1。 可以通过导航到数据资源管理器下的容器设置，将此值更改为 "n" 秒。 
 
* 通过 Azure SDK 或 PowerShell 或 CLI，可以通过将 "分析 TTL" 选项设置为 "-1" 或 "n" 来启用它。 

若要了解详细信息，请参阅[如何对容器配置分析 TTL](configure-synapse-link.md#create-analytical-ttl)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅下列文档：

* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)

* [开始使用 Azure Synapse Link for Azure Cosmos DB](configure-synapse-link.md)

* [有关 Synapse Link for Azure Cosmos DB 的常见问题](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Link for Azure Cosmos DB 用例](synapse-link-use-cases.md)
