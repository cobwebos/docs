---
title: 在 Azure Cosmos DB 中弹性缩放 Cassandra API
description: 了解可用于缩放 Azure Cosmos DB Cassandra API 帐户的选项及其优缺点
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 8f84d611ecdf3fc0f86273498753e550315cd878
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586178"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>弹性缩放 Azure Cosmos DB Cassandra API 帐户

有多种选项可用于探索 Azure Cosmos DB Cassandra API 的弹性特性。 若要了解如何在 Azure Cosmos DB 中有效地进行缩放，必须了解如何预配合适数量的请求单位（RU/秒），以考虑系统的性能需求。 若要了解请求单位的详细信息，请参阅[请求单位](request-units.md)一文。 

对于 Cassandra API，可以使用 [.NET 和 Java SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api) 检索各个查询的请求单位费用。 这有助于确定需要在服务中预配的 RU/秒的量。

![数据库操作消耗请求单位](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>处理速率限制（429 错误）

如果客户端消耗的资源超过预配数量（RU/秒），Azure Cosmos DB 将返回速率限制 (429) 错误。 Azure Cosmos DB 中的 Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误。 

如果系统对延迟不敏感，那么通过使用重试来处理吞吐量速率限制就足够了。 请参阅 [Java 代码示例](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)，了解如何通过在 Java 中为 [ Cassandra 重试策略](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)使用 [Azure Cosmos DB 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions)，以便以透明方式处理速率限制。 还可以使用 [Spark 扩展](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)来处理速率限制。

## <a name="manage-scaling"></a>管理缩放

如果需要最大程度地减少延迟，可以使用一系列选项来管理 Cassandra API 中的缩放和预配吞吐量 (RU)：

* [使用 Azure 门户手动管理](#use-azure-portal)
* [使用控制平面功能以编程方式管理](#use-control-plane)
* [通过将 CQL 命令与特定 SDK 结合使用以编程方式管理](#use-cql-queries)
* [使用自动缩放动态管理](#use-autoscale)

下面的几节介绍了这些方法的优缺点。 然后，可以确定最佳策略来平衡系统的缩放需求、整体成本和解决方案的效率需求。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>使用 Azure 门户

可以使用 Azure 门户对 Azure Cosmos DB Cassandra API 帐户中的资源进行缩放。 若要了解详细信息，请参阅[在容器和数据库上预配吞吐量](set-throughput.md)一文。 本文介绍了在 Azure 门户中在[数据库](set-throughput.md#set-throughput-on-a-database)或[容器](set-throughput.md#set-throughput-on-a-container)级别设置吞吐量的相对权益。 这些文章中提到的“数据库”和“容器”术语分别映射到 Cassandra API 的“密钥空间”和“表”。

此方法的优点是，它是管理数据库的吞吐容量的直接全包式方式。 不过，缺点在于，在许多情况下，缩放方法可能需要一定级别的自动化，以便既具有成本效益又具有高性能。 以下各节介绍相关的场景和方法。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>使用控制面板

Azure Cosmos DB 的 Cassandra API 提供了使用各种控制平面功能以编程方式调整吞吐量的功能。 有关指导和示例，请参阅 [Azure 资源管理器](manage-cassandra-with-resource-manager.md)、[PowerShell](powershell-samples-cassandra.md) 和 [Azure CLI](cli-samples-cassandra.md) 文章。

此方法的优点是可以根据计时器自动扩展或缩减资源，以反映活动的高峰或低活动期。 请参阅[此处](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的示例，了解如何使用 Azure Functions 和 PowerShell 实现此目的。

此方法的一个缺点是，你无法实时响应不可预知的规模需求变化。 相反，你可能需要在客户端/SDK 级别利用系统中的应用程序上下文，或使用[自动缩放](provision-throughput-autoscale.md)。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>将 CQL 查询与特定 SDK 配合使用

可以通过对给定的数据库或容器执行 [CQL ALTER 命令](cassandra-support.md#keyspace-and-table-options)来使用代码动态缩放系统。

此方法的优点是，它使你能够以一种适合应用程序的自定义方式对缩放需求进行动态响应。 使用此方法，仍可利用标准 RU/秒的费用和速率。 如果系统的缩放需求大部分是可预测的（大约 70% 或更多），那么将 SDK 与 CQL 配合使用可能是一种比使用自动缩放更为经济高效的自动缩放方法。 此方法的缺点是，在速率限制可能会增加延迟的情况下，实现重试会相当复杂。

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>使用自动缩放预配吞吐量

除了标准（手动）或以编程方式预配吞吐量外，还可以在自动缩放预配的吞吐量中配置 Azure cosmos 容器。 自动缩放会自动立即缩放，以满足指定 RU 范围内的消耗需求，而不会影响 SLA。 若要了解详细信息，请参阅[在自动缩放中创建 Azure Cosmos 容器和数据库](provision-throughput-autoscale.md)一文。

此方法的优点是，它是在系统中管理缩放需求的最简单的方法。 它保证不会**在已配置的 RU 范围内**应用速率限制。 缺点是，如果系统中的缩放需求是可预测的，那么相比使用上面提到的定制控制平面或 SDK 级别方法，自动缩放在处理缩放需求方面可能并没有那么经济高效。

若要设置或更改使用 CQL 的自动缩放的最大吞吐量 (RU)，请使用以下内容（相应地替换密钥空间/表名称）：

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>后续步骤

- 通过使用 Java 应用程序开始[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)
