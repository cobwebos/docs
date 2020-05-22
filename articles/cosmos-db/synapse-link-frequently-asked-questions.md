---
title: 有关 Azure Synapse Link for Azure Cosmos DB 的常见问题
description: 获取有关 Synapse Link for Azure Cosmos DB 在计费、分析存储、安全性、分析存储生存时间等方面的常见问题的答案。
author: srchi
ms.author: srchi
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 36be05f72597ae9864eff812862589cafb1f5b0d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596466"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>有关 Azure Synapse Link for Azure Cosmos DB 的常见问题

Azure Synapse Link for Azure Cosmos DB 在 Azure Cosmos DB 和 Azure Synapse Analytics 之间建立紧密的集成。 它使客户能够通过与事务工作负荷的完全性能隔离且无需 ETL 管道对其操作数据运行准实时分析。 本文解答有关 Synapse Link for Azure Cosmos DB 的常见问题。

## <a name="general-faq"></a>常见问题解答

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>所有 Azure Cosmos DB API 是否都支持 Synapse Link？
在公共预览版中，仅 Azure Cosmos DB SQL (Core) API 支持 Synapse Link。 对 Cosmos DB 的用于 Mongo DB 的 API 和 Cassandra API 的支持当前处于封闭预览阶段。  若要请求对封闭预览版的访问权限，请联系 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com)。

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>多区域 Azure Cosmos 帐户是否支持 Synapse Link？
是的，对于多区域 Azure Cosmos 帐户，存储在分析存储中的数据也是全球分布的。 无论是单一写入区域（单主数据库）还是多个写入区域（也称为多主数据库），都可以从最近的本地区域提供从 Azure Synapse Analytics 执行的分析查询。

计划配置具有分析存储支持的多区域 Azure Cosmos 帐户时，建议在创建帐户时添加所有必要的区域。

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>我是否可以选择仅为特定区域启用 Synapse Link，而不为多区域帐户设置中的所有区域启用它？
在预览版中，为多区域帐户启用 Synapse Link 时，将在所有区域中创建分析存储。 基础数据针对事务存储中的吞吐量和事务一致性进行了优化。

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>我是否可以为 Azure Cosmos 帐户禁用 Synapse Link 功能？
目前，在帐户级别启用 Synapse Link 功能后，无法禁用它。  如果要关闭该功能，则必须删除并重新创建新的 Azure Cosmos 帐户。

