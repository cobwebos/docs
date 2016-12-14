---
title: "调试 U-SQL 作业 | Microsoft Docs"
description: "了解如何使用 Visual Studio 调试 U-SQL 失败的顶点。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4a4cd690645200bc5f208fd0d15fe58537f9b2a4


---
# <a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>在 U-SQL 中为 Data Lake Analytics 作业调试 C# 代码
了解如何使用 Azure Data Lake Visual Studio 工具来调试由于用户代码 bug 而失败的 U-SQL 作业。 

Visual Studio 工具让你可从群集下载已编译的代码和必要的顶点数据，以跟踪和调试失败的作业。

大数据系统通常通过语言（如 Java、C#、Python）提供扩展性模型。许多此类系统提供受限的运行时调试信息，很难调试自定义代码中的运行时错误。 最新的 Visual Studio 工具拥有名为“失败的顶点调试”的功能。 使用此功能，可以将运行时数据从 Azure 下载到本地工作站，以便从云中使用相同的运行时和精确的输入数据来调试失败的自定义 C# 代码。  解决问题后，可以在 Azure 中通过工具重新运行修改后的代码。

有关此功能的视频演示，请参阅 [Debug your custom code in Azure Data Lake Analytics](https://mix.office.com/watch/1bt17ibztohcb)（在 Azure Data Lake Analytics 中调试自定义代码）。

> [!NOTE]
> 如果不具有以下两个窗口升级： [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) 和[适用于 Windows 的通用 C 运行时](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)，Visual Studio 可能会挂起或崩溃。
> 
> 

## <a name="prerequisites"></a>先决条件
* 通读完[入门](data-lake-analytics-data-lake-tools-get-started.md)这篇文章。

## <a name="create-and-configure-debug-projects"></a>创建和配置调试项目
在 Lake Visual Studio 工具中打开失败的作业时，将收到警报。 将在错误选项卡和顶部窗口的黄色警告栏中显示详细的错误信息。 

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 下载顶点](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**下载顶点并创建调试解决方案**

1. 在 Visual Studio 中打开失败的 U-SQL 作业。
2. 单击“下载”以下载所有必需资源和输入流。 如果下载失败，单击“重试”。
3. 下载完成后，单击“打开”，创建本地调试项目。 将创建名为 **VertexDebug** 的新 Visual Studio 解决方案和名为 **LocalVertexHost** 的空项目。

如果在 U-SQL 代码隐藏文件 (Script.usql.cs) 中使用用户定义的运算符，必须创建一个具有用户定义运算符代码的类库 C# 项目，并包括 VertexDebug 解决方案中的项目。

如果对 Data Lake Analytics 数据库注册了.dll 程序集，必须将程序集的源代码添加到 VertexDebug 解决方案中。

如果为 U-SQL 代码创建了单独的 C# 类库并对 Data Lake Analytics 数据库注册了.dll 程序集，需要将程序集的源 C# 项目添加到 VertexDebug 解决方案中。

在某些极少数情况下，你会在初始解决方案的 U-SQL 代码隐藏文件 (Script.usql.cs) 中使用用户定义的运算符。 如果想要使其正常运行，需要创建一个包含源代码的 C# 库，并将程序集名称更改为在群集中注册的名称。 可以检查群集中运行的脚本，获取在群集中注册的程序集名称。 可以打开 U-SQL 作业，然后单击作业面板中的“脚本”来执行此操作。 

**配置解决方案**

1. 在解决方案资源管理器上，右键单击刚创建的 C# 项目，然后单击“属性”。
2. 将输出路径设置为 LocalVertexHost 项目工作目录路径。 可以通过 LocalVertexHost 属性获取 LocalVertexHost 项目工作目录路径。
3. 生成 C# 项目，从而将.pdb 文件放入 LocalVertexHost 项目工作目录，也可以手动将.pdb 文件复制到此文件夹。
4. 在“异常设置”中，检查“公共语言运行时异常”：

![Azure Data Lake Analytics U-SQL 调试 Visual Studio 设置](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

## <a name="debug-the-job"></a>调试作业
通过下载顶点并配置环境创建调试解决方案后，就可以开始调试 U-SQL 代码了。

1. 从“解决方案资源管理器”中，右键单击刚创建的 **LocalVertexHost** 项目，指向“调试”，然后单击“启动新实例”。 LocalVertexHost 必须设置为启动项目。 第一次可以看看以下有可能会忽略的消息。 一分钟后就可进入调试屏幕。
   
   ![Azure Data Lake Analytics U-SQL 调试 Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)
2. 使用基于 Visual Studio 的调试体验（监视、变量等）解决问题。 
3. 确定出现问题后，修复代码，并在再次测试之前重新生成 C# 项目，直到解决所有问题。 调试成功完成后，输出窗口将显示以下消息 
   
     程序“LocalVertexHost.exe”已退出，代码 0 (0x0)。

## <a name="resubmit-the-job"></a>提交作业
U-SQL 代码调试完成后，可以重新提交失败的作业。

1. 将新.dll 程序集注册到 ADLA 数据库。
   
   1. 从 Data Lake Visual Studio 工具中的“服务器资源管理器/云资源管理器”上，展开“数据库”节点 
   2. 右键单击“要注册的程序集”程序集。 
   3. 将新.dll 程序集注册到 ADLA 数据库。
2. 或者，将 C# 代码复制到 script.usql.cs — C# 代码隐藏文件。
3. 重新提交作业。

## <a name="next-steps"></a>后续步骤
* [教程：Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
* [教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [为 Azure Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)




<!--HONumber=Nov16_HO3-->


