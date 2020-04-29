---
title: 使用 .NET 进行 Apache Spark 与 Azure Synapse Analytics
description: 了解如何使用 .NET 和 Apache Spark 执行批处理、实时流式处理、机器学习和在 Azure Synapse Analytics 笔记本中编写即席查询。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430508"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>使用 .NET 进行 Apache Spark 与 Azure Synapse Analytics

适用于[Apache Spark 的 .net](https://dot.net/spark)是适用于 Spark 的免费、开源和跨平台 .net 支持。 .NET for Apache Spark 为 Spark 提供 .NET 绑定，使你可以通过 c # 和 F # 访问 Spark Api。 利用 .NET for Apache Spark，你可以使用 .NET 为 Spark 编写和执行用户定义的函数。 使用用于 Spark 的 .NET Api，你可以访问 Spark 的所有方面，这些方面有助于分析数据，包括 Spark SQL 和结构化流式处理。

可以使用 Apache Spark .NET 通过 Spark 批处理作业定义或交互式 Azure Synapse Analytics 笔记本分析数据。 本文介绍如何在 Azure Synapse 中使用这两种方法使用 .NET 进行 Apache Spark。 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>使用 Spark 作业定义提交批处理作业

请访问教程，了解如何使用 Azure Synapse Analytics[创建 Synapse Spark 池 Apache Spark 作业定义](apache-spark-job-definitions.md)。 如果尚未将应用打包到 Azure Synapse，请完成以下步骤。

1. 运行以下命令以发布应用程序。 请确保将*mySparkApp*替换为应用的路径。

   在 Windows 上： 

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **在 Linux 上：**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 笔记本中用于 Apache Spark 的 .NET

创建新笔记本时，请选择想要表达业务逻辑的语言内核。 支持多种语言，包括 c #。

若要在 Azure Synapse Analytics 笔记本中使用 .NET 进行 Apache Spark，请选择 **.Net Spark （c #）** 作为内核，并将笔记本附加到现有的 Spark 池。

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 笔记本中用于 Apache Spark 的 .NET 

笔记本是为你的 .NET 构建 Apache Spark 管道和方案的一个不错的选择。 可以快速高效地开始使用、了解、筛选、显示和可视化数据。 数据工程师、数据科学家、业务分析人员和机器学习工程师都可以通过共享的交互式文档进行协作。 数据浏览会立即看到结果，并且可以在同一笔记本中直观显示数据。

### <a name="how-to-use-notebooks"></a>如何使用笔记本

创建新笔记本时，请选择想要表达业务逻辑的语言内核。 支持多种语言，包括 c #。 

若要在 Azure Synapse Analytics 笔记本中使用 .NET 进行 Apache Spark，请选择 **.Net Spark （c #）** 作为内核，并将笔记本附加到现有的 Spark 池。 

.NET Spark 笔记本基于 .NET 交互式体验，提供交互式 c # 体验，能够将 .NET 用于 Spark，并已预定义了 Spark 会话变量`spark` 。

### <a name="sparknet-c-kernel-features"></a>Spark.NET c # 内核功能

在 Azure Synapse Analytics 笔记本中使用 .NET for Apache Spark 时，可以使用以下功能：

* 声明性 HTML：使用 HTML 语法（如标头、项目符号列表，甚至是显示图像）从单元中生成输出。
* 简单的 c # 语句（例如，赋值、打印到控制台、引发异常，等等）。
* 多行 c # 代码块（如 if 语句、foreach 循环、类定义等）。
* 访问标准 c # 库（如 System、LINQ、枚举等）。
* 支持[c # 8.0 语言功能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* "spark" 作为预定义的变量，以使你能够访问 Apache Spark 会话。
* 支持定义[可在 Apache Spark 中运行的 .net 用户定义函数](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)。
* 支持使用不同的`XPlot.Plotly`图表（如线条、条形或直方图）和布局（例如，单个、重叠等）来可视化 Spark 作业的输出。
* 能够在 c # 笔记本中包含 NuGet 包。

## <a name="next-steps"></a>后续步骤

* [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET 交互](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)