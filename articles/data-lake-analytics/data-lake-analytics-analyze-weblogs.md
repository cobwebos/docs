---
title: "使用 Azure Data Lake Analytics 分析网站日志 |Microsoft Docs"
description: "了解如何使用 Data Lake Analytics 分析网站日志。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 52d19297ae5c34f9daf5e42250a53a78e0168192
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>使用 Azure Data Lake Analytics 分析网站日志
了解如何使用 Data Lake Analytics 分析网站日志，尤其是找出尝试访问网站时哪些引用发生了错误。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2015 或 Visual Studio 2013**。
* **[针对 Visual Studio 的 Data Lake 工具](http://aka.ms/adltoolsvs)**。

    安装好适用于 Visual Studio 的 Data Lake 工具后，将在 Visual Studio 的“工具”菜单中看到“Data Lake”项：

    ![U SQL Visual Studio 菜单](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Data Lake Analytics 和适用于 Visual Studio 的 Data Lake 工具的基本知识**。 如要入门，请参阅：

  * [通过适用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* **Data Lake Analytics 帐户**  请参阅[创建 Azure Data Lake Analytics 帐户](data-lake-analytics-get-started-portal.md)。
* **安装示例数据。** 在 Azure 门户中，打开 Data Lake Analytics 帐户，再依次单击左侧菜单上的“示例脚本”和“复制示例数据”。 

## <a name="connect-to-azure"></a>连接到 Azure
必须先连接到 Azure，才能生成并测试任意 U SQL 脚本。

**连接到 Data Lake Analytics**

1. 打开 Visual Studio。
2. 单击“Data Lake”>“选项和设置”。
3. 如果有人已登录，请按照说明单击“登录”或“更改用户”。
4. 单击“确定”以关闭“选项和设置”对话框。

**浏览 Data Lake Analytics 帐户**

1. 从 Visual Studio 中，通过按 **CTRL+ALT+S**打开“服务器资源管理器” 。
2. 在“服务器资源管理器”中，展开“Azure”，并展开“Data Lake Analytics”。 如果有 Data Lake Analytics 帐户，将看到其列表。 无法从 Visual Studio 创建 Data Lake Analytics 帐户。 若要创建帐户，请参阅 [Get Started with Azure Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)（使用 Azure 门户开始 Azure Data Lake Analytics 入门）或 [Get Started with Azure Data Lake Analytics using Azure PowerShell](data-lake-analytics-get-started-powershell.md)（使用 Azure PowerShell 开始 Azure Data Lake Analytics 入门）。

## <a name="develop-u-sql-application"></a>开发 U SQL 应用程序
U-SQL 应用程序主要是 U-SQL 脚本。 若要了解有关 U SQL 的详细信息，请参阅 [Get started with U-SQL](data-lake-analytics-u-sql-get-started.md)（U-SQL 入门）。

可以向应用程序中添加用户定义的运算符。  有关详细信息，请参阅 [Develop U-SQL user defined operators for Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-user-defined-operators.md)（为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符）。

**创建并提交 Data Lake Analytics 作业**

1. 单击“文件”>“新建”>“项目”。
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
            DISTRIBUTED BY HASH(Year)
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
6. 切换回“提交”按钮旁边的第一个 U-SQL 脚本，指定 Analytics 帐户。
7. 在“解决方案资源管理器”中，右键单击 “Script.usql”，并单击“生成脚本”。 验证“输出”窗格中的结果。
8. 在“解决方案资源管理器”中，右键单击 “Script.usql”，并单击“提交脚本”。
9. 验证“Analytics 帐户” 是否是想要运行作业的帐户，并单击“提交”。 完成提交后，“适用于 Visual Studio 的 Data Lake 工具结果”窗口中会出现提交结果和作业链接。
10. 等待作业成功完成。  如果作业失败，最可能的原因是缺少源文件。  请参阅本教程中的先决条件部分。 有关其他疑难解答信息，请参阅 [Monitor and troubleshoot Azure Data Lake Analytics jobs](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)（对 Azure Data Lake Analytics 作业进行监视和疑难解答）。

    作业完成后，将看到以下屏幕：

    ![Data Lake Analytics 分析网络日志网站日志](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. 现在请为 **Script1.usql** 重复步骤 7-10。

**查看作业输出**

1. 在“服务器资源管理器”中依次展开 “Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“存储帐户”，右键单击默认 Data Lake Storage 帐户，并单击“资源管理器”。
2. 双击“示例”打开文件夹，并双击“输出”。
3. 双击 “UnsuccessfulResponsees.log”。
4. 也可以双击该作业图形视图中的输出文件直接导航到输出。

## <a name="see-also"></a>另请参阅
若要借助不同的工具开始使用 Data Lake Analytics，请参阅：

* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [通过 Azure PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 .NET SDK 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-net-sdk.md)
