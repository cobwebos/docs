---
title: Azure 流分析预览功能
description: 本文列出了当前以预览版提供的 Azure 流分析功能。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7391fbccaf7983a070d80da64a2908333280420b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83608995"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 流分析预览功能

本文汇总了当前以预览版提供的 Azure 流分析的所有功能。 建议不要在生产环境中使用预览功能。

## <a name="public-previews"></a>公共预览版

以下功能以公共预览版提供。 现在可以使用这些功能，但请勿在生产环境中使用它们。

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>通过托管标识对 SQL 数据库输出进行身份验证

Azure 流分析支持对 Azure SQL 数据库输出接收器进行[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)。 托管标识消除了基于用户的身份验证方法的如下限制：发生密码更改或用户令牌过期（每隔 90 天过期）时需要重新进行身份验证。 当你不再需要手动进行身份验证时，流分析部署可以完全自动化。

### <a name="output-to-azure-synapse-analytics"></a>输出到 Azure Synapse Analytics

Azure 流分析作业可以输出到 [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) 中的 SQL 池表，并可处理高达 200 MB/秒的吞吐量速率。这可支持报表和仪表板等工作负载带来的最苛刻的实时分析和热路径数据处理需求。  


### <a name="online-scaling"></a>联机缩放

通过联机缩放，如果需要更改 SU 分配，无需停止作业。 可以增加或减少正在运行的作业的 SU 容量，而无需将其停止。 这基于客户对流分析目前提供的长时间运行的任务关键管道的承诺。 有关详细信息，请参阅[配置 Azure 流分析流式处理单元](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)。

### <a name="c-custom-de-serializers"></a>C# 自定义反序列化程序
开发人员可以利用 Azure 流分析的强大功能来处理 Protobuf、XML 或任何自定义格式的数据。 可以在 C# 中实现[自定义反序列化程序](custom-deserializer-examples.md)，然后可以使用该程序对 Azure 流分析接收的事件进行反序列化处理。

### <a name="extensibility-with-c-custom-code"></a>使用 C# 自定义代码的可扩展性

在云中或 IoT Edge 上创建流分析模块的开发人员可以编写或重复使用自定义 C# 函数，并在查询中通过[用户定义的函数](stream-analytics-edge-csharp-udf-methods.md)直接调用这些函数。


### <a name="debug-query-steps-in-visual-studio"></a>在 Visual Studio 中调试查询步骤

在适用于 Visual Studio 的 Azure 流分析工具中执行本地测试时，可以轻松预览数据关系图上的中间行集。 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>在 Visual Studio Code 中使用实时数据进行本地测试

将作业提交到 Azure 之前，可以针对本地计算机上的实时数据测试查询。 每个测试迭代平均花费不到 2 到 3 秒，这会使开发过程非常高效。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>适用于 Azure 流分析的 Visual Studio Code

可以在 Visual Studio Code 中创建 Azure 流分析作业。 请参阅我们的 [VS Code 入门教程](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)。


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>通过 Azure 机器学习管理的自定义 ML 模型进行实时高性能评分

Azure 流分析通过利用自定义预先训练的机器学习模型（由 Azure 机器学习管理，并在 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 中托管），并使用不需要编写代码的工作流，来支持高性能实时评分。 [注册](https://aka.ms/asapreview1)即可获取预览版


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio 中的实时数据测试

适用于 Azure 流分析的 Visual Studio 工具增强了本地测试功能，通过该功能可针对来自云源（如事件中心或 IoT 中心）的实时事件流测试查询。 了解如何[使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)。


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 用户定义函数

使用 .NET Standard 用户定义函数，可以将 .NET Standard 代码作为流式管道的一部分运行。 可以创建简单的 C# 类或导入完整的项目和库。 Visual Studio 支持完整的创作和调试体验。 有关详细信息，请访问[为 Azure 流分析 Edge 作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="other-previews"></a>其他预览

如若请求，还可以使用以下预览功能。

### <a name="support-for-azure-stack"></a>支持 Azure Stack
此功能在 Azure IoT Edge 运行时启用，利用自定义 Azure Stack 功能，比如以原生方式支持在 Azure Stack 上运行的本地输入和输出（例如事件中心、IoT 中心、Blob 存储）。 利用这一新的集成，你可以构建混合体系结构，该体系结构可以在数据生成的位置附近分析数据，从而降低延迟并最大程度增加见解。
可以通过此功能构建混合体系结构，该体系结构可以在数据生成的位置附近分析数据，从而降低延迟并最大程度增加见解。 必须[注册](https://aka.ms/asapreview1)才能获取此预览版。
