---
title: "调试 U-SQL 作业 | Microsoft Docs"
description: "了解如何使用 Visual Studio 调试 U-SQL 失败的顶点。 "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: a5bb452582f05981a17c2514e0e40db0571bf61d
ms.openlocfilehash: f9b485bfbfbeb8a95ae1908ef6b1733b9cc6999a


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>调试失败的 U-SQL 作业的用户定义 C# 代码

了解如何使用针对 Visual Studio 的 Azure Data Lake 工具调试失败且显示用户定义代码 bug 的 U-SQL 作业。

## <a name="background"></a>背景

U-SQL 通过 C# 提供扩展性模型，用户可以编写用户定义的 C# 代码（如用户定义的提取器和化简器等），实现更大的扩展性（详细了解 [U-SQL 用户定义的代码](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)）。 但是，没有开发人员可以编写代码而不出错，并且在大型数据系统中调试很难，因为许多系统仅提供有限的运行时调试信息（如日志等）。 

针对 Visual Studio 的 ADL 工具提供了一种称为“失败顶点调试”的功能，使用该功能可以从云轻松地将失败环境（包括中间输入数据和用户代码等）克隆到本地计算机，以使用云中相同的运行时和精确的输入数据跟踪和调试失败的作业。

以下视频演示了针对 Visual Studio 的 ADL 工具中的**失败顶点调试**功能。

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>
>

> [!NOTE]
> 如果不具有以下两个窗口升级： [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) 和[适用于 Windows 的通用 C 运行时](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)，Visual Studio 可能会挂起或崩溃。
> 
> 

## <a name="prerequisites"></a>先决条件
* 通读完[入门](data-lake-analytics-data-lake-tools-get-started.md)这篇文章。

## <a name="download-failed-vertex-to-local"></a>将失败顶点下载到本地

在针对 Visual Studio 的 ADL 工具中打开失败的作业时，会收到警报。 将在错误选项卡和顶部窗口的黄色警告栏中显示详细的错误消息。

1. 单击“下载”以下载所有必需资源和输入流。 如果下载失败，单击“重试”。 

2. 下载完成后单击“打开”以生成本地调试环境。 将自动创建并打开一个包含调试解决方案的新 Visual Studio 实例。 

3. 隐藏代码的失败作业与使用程序集的失败作业的调试步骤略有不同。

    - [调试隐藏代码的失败作业](#debug-job-failed-with-code-behind)
    - [调试使用程序集的失败作业](#debug-job-failed-with-assemblies)

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 下载顶点](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>调试隐藏代码的失败作业

如果在代码隐藏文件（在 U-SQL 项目中通常命名为“Script.usql.cs”）中编写用户定义的代码，然后 U-SQL 作业失败，源代码将自动导入到生成的调试解决方案中，只需使用基于 Visual Studio 的调试体验（监视、变量等）即可解决该问题。 

调试之前，请确保在“异常设置”窗口中选中了“公共语言运行时异常” (**Ctrl + Alt + E**)。

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 设置](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. 按 **F5**，将自动运行代码隐藏代码，直到被异常停止。

2. 打开项目中的 **ADLTool_Codebehind.usql.cs**，设置断点，然后按 F5 逐步调试代码。

    ![Azure Data Lake Analytics U-SQL 调试异常](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>调试使用程序集的失败作业

如果在 U-SQL 脚本中使用已注册的程序集，系统无法自动获取源代码，你需要在调试用户定义的代码之前执行一些配置，即，必须将程序集的源代码添加到自动生成的解决方案中。

### <a name="configure-the-solution"></a>配置解决方案

1. 右键单击“解决方案 ‘VertexDebug’” > “添加” > “现有项目...”找到程序集的源代码，并将该项目添加到调试解决方案中。

    ![Azure Data Lake Analytics U-SQL 调试添加项目](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 在解决方案中右键单击“LocalVertexHost” > “属性”，复制“工作目录”路径。

3. 右键单击“程序集源代码项目” > “属性”，选择左侧的“生成”选项卡，将 2 中复制的路径粘贴到“输出” > “输出路径”。  

    ![Azure Data Lake Analytics U-SQL调试设置 pdb 路径](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. 按 **Ctrl + Alt + E**，在“异常设置”窗口中，选中“公共语言运行时异常”。

### <a name="start-debug"></a>开始调试

1. 右键单击“程序集源代码项目” > “重新生成”，将 pdb 文件输出到 LocalVertexHost 工作目录。

2. 按 **F5**，将自动运行该项目，直到被异常停止。 第一次可以看看以下有可能会忽略的消息。 一分钟后就可进入调试屏幕。

    ![Azure Data Lake Analytics U-SQL 调试 Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. 打开源代码并设置断点，然后按 **F5** 逐步调试代码。

也可以使用其他基于 Visual Studio 的调试体验（监视、变量等）调试问题。 

**请注意，**每次修改代码后需要重新生成程序集源代码项目，使新的 pdb 文件生效。

调试成功完成后，输出窗口将显示以下消息：

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL 调试成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>提交作业
完成调试后，可以重新提交失败的作业。

1. 将新.dll 程序集注册到 ADLA 数据库。

    1. 在服务器资源管理器/Cloud Explorer 中，展开“ADLA 帐户” > “数据库”节点。
    2. 右键单击“程序集”，然后单击“注册程序集”。 
    3. 将新.dll 程序集注册到 ADLA 数据库。
    ![Azure Data Lake Analytics U-SQL 调试注册程序集](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. 或者，将 C# 代码复制回 script.usql.cs - C# 代码隐藏文件。
3. 重新提交作业。

## <a name="next-steps"></a>后续步骤

* [U-SQL 可编程性指南](data-lake-analytics-u-sql-programmability-guide.md)
* [为 Azure Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [教程：Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
* [教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)





<!--HONumber=Jan17_HO1-->


