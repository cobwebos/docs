---
title: Cassandra API 在 Azure Cosmos DB 中的弹性缩放
description: 了解可用于缩放 Azure Cosmos DB Cassandra API 帐户及其优点/缺点的选项
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: e2967a6d12fba2d81dad9de31e7476a027a39d1c
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468824"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>弹性缩放 Azure Cosmos DB Cassandra API 帐户

有多种不同的选项可用于探索 Azure Cosmos DB API for Cassandra 的弹性特性。 若要了解如何在 Azure Cosmos DB 中有效地进行缩放，必须了解如何预配合适数量的请求单位（RU/s），以考虑系统的性能需求。 有关请求单位的详细信息，请参阅 "[请求单位](request-units.md)" 一文。 

![数据库操作消耗请求单位](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>处理速率限制（429错误）

如果客户端消耗的资源比你预配的数量多（RU/秒），Azure Cosmos DB 将返回 "速率限制（429）" 错误。 Azure Cosmos DB 中的 Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误。 

如果你的系统对延迟不敏感，则可能足以使用重试来处理吞吐量速率限制。 请参阅[java 代码示例](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)，了解如何使用 java 中的[Cassandra 重试策略](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)的[Azure Cosmos DB 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions)以透明方式处理速率限制。 你还可以使用[Spark 扩展](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)来处理速率限制。

## <a name="manage-scaling"></a>管理缩放

如果需要最大程度地减少延迟，则可以使用一系列选项来管理 Cassandra API 中的缩放和预配吞吐量（ru）：

* [使用 Azure 门户手动](#use-azure-portal)
* [使用控制平面功能以编程方式](#use-control-plane)
* [通过将 CQL 命令与特定 SDK 结合使用来以编程方式](#use-cql-queries)
* [使用 Autopilot 动态](#use-autopilot)

以下各节介绍了每种方法的优点和缺点。 然后，可以确定最佳策略来平衡系统的缩放需求、整体成本和解决方案的效率需求。

## <a id="use-azure-portal"></a>使用 Azure 门户

你可以使用 Azure 门户 Azure Cosmos DB Cassandra API 帐户中的资源进行缩放。 若要了解详细信息，请参阅有关在[容器和数据库上预配吞吐量](set-throughput.md)的文章。 本文介绍了在 Azure 门户中的[数据库](set-throughput.md#set-throughput-on-a-database)或[容器](set-throughput.md#set-throughput-on-a-container)级别设置吞吐量的相对优势。 这些文章中提到的 "数据库" 和 "容器" 术语分别映射到 Cassandra API 的 "密钥空间" 和 "表"。

此方法的优点是，它是管理数据库的吞吐量容量的直接全包式方式。 不过，缺点在于，在许多情况下，缩放方法可能需要某些级别的自动化，这两种方法都是经济高效且高性能的。 以下各节介绍相关的方案和方法。

## <a id="use-control-plane"></a>使用控制平面

Azure Cosmos DB 的 Cassandra API 提供使用各种控制平面功能以编程方式调整吞吐量的功能。 有关指导和示例，请参阅[Azure 资源管理器](manage-cassandra-with-resource-manager.md)、 [Powershell](powershell-samples-cassandra.md)和[Azure CLI](cli-samples-cassandra.md)文章。

此方法的优点是可以根据计时器自动扩展或缩减资源，以考虑高峰活动或低活动期间。 请参阅[此处](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的示例，了解如何使用 Azure Functions 和 Powershell 实现此目的。

此方法的一个缺点是，您无法实时响应不可预知的规模需求变化。 相反，你可能需要在客户端/SDK 级别或使用[Autopilot](provision-throughput-autopilot.md)的系统中利用应用程序上下文。

## <a id="use-cql-queries"></a>将 CQL 查询用于特定 SDK

可以通过对给定的数据库或容器执行[CQL ALTER 命令](cassandra-support.md#keyspace-and-table-options)，使用代码动态缩放系统。

此方法的优点在于，它使你能够以一种适合应用程序的自定义方式对缩放需求进行动态响应。 使用此方法，仍可利用标准 RU/秒的费用和费率。 如果你的系统的缩放需求主要是可预测的（大约70% 或更多），则将 SDK 与 CQL 配合使用可能是使用 Autopilot 进行自动缩放的更具成本效益的方法。 此方法的缺点是，在速率限制可能会增加延迟的情况下，实现重试会相当复杂。

## <a id="use-autopilot"></a>使用 Autopilot

除了手动或以编程方式预配吞吐量外，还可以在 Autopilot 模式下配置 Azure cosmos 容器。 Autopilot 模式将自动并立即扩展到指定 RU 范围内的消耗需求，而不会影响 Sla。 若要了解详细信息，请参阅在[autopilot 模式下创建 Azure Cosmos 容器和数据库](provision-throughput-autopilot.md)一文。

此方法的优点是，它是在系统中管理缩放需求的最简单方法。 它保证不**在配置的 RU 范围内**应用速率限制。 缺点是，如果您的系统中的缩放需求是可预测的，则与使用上面提到的订购控制平面或 SDK 级别方法相比，Autopilot 可能是处理缩放需求的成本较低的方式。

## <a name="next-steps"></a>后续步骤

- 通过使用 Java 应用程序开始[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)
