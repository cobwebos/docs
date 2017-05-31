---
title: "在 Azure Data Lake Analytics 中使用 U-SQL 联接组合行集 | Microsoft Docs"
description: "了解 U-SQL 如何使用联接运算符在 Azure Data Lake Analytics 中组合行集。"
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
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5ffc7116812e9f18f972eac2daff7935b00891f1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="combine-rowsets-with-u-sql-joins"></a>使用 U-SQL 联接组合行集

U-SQL 提供常见的联接运算符，例如 INNER JOIN、LEFT/RIGHT/FULL OUTER JOIN、SEMI JOIN 来联接表和任何行集（甚至包括那些从文件生成的行集）。

以下脚本将 ``@searchlog`` 与播发印象日志相联接，提供给定日期查询字符串的播发。

```
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
```

U-SQL 仅支持符合 ANSI 的联接语法：Rowset1 JOIN Rowset2 ON 谓词。 _不_支持 FROM Rowset1, Rowset2 WHERE 谓词的旧语法。
JOIN 中的谓词必须是相等联接而不是表达式。 如果想要使用表达式，请将其添加到前面行集的 SELECT 子句中。 如果想要执行不同的比较，可以将其移入 WHERE 子句。

### <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)


