---
title: 选择实时分析和流式处理 Azure 上的处理技术
description: 了解有关如何选择右实时分析和流处理技术来构建在 Azure 上的应用程序。
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242687"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>选择实时分析和流式处理 Azure 上的处理技术

有多个服务可用于实时分析和 Azure 上的流式处理。 本文提供了您需要决定哪种技术是最适合你的应用程序的信息。

## <a name="when-to-use-azure-stream-analytics"></a>何时使用 Azure Stream Analytics

Azure Stream Analytics 是 Azure 上的流分析的建议的服务。 它适用于广泛的方案，其中包括但不限于：

* 数据可视化效果的仪表板
* 实时[警报](stream-analytics-set-up-alerts.md)从临时和空间模式或异常
* 提取、转换、加载 (ETL)
* [事件溯源模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

添加 Azure Stream Analytics 作业到你的应用程序是最快的方式获取启动流分析和运行在 Azure 中，使用 SQL 语言已经知道。 Azure Stream Analytics 是一种作业服务，无需花时间管理群集，因此无需担心停机的情况下在作业级别 99.9%的 SLA。 计费还会在作业级别进行启动成本较低 （一个流式处理单位），但可缩放 （最多 192 流式处理单位）。 很多更具成本效益比运行和维护群集运行几个 Stream Analytics 作业。

Azure Stream Analytics 具有丰富的开箱体验。 您可以立即利用以下功能而无需任何其他设置：

* 内置临时运算符，如[开窗聚合](stream-analytics-window-functions.md)，临时联接和临时分析函数。
* 本机 Azure[输入](stream-analytics-add-inputs.md)并[输出](stream-analytics-define-outputs.md)适配器
* 支持缓慢更改[引用数据](stream-analytics-use-reference-data.md)（也称为查找表），包括地理围栏的地理空间引用数据联接。
* 集成解决方案，如[异常情况检测](stream-analytics-machine-learning-anomaly-detection.md)
* 在同一查询中的多个时间窗口
* 编写任意序列中的多个临时运算符的功能。
* 在 100 毫秒的端到端延迟到达事件中心输出事件中心中的登录的输入包括在持续的高吞吐量的网络延迟从 / 向事件中心

## <a name="when-to-use-other-technologies"></a>何时使用其他技术

### <a name="you-need-to-input-from-or-output-to-kafka"></a>你需要输入或输出到 Kafka

Azure Stream Analytics 不会具有 Apache Kafka 的输入或输出适配器。 如果你有登录中的事件或需要将发送到 Kafka，并且没有要求运行 Kafka 群集，可以继续使用 Stream Analytics 通过将事件发送到事件中心使用事件中心 Kafka API 而无需更改事件发送方。 如果需要运行 Kafka 群集，则可以使用 Spark 结构化流式处理，而完全支持[Azure Databricks](../azure-databricks/index.yml)，或在 Storm [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md)。

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>你想要在非 JavaScript 语言编写 Udf、 Uda 和自定义反序列化程序或C#

Azure Stream Analytics 支持用户定义函数 (UDF) 或用户定义聚合 (UDA) 在 JavaScript 中用于云作业和C#IoT Edge 作业。 C#此外支持用户定义的反序列化程序。 如果你想要在其他语言，如 Java 或 Python 中实现反序列化程序、 UDF 或 UDA 可以使用 Spark 结构化流式处理。 您还可以运行事件中心**EventProcessorHost**上您的虚拟机以进行任意的流式处理。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>您的解决方案是在多云或本地环境中

Azure Stream Analytics 是 Microsoft 的专有技术，仅可在 Azure 上。 如果需要可移植性跨云或本地解决方案，请考虑 Spark 结构化流式处理、 Storm 等的开放源代码技术。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建 Stream Analytics 作业](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 创建 Stream Analytics 作业](stream-analytics-quick-create-powershell.md)
* [使用 Visual Studio 创建 Stream Analytics 作业](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio Code 创建 Stream Analytics 作业](quick-create-vs-code.md)