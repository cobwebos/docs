---
title: 使用 Azure Cosmos DB 中的全局分布式分析的解决方案。
description: 了解可使用 Azure Cosmos DB 中的全局分布式分析生成的解决方案。
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681749"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用全局分布式分析的解决方案

本文介绍可使用 Azure Cosmos DB 中的全局分布式分析生成的解决方案。

## <a name="retail-and-consumer-goods"></a>零售和日用消费品

可以使用 Azure Cosmos DB 中的 Spark 支持提供实时建议和套餐。 可以通过实时个性化和产品建议帮助客户发现所需商品。

* 可以使用 Apache Spark 运行时提供的内置机器学习支持跨产品目录生成实时建议。

* 可以通过挖掘点击流数据、购买数据和客户数据，提供可以提高顾客终生价值的针对性建议。

* 可以利用 Azure Cosmos DB 的全球分布特性，在数毫秒内分析完大量分布在多个区域的产品数据。

* 可以快速获取分布在各地的用户和数据的见解。 可以在正确的时间为正确的用户提供正确的广告，提高促销转化率。

* 可以利用内置的 Spark 流式处理功能，将其与静态客户数据组合使用，以便丰富实时数据。 这样就可以根据客户当前的操作提供更个性化且更具针对性的实时广告。

下图显示了如何使用 Azure Cosmos DB Spark 支持来优化定价和促销：

![Azure Cosmos DB 的用于优化定价和促销的 Spark 支持](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


下图显示了如何在实时建议引擎中使用 Azure Cosmos DB Spark 支持：

![实时建议引擎中的 Azure Cosmos DB Spark 支持](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>制造和 IoT

可以通过 Azure Cosmos DB 的内置分析平台对来自全球数百万台设备的 IoT 数据进行实时分析。 可以进行各种现代创新，例如天气模式预测、预测分析、能源优化。

* 可以使用 Azure Cosmos DB 来挖掘数据（例如实时资产指标和天气因素），然后应用智能电网分析来优化现场连接设备的性能。 智能电网分析是控制运营成本、提高电网可靠性以及向使用者提供个性化能源服务的关键。

下图显示了如何使用 Azure Cosmos DB 的 Spark 支持，以便从 IoT 设备读取指标并应用智能电网分析：

![Azure Cosmos DB 的用于从 IoT 设备读取指标的 Spark 支持](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>预见性维护

* 维护资产（例如在从小型钻井平台到深水平台中使用的压缩机）是一项复杂的工作。 这些资产位于全球，生成数 PB 的数据。 可以使用 Azure Cosmos DB 构建端到端预测性数据管道，该管道使用 Spark 流式处理来处理大量的传感器遥测数据、存储资产部件以及传感器映射数据。

* 可以构建并部署机器学习模型，以便在资产故障发生之前预测它们，以及在故障发生之前发出维护工作订单。

下图显示了如何使用 Azure Cosmos DB 的 Spark 支持来构建预测性维护系统：

![Azure Cosmos DB 的 Spark 支持，用于构建预测性维护系统](./media/analytics-solution-architectures/predictive-maintenance-system.png)

下图显示了如何使用 Azure Cosmos DB 的 Spark 支持来构建实时车辆诊断系统：

![Azure Cosmos DB 的 Spark 支持，用于构建实时车辆诊断系统](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>游戏

* Azure Cosmos DB 内置了 Spark 支持，可以用来轻松地快速构建、缩放和部署高级分析和机器学习模型，尽可能构建最佳游戏体验。

* 你可以分析玩家、购买和行为数据，创建相关的个性化套餐，以便获得高转化率。

* 可以使用 Spark 机器学习分析游戏遥测数据并获得相关见解。 可以诊断和预防加载时间过长的问题以及游戏中的问题。

下图显示了如何在游戏分析中使用 Azure Cosmos DB 的 Spark 支持：

![Azure Cosmos DB 的 Spark 支持，用于游戏分析](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure Cosmos DB 的优势，请参阅[概述](introduction.md)文章。
* [Azure Cosmos DB API for MongoDB 入门](mongodb-introduction.md)
* [Azure Cosmos DB Cassandra API 入门](cassandra-introduction.md)
* [Azure Cosmos DB Gremlin API 入门](graph-introduction.md)
* [Azure Cosmos DB 表 API 入门](table-introduction.md)
