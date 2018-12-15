---
title: Azure 流分析预览功能
description: 本文列出了当前以预览版提供的 Azure 流分析功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 637afab45e04c68777f8d1b42817c912cdc09941
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133734"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 流分析预览功能

本文汇总了当前以预览版提供的 Azure 流分析的所有功能。 建议不要在生产环境中使用预览功能。

## <a name="public-previews"></a>公共预览版

以下功能以公共预览版提供。 现在可以使用这些功能，但请勿在生产环境中使用它们。

### <a name="integration-with-azure-machine-learning"></a>与 Azure 机器学习集成

可使用机器学习 (ML) 函数缩放流分析作业。 若要详细了解如何在流分析作业中使用 ML 函数，请访问[使用 Azure 机器学习函数缩放流分析作业](stream-analytics-scale-with-machine-learning-functions.md)。 查看[使用 Azure 流分析和 Azure 机器学习执行情绪分析](stream-analytics-machine-learning-integration-tutorial.md)的实际方案。

### <a name="session-windows"></a>会话窗口

流分析提供对开窗函数的本机支持，使开发人员能够最小的工作量创建复杂的流进程作业。 [会话窗口](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics)对差不多同时到达的事件进行分组，筛选出没有数据的时间段。 若要详细了解开窗函数，请访问[流分析开窗函数简介](stream-analytics-window-functions.md)。

### <a name="blob-output-partitioning-by-custom-time"></a>按自定义时间划分的 Blob 输出分区

Azure 流分析可根据自定义时间属性输出到 Blob 存储。 有关详细信息，请访问 [Azure 流分析 Blob 存储输出的自定义 DateTime 路径模式](stream-analytics-custom-path-patterns-blob-storage-output.md)。

### <a name="javascript-user-defined-aggregate"></a>JavaScript 用户定义的聚合

Azure 流分析支持以 JavaScript 编写的用户定义的聚合 (UDA)，可实现复杂的有状态业务逻辑。 参阅 [Azure 流分析 JavaScript 用户定义的聚合](stream-analytics-javascript-user-defined-aggregates.md)文档，了解如何使用 UDA。 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio 中的实时数据测试

适用于 Azure 流分析的 Visual Studio 工具增强了本地测试功能，通过该功能可针对来自云源（如事件中心或 IoT 中心）的实时事件流测试查询。 了解如何[使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 用户定义函数

使用 .NET Standard 用户定义函数，可以将 .NET Standard 代码作为流式管道的一部分运行。 可以创建简单的 C# 类或导入完整的项目和库。 Visual Studio 支持完整的创作和调试体验。 有关详细信息，请访问[为 Azure 流分析 Edge 作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="private-previews"></a>个人预览版

以下功能以个人预览版提供。 若要访问这些预览功能，请访问 Azure 流分析个人预览版[注册](https://aka.ms/ASApreview1)页。

### <a name="anomaly-detection"></a>异常检测

Azure 流分析引入了新的机器学习模型，除了支持双向、慢正和慢负趋势检测外，还支持“峰值”和“低值”检测。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 流分析的 C# 自定义反序列化程序

开发人员现在可以在 C# 中实现自定义反序列化程序，对 Azure 流分析接收的事件进行反序列化。 可以进行反序列化的格式示例包括 Parquet、Protobuf、XML 或任何二进制格式。

### <a name="blob-output-partitioning-by-custom-attribute"></a>按自定义属性划分的 Blob 输出分区

现在，可以根据查询中的任何列将 Azure 流分析输出分区为 Blob 存储。

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>向 Azure Data Lake Storage 的 Azure 资源托管标识身份验证

现在，可以在写入 Azure Data Lake Storage Gen1 时，使用基于 Azure 资源的托管标识身份验证来实施实时管道，从而允许你以编程方式创建作业。 有关详细信息，请访问[使用 Azure 资源的托管标识向 Azure Data Lake Storage Gen1 输出对 Azure 流分析作业进行身份验证](stream-analytics-managed-identities-adls.md)。

## <a name="next-steps"></a>后续步骤

* [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)（Azure 流分析中的八项性功能）

* [4 new features now available in Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)（Azure 流分析中现已提供的 4 项新功能）
