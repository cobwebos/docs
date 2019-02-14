---
title: Azure 流分析预览功能
description: 本文列出了当前以预览版提供的 Azure 流分析功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 09f1bdfa4c9a7a179bddf9473b553924bfb58fb7
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768556"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 流分析预览功能

本文汇总了当前以预览版提供的 Azure 流分析的所有功能。 建议不要在生产环境中使用预览功能。

## <a name="public-previews"></a>公共预览版

以下功能以公共预览版提供。 现在可以使用这些功能，但请勿在生产环境中使用它们。

### <a name="sql-database-reference-data"></a>SQL 数据库引用数据

Azure 流分析支持将 Azure SQL 数据库用作参考数据的输入源。 可以在 Azure 门户和 Visual Studio 中配合流分析工具将 SQL 数据库用作 Stream Analytics 作业的参考数据。 有关详细信息，请访问[将 SQL 数据库中的参考数据用于 Azure 流分析作业](sql-reference-data.md)。

### <a name="integration-with-azure-machine-learning"></a>与 Azure 机器学习集成

可使用机器学习 (ML) 函数缩放流分析作业。 若要详细了解如何在流分析作业中使用 ML 函数，请访问[使用 Azure 机器学习函数缩放流分析作业](stream-analytics-scale-with-machine-learning-functions.md)。 查看[使用 Azure 流分析和 Azure 机器学习执行情绪分析](stream-analytics-machine-learning-integration-tutorial.md)的实际方案。

### <a name="javascript-user-defined-aggregate"></a>JavaScript 用户定义的聚合

Azure 流分析支持以 JavaScript 编写的用户定义的聚合 (UDA)，可实现复杂的有状态业务逻辑。 参阅 [Azure 流分析 JavaScript 用户定义的聚合](stream-analytics-javascript-user-defined-aggregates.md)文档，了解如何使用 UDA。 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio 中的实时数据测试

适用于 Azure 流分析的 Visual Studio 工具增强了本地测试功能，通过该功能可针对来自云源（如事件中心或 IoT 中心）的实时事件流测试查询。 了解如何[使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 用户定义函数

使用 .NET Standard 用户定义函数，可以将 .NET Standard 代码作为流式管道的一部分运行。 可以创建简单的 C# 类或导入完整的项目和库。 Visual Studio 支持完整的创作和调试体验。 有关详细信息，请访问[为 Azure 流分析 Edge 作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="private-previews"></a>个人预览版

以下功能以个人预览版提供。

### <a name="anomaly-detection"></a>异常检测

Azure 流分析引入了新的机器学习模型，除了支持双向、慢正和慢负趋势检测外，还支持“峰值”和“低值”检测。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 流分析的 C# 自定义反序列化程序

开发人员现在可以在 C# 中实现自定义反序列化程序，对 Azure 流分析接收的事件进行反序列化。 可以进行反序列化的格式示例包括 Parquet、Protobuf、XML 或任何二进制格式。

### <a name="managed-identities-for-azure-resource-authentication-to-azure-data-lake-storage"></a>向 Azure Data Lake Storage 的 Azure 资源托管标识身份验证

现在，可以在写入 Azure Data Lake Storage Gen1 时，使用基于 Azure 资源的托管标识身份验证来实施实时管道，从而允许你以编程方式创建作业。 有关详细信息，请访问[使用 Azure 资源的托管标识向 Azure Data Lake Storage Gen1 输出对 Azure 流分析作业进行身份验证](stream-analytics-managed-identities-adls.md)。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>适用于 Azure 流分析的 Visual Studio Code

可以在 Visual Studio Code 中创建 Azure 流分析作业。

## <a name="next-steps"></a>后续步骤

* [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)（Azure 流分析中的八项性功能）

* [4 new features now available in Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)（Azure 流分析中现已提供的四项新功能）
