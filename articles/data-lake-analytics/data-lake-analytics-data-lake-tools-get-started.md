---
title: 查询 Azure Data Lake Analytics-Visual Studio
description: 了解如何安装针对 Visual Studio 的 Data Lake 工具，以及如何开发和测试 U-SQL 脚本。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315768"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake 和流分析工具包括与两项 Azure 服务相关的功能：Azure Data Lake Analytics 和 Azure 流分析。 有关 Azure 流分析方案的详细信息，请参阅[适用于 Visual Studio 的 Azure 流分析工具](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md)。

本文介绍如何使用 Visual Studio 创建 Azure Data Lake Analytics 帐户。 可以在[U SQL](data-lake-analytics-u-sql-get-started.md)中定义作业，并将作业提交到 Data Lake Analytics 服务。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

> [!IMPORTANT]
> 建议升级到针对 Visual Studio 的 Azure Data Lake 工具2.3.3000.4 或更高版本。 以前的版本不再可以下载，现已弃用。
>
> 1. 检查是否使用的是针对 Visual Studio 的 Azure Data Lake 工具早于 2.3.3000.4 的版本。
>
>    ![检查工具版本](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. 如果版本是早于 2.3.3000.4 的版本，请通过访问下载中心更新针对 Visual Studio 的 Azure Data Lake 工具：
>    - [适用于 Visual Studio 2017 和2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [对于 Visual Studio 2013 和 Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>先决条件

* **Visual Studio**：除 Express 之外的所有版本都受支持。

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Microsoft Azure SDK for .NET 2.7.1 版或更高版本。 使用 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)进行安装。
* Data Lake Analytics 帐户。 若要创建帐户，请参阅[通过 Azure 门户开始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Azure Data Lake 工具

本教程需要安装针对 Visual Studio 的 Data Lake 工具。 有关详细信息，请参阅[安装适用于 Visual Studio Data Lake 工具](data-lake-analytics-data-lake-tools-install.md)。

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>连接到 Azure Data Lake Analytics 帐户

1. 打开 Visual Studio。

1. 通过选择 "**查看** > **服务器资源管理器**" 打开**服务器资源管理器**。

1. 右键单击 " **Azure**"，然后选择 "**连接到 Microsoft Azure 订阅**"。 在 "**登录到你的帐户**" 中，按照说明进行操作。

1. 在**服务器资源管理器**中，选择 " **Azure** > **Data Lake Analytics**"。 将看到 Data Lake Analytics 帐户列表。

## <a name="write-your-first-u-sql-script"></a>编写第一个 U-SQL 脚本

以下文本是一个简单的 U-SQL 脚本。 它定义一个小型数据集，并将该数据集作为名为 `/data.csv` 的文件写入默认 Data Lake Store 中。

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>提交 Data Lake Analytics 作业

1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。

1. 选择 " **"，然后**选择 "**下一步**"。 在 "**配置新项目**" 中，选择 "**创建**"。

   Visual Studio 将创建包含**script.usql**文件的解决方案。

1. 粘贴脚本，将[第一个 U SQL 脚本写入](#write-your-first-u-sql-script)" **script.usql** " 窗口。

1. 在**解决方案资源管理器**中，右键单击**script.usql**，然后选择 "**提交脚本**"。

1. 在 "**提交作业**" 中，选择 Data Lake Analytics 帐户并选择 "**提交**"。

   ![提交 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

作业提交后，将打开“作业视图”选项卡以显示作业进度。

* “作业摘要”显示作业的摘要。
* “作业图”直观显示作业的进度。
* “元数据操作”显示对 U-SQL 目录已执行的所有操作。
* “数据”显示所有输入和输出。
* **状态历史记录**显示时间线和状态详细信息。
* **AU 分析**显示作业中使用了多少个澳大利亚，并探索了不同 AU 分配策略的模拟。
* “诊断”提供有助于作业执行和性能优化的高级分析。

![U-SQL Visual Studio Data Lake Analytics 作业性能图表](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

若要查看最新的作业状态和刷新屏幕，请选择 "**刷新**"。

## <a name="check-job-status"></a>检查作业状态

1. 在**服务器资源管理器**中，选择 " **Azure** > **Data Lake Analytics**"。

1. 展开 Data Lake Analytics 帐户名。

1. 双击“作业”。

1. 选择之前提交的作业。

## <a name="see-the-job-output"></a>查看作业输出

1. 在**服务器资源管理器**中，浏览到所提交的作业。

1. 单击“数据”选项卡。

1. 在“作业输出”选项卡中，选择 `"/data.csv"` 文件。

## <a name="next-steps"></a>后续步骤

* [在自己的工作站上运行 U-SQL 脚本进行测试和调试](data-lake-analytics-data-lake-tools-local-run.md)
* [使用 Azure Data Lake Tools for Visual Studio Code 在 U-SQL 作业中调试 C# 代码](data-lake-tools-for-vscode-local-run-and-debug.md)
* [使用针对 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
