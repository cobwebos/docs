---
title: 在 Azure 上选择实时和流处理解决方案
description: 了解如何选择适当的实时分析和流式处理技术，以便在 Azure 上构建应用程序。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: a204aacc0061091e0a273581e766bb669d191f0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903698"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>在 Azure 上选择实时分析和流式处理技术

Azure 上提供了用于实时分析和流式处理的多种服务。 本文中的信息可帮助你确定哪种技术最适合你的应用程序。

## <a name="when-to-use-azure-stream-analytics"></a>何时使用 Azure 流分析

Azure 流分析是 Azure 上提供的建议用于流分析的服务。 该服务适用于多种方案，包括但不限于：

* 数据可视化仪表板
* 针对时态和空间模式或异常生成实时[警报](stream-analytics-set-up-alerts.md)
* 提取、转换、加载 (ETL)
* [事件来源模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

将 Azure 流分析作业添加到应用程序，是在 Azure 中使用熟悉的 SQL 语言启动和运行流分析的最快捷方法。 Azure 流分析是一个作业服务，因此你无需花费时间来管理群集，也无需担心出现停机，因为它在作业级别提供 99.9% SLA。 还可以在作业级别进行计费，使创业成本降低（只需一个流单元），但流单元可缩放（最多可以购买 192 个）。 运行少量的流分析作业比运行并维护群集要经济高效得多。

Azure 流分析提供丰富的全新体验。 无需进行任何额外的设置，就能立即利用以下功能：

* 内置时态运算符，例如[开窗聚合](stream-analytics-window-functions.md)、时态联接和时态分析函数。
* 本机 Azure [输入](stream-analytics-add-inputs.md)和[输出](stream-analytics-define-outputs.md)适配器
* 支持慢速变化的[参考数据](stream-analytics-use-reference-data.md)（也称为查找表），包括与地理围栏的地理空间参考数据相联接。
* 集成的解决方案，例如[异常情况检测](stream-analytics-machine-learning-anomaly-detection.md)
* 同一查询中的多个时间窗口
* 可按任意顺序编写多个时态运算符。
* 从输入抵达事件中心到输出进入事件中心的端到端延迟不超过 100 毫秒（包括与事件中心之间的网络延迟），且能保持较高的吞吐量

## <a name="when-to-use-other-technologies"></a>何时使用其他技术

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>要使用除 JavaScript 以外的语言编写 UDF、UDA 和自定义反序列化程序

Azure 流分析支持在适用于云作业的 JavaScript 中以及适用于 IoT Edge 作业的 C# 中使用用户定义的函数 (UDF) 或用户定义的聚合 (UDA)。 还支持 C# 用户定义的反序列化程序。 若要在其他语言（例如 Java 或 Python）中实现反序列化程序、UDF 或 UDA，可以使用 Spark 结构化流。 还可以在自己的虚拟机上运行事件中心 **EventProcessorHost**，以执行任意流式处理。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>解决方案位于多云环境或本地环境中

Azure 流分析是 Microsoft 的专属技术，只能在 Azure 上使用。 如果需要在云之间或本地位置之间移植解决方案，请考虑使用 Spark 结构化流或 Storm 等开源技术。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio Code 创建流分析作业](quick-create-visual-studio-code.md)
