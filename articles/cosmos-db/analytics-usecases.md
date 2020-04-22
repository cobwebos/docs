---
title: 将内置分析与 Azure Cosmos DB 配合使用的用例。
description: 了解如何在不同用例中将内置分析与 Azure Cosmos DB 配合使用。
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: adf1db2d737de7004b5ab71c8e0e44d61d1da8c9
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768513"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>将内置分析与 Azure Cosmos DB 配合使用的用例

将 Azure Cosmos DB 中的内置分析与 Apache Spark 配合使用可以实现以下用例：

## <a name="htap-scenarios"></a>HTAP 方案

Azure Cosmos DB 中的内置分析可用于：

* 在事务处理期间检测欺诈行为。
* 在购物者浏览电子商务店铺时向他们提供建议。
* 提醒操作员关键的生产设备部件即将发生故障。
* 通过直接在操作数据中嵌入实时分析来创建快速可操作的见解。

Azure Cosmos DB 使用本机内置的 Apache Spark 来支持混合事务/分析处理 (HTAP) 方案。 Azure Cosmos DB 消除了传统系统存在的操作与分析隔离。

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>全球分布式数据湖，无需任何 ETL

Azure Cosmos DB 具有本机内置的 Apache Spark，提供了一种快速、简单且可扩展的方式来构建提供单个系统映像的全球分布式数据湖。 全球分布的多模型数据无需投资昂贵的 ETL 管道并按需扩展，从而彻底改变了数据团队分析其数据集的方式。

## <a name="time-series-analytics-over-historic-data"></a>基于历史数据的时序分析

在某些情况下，你可能需要根据过去完成的事件中特定时间点的数据来解答问题。 例如，要获取特定日期的 CRM 活动状态计数。 如果你在一周前运行了报告，则状态计数是根据该时间点的每个活动的状态计算的。 如果今天运行相同的报告，将会得到处于今天的状态的活动计数，而这些状态可能与过去一周的状态不同，因为这些活动经历了从开始到结束的生命周期。 因此，您需要在案例生命周期的每个阶段报告快照。

使用 Azure Cosmos DB，用户可以实现“按时间顺序查看”的概念，可以追溯的方式针对数据查询和运行分析，并询问在特定的历史时间点查看数据的方式。 这意味着，用户可以轻松查看数据的当前视图和历史视图，并对其运行分析。

## <a name="globally-distributed-machine-learning-and-ai"></a>全球分布式机器学习和 AI

随着企业应对快速增长的数据量和不断增长的各种数据类型和格式，在全球 PB 范围内获得更深入、更准确的见解的能力几乎是不可能的。 Azure Cosmos DB 具有本机内置的 Apache Spark，提供全球分布式分析平台，提供广泛的机器学习算法库。 您可以使用交互式 Jupyter 笔记本来构建和训练模型以及群集管理功能。 这些功能使您能够按需预配高度调谐和自动弹性 Spark 群集。

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>对多模型全球分布式数据的深入了解

随着数据量和复杂性的不断增大，深度学习是提供大数据预测分析解决方案的理想方式。 使用深度学习，企业可以运用强大的非结构化和半结构化数据来实现利用 AI、自然语言处理等技术的用例。

## <a name="reporting-integrating-with-power-apps-power-bi"></a>报告（与 Power Apps、Power BI 集成）

Power BI 提供交互式可视化和自助式商业智能功能，使最终用户能够自行创建报告和仪表板。 使用内置的 Spark 连接器可将 Power BI Desktop 连接到 Azure Cosmos DB 中的 Apache Spark 群集。 此连接器允许您使用直接查询在 Azure Cosmos DB 中卸载处理到 Apache Spark，当您有大量不想加载到 Power BI 的数据或要执行接近实时分析的数据时，这非常出色。

## <a name="iot-analytics-at-global-scale"></a>全球规模的物联网分析

增加网络传感器后生成的数据可为以往不透明的系统和流程带来前所未有的可见性。 关键是在这个信息洪流中寻找可操作的见解，而不管 IoT 设备在全球的分布位置如何。 Azure Cosmos DB 允许 IOT 公司在世界各地实时分析高速传感器和时间序列数据。 它可以让你充分利用互联世界的真正价值，提供更好的客户体验、运营效率和新的收入机会。

## <a name="stream-processing-and-event-analytics"></a>流处理和事件分析 

从日志文件到传感器数据，企业越来越需要处理数据流的能力。 这些数据以稳定流的形式传入，并且往往同时来自多个源。 尽管可以将这些数据流存储在磁盘上并以追溯方式对其进行分析，但有时，最好或者必须在数据抵达时对其进行处理或采取措施。 例如，可以实时处理与金融交易相关的数据流，以识别并拒绝可能的欺诈性交易。

## <a name="interactive-analytics"></a>交互式分析

除了运行预定义的查询来创建有关销售、生产力或股票价格的静态仪表板以外，还可以交互方式浏览数据。 交互式分析允许提出问题、查看结果、根据响应更改初始问题，或更深入地钻取结果。 Azure Cosmos DB 中的 Apache Spark 通过快速响应和做出调整来支持交互式查询。

## <a name="data-exploration-using-jupyter-notebooks"></a>使用朱派特笔记本进行数据探索

当您有一个新的数据集时，在深入了解正在运行的模型和测试之前，您需要检查数据。 换句话说，您需要执行探索性数据分析。 数据探索可以为多个决策提供信息。 例如，您可以查找详细信息，例如适合在数据上使用的方法、数据是否符合某些建模假设、是否应清理数据、重组等。通过使用 Azure Cosmos DB 的本机内置 Jupyter 笔记本和 Apache Spark，您可以对事务和分析数据进行快速有效的探索性数据分析。

## <a name="next-steps"></a>后续步骤

要开始使用这些用例，请访问以下文章：

* [Azure 宇宙 DB 中的内置犹太笔记本](cosmosdb-jupyter-notebooks.md)
* [如何为 Azure Cosmos 帐户启用笔记本](enable-notebooks.md)
* [创建笔记本以分析和可视化数据](create-notebook-visualize-data.md)