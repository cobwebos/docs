---
title: "使用 Azure Data Lake Analytics 分析网站日志 |Microsoft Docs"
description: "了解如何使用 Data Lake Analytics 分析网站日志。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: e820ca068bd9be151c4241bb233806847855933c


---
# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>教程：使用 Azure Data Lake Analytics 分析网站日志
了解如何使用 Data Lake Analytics 分析网站日志，尤其是找出尝试访问网站时哪些引用发生了错误。

> [!NOTE]
> 如果只是想要了解应用程序的工作，浏览 [使用 Data Lake Analytics 交互式教程](data-lake-analytics-use-interactive-tutorials.md)更节省时间。 本教程基于相同的方案和示例代码。 本教程的目的是为开发人员提供端到端创建和运行 Data Lake Analytics 应用程序的体验。
>
>

## <a name="prerequisites"></a>先决条件：
* **Visual Studio 2015、Visual Studio 2013 Update 4 或安装有 Visual C++ 的 Visual Studio 2012**。
* **用于 .NET 的 Microsoft Azure SDK 2.5 或更高版本**。  可以使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)安装它。
* **[适用于 Visual Studio 的 Data Lake 工具](http://aka.ms/adltoolsvs)**。

    安装好适用于 Visual Studio 的 Data Lake 工具后，将在 Visual Studio 中看到 **Data Lake** 菜单：

    ![U SQL Visual Studio 菜单](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Data Lake Analytics 和适用于 Visual Studio 的 Data Lake 工具的基本知识**。 如要入门，请参阅：

  * [通过 Azure 门户实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。
  * [通过适用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* **Data Lake Analytics 帐户**  请参阅[创建 Azure Data Lake Analytics 帐户](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account)。

    Data Lake 工具不支持创建 Data Lake Analytics 帐户。  因此必须使用 Azure 门户、Azure PowerShell、.NET SDK 或 Azure CLI 创建该帐户。
* **将示例数据上传到 Data Lake Analytics 帐户。** 请参阅[复制示例数据文件](data-lake-analytics-get-started-portal.md#prepare-source-data)。

    若要运行 Data Lake Analytics 作业，需要提供一些数据。 尽管 Data Lake 工具支持上载数据，但为了方便理解本教程，这里将使用门户来上载示例数据。

## <a name="connect-to-azure"></a>连接到 Azure
必须先连接到 Azure，才能生成并测试任意 U SQL 脚本。

**连接到 Data Lake Analytics**

1. 打开 Visual Studio。
2. 在 “Data Lake” 菜单上，单击“选项和设置”。
3. 如果有人已登录，请按照说明单击“登录”或“更改用户”。
4. 单击“确定”以关闭“选项和设置”对话框。

**浏览 Data Lake Analytics 帐户**

1. 从 Visual Studio 中，通过按 **CTRL+ALT+S**打开“服务器资源管理器” 。
2. 在“服务器资源管理器”中，展开“Azure”，然后展开“Data Lake Analytics”。 如果有 Data Lake Analytics 帐户，将看到其列表。 无法从 Visual Studio 创建 Data Lake Analytics 帐户。 若要创建帐户，请参阅 [Get Started with Azure Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)（使用 Azure 门户开始 Azure Data Lake Analytics 入门）或 [Get Started with Azure Data Lake Analytics using Azure PowerShell](data-lake-analytics-get-started-powershell.md)（使用 Azure PowerShell 开始 Azure Data Lake Analytics 入门）。

## <a name="develop-u-sql-application"></a>开发 U SQL 应用程序
U-SQL 应用程序主要是 U-SQL 脚本。 若要了解有关 U SQL 的详细信息，请参阅 [Get started with U-SQL](data-lake-analytics-u-sql-get-started.md)（U-SQL 入门）。

可以向应用程序中添加用户定义的运算符。  有关详细信息，请参阅 [Develop U-SQL user defined operators for Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-user-defined-operators.md)（为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符）。

**创建并提交 Data Lake Analytics 作业**

1. 在“文件”菜单中，单击“新建”，然后单击“项目”。
2. 选择“U-SQL 项目”类型。

    ![新建 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. 单击 **“确定”**。 Visual Studio 将创建包含 Script.usql 文件的解决方案。
4. 在 Script.usql 文件中输入以下脚本：

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    若要了解 U-SQL，请参阅 [Get started with Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md)（Azure Data Lake Analytics U-SQL 语言入门）。    
5. 向项目中添加新的 U-SQL 脚本并输入以下命令：

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. 切换回“提交”按钮旁边的第一个 U-SQL 脚本，指定你的 Analytics 帐户。
7. 在“解决方案资源管理器”中，右键单击 “Script.usql”，然后单击“生成脚本”。 验证“输出”窗格中的结果。
8. 在“解决方案资源管理器”中，右键单击 “Script.usql”，然后单击“提交脚本”。
9. 验证“Analytics 帐户” 是否是你想要运行作业的帐户，然后单击“提交”。 完成提交后，“适用于 Visual Studio 的 Data Lake 工具结果”窗口中会出现提交结果和作业链接。
10. 等待作业成功完成。  如果作业失败，最可能的原因是缺少源文件。  请参阅本教程中的先决条件部分。 有关其他疑难解答信息，请参阅 [Monitor and troubleshoot Azure Data Lake Analytics jobs](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)（对 Azure Data Lake Analytics 作业进行监视和疑难解答）。

    作业完成后，将看到以下屏幕：

    ![Data Lake Analytics 分析网络日志网站日志](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. 现在请为 **Script1.usql** 重复步骤 7-10。

> [!NOTE]
> 对于同一个脚本中已创建或修改的 U-SQL 表，无法执行读取或写入操作。  这就是为什么此示例使用两个脚本。
>
>

**查看作业输出**

1. 在“服务器资源管理器”中依次展开 “Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“存储帐户”，右键单击默认 Data Lake Storage 帐户，然后单击“资源管理器”。
2. 双击“示例”打开文件夹，然后双击“输出”。
3. 双击 “UnsuccessfulResponsees.log”。
4. 也可以双击该作业图形视图中的输出文件直接导航到输出。

## <a name="see-also"></a>另请参阅
若要借助不同的工具开始使用 Data Lake Analytics，请参阅：

* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [通过 Azure PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 .NET SDK 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-net-sdk.md)

查看更多开发主题：

* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md)
* [为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)



<!--HONumber=Nov16_HO3-->


