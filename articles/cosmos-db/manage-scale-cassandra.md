---
title: 在 Azure 宇宙 DB 中使用卡桑德拉 API 进行弹性扩展
description: 了解可用于缩放 Azure Cosmos DB Cassandra API 帐户的选项及其优点/缺点
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474673"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>弹性缩放 Azure 宇宙 DB Cassandra API 帐户

有多种选项可用于探索 Cassandra 的 Azure Cosmos DB API 的弹性特性。 要了解如何在 Azure Cosmos DB 中有效扩展，请务必了解如何预配适当数量的请求单位 （RU/s） 以考虑系统中的性能要求。 要了解有关请求单位详细信息，请参阅[请求单位](request-units.md)一文。 

对于 Cassandra API，您可以使用[.NET 和 Java SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api)检索单个查询的请求单元费用。 这有助于确定您需要在服务中预配的 RU/s 量。

![数据库操作消耗请求单位](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>处理速率限制（429 个错误）

如果客户端消耗的资源 （RU/s） 多于预配的金额，Azure Cosmos DB 将返回速率限制 （429） 错误。 Azure Cosmos DB 中的 Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误。 

如果系统对延迟不敏感，则使用重试来处理吞吐量速率限制可能就足够了。 有关如何使用 Java 中的[Cassandra 重试策略](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)的[Azure Cosmos DB 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions)，请参阅 Java[代码示例](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)，了解如何透明地处理速率限制。 您还可以使用[Spark 扩展](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)来处理速率限制。

## <a name="manage-scaling"></a>管理缩放

如果需要最小化延迟，在 Cassandra API 中存在用于管理扩展和预配吞吐量 （R） 的一系列选项：

* [通过使用 Azure 门户手动](#use-azure-portal)
* [使用控制平面功能以编程方式](#use-control-plane)
* [将 CQL 命令与特定 SDK 一起使用，以编程方式](#use-cql-queries)
* [使用自动驾驶仪动态](#use-autopilot)

以下各节说明每种方法的优点和缺点。 然后，您可以决定最佳策略，以平衡系统的扩展需求、解决方案的总体成本和效率需求。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>使用 Azure 门户

您可以使用 Azure 门户缩放 Azure Cosmos DB Cassandra API 帐户中的资源。 要了解更多信息，请参阅有关[在容器和数据库上预配吞吐量](set-throughput.md)的文章。 本文介绍了在 Azure 门户中设置[数据库](set-throughput.md#set-throughput-on-a-database)或[容器](set-throughput.md#set-throughput-on-a-container)级别的吞吐量的相对好处。 这些文章中提到的术语"数据库"和"容器"分别映射到 Cassandra API 的"键空间"和"表"。

此方法的优点是它是管理数据库上的吞吐量容量的一种简单交钥匙方法。 但是，缺点是在许多情况下，您的扩展方法可能需要某些级别的自动化才能既经济又高性能。 下一节将解释相关方案和方法。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>使用控制平面

Azure Cosmos DB 的 Cassandra API 提供了使用各种控制平面功能以编程方式调整吞吐量的功能。 有关指南和示例，请参阅[Azure 资源管理器](manage-cassandra-with-resource-manager.md)、[电源外壳](powershell-samples-cassandra.md)和[Azure CLI](cli-samples-cassandra.md)文章。

此方法的优点是，您可以基于计时器自动扩展或缩减资源，以考虑峰值活动或低活动周期。 请看[我们在此](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的示例，了解如何使用 Azure 函数和 Powershell 实现此目的。

此方法的缺点是，您无法实时响应不可预测的不断变化的规模需求。 相反，您可能需要利用系统中的应用程序上下文、客户端/SDK 级别或使用[自动驾驶仪](provision-throughput-autopilot.md)。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>使用带有特定 SDK 的 CQL 查询

您可以通过执行给定数据库或容器的[CQL ALTER 命令](cassandra-support.md#keyspace-and-table-options)，使用代码动态缩放系统。

此方法的优点是，它允许您以适合应用程序的自定义方式动态响应扩展需求。 使用这种方法，您仍然可以利用标准 RU/s 费用和费率。 如果您的系统规模需求大部分是可预测的（大约 70% 或更多），则使用带有 CQL 的 SDK 可能比使用自动驾驶pilot更具成本效益。 此方法的缺点是，实现重试可能相当复杂，而速率限制可能会增加延迟。

## <a name="use-autopilot"></a><a id="use-autopilot"></a>使用自动驾驶仪

除了手动或编程的方式预配吞吐量外，您还可以在自动驾驶模式下配置 Azure 宇宙容器。 自动驾驶模式将自动立即扩展到指定的 RU 范围内的消费需求，而不会损害 SL。 要了解更多信息，请参阅[在自动驾驶模式下创建 Azure Cosmos 容器和数据库](provision-throughput-autopilot.md)一文。

此方法的优点是，它是管理系统中缩放需求的最简单方法。 它保证不在**配置的 RU 范围内**应用速率限制。 缺点是，如果系统中的缩放需求是可预测的，则与使用上述定制控制平面或 SDK 级别方法相比，Autopilot 可能是处理缩放需求的成本效益较低的方法。

## <a name="next-steps"></a>后续步骤

- 通过使用 Java 应用程序开始[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)
