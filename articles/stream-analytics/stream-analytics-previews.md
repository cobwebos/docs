---
title: Azure 流分析预览功能
description: 本文列出了当前以预览版提供的 Azure 流分析功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e63937cedf44b1642e091a4744d898a26422be10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393694"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 流分析预览功能

本文汇总了当前以预览版提供的 Azure 流分析的所有功能。 建议不要在生产环境中使用预览功能。

## <a name="public-previews"></a>公共预览版

以下功能以公共预览版提供。 现在可以使用这些功能，但请勿在生产环境中使用它们。

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code 用于 Azure Stream Analytics （发布 2019 年 5）

可以在 Visual Studio Code 中创建 Azure 流分析作业。 请参阅我们[VS Code 入门教程](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)。

### <a name="anomaly-detection"></a>异常检测

Azure 流分析引入了新的机器学习模型，除了支持双向、慢正和慢负趋势检测外，还支持“峰值”和“低值”检测   。 有关详细信息，请访问 [Azure 流分析中的异常情况检测](stream-analytics-machine-learning-anomaly-detection.md)。

### <a name="integration-with-azure-machine-learning"></a>与 Azure 机器学习集成

可使用机器学习 (ML) 函数缩放流分析作业。 若要详细了解如何在流分析作业中使用 ML 函数，请访问[使用 Azure 机器学习函数缩放流分析作业](stream-analytics-scale-with-machine-learning-functions.md)。 查看[使用 Azure 流分析和 Azure 机器学习执行情绪分析](stream-analytics-machine-learning-integration-tutorial.md)的实际方案。

### <a name="javascript-user-defined-aggregate"></a>JavaScript 用户定义的聚合

Azure 流分析支持以 JavaScript 编写的用户定义的聚合 (UDA)，可实现复杂的有状态业务逻辑。 参阅 [Azure 流分析 JavaScript 用户定义的聚合](stream-analytics-javascript-user-defined-aggregates.md)文档，了解如何使用 UDA。 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio 中的实时数据测试

适用于 Azure 流分析的 Visual Studio 工具增强了本地测试功能，通过该功能可针对来自云源（如事件中心或 IoT 中心）的实时事件流测试查询。 了解如何[使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 用户定义函数

使用 .NET Standard 用户定义函数，可以将 .NET Standard 代码作为流式管道的一部分运行。 可以创建简单的 C# 类或导入完整的项目和库。 Visual Studio 支持完整的创作和调试体验。 有关详细信息，请访问[为 Azure 流分析 Edge 作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="other-previews"></a>其他预览

以下功能均在预览版中可用。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#在 IoT Edge 和云 (Announced 2019 年 5) 上的 Azure Stream Analytics 的自定义反序列化程序

开发人员可以实现自定义反序列化程序在C#进行反序列化由 Azure Stream Analytics 接收到的事件。 可以进行反序列化的格式示例包括 Parquet、Protobuf、XML 或任何二进制格式。 注册此预览版[此处](https://aka.ms/asapreview1)。

### <a name="parquet-output-announced-may-2019"></a>Parquet 输出 （宣布 2019 年 5）
Parquet 是启用有效的大数据处理以分列格式。 通过将输出在 data lake 中的 Parquet 格式的数据，可以充分利用 Azure Stream Analytics 到 power 大型缩放流式处理 ETL 和运行批处理、 训练机器学习算法或对历史数据运行交互式查询。 注册此预览版[此处](https://aka.ms/asapreview1)。

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>与事件中心 (Announced 2019 年 5) 单击一次集成 
通过这种集成，你现在可以将传入的数据可视化并开始从事件中心门户编写一次单击的 Stream Analytics 查询。 查询准备就绪后，你将能够再它正式采用几次单击，并开始获取实时见解。 这将显著减少的时间和成本来开发实时分析解决方案。 注册此预览版[此处](https://aka.ms/asapreview1)。

### <a name="support-for-azure-stack-announced-may-2019"></a>针对 Azure Stack （宣布 2019 年 5） 的支持
在 Azure IoT Edge 运行时，启用此功能利用自定义 Azure Stack 功能，如本机支持本地输入和输出 （例如事件中心，IoT 中心，Blob 存储） 的 Azure Stack 上运行。 这一新集成，可构建可以分析你的数据接近于生成位置、 降低延迟和最大化见解的混合体系结构。
注册此预览版[此处](https://aka.ms/asapreview1)。

