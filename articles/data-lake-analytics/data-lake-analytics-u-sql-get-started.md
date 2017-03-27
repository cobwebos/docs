---
title: "教程：Azure Data Lake Analytics U-SQL 语言入门 | Microsoft 文档"
description: "使用本教程了解 Azure Data Lake Analytics U-SQL 语言。"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: dc8c7beaf5b8e8d4f5467ffe22390c41f446d787
ms.lasthandoff: 03/04/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>教程：Azure Data Lake Analytics U-SQL 语言入门
U-SQL 是一种结合了 SQL 优点的语言，让你的代码富有表达力，能够处理任何规模的数据。 通过 U-SQL 的可缩放分布式查询功能，可以跨关系存储（如 Azure SQL 数据库）高效分析其中的数据。 使用 U-SQL，可以通过在读取和插入自定义逻辑和 UDF 时应用架构来处理非结构化数据。 此外，U-SQL 还提供可扩展性，让你更精细地控制大规模执行的方式。 若要详细了解 U-SQL 背后的设计理念，请参阅 Visual Studio 博客文章 [U-SQL（使大数据处理更轻松的语言）简介](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

U-SQL 在某些方面与 ANSI SQL 或 T-SQL 不同。 例如，SELECT 这样的关键字必须全部字母大写。

 其类型系统及 SELECT 子句和 WHERE 谓词中的表达式语言均为 C#。 这意味着数据类型为 C# 类型且这些类型使用 C# NULL 语义，并且谓词内的比较操作遵循 C# 语法（例如，a == "foo"）。 这也意味着值是完整的.NET 对象，因此可以轻松使用任何方法来操作对象（例如 "f o o o".Split(' ')）。

有关 U-SQL 的详细信息，请参阅 [U-SQL Language Reference](http://go.microsoft.com/fwlink/p/?LinkId=691348)（U-SQL 语言参考）。

### <a name="prerequisites"></a>先决条件
如果尚未这样做，请阅读并完成[教程：使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。 完成该教程后，请返回到本文。

在本教程中，可以使用以下 U-SQL 脚本运行 Azure Data Lake Analytics 作业：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

此脚本没有任何转换步骤。 它从名为 SearchLog.tsv 的源文件中进行读取，将其架构化，并将行集重新写入到名为 SearchLog-first-u-sql.csv 的文件。

请注意“持续时间”字段中数据类型旁边的问号。 它表示“持续时间”字段可以为 null。

在脚本中，可以找到以下概念和关键字：

* 行集变量：生成行集的每个查询表达式都可以分配给一个变量。 在脚本中，U-SQL 遵循 T-SQL 变量命名模式（例如 @searchlog）。

 >[!NOTE]
 >分配不会强制执行。 它只是为表达式命名，以便可以构建更复杂的表达式。
* EXTRACT：通过使用此关键字，可以在读取时定义架构。 按列名称和每列 C# 类型名称对指定架构。 架构使用所谓的提取器（例如 Extractors.Tsv()）来提取 .tsv 文件。 可以开发自定义提程序。
* OUTPUT：此关键字获取行集，并将其序列化。 Outputters.Csv() 将逗号分隔的文件写入到指定位置。 还可以开发自定义输出器。

 >[!NOTE]
 >这两个路径是相对路径。 也可使用绝对路径。 例如：    
 >     adl://\<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >必须使用绝对路径来访问链接存储帐户中的文件。  链接 Azure 存储帐户中存储的文件的语法是：wasb://\<BlobContainerName>@\<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >当前不支持对含公共 Blob 的 Azure Blob 存储容器或公共容器的访问权限。

## <a name="use-scalar-variables"></a>使用标量变量
也可以使用标量变量简化脚本维护工作。 前面的 U-SQL 脚本也可以按如下方式编写：

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>转换行集
使用 **SELECT** 转换行集：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 子句使用 [C# 布尔表达式](https://msdn.microsoft.com/library/6a71f45d.aspx)。 可以使用 C# 表达式语言执行自己的表达式和函数。 将这些表达式和函数与逻辑“与 (And)”和“或 (Or)”结合，甚至可以执行更复杂的筛选操作。

以下脚本使用 DateTime.Parse() 方法和一个“与”。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >第二个查询基于第一个行集结果执行操作，这将创建两个筛选器的组合。 还可以重复使用变量名，将从词法上审视这些名称。

## <a name="aggregate-rowsets"></a>聚合行集
U-SQL 为你提供了熟悉的 ORDER BY、GROUP BY 和聚合。

以下查询查找每个区域的总持续时间，然后按顺序显示前五个持续时间。

U-SQL 行集不为下一个查询保留其顺序。 因此，若要对输出进行排序，需要向 OUTPUT 语句添加 ORDER BY：

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL ORDER BY 子句必须与 SELECT 表达式中的 FETCH 子句结合使用。

可使用 U-SQL HAVING 子句将输出限制为满足 HAVING 条件的组：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>联接数据
U-SQL 提供常见的联接运算符，例如 INNER JOIN、LEFT/RIGHT/FULL OUTER JOIN、SEMI JOIN 来联接表和任何行集（甚至包括那些从文件生成的行集）。

以下脚本将搜索日志与播发印象日志联接起来，提供给定日期查询字符串的播发。

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL 仅支持符合 ANSI 的联接语法：Rowset1 JOIN Rowset2 ON 谓词。 _不_支持 FROM Rowset1, Rowset2 WHERE 谓词的旧语法。
JOIN 中的谓词必须是相等联接而不是表达式。 如果想要使用表达式，请将其添加到前面行集的 SELECT 子句中。 如果想要执行不同的比较，可以将其移入 WHERE 子句。

## <a name="create-databases-table-valued-functions-views-and-tables"></a>创建数据库、表值函数、视图和表
在 U-SQL 中，可以在数据库和架构的上下文中使用数据，并非始终需要读取或写入文件。

每个 U-SQL 脚本通过默认数据库 (master) 和默认架构 (DBO) 作为其默认上下文运行。 可以创建自己的数据库或架构。 若要更改上下文，请使用 USE 语句。

### <a name="create-a-tvf"></a>创建 TVF
在前面的 U-SQL 脚本中，重复使用了 EXTRACT 从同一源文件中进行读取操作。 使用 U-SQL 表值函数 (TVF)，可以封装数据供将来重复使用。  

以下脚本在默认数据库和架构中创建名为 *Searchlog()* 的 TVF：

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

以下脚本演示如何使用在前面的脚本中定义的 TVF：

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>创建视图
如果只有一个想要抽象但不希望从其创建参数的查询表达式，可以创建视图，而不是表值函数。

以下脚本在默认数据库和架构中创建名为 *SearchlogView* 的视图：

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

以下脚本演示如何使用定义的视图：

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>创建表
与关系数据库表一样，使用 U-SQL 可以创建具有预定义架构的表或创建可从填充表的查询推测架构的表（也称为 CREATE TABLE AS SELECT 或 CTAS）。

使用以下脚本创建一个数据库和两个表：

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>查询表
可以通过与查询数据文件相同的方式查询表（例如，在前面的脚本中创建的表）。 现在可以引用表名，而不是通过 EXTRACT 创建行集。

若要从表中读取，请修改前面使用的转换脚本：

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >目前，不能在创建表的同一脚本中对该表上运行 SELECT。

## <a name="conclusion"></a>结束语
本教程仅介绍一小部分 U-SQL。 由于范围受限制，本教程未讨论 U-SQL 的许多其他优点。 例如，你可以：

* 使用 CROSS APPLY 将部分字符串、数组和映射解压到行。
* 操作数据的分区集（文件集和分区表）。
* 开发用户定义的运算符，如提取器、输出器、处理器和用户定义的 C# 聚合函数。
* 使用 U-SQL 开窗函数。
* 管理使用视图、表值函数和存储过程的 U-SQL 代码。
* 在处理的节点上运行任意自定义代码。
* 连接到 SQL 数据库、跨库联合查询、U-SQL 和 Azure Data Lake 数据。

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>让我们知道你的想法
* [提交功能请求](http://aka.ms/adlafeedback)
* [在论坛中获得帮助](http://aka.ms/adlaforums)
* [提供有关 U-SQL 的反馈](http://aka.ms/usqldiscuss)

