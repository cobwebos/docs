---
title: 通过 Visual Studio 开始使用 Azure Data Lake Analytics | Microsoft Docs
description: 了解如何安装针对 Visual Studio 的 Data Lake 工具，以及如何开发和测试 U-SQL 脚本。
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/02/2018
ms.author: saveenr, yanacai
ms.openlocfilehash: d0974e3258e0def09fe12d348180dcedf216401c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


了解如何使用 Visual Studio 创建 Azure Data Lake Analytics 帐户、在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定义作业，并将作业提交到 Data Lake Analytics 服务。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

>[!IMPORTANT]
>
>为新的一般数据保护条例 (GDPR)（于 2018 年 5 月 25 日生效）做准备，建议针对 Visual Studio 的 Azure Data Lake 工具的用户升级到版本 2.3.3000.4 或更高版本。 此版本包括基于最新数据保护要求所做的更改。 请注意，以前的版本不可供下载并将弃用。 
>
>**我需要做什么？**
>
>1. 检查是否使用的是针对 Visual Studio 的 Azure Data Lake 工具早于 2.3.3000.4 的版本。 
>   
>   ![检查工具版本](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
>2. 如果版本是早于 2.3.3000.4 的版本，请通过访问下载中心更新针对 Visual Studio 的 Azure Data Lake 工具： 
>    - [对于 Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [对于 Visual Studio 2013 和 Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>先决条件

* **Visual Studio**：支持除 Express 以外的所有版本。
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **用于 .NET 的 Microsoft Azure SDK** 2.7.1 版或更高版本。  使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)进行安装。
* **Data Lake Analytics** 帐户。 若要创建帐户，请参阅[通过 Azure 门户开始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Azure Data Lake 工具

### <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>安装针对 Visual Studio 2017 的 Azure Data Lake 工具

Visual Studio 2017 15.3 或更高版本支持针对 Visual Studio 的 Azure Data Lake 工具。 Visual Studio 安装程序中的**数据存储和处理**以及 **Azure 开发**工作负载包含此工具。 作为 VisualStudio 安装的一部分，启用这两个工作负载中的任何一个。  

按如下所示启用“数据存储和处理”工作负荷：![启用数据存储和处理工作负荷](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

按如下所示启用“Azure 开发”工作负荷：![启用 Azure 开发工作负荷](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

### <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>安装针对 Visual Studio 2013 和 2015 的 Azure Data Lake 工具

[从下载中心](http://aka.ms/adltoolsvs)下载并安装针对 Visual Studio 的 Azure Data Lake 工具。 安装后，请注意：
* “服务器资源管理器” > **Azure**节点包含“Data Lake Analytics”节点。 
* “工具”菜单中有一个“Data Lake”项。

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>连接到 Azure Data Lake Analytics 帐户

1. 打开 Visual Studio。
2. 选择“查看” > “服务器资源管理器”以打开“服务器资源管理器”。
3. 右键单击“Azure”。 然后选择“连接到 Microsoft Azure 订阅”，并按照说明操作。
4. 在“服务器资源管理器”中，选择“Azure” > “Data Lake Analytics”。 将看到 Data Lake Analytics 帐户列表。


## <a name="write-your-first-u-sql-script"></a>编写第一个 U-SQL 脚本

以下文本是一个简单的 U-SQL 脚本。 它定义一个小型数据集，并将该数据集作为名为 `/data.csv` 的文件写入默认 Data Lake Store 中。

```
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

### <a name="submit-a-data-lake-analytics-job"></a>提交 Data Lake Analytics 作业

1. 选择“文件” > “新建” > “项目”。

2. 选择“U-SQL 项目”类型，然后单击“确定”。 Visual Studio 将创建包含 **Script.usql** 文件的解决方案。

3. 将上一个脚本粘贴到 **Script.usql** 窗口中。

4. 在 **Script.usql** 窗口的左上角，指定 Data Lake Analytics 帐户。

    ![提交 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. 在 **Script.usql** 窗口的左上角，选择“提交”。
6. 验证“Analytics 帐户”，然后选择“提交”。 完成提交后，“适用于 Visual Studio 的 Data Lake 工具结果”窗口中会出现提交结果。

    ![提交 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. 若要查看最新作业状态和刷新屏幕，请单击“刷新”。 作业成功时，将显示“作业图”、“元数据操作”、“状态历史记录”和“诊断”：

    ![U-SQL Visual Studio Data Lake Analytics 作业性能图表](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * “作业摘要”显示作业的摘要。   
   * “作业详细信息”显示有关该作业的更具体的信息，包括脚本、资源和顶点。
   * “作业图”直观显示作业的进度。
   * “元数据操作”显示对 U-SQL 目录已执行的所有操作。
   * “数据”显示所有输入和输出。
   * “诊断”提供有助于作业执行和性能优化的高级分析。

### <a name="to-check-job-state"></a>检查作业状态

1. 在“服务器资源管理器”中，选择“Azure” > “Data Lake Analytics”。 
2. 展开 Data Lake Analytics 帐户名。
3. 双击“作业”。
4. 选择之前提交的作业。

### <a name="to-see-the-output-of-a-job"></a>查看作业的输出

1. 在“服务器资源管理器”中，浏览到所提交的作业。
2. 单击“数据”选项卡。
3. 在“作业输出”选项卡中，选择 `"/data.csv"` 文件。

## <a name="next-steps"></a>后续步骤

* [在自己的工作站上运行 U-SQL 脚本进行测试和调试](data-lake-analytics-data-lake-tools-local-run.md)
* [使用 Azure Data Lake Tools for Visual Studio Code 在 U-SQL 作业中调试 C# 代码](data-lake-tools-for-vscode-local-run-and-debug.md)
* [使用用于 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
