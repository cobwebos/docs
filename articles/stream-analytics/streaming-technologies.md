---
title: 在 Azure 上选择实时和流处理解决方案
description: 了解如何选择正确的实时分析和流式处理技术，以便在 Azure 上生成应用程序。
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 3bde2964c40553d02a56f57f9c459cc6afa3f660
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924902"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>选择 Azure 上的实时分析和流式处理技术

Azure 上的实时分析和流式处理可以使用多个服务。 本文提供了决定哪种技术最适合你的应用程序所需的信息。

## <a name="when-to-use-azure-stream-analytics"></a>何时使用 Azure 流分析

Azure 流分析是 Azure 上的流分析的建议服务。 这适用于包括但不限于的各种方案：

* 用于数据可视化的仪表板
* 临时和空间模式或异常的实时[警报](stream-analytics-set-up-alerts.md)
* 提取、转换、加载 (ETL)
* [事件来源模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

将 Azure 流分析作业添加到您的应用程序是在 Azure 中使用您已知道的 SQL 语言启动和运行流式处理分析的最快方法。 Azure 流分析是一种作业服务，因此你无需花费时间管理群集，并且你无需担心在作业级别出现 99.9% SLA 的停机时间。 还可以在作业级别进行计费，使启动成本降低（一个流式处理单位），但可缩放（最多192个流式处理单位）。 运行几个流分析作业比运行和维护群集更加经济高效。

Azure 流分析具有全新的全新体验。 你可以立即利用以下功能，而无需进行任何其他设置：

* 内置临时运算符，如[开窗聚合](stream-analytics-window-functions.md)、临时联接和临时分析函数。
* 本机 Azure[输入](stream-analytics-add-inputs.md)适配器和[输出](stream-analytics-define-outputs.md)适配器
* 支持慢速更改[引用数据](stream-analytics-use-reference-data.md)（也称为查找表），包括与地理围栏的地理空间引用数据加入。
* 集成解决方案，如[异常检测](stream-analytics-machine-learning-anomaly-detection.md)
* 同一查询中的多个时间窗口
* 能够在任意序列中组合多个时态运算符。
* 从事件中心到达的输入到100毫秒的端到端延迟，以便在事件中心（包括从和到事件中心的网络延迟）中输出登陆

## <a name="when-to-use-other-technologies"></a>何时使用其他技术

### <a name="you-need-to-input-from-or-output-to-kafka"></a>需要输入或输出到 Kafka

Azure 流分析没有 Apache Kafka 的输入或输出适配器。 如果有登录事件或需要发送到 Kafka 的事件，但不需要运行自己的 Kafka 群集，则可以通过使用事件中心 Kafka API 将事件发送到事件中心来继续使用流分析，而无需更改事件发送方。 如果确实需要运行自己的 Kafka 群集，则可以使用 Spark 结构化流式处理，这在[Azure HDInsight](../hdinsight/storm/apache-storm-overview.md)上[Azure Databricks](../azure-databricks/index.yml)或风暴上完全受支持。

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>要使用 JavaScript 以外的语言编写 Udf、Uda 和自定义反C#

Azure 流分析支持适用于云作业的 JavaScript 中的用户定义函数（UDF）或用户定义聚合（UDA） C# ，以及用于 IoT Edge 作业。 C#还支持用户定义的反。 如果要在其他语言（如 Java 或 Python）中实现反序列化程序、UDF 或 UDA，可以使用 Spark 结构化流式处理。 你还可以在自己的虚拟机上运行事件中心**EventProcessorHost** ，以执行任意流式处理。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>你的解决方案位于多云或本地环境中

Azure 流分析是 Microsoft 的专有技术，只能在 Azure 上使用。 如果你需要解决方案可跨云或本地移植，请考虑开源技术，例如 Spark 结构化流式处理或风暴。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio Code 创建流分析作业](quick-create-vs-code.md)