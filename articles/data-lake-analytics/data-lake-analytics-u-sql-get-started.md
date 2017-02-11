---
title: "使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本 | Microsoft 文档"
description: "了解如何安装用于 Visual Studio 的 Data Lake 工具，以及如何开发和测试 U-SQL 脚本。 "
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
ms.sourcegitcommit: 5137ccfd2c809fe17cc7fdf06941ebd797288d81
ms.openlocfilehash: 496c3c1cc0f203a58a6f81476393b369e6a76215


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>教程：Azure Data Lake Analytics U-SQL 语言入门
U SQL 是一种统一了 SQL 优点的语言，让你的代码富有表达力，能够处理任何规模的数据。 借助 U-SQL 的可缩放分布式查询功能，可以跨关系存储（如 Azure SQL 数据库）高效分析其中的数据。  通过对读取应用构架、插入自定义逻辑和 UDF 以及包括可扩展性以允许大规模精细化控制，让你能够处理非结构化数据。 若要了解 U-SQL 设计理念的详细信息，请参阅此 [Visual Studio 博客文章](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

从 ANSI SQL 或 T-SQL 处理会有一些差异。 例如，其关键字（如 SELECT）必须所有字母都大写。

它是类型系统，表达式语言位于选择子句中，在这些子句中谓词等均为 C#。
这意味着数据类型为 C# 类型且数据类型使用 C# NULL 语义，并且谓词内的比较操作遵循 C# 语法（例如，== "foo"）。  这也意味着值是完整的.NET 对象，让你可以轻松使用任何方法来操作对象（例如 "f o o o".Split(' ')）。

有关详细信息，请参阅 [U-SQL Reference](http://go.microsoft.com/fwlink/p/?LinkId=691348)（U-SQL 参考）。

### <a name="prerequisites"></a>先决条件
完成[教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。

在本教程中，可以使用以下 U-SQL 脚本运行 Data Lake Analytics 作业：

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

此脚本没有任何转换步骤。 它从名为 **SearchLog.tsv** 的源代码文件中进行读取，将其架构化，并将输出行集返回到名为 **SearchLog-first-u-sql.csv** 的文件。

请注意“持续时间”字段数据类型旁边的问号按钮。 这意味着“持续时间”字段可能为空。

脚本中的一些概念和关键字：

* **行集变量**：生成行集的每个查询表达式可以分配给一个变量。 U-SQL 遵循 T-SQL 变量命名模式，例如，脚本中的 **@searchlog**。
    请注意分配不会强制执行。 它只是为表达式命名，让你能够构建更复杂的表达式。
* **EXTRACT** 让你能够在读取时定义架构。 按列名称和每列 C# 类型名称对指定架构。 它使用**提取程序**，例如，**Extractors.Tsv()** 提取 tsv 文件。 可以开发自定义提程序。
* **OUTPUT** 采用行集并将其序列化。 Outputters.Csv() 将以逗号分隔的文件输出到指定位置。 还可以开发自定义 Outputters。
* 请注意两个路径是相对路径。 也可使用绝对路径。  例如
  
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    必须使用绝对路径来访问链接的存储帐户中的文件。  链接的 Azure 存储帐户中所储存文件的语法是：
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > 当前不支持对含公共 Blob 的 Azure Blob 容器或公共容器的访问权限。
  > 
  > 

## <a name="use-scalar-variables"></a>使用标量变量
也可以使用标量变量简化脚本维护工作。 前面的 U-SQL 脚本也可以如下方式编写：

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

请注意，第二个查询基于第一个行集结果执行操作，因此结果是两个筛选器的组合。 可以重复使用变量名且会从词法上审视这些名称。

## <a name="aggregate-rowsets"></a>聚合行集
U-SQL 为你提供了熟悉的 **ORDER BY****GROUP BY** 和聚合。

以下查询查找每个区域的总持续时间，并按顺序输出前五个持续时间。

U-SQL 行集不为下一个查询保留其顺序。 因此，若要对输出进行排序，需要向 OUTPUT 语句添加 ORDER BY，如下所示：

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

在 SELECT 表达式中，U-SQL ORDER BY 子句需与 FETCH 子句结合。

可使用 U-SQL HAVING 子句来限制满足 HAVING 条件的组的输出：

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


U-SQL 仅支持符合 ANSI 的联接语法：Rowset1 JOIN Rowset2 ON 谓词。 不支持 FROM Rowset1、Rowset2 WHERE 谓词的旧语法。
JOIN 中的谓词必须是相等联接而不是表达式。 如果想要使用表达式，请将其添加到前面行集的 SELECT 子句中。 如果想要执行不同的比较，可以将其移入 WHERE 子句。

## <a name="create-databases-table-valued-functions-views-and-tables"></a>创建数据库、表值函数、视图和表
U-SQL 让你可以使用数据库和架构上下文中的数据。 因此你不必总是读取数据或将其写入文件中。

每个 U-SQL 脚本通过默认数据库 (master) 和默认架构 (DBO) 作为其默认上下文运行。 可以创建自己的数据库和/或架构。 若要更改的上下文，请使用 **USE** 语句来更改上下文。

### <a name="create-a-table-valued-function-tvf"></a>创建表值函数 (TVF)
在前面的 U-SQL 脚本中，你重复使用了 EXTRACT 从同一源文件中进行读取操作。 U-SQL 表值函数让你能够封装数据供将来重复使用。   

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
如果只有一个想要抽象但不希望将其参数化的查询表达式，可以创建视图，而不是表值函数。

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
类似于关系数据库表，U-SQL 让你可以创建具有预定义架构的表或创建表并从填充表的查询中推测其架构（也称为 CREATE TABLE AS SELECT 或CREATE TABLE AS SELECT 或 CTAS）。

以下脚本创建一个数据库和两个表：

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
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>查询表
可以通过与查询数据文件相同的方式查询表（在前面的脚本中创建）。 现在可以引用表名，而不是通过 EXTRACT 创建行集。

修改以前使用的转换脚本，从表中读取：

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

请注意，当前不能在创建表的脚本中的表上运行 SELECT。

## <a name="conclusion"></a>结束语
本教程介绍的内容仅为 U-SQL 的一小部分。 由于本教程范围所限，不能涵盖所有内容，如：

* 使用 CROSS APPLY 将部分字符串、数组和映射解压到行。
* 操作数据的分区集（文件集和分区表）。
* 开发用户定义的运算符，如提取程序、outputters、处理器、用户定义的 C# 聚合函数。
* 使用 U-SQL 开窗函数。
* 管理具有视图、表值函数和存储过程的 U-SQL 代码。
* 在处理的节点上运行任意自定义代码。
* 连接到 Azure SQL 数据库、跨库联合查询、U-SQL 和 Azure Data Lake 数据。

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>让我们知道你的想法
* [提交功能请求](http://aka.ms/adlafeedback)
* [在论坛中获得帮助](http://aka.ms/adlaforums)
* [提供有关 U-SQL 的反馈](http://aka.ms/usqldiscuss)




<!--HONumber=Dec16_HO2-->


