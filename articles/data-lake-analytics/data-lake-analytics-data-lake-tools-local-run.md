---
title: "使用本地运行和 Azure Data Lake U-SQL SDK 来测试及调试 U-SQL 作业 | Microsoft 文档"
description: "了解如何使用用于 Visual Studio 的 Azure Data Lake 工具和 Azure Data Lake U-SQL SDK 来测试及调试本地工作站上的 U-SQL 作业。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>使用本地运行和 Azure Data Lake U-SQL SDK 来测试及调试 U-SQL 作业

与在 Azure Data Lake 服务中一样，可以使用用于 Visual Studio 的 Azure Data Lake 工具和 Azure Data Lake U-SQL SDK 在工作站上运行 U-SQL 作业。 这两个本地运行功能可节省测试和调试 U-SQL 作业的时间。

## <a name="understand-the-data-root-folder-and-the-file-path"></a>了解数据根文件夹和文件路径

本地运行和 U-SQL SDK 都需要数据根文件夹。 数据根文件夹是本地计算帐户的“本地存储”。 它相当于 Data Lake Analytics 帐户的 Azure Data Lake Store 帐户。 切换到不同数据根文件夹与切换到不同存储帐户的操作相同。 如果想要使用不同的数据根文件夹访问一般的共享数据，则必须在脚本中使用绝对路径。 或者，在数据根文件夹下创建指向共享数据的文件系统符号链接（例如，NTFS 上的 **mklink**）。

数据根文件夹的用途：

- 存储元数据，包括数据库、表、表值函数 (TVF) 和程序集。
- 查找在 U-SQL 中定义为相对路径的输入和输出路径。 使用相对路径可更轻松地将 U-SQL 项目部署到 Azure。

可在 U-SQL 脚本中使用相对路径和本地绝对路径。 相对路径相对于指定的数据根文件夹路径。 建议使用“/”作为路径分隔符，使脚本与服务器端兼容。 下面是一些相对路径及其等效绝对路径的示例。 在这些示例中，C:\LocalRunDataRoot 是数据根文件夹。

|相对路径|绝对路径|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>从 Visual Studio 使用本地运行

用于 Visual Studio 的 Data Lake 工具在 Visual Studio 中提供 U-SQL 本地运行体验。 使用此功能可以：

- 本地运行 U-SQL 脚本以及 C# 程序集。
- 本地调试 C# 程序集。
- 从服务器资源管理器创建、查看和删除 U-SQL 目录（本地数据库、程序集、架构和表）。 还可以通过服务器资源管理器查找本地目录。

    ![用于 Visual Studio 的 Data Lake 工具本地运行本地目录](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake 工具安装程序创建要用作默认数据根文件夹的 C:\LocalRunRoot 文件夹。 默认本地运行并行度为 1。

### <a name="to-configure-local-run-in-visual-studio"></a>在 Visual Studio 中配置本地运行

1. 打开 Visual Studio。
2. 打开“服务器资源管理器”。
3. 展开“Azure” > “Data Lake Analytics”。
4. 单击“Data Lake”菜单，并单击“选项和设置”。
5. 在左侧树中，依次展开“Azure Data Lake”、“常规”。

    ![用于 Visual Studio 的 Data Lake 工具本地运行配置设置](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

执行本地运行需要一个 Visual Studio U-SQL 项目。 此部分不同于从 Azure 运行 U-SQL 脚本。

### <a name="to-run-a-u-sql-script-locally"></a>本地运行 U-SQL 脚本
1. 从 Visual Studio，打开 U-SQL 项目。   
2. 在解决方案资源管理器中，右键单击 U-SQL 脚本，并单击“提交脚本”。
3. 选择“(本地)”作为本地运行脚本的 Analytics 帐户。
还可在脚本窗口顶部单击“(本地)”帐户，并单击“提交”（或使用 Ctrl + F5 快捷键）。

    ![用于 Visual Studio 的 Data Lake 工具本地运行提交作业](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>在本地调试脚本和 C# 程序集

无需将 C# 程序集提交并注册到 Azure Data Lake Analytics 服务即可对其进行调试。 可以在两个代码隐藏文件和引用的 C# 项目中设置断点。

#### <a name="to-debug-local-code-in-code-behind-file"></a>在代码隐藏文件中调试本地代码

1. 在代码隐藏文件中设置断点。
2. 按 F5 在本地调试脚本。

> [!NOTE]
   > 以下过程仅适用于 Visual Studio 2015。 在旧版 Visual Studio 中，可能需要手动添加 pdb 文件。  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>在引用的 C# 项目中调试本地代码

1. 创建 C# 程序集项目，生成该项目以生成输出 dll。
2. 使用 U-SQL 语句注册该 dll：

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. 在 C# 代码中设置断点。
4. 按 F5，在本地调试引用 C# dll 的脚本。

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>从 Data Lake U-SQL SDK 使用本地运行

除使用 Visual Studio 本地运行 U-SQL 脚本外，还可使用 Azure Data Lake U-SQL SDK 通过命令行及编程接口本地运行 U-SQL 脚本。 使用这些方法还可以缩放 U-SQL 本地测试。

深入了解 [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)。


## <a name="next-steps"></a>后续步骤

* 若要查看更复杂的查询，请参阅 [Analyze website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)（使用 Azure Data Lake Analytics 分析网站日志）。
* 若要查看作业详细信息，请参阅 [Use Job Browser and Job View for Azure Data Lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md)（对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图）。
* 若要使用顶点执行视图，请参阅 [Use the Vertex Execution View in Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)（使用用于 Visual Studio 的 Data Lake 工具中的顶点执行视图）。
