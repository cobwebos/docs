---
title: 使用 Azure Cosmos DB 的内置分析的用例。
description: 了解如何在不同用例中将内置分析与 Azure Cosmos DB 一起使用。
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757081"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>用于内置分析的使用案例 Azure Cosmos DB

以下用例可以通过将内置分析与 Azure Cosmos DB 中的 Apache Spark 一起使用来实现：

## <a name="htap-scenarios"></a>HTAP 方案

Azure Cosmos DB 中的内置分析可用于：

* 在事务处理期间检测欺诈行为。
* 向购物者浏览电子商务商店时提供建议。
* 提醒操作员关键生产设备即将发生故障。
* 通过直接在操作数据上嵌入实时分析来创建快速、可操作的见解。

Azure Cosmos DB 使用本机内置 Apache Spark 支持混合事务/分析处理（HTAP）方案。 Azure Cosmos DB 消除了传统系统附带的操作和分析分离。

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>全球分布式 data lake，无需任何 ETL

使用本机内置 Apache Spark，Azure Cosmos DB 可提供一种快速、简单且可扩展的方式来构建提供单个系统映像的全球分布式 data lake。 全球分布的多模型数据无需投资昂贵的 ETL 管道，并按需缩放，revolutionizing 数据团队分析其数据集的方式。

## <a name="time-series-analytics-over-historic-data"></a>基于历史数据的时序分析

在某些情况下，你可能需要根据在过去完成的事件的特定时间点来回答问题。 例如，若要获取特定日期的 CRM 活动状态计数，则为。 如果每周运行一次报表，则状态的计数将为每个活动在该时间点的状态。 现在运行相同的报表将为你显示其状态为 "今天" 的活动的计数，这些活动可能在上周发生了变化，因为他们会经历从开放到关闭的生命周期。 因此，您需要在案例生命周期的每个阶段报告快照。

在传统的数据仓库方案中，由于数据仓库并未设计用于合并数据，并且数据仅提供了正在进行的操作的当前视图，因此不可能使用 snapshot 这一概念。 使用 Azure Cosmos DB，用户可以实现时间段概念，能够在数据回顾性地上查询和运行分析，并要求数据在历史记录中的特定时间点查看。 这意味着用户可以轻松地查看数据的当前视图和历史视图，并对其运行分析。

## <a name="globally-distributed-machine-learning-and-ai"></a>全球分布式机器学习和 AI

随着企业面临迅速增长的数据量，以及扩展各种数据类型和格式的数据，获得更深入、更准确的见解的能力会在全球范围内扩展到 pb 级。 使用本机内置 Apache Spark，Azure Cosmos DB 提供了一个全局分布式分析平台，它提供了大量的机器学习算法库。 您可以使用交互式 Jupyter 笔记本来构建和训练模型以及群集管理功能。 这些功能使你能够按需预配高度优化的自动弹性 Spark 群集。

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>深入了解多模型全局分布的数据

深度学习是提供大数据预测分析解决方案的理想方法，因为数据量和复杂性不断增长。 通过深度学习，企业可以利用非结构化和半结构化数据的强大功能来提供利用 AI、自然语言处理等技术的用例。

## <a name="reporting-integrating-with-power-apps-power-bi"></a>报告（与电源应用集成，Power BI）

Power BI 提供交互式可视化，提供自助服务商业智能功能，使最终用户能够自行创建报表和仪表板。 通过使用内置的 Spark 连接器，可以将 Power BI Desktop 连接到 Azure Cosmos DB 中 Apache Spark 的群集。 此连接器可让你使用直接查询将处理卸载到 Azure Cosmos DB 中的 Apache Spark，这非常适用于你要将大量数据加载到 Power BI，或者想要执行近乎实时分析。

## <a name="iot-analytics-at-global-scale"></a>全球范围的 IoT 分析

通过增加网络传感器生成的数据使对以前不透明的系统和过程具有前所未有的可见性。 关键是在此 torrent 信息中查找可操作的见解，而不考虑 IoT 设备在全球分布的位置。 Azure Cosmos DB 使 IOT 公司能够在世界各地实时分析高速传感器，并实时分析时序数据。 利用它，您可以充分利用互连世界的真正价值，提供更好的客户体验、运营效率和新的收入机会。

## <a name="stream-processing-and-event-analytics"></a>流处理和事件分析 

从日志文件到传感器数据，企业越来越需要处理数据的 "流"。 此数据从多个源同时进入稳定流。 虽然可以将这些数据流存储在磁盘上并对其进行分析回顾性地，但有时最好或重要的是处理数据，并对数据进行操作。 例如，可以实时处理与财务交易相关的数据流，以确定并拒绝可能的欺诈性交易。

## <a name="interactive-analytics"></a>交互式分析

除了运行预定义的查询来创建销售、生产力或股票价格的静态仪表板外，您可能还需要以交互方式浏览数据。 交互式分析允许您提出问题、查看结果、更改基于响应的初始问题或深入了解结果。 Azure Cosmos DB 中的 Apache Spark 通过快速响应和适应来支持交互式查询。

## <a name="data-exploration-using-jupyter-notebooks"></a>使用 Jupyter 笔记本进行数据浏览

如果有新的数据集，则在深入了解正在运行的模型和测试之前，需要检查数据。 换句话说，您需要执行探索数据分析。 数据浏览可以通知多种决定。 例如，您可以找到一些详细信息，例如适用于您的数据的方法、数据是否符合某些建模假设、是否应清除数据、是否进行重新构建等。通过使用 Azure Cosmos DB 的本机内置 Jupyter 笔记本和 Apache Spark，你可以对事务数据和分析数据进行快速、高效的探索数据分析。

## <a name="next-steps"></a>后续步骤

若要开始使用这些用例，请转到以下文章：

* [Azure Cosmos DB 中的内置 Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)
* [如何为 Azure Cosmos 帐户启用笔记本](enable-notebooks.md)
* [创建用于分析和可视化数据的笔记本](create-notebook-visualize-data.md)