我们知道，如果在帐户级别启用了 Synapse Link 功能，但没有启用了分析存储的容器，则不会产生任何计费影响。

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析存储

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>我是否可以在现有容器上启用分析存储？
目前，只能为新容器（在新帐户和现有帐户中）启用分析存储。

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>在容器创建期间启用分析存储后，我是否可以在 Azure Cosmos 容器上禁用分析存储？
目前，在容器创建期间启用分析存储后，无法在 Azure Cosmos 容器上禁用分析存储。

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>具有自动缩放预配吞吐量的 Azure Cosmos 容器是否支持分析存储？
是的，可以在具有自动缩放预配吞吐量的容器上启用分析存储。

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>是否会影响 Azure Cosmos DB 事务存储预配 RU？
Azure Cosmos DB 保证事务和分析工作负荷之间的性能隔离。 在容器上启用分析存储不会影响 Azure Cosmos DB 事务存储上预配的 RU/s。 分析存储的事务（读取和写入）和存储成本将单独收费。 有关更多详细信息，请参阅 [Azure Cosmos DB 分析存储的定价](analytical-store-introduction.md#analytical-store-pricing)。

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>对事务存储的删除和更新操作是否反映在分析存储中？ 
是的，对事务存储中的数据的删除和更新将反映在分析存储中。 可以在容器上配置生存时间 (TTL) 以包括历史数据，以便分析存储保留满足分析 TTL 条件的项的所有版本。 有关更多详细信息，请参阅[分析 TTL 概述](analytical-store-introduction.md#analytical-ttl)。

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>我是否可以从 Azure Synapse Analytics 以外的分析引擎连接到分析存储？
只能使用 Azure Synapse Analytics 提供的各种运行时来访问和运行针对分析存储的查询。 可以使用以下对象查询和分析分析存储：

* 完全支持 Scala、Python、SparkSQL 和 C# 的 Synapse Spark。 Synapse Spark 是数据工程和科学方案的核心
* 具有 T-SQL 语言且支持熟悉的 BI 工具（例如，Power BI Premium 等）的 SQL 无服务器

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>我是否可以从预配的 Synapse SQL 连接到分析存储？
目前，无法从预配的 Synapse SQL 访问分析存储。

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>我是否可以将 Synapse 中的查询聚合结果写回到分析存储？
分析存储是 Azure Cosmos 容器中的只读存储。 因此，无法直接将聚合结果写回到分析存储，但是可以将其写入另一个容器的 Azure Cosmos DB 事务存储，以后可将其用作服务层。

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>从事务存储到分析存储的自动同步复制是异步的还是同步的，以及有哪些延迟？ 
复制是异步的，预期的延迟当前大约为 2 分钟。

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>是否有事务存储中的项不会自动传播到分析存储的方案？
如果容器中的特定项违反了[用于分析的完善定义的架构](analytical-store-introduction.md#analytical-schema)，则不会将其包含在分析存储中。 如果你的方案被完善定义的用于分析的架构阻止，请向 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com)发送电子邮件以获取帮助。

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>我是否可以使用与对事务存储中的数据进行分区的不同方式，对分析存储中的数据进行分区？
基于事务存储中分片的水平分区，对分析存储中的数据进行分区。 目前，无法为分析存储选择其他分区策略。

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>我是否可以自定义或覆盖在分析存储中将事务数据转换为纵栏格式的方式？
目前，当数据项自动从事务存储传播到分析存储时，无法对其进行转换。 如果你的方案被此限制阻止，请向 [Azure Cosmos DB 团队](mailto:cosmosdbsynapselink@microsoft.com)发送电子邮件。

## <a name="analytical-time-to-live-ttl"></a>分析生存时间 (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>是否在容器和项级别支持分析数据的 TTl？
目前，分析数据的 TTl 只能在容器级别配置，不支持在项级别设置分析 TTL。

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>在 Azure Cosmos DB 容器上设置容器级别分析 TTL 后，我是否可以在以后将其更改为其他值？
是的，可以将分析 TTL 更新为任何有效的值。 有关分析 TTL 的更多详细信息，请参阅[分析 TTL](analytical-store-introduction.md#analytical-ttl) 一文。

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>某个项在事务存储中超过 TTL 后，我是否可以在分析存储中更新或删除该项？
所有事务更新和删除都将复制到分析存储，但如果已从事务存储中清除该项，则无法在分析存储中更新。 若要了解详细信息，请参阅[分析 TTL](analytical-store-introduction.md#analytical-ttl) 一文。

## <a name="billing"></a>计费

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Synapse Link for Azure Cosmos DB 的计费模型是什么？
[Azure Cosmos DB 分析存储](analytical-store-introduction.md)目前以公共预览版提供，在 2020 年 8 月 30 日之前，不会对分析存储收费。 Synapse Spark 和 Synapse SQL 通过 [Synapse 服务消耗](https://azure.microsoft.com/pricing/details/synapse-analytics/)进行计费。

## <a name="security"></a>安全性

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>对分析存储进行身份验证的方式有哪些？
对分析存储进行身份验证的方式与对事务存储进行身份验证的方式相同。 对于给定的数据库，可以使用主密钥或只读密钥进行身份验证。 可以利用 Synapse Studio 中的链接服务，以防止粘贴 Spark 笔记本中的 Azure Cosmos DB 密钥。 有权访问工作区的所有用户都可以访问此链接服务。

## <a name="synapse-run-times"></a>Synapse 运行时

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>当前支持的用于访问 Azure Cosmos DB 分析存储的 Synapse 运行时有哪些？

|Synapse 运行时 |当前支持 |
|---------|---------|
|Synapse Spark 池 | 读取、写入（通过事务存储）、表、临时视图 |
|Synapse SQL 无服务器    | 读取、写入（封闭预览）  |
|Synapse SQL 预配   |  不可用 |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Synapse Spark 表与 Synapse SQL 无服务器表同步的方式是否与 Azure Data Lake 同步的方式相同？
目前，此功能不可用。

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>我是否可以从分析存储执行 Spark 结构化流式处理？
目前，对 Azure Cosmos DB 的 Spark 结构化流式处理支持是使用事务存储的更改源功能实现的，并且尚不受分析存储支持。

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>在 Synapse Studio 中，如何识别是否连接到启用了分析存储的 Azure Cosmos DB 容器？
使用分析存储启用的 Azure Cosmos DB 容器具有以下图标：

![使用分析存储启用的 Azure Cosmos DB 容器 - 图标](./media/synapse-link-frequently-asked-questions/analytical-store-icon.png)

事务存储容器将使用以下图标表示：

![使用事务存储启用的 Azure Cosmos DB 容器 - 图标](./media/synapse-link-frequently-asked-questions/transactional-store-icon.png)
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>如何从 Synapse Studio 传递 Azure Cosmos DB 凭据？
目前，Azure Cosmos DB 凭据是由有权访问 Azure Cosmos DB 数据库的用户在创建链接服务时传递的。 有权访问工作区的其他用户可以访问该存储。

## <a name="next-steps"></a>后续步骤

* 了解 [Synapse Link 的优点](synapse-link.md#synapse-link-benefits)

* 了解 [Synapse Link 和 Azure Cosmos DB 之间的集成](synapse-link.md#synapse-link-integration)。
