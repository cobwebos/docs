---
title: Azure Cosmos DB 中 Cassandra API 的弹性缩放
description: 了解可用于缩放 Azure Cosmos DB Cassandra API 帐户的选项及其优点/缺点
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 13d7e0bfd3c7061d9dec68a1d14ff2a5e2c05fcd
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791249"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>弹性缩放 Azure Cosmos DB Cassandra API 帐户

有多种不同的选项可以探索 Azure Cosmos DB API for Cassandra 的弹性。 若要了解如何在 Azure Cosmos DB 中有效进行缩放，必须知道如何根据系统中的性能需求预配适当数量的请求单位（RU/秒）。 若要详细了解请求单位，请参阅[请求单位](request-units.md)一文。 

对于 Cassandra API，可以使用 [.NET 和 Java SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api) 检索单个查询的请求单位费用。 这有助于确定你在服务中需要预配的 RU 数/秒。

![数据库操作消耗请求单位](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>处理速率限制（429 错误）

如果客户端消耗的资源（RU/秒）超过了预配的量，Azure Cosmos DB 将返回速率限制 (429) 错误。 Azure Cosmos DB 中的 Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误。 

如果系统对延迟不敏感，使用重试可能就足以应对吞吐量速率限制。 请参阅 [Java 代码示例](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)，了解如何通过 Java 使用用于 [Cassandra 重试策略](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)的 [Azure Cosmos DB 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions)，从而以透明方式处理速率限制。 还可以使用 [Spark 扩展](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)来处理速率限制。

## <a name="manage-scaling"></a>管理缩放

如果需要最大程度地降低延迟，可以在 Cassandra API 中使用多种选项来管理缩放和预配吞吐量 (RU)：

* [使用 Azure 门户手动管理](#use-azure-portal)
* [使用控制平面功能以编程方式管理](#use-control-plane)
* [结合使用 CQL 命令和特定的 SDK 以编程方式管理](#use-cql-queries)
* [使用自动缩放动态](#use-autoscale)

以下部分解释了每种方法的优点和缺点。 然后，可以确定最佳的策略，以便在系统的缩放需求、整体成本以及解决方案的效率需求之间做出平衡。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>使用 Azure 门户

可以使用 Azure 门户缩放 Azure Cosmos DB Cassandra API 帐户中的资源。 有关详细信息，请参阅有关[对容器和数据库预配吞吐量](set-throughput.md)的文章。 此文解释了通过 Azure 门户在[数据库](set-throughput.md#set-throughput-on-a-database)或[容器](set-throughput.md#set-throughput-on-a-container)级别设置吞吐量的相对优势。 这些文章中提到的术语“数据库”和“容器”分别对应于 Cassandra API 的“密钥空间”和“表”。

此方法的优点是能够以直截了当的统包方式管理数据库的吞吐量。 但缺点是，在许多情况下，缩放方法可能要求实现某些程度的自动化，它既要确保经济高效，同时又要具备高性能。 后续部分将介绍相关的方案和方法。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>使用控制平面

用于 Cassandra 的 Azure Cosmos DB API 提供使用各种控制平面功能以编程方式调整吞吐量的功能。 有关指导和示例，请参阅[Azure 资源管理器](manage-cassandra-with-resource-manager.md)、 [PowerShell](powershell-samples-cassandra.md)和[Azure CLI](cli-samples-cassandra.md)文章。

此方法的优点是可以在考虑活动高峰和活动低潮期的情况下，基于某个计时器将资源的扩展和缩减自动化。 请参阅[此处](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的示例，了解如何使用 Azure Functions 和 PowerShell 实现此目的。

此方法的缺点是，可能无法实时应对不可预测的且不断变化的规模需求。 相反，你可能需要在你的系统中、在客户端/SDK 级别或使用[自动缩放](provision-throughput-autoscale.md)时使用应用程序上下文。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>将 CQL 查询与特定的 SDK 配合使用

可以针对给定的数据库或容器执行 [CQL ALTER 命令](cassandra-support.md#keyspace-and-table-options)，通过代码动态缩放系统。

此方法的优点在于，能够以适合应用程序的自定义方式动态应对缩放需求。 使用此方法时，仍可享用标准的 RU/秒费用和费率。 如果你的系统的缩放需求主要是可预测的（大约70% 或更多），则将 SDK 与 CQL 配合使用可能是使用自动缩放比使用自动缩放更为经济高效的方法。 此方法的缺点是，实现重试可能会很复杂，同时，速率限制可能会增大延迟。

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>使用自动缩放预配吞吐量

除了标准（手动）或以编程方式预配吞吐量外，还可以配置自动缩放预配吞吐量的 Azure cosmos 容器。 自动缩放会自动并立即扩展到指定 RU 范围内的消耗需求，而不会影响 Sla。 若要了解详细信息，请参阅在[自动缩放中创建 Azure Cosmos 容器和数据库](provision-throughput-autoscale.md)一文。

此方法的优点是，它是在系统中管理缩放需求的最简单方法。 它保证不会在已配置的 RU 范围内  应用速率限制。 缺点是，如果您的系统中的缩放需求是可预测的，则自动缩放可能是比使用上面提到的订购控制平面或 SDK 级别方法更经济高效的方式来处理缩放需求。

## <a name="next-steps"></a>后续步骤

- 通过使用 Java 应用程序开始[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)
