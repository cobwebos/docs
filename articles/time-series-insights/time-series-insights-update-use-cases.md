---
title: Preview use cases - Azure Time Series Insights | Microsoft Docs
description: Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: seodec18
ms.openlocfilehash: 92d738542076b755a26e8cff2e7fb1aa0384cb22
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227729"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure 时序见解预览版用例

This article summarizes several common use cases for Azure Time Series Insights Preview. The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

Specifically, this article answers the following questions:

* 时序见解的常见用例有哪些？
* What are the benefits of using Time Series Insights for [data exploration and visual anomaly detection](#data-exploration-and-visual-anomaly-detection)?
* What are the benefits of using Time Series Insights for [operational analysis and process efficiency](#operational-analysis-and-driving-process-efficiency)?
* What are the benefits of using Time Series Insights for [advanced analytics](#advanced-analytics)?

An overview of these use scenarios is described in the following sections.

## <a name="introduction"></a>简介

Azure Time Series Insights is an end-to-end, platform-as-a-service offering. 它用于收集、处理、分析和查询高度情景化且优化了时序的 IoT 规模的数据。 时序见解是即席数据浏览和运营分析的理想选择。 Time Series Insights is a uniquely extensible, customized service offering that meets the broad needs of industrial IoT deployments.

## <a name="data-exploration-and-visual-anomaly-detection"></a>数据浏览和直观异常检测

即时浏览和分析数十亿事件，确定其中的异常，发现数据的隐藏趋势。 时序见解可为 IoT 和 DevOps 分析工作负荷提供近实时的性能。

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Most customers agree that the minimal amount of time required to gain insight is one of the standout features of Time Series Insights:

* 时序见解无需前期数据准备。 
* It works fast to connect you to billions of events in your Azure IoT Hub or Azure Event Hubs instances in minutes. 
* 连接后，可直观显示和分析数十亿事件，发现异常，发现数据的隐藏趋势。

时序见解直观且易于使用。 连一行代码也无需编写即可与数据交互。 There’s also no new language you're required to learn, although Time Series Insights provides a granular text-based querying language for advanced users who are familiar with SQL. 为初学者提供“选择+单击”式探索模式。

Customers can take advantage of the speed to diagnose asset-related issues quickly. They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. They also can identify areas to flag for further investigation as part of their data science initiatives. 

与时序见解中存储的数据进行交互主要有三种方式：

* 第一个也是最简单的方法是使用时序见解预览版资源管理器。 可以使用它在一个位置快速显示所有 IoT 数据。 It provides tools like the heat map to help you spot anomalies in your data. 它还提供透视视图。 使用它可以在一个仪表板中比较一个或多个时序见解环境中的多达四个视图。 通过仪表板，可以查看所有位置的时序数据。 了解[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)的详细信息。 要规划时序见解环境，请阅读[时序见解规划](./time-series-insights-update-plan.md)。

* The second way to start is to use the JavaScript SDK to quickly embed powerful charts and graphs in your web application. 只需几行代码，即可编写功能强大的查询。 Use them to populate line charts, pie charts, bar charts, heat maps, data grids, and more. 通过使用 SDK，所有这些元素都是现成可用的。 SDK 还提取时序见解查询 API。 可以使用它们来创建类似 SQL 的谓词，用于查询要在仪表板上显示的数据。 对于混合表示层解决方案，时序见解提供参数化 URL。 它们提供与时序见解预览版资源管理器之间的无缝连接点，可帮助深入分析数据。

  * Read about the [Time Series Insights JS client library](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) and the [Time Series Insights client](https://github.com/Microsoft/tsiclient) documentation to learn more about the JavaScript SDK.

  * Learn more about sharing URLs and the new UI by reviewing [Visualize data in the Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* 第三种方法是使用功能强大的 API 查询存储在时序见解中的数据。 Time Series Insights has temporal operators such as `from`, `to`, `first`, and `last`. It has aggregations and transformations such as `average`, `min`, `max`, `split by`, `order by`, and `DateHistogram`. It also has filtering operators such as `has`, `in`, `and`, `or`, `greater than`, and `REGEX`. 所有这些运算符使下游应用程序能够快速找到数据中的相关趋势和模式。 Use them to populate homegrown visualizations to spot anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>可操作分析和提高处理效率

使用时序见解可大规模监视设备的运行状况、使用情况和性能。 时序见解提供了一种衡量操作效率的简便方法。 时序见解有助于管理多种不可预测的 IoT 工作负荷，不影响引入或查询性能。

[![概述](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

如果与正确的技术或解决方案相结合，来自操作过程的数据的流式传输和连续处理可成功地转换任何业务。 这些解决方案通常是多个系统的组合。 They enable exploration and analysis of data that changes constantly, especially in the IoT realm, and share a common pattern.

这些模式通常始于支持 IoT 的平台，这类平台从各种语言环境的设备和传感器中引入数十亿个事件。 这些系统可以处理和分析流数据，获取实时见解和操作。 Data is typically archived to warm and cold store for near real-time and batch analytics.

收集的数据经过一系列处理，以便对数据进行净化并使其适合特定上下文，从而使其可用于下游查询和分析方案。 Azure 提供丰富的服务，可应用于资产维护和制造等 IoT 方案。 这些服务包括时序见解、IoT 中心、事件中心、Azure 流分析、Azure Functions、Azure 逻辑应用、Azure Databricks、Azure 机器学习和 Power BI。

解决方案体系结构可以通过以下方式实现：

* 通过 IoT 中心或事件中心来引入数据，以获得最佳的安全性、吞吐量和延迟。
* 执行数据处理和计算。 通过流分析、逻辑应用和 Azure Functions 等服务传送引入的数据。 使用的服务取决于特定的数据处理需求。
* 来自处理管道的计算信号被推送到时序见解进行存储和分析。

时序见解提供近实时数据浏览和基于资产的历史数据见解。 根据业务需求，可通过将时序见解连接到 Azure HDInsight，基于存储在时序见解中的数据来运行 MapReduce 和 Hive 作业。 可通过时序见解公共图面查询 API 向 Power BI 和其他客户应用程序提供存储在时序见解中的数据。 此数据可用于深层业务和操作智能方案。

## <a name="advanced-analytics"></a>高级分析

与机器学习和 Azure Databricks 等高级分析服务集成。 时序见解从数百万台设备中引入原始数据。 它添加了可由 Azure 分析服务套件无缝使用的上下文数据。

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

高级分析和机器学习会使用和处理大量数据。 该数据用于制定数据驱动的决策并执行预测分析。 在 IoT 用例中，高级分析算法可以从数百万台设备中收集数据。 这些设备每秒内会多次传输数据。 从 IoT 设备收集的数据是原始数据。 它缺少上下文信息，例如设备的位置和传感器读数的单位。 因此，原始数据很难直接用于高级分析。

时序见解以两种简单且经济高效的方式在 IoT 数据与高级分析之间搭建了桥梁：

* 首先，时序见解通过使用 IoT 中心从数百万台设备收集原始遥测数据。 它通过上下文信息来丰富数据，并将数据转换为 parquet 格式。 此格式可以轻松与其他高级分析服务集成，例如机器学习、Azure Databricks 和第三方应用程序。

    时序见解可以作为整个组织中所有数据的真实来源。 它创建了一个供下游分析工作负荷使用的中央存储库。 由于时序见解是一种近实时存储服务，因此高级分析模型可以利用传入的 IoT 遥测数据不断学习。 这样一来，模型可以更准确地进行预测。

* Second, the output of machine learning and prediction models can be fed into Time Series Insights to visualize and store their results. 此过程可帮助组织优化和调整其模型。 使用时序见解，可轻松直观显示与训练模型输出相同平面上的流式遥测数据。 通过这种方式，它可以帮助数据科学团队发现异常并识别模式。

## <a name="next-steps"></a>后续步骤

* 了解[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)的详细信息。
* Read [Time Series Insights Preview planning](./time-series-insights-update-plan.md) to plan out your environment.
* 阅读[时序见解客户端](https://github.com/Microsoft/tsiclient)文档。
