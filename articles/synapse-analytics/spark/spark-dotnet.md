---
title: 将 .NET for Apache Spark 与 Azure Synapse Analytics 配合使用
description: 了解如何使用 .NET 和 Apache Spark 执行批处理、实时流式处理、机器学习操作，以及在 Azure Synapse Analytics 笔记本中编写即席查询。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 3882352c7e1d484818a58d7bd4410cbd66bd6637
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587793"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>将 .NET for Apache Spark 与 Azure Synapse Analytics 配合使用

[.NET for Apache Spark](https://dot.net/spark) 是针对 Spark 的免费开源跨平台 .NET 支持。 .NET for Apache Spark 针对 Spark 提供了 .NET 绑定，允许你通过 C# 和 F# 访问 Spark API。 利用 .NET for Apache Spark，你可以通过 .NET 为 Spark 编写和执行用户定义的函数。 使用 .NET APIs for Spark，你可以在分析数据时访问 Spark 的所有方面，包括 Spark SQL 和结构化流式处理。

可以使用 NET for Apache Spark 通过 Spark 批处理作业定义或交互式 Azure Synapse Analytics 笔记本来分析数据。 本文介绍如何通过这两种技术将 .NET for Apache Spark 与 Azure Synapse 配合使用。

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>提交使用了 Spark 作业定义的批处理作业

访问教程，了解如何使用 Azure Synapse Analytics [创建 Synapse Spark 池的 Apache Spark 作业定义](apache-spark-job-definitions.md)。 如果尚未将应用打包提交到 Azure Synapse，请完成以下步骤。

1. 运行以下命令来发布你的应用。 请确保将 mySparkApp 替换为你的应用的路径。

   **在 Windows 上：**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **在 Linux 上：**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 笔记本中的 .NET for Apache Spark

创建新笔记本时，需要选择用来表述业务逻辑的语言内核。 提供了针对多种语言（包括 C#）的内核支持。

若要在 Azure Synapse Analytics 笔记本中使用 .NET for Apache Spark，请选择 **.NET Spark (C#)** 作为内核，并将笔记本附加到现有的 Spark 池。

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 笔记本中的 .NET for Apache Spark 

若要为 .NET for Apache Spark 管道和方案构建原型，笔记本是一个不错的方法。 你可以快速高效地开始使用、了解、筛选、显示和可视化数据。 数据工程师、数据科学家、业务分析员和机器学习工程师都可以通过共享的交互式文档进行协作。 你可以看到数据探索的即时结果，并且可以在同一个笔记本中可视化你的数据。

### <a name="how-to-use-notebooks"></a>如何使用笔记本

创建新笔记本时，你需要选择用来表述业务逻辑的语言内核。 提供了针对多种语言（包括 C#）的内核支持。

若要在 Azure Synapse Analytics 笔记本中使用 .NET for Apache Spark，请选择 **.NET Spark (C#)** 作为内核，并将笔记本附加到现有的 Spark 池。

.NET Spark 笔记本基于 .NET 交互式体验并提供了交互式 C# 体验，能够直接将 .NET for Spark 与已预定义的 Spark 会话变量 `spark` 配合使用。

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# 内核功能

在 Azure Synapse Analytics 笔记本中使用 .NET for Apache Spark 时，可以使用以下功能：

* 声明性 HTML：使用 HTML 语法（例如标题、项目符号列表甚至显示图像）从单元中生成输出。
* 简单的 C# 语句（例如分配、输出到控制台、引发异常，等等）。
* 多行 C# 代码块（例如 if 语句、foreach 循环、类定义，等等）。
* 对标准 C# 库（例如 System、LINQ、Enumerables，等等）的访问权限。
* 支持 [C# 8.0 语言功能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 预定义的变量“spark”，使你能够访问 Apache Spark 会话。
* 支持定义[可在 Apache Spark 内运行的 .NET 用户定义函数](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)。
* 支持通过 `XPlot.Plotly` 库使用不同的图表（例如折线图、条形图或直方图）和布局（例如单一布局、重叠布局，等等）来可视化 Spark 作业的输出。
* 能够在 C# 笔记本中包括 NuGet 程序包。

## <a name="next-steps"></a>后续步骤

* [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)