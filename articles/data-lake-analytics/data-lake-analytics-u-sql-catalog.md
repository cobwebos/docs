---
title: "U-SQL 目录入门 | Microsoft Docs"
description: "了解如何使用 U-SQL 目录共享代码和数据。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 08364c6c7bea53807844e3b1cc327dc3742e0487
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-the-u-sql-catalog"></a>U-SQL 目录入门

## <a name="create-a-tvf"></a>创建 TVF

在前面的 U-SQL 脚本中，重复使用了 EXTRACT 从同一源文件中进行读取操作。 使用 U-SQL 表值函数 (TVF)，可以封装数据供将来重复使用。  

以下脚本在默认数据库和架构中创建名为 `Searchlog()` 的 TVF：

```
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
```

以下脚本演示如何使用在前面的脚本中定义的 TVF：

```
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
```

## <a name="create-views"></a>创建视图

如果具有单个查询表达式而不是 TVF，可使用 U-SQL 视图封装该表达式。

以下脚本在默认数据库和架构中创建名为 `SearchlogView` 的视图：

```
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
```

以下脚本演示如何使用定义的视图：

```
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
```

## <a name="create-tables"></a>创建表
与关系数据库表一样，使用 U-SQL 可以创建具有预定义架构的表或创建可从填充表的查询推测架构的表（也称为 CREATE TABLE AS SELECT 或 CTAS）。

使用以下脚本创建一个数据库和两个表：

```
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
```

## <a name="query-tables"></a>查询表
可以通过与查询数据文件相同的方式查询表（例如，在前面的脚本中创建的表）。 现在可以引用表名，而不是通过 EXTRACT 创建行集。

若要从表中读取，请修改前面使用的转换脚本：

```
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
```

 >[!NOTE]
 >目前，不能在创建表的同一脚本中对该表上运行 SELECT。

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

