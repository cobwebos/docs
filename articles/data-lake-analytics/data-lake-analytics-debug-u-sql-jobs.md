---
title: 在用户定义的 C# 代码中调试失败的 Azure Data Lake U-SQL 作业 | Microsoft Docs
description: 了解如何使用针对 Visual Studio 的 Azure Data Lake 工具调试 U-SQL 失败顶点。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/31/2017
ms.author: yanacai
ms.openlocfilehash: b614583079347c2634f8d03531517d1d32c75132
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>调试失败 U-SQL 作业的用户定义 C# 代码

U-SQL 使用 C# 提供扩展性模型。 在 U-SQL 脚本中，可以轻松调用 C# 函数，并执行类似于 SQL 的声明性语言所不支持的分析函数。 若要详细了解 U-SQL 扩展性，请参阅 [U-SQL 可编程性指南](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)。 

在实践中，任何代码都可能需要调试，但调试在云中包含自定义代码且日志文件有限的分布式作业有一定的难度。 [针对 Visual Studio 的 Azure Data Lake 工具](http://aka.ms/adltoolsvs)提供一项称作“失败顶点调试”的功能，可帮助我们更轻松地调试自定义代码中发生的失败。 当 U-SQL 作业失败时，服务会保留失败状态，该工具可帮助将云故障环境下载到本地计算机进行调试。 本地下载捕获整个云环境，包括任何输入数据和用户代码。

下面的视频展示了针对 Visual Studio 的 Azure Data Lake 工具中的“失败顶点调试”功能。

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> 若要使用此功能，Visual Studio 要求安装以下两个更新：[Microsoft Visual C++ 2015 Redistributable 更新 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) 和[用于 Windows 的通用 C 运行时](https://www.microsoft.com/download/details.aspx?id=50410)。
>

## <a name="download-failed-vertex-to-local-machine"></a>将失败顶点下载到本地计算机

在针对 Visual Studio 的 Azure Data Lake 工具中打开失败作业时，错误选项卡中会显示含详细错误消息的黄色警报栏。

1. 单击“下载”以下载所有必需资源和输入流。 如果下载未完成，请单击“重试”。

2. 下载完成后，单击“打开”，生成本地调试环境。 将打开一个新的调试解决方案，如果在 Visual Studio 中打开了现有的解决方案，请确保在调试之前保存并关闭它。

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 下载顶点](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>配置调试环境

> [!NOTE]
> 调试前，请务必检查“异常设置”窗口中的“公共语言运行时异常”(Ctrl+Alt+E)。

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 设置](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

在新启动的 Visual Studio 实例中，不一定能够找到用户定义的 C# 源代码：

1. [我可以在解决方案中查找源代码](#source-code-is-included-in-debugging-solution)

2. [我无法在解决方案中查找源代码](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>源代码包含在调试解决方案中

在以下两种情况下会捕获 C# 源代码：

1. 用户代码在代码隐藏文件（在 U-SQL 项目中通常命名为 `Script.usql.cs`）中定义。

2. 用户代码在 U-SQL 应用程序的 C# 类库项目中定义，并已注册为包含**调试信息**的程序集。

如果已将源代码导入解决方案，则可以使用 Visual Studio 调试工具（监视、变量等）来排查问题：

1. 按 **F5** 开始调试。 代码将一直运行，直到由于出现异常而停止。

2. 打开源代码文件并设置断点，按 **F5** 逐步调试代码。

    ![Azure Data Lake Analytics U-SQL 调试异常](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>源代码未包含在调试解决方案中

如果用户代码未包含在代码隐藏文件中，或未注册包含**调试信息**的程序集，则源代码不会自动包含在调试解决方案中。 在这种情况下，需要执行额外的步骤来添加源代码：

1. 右键单击“解决方案 'VertexDebug'”，并选择“添加”>“现有项目...”，找到程序集源代码，并将项目添加到调试解决方案。

    ![Azure Data Lake Analytics U-SQL 调试添加项目](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 获取 **FailedVertexDebugHost** 项目的项目文件夹路径。 

3. 右键单击添加的程序集源代码项目，选择“属性”，选择左侧的“生成”选项卡，将复制的以 \bin\debug 结尾的路径粘贴为“输出”中的“输出路径”。 最终输出路径类似于 <DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\"。

    ![Azure Data Lake Analytics U-SQL调试设置 pdb 路径](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

完成这些设置后，按 **F5** 开始调试并设置断点。 还可以使用 Visual Studio 调试工具（监视、变量等）排查问题。

> [!NOTE]
> 每次修改代码以生成更新版 .pdb 文件后，都要重新生成程序集源代码项目。

## <a name="resubmit-the-job"></a>提交作业

调试后，如果项目成功完成，输出窗口中显示以下消息：

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL 调试成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

若要重新提交失败的作业：

1. 对于包含代码隐藏解决方案的作业，请将 C# 代码复制到代码隐藏源文件（通常为 `Script.usql.cs`）中。

2. 对于包含程序集的作业，请在调试解决方案中右键单击程序集源代码项目，并将更新的 .dll 程序集注册到 Azure Data Lake 目录中。

3. 重新提交 U-SQL 作业。

## <a name="next-steps"></a>后续步骤

- [U-SQL 可编程性指南](data-lake-analytics-u-sql-programmability-guide.md)
- [为 Azure Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [使用本地运行和 Azure Data Lake U-SQL SDK 来测试及调试 U-SQL 作业](data-lake-analytics-data-lake-tools-local-run.md)
- [如何对异常的定期作业进行故障排除](data-lake-analytics-data-lake-tools-debug-recurring-job.md)