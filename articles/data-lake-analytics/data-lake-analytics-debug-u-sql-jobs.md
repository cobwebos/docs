---
title: "调试 U-SQL 作业 | Microsoft Docs"
description: "了解如何使用 Visual Studio 调试 U-SQL 失败顶点。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>调试失败 U-SQL 作业的用户定义 C# 代码

U-SQL 提供 C# 扩展性模型，因此可以编写代码来添加自定义提取程序或减压器等功能。 若要了解详细信息，请参阅 [U-SQL 可编程性指南](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)。 在实践中，任何代码都可能需要调试，大数据系统只能提供有限的运行时调试信息，如日志文件。

针对 Visual Studio 的 Azure Data Lake 工具提供“失败顶点调试”功能，以便用户能够将失败作业从云中克隆到本地计算机进行调试。 本地克隆捕获的是整个云环境，包括任何输入数据和用户代码。

下面的视频展示了针对 Visual Studio 的 Azure Data Lake 工具中的“失败顶点调试”功能。

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio 要求安装以下两个更新（如果尚未安装的话）：[Microsoft Visual C++ 2015 Redistributable 更新 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) 和[用于 Windows 的通用 C 运行时](https://www.microsoft.com/download/details.aspx?id=50410)。

## <a name="download-failed-vertex-to-local-machine"></a>将失败顶点下载到本地计算机

在针对 Visual Studio 的 Azure Data Lake 工具中打开失败作业时，错误选项卡中会显示含详细错误消息的黄色警报栏。

1. 单击“下载”以下载所有必需资源和输入流。 如果下载未完成，请单击“重试”。

2. 下载完成后，单击“打开”，生成本地调试环境。 此时，系统会自动创建并打开包含调试解决方案的新 Visual Studio 实例。

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 下载顶点](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

作业可能包含代码隐藏源文件或注册的程序集，这两种类型内容的调试方案不同。

- [调试包含代码隐藏源文件的失败作业](#debug-job-failed-with-code-behind)
- [调试包含程序集的失败作业](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>调试包含代码隐藏源文件的失败作业

如果 U-SQL 作业失败且作业包含用户代码（在 U-SQL 项目中通常名为 `Script.usql.cs`），那么源代码会导入调试解决方案中。  在此解决方案中，可以使用 Visual Studio 调试工具（监视、变量等）排查问题。

> [!NOTE]
> 调试前，请务必检查“异常设置”窗口中的“公共语言运行时异常”(Ctrl+Alt+E)。

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 设置](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. 按 F5 运行代码隐藏源文件。 在异常出现时，将停止运行。

2. 打开 `ADLTool_Codebehind.usql.cs` 文件并设置断点，再按 F5 逐步调试代码。

    ![Azure Data Lake Analytics U-SQL 调试异常](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>调试使用程序集的失败作业

如果在 U-SQL 脚本中使用注册的程序集，系统将无法自动获取源代码。 在这种情况下，手动将程序集的源代码文件添加到解决方案中。

### <a name="configure-the-solution"></a>配置解决方案

1. 右键单击“解决方案 VertexDebug”，依次单击“添加”和“现有项目...”，以找到程序集的源代码文件，并将项目添加到调试解决方案中。

    ![Azure Data Lake Analytics U-SQL 调试添加项目](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 在解决方案中，右键单击“LocalVertexHost”，单击“属性”，再复制“工作目录”路径。

3. 右键单击“程序集源代码项目”，单击“属性”，选择左侧的“生成”选项卡，将复制的路径粘贴为“输出”中的“输出路径”。

    ![Azure Data Lake Analytics U-SQL调试设置 pdb 路径](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. 按 **Ctrl + Alt + E**，在“异常设置”窗口中，选中“公共语言运行时异常”。

### <a name="start-debug"></a>开始调试

1. 右键单击“程序集源代码项目”，单击“重新生成”，将 .pdb 文件输出到 `LocalVertexHost` 工作目录。

2. 按 F5，项目在出现异常时停止运行。 可能会看到以下出于稳妥考虑而忽略的警告消息。 最多等待一分钟即可进入调试屏幕。

    ![Azure Data Lake Analytics U-SQL 调试 Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. 打开源代码并设置断点，再按 F5 逐步调试代码。

还可以使用 Visual Studio 调试工具（监视、变量等）排查问题。

> [!NOTE]
> 每次修改代码以生成更新版 .pdb 文件后，都要重新生成程序集源代码项目。

调试后，如果项目成功完成，输出窗口中显示以下消息：

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Azure Data Lake Analytics U-SQL 调试成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>提交作业

完成调试后，重新提交失败作业。

1. 对于包含代码隐藏解决方案的作业，请将 C# 代码复制到代码隐藏源文件（通常为 `Script.usql.cs`）中。
2. 对于包含程序集的作业，请将更新后的 .dll 程序集注册到 ADLA 数据库中：
    1. 在服务器资源管理器或 Cloud Explorer 中，依次展开“ADLA 帐户”>“数据库”节点。
    2. 右键单击“程序集”，向 ADLA 数据库（![Azure Data Lake Analytics U-SQL 调试注册程序集](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)）注册新的 .dll 程序集
3. 重新提交作业。

## <a name="next-steps"></a>后续步骤

- [U-SQL 可编程性指南](data-lake-analytics-u-sql-programmability-guide.md)
- [为 Azure Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
