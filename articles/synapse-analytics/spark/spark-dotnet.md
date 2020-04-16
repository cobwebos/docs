---
title: 使用 .NET 进行与 Azure 突触分析一起阿帕奇火花
description: 了解如何使用 .NET 和 Apache Spark 在 Azure Synapse 分析笔记本中执行批处理、实时流式处理、机器学习和编写临时查询。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430508"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>使用 .NET 进行与 Azure 突触分析一起阿帕奇火花

[阿帕奇 Spark 的 .NET](https://dot.net/spark)是免费、开源的，并且跨平台 .NET 支持 Spark。 阿帕奇 Spark 的 .NET 为 Spark 提供了 .NET 绑定，允许您通过 C# 和 F# 访问 Spark API。 使用 .NET 表示 Apache Spark，您可以使用 .NET 编写和执行 Spark 的用户定义的函数。 .NET Spark API 使您能够访问 Spark 的所有方面，帮助您分析数据，包括 Spark SQL 和结构化流式处理。

您可以通过 Spark 批处理作业定义或使用交互式 Azure 突触分析笔记本使用 .NET 分析数据。 在本文中，您将了解如何使用这两种技术将 .NET 用于 Apache Spark 和 Azure 突触。 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>使用 Spark 作业定义提交批处理作业

访问本教程，了解如何使用 Azure 突触分析[为 Synapse Spark 池创建 Apache Spark 作业定义](apache-spark-job-definitions.md)。 如果尚未打包应用以提交到 Azure 同步，请完成以下步骤。

1. 运行以下命令以发布应用。 请务必将*mySparkApp*替换为应用的路径。

   **在 Windows 上：**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **在 Linux 上：**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure 突触分析笔记本中用于 Apache Spark 的 NET

创建新笔记本时，您可以选择要表达业务逻辑的语言内核。 对多种语言（包括 C#）有内核支持。

要在 Azure 同步分析笔记本中使用 .NET 用于 Apache Spark，请选择 **.NET Spark （C#）** 作为内核，并将笔记本附加到现有的 Spark 池。

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure 突触分析笔记本中用于 Apache Spark 的 NET 

笔记本是为 Apache Spark 管道和方案原型制作 .NET 的绝佳选项。 您可以快速高效地开始使用、理解、筛选、显示和可视化数据。 数据工程师、数据科学家、业务分析师和机器学习工程师都能够通过共享的交互式文档进行协作。 您会立即看到数据浏览的结果，并可以在同一笔记本中可视化数据。

### <a name="how-to-use-notebooks"></a>如何使用笔记本

创建新笔记本时，请选择要表达业务逻辑的语言内核。 对多种语言（包括 C#）有内核支持。 

要在 Azure 同步分析笔记本中使用 .NET 用于 Apache Spark，请选择 **.NET Spark （C#）** 作为内核，并将笔记本附加到现有的 Spark 池。 

.NET Spark 笔记本基于 .NET 交互式体验，并提供交互式 C# 体验，能够使用 .NET 用于开箱即用的 Spark，Spark 会话变量`spark`已预定义。

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# 内核功能

在 Azure 同步分析笔记本中使用 .NET 表示 Apache Spark 时，可以使用以下功能：

* 声明性 HTML：使用 HTML 语法（如标头、项目符号列表，甚至显示图像）从单元格生成输出。
* 简单的 C# 语句（如分配、打印到控制台、引发异常等）。
* 多行 C# 代码块（如 if 语句、foreach 循环、类定义等）。
* 访问标准 C# 库（如系统、LINQ、枚举等）。
* 支持[C# 8.0 语言功能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* "火花"作为预定义的变量，让您访问 Apache Spark 会话。
* 支持定义[可以在 Apache Spark 中运行的 .NET 用户定义的函数](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)。
* 支持使用`XPlot.Plotly`库使用不同的图表（如线图、条形图或直方图）和布局（如单张、叠加等）可视化 Spark 作业的输出。
* 能够将 NuGet 包包含在 C# 笔记本中。

## <a name="next-steps"></a>后续步骤

* [阿帕奇火花文档的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET 互动](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)