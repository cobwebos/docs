---
title: "在 Azure Data Lake Analytics 中使用 R 扩展 U-SQL 脚本 | Microsoft Docs"
description: "了解如何在 U-SQL 脚本中运行 R 代码"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: zh-cn
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>教程：使用 R 扩展 U-SQL 入门

以下示例演示了用于部署 R 代码的基本步骤：
* 使用 REFERENCE ASSEMBLY 语句为 U-SQL 脚本启用 R 扩展。
* 使用 REDUCE 操作对某个键的输入数据进行分区。
* U-SQL 的 R 扩展包括内置简化器 (Extension.R.Reducer)，可在分配给简化器的每个顶点上运行 R 代码。 
* 名为 inputFromUSQL 和 outputToUSQL 的专用命名数据框分别用于在 USQL 和 R 之间传递数据。已修复输入和输出数据框标识符名称（即，用户无法更改这些预定义的输入和输出数据框标识符名称）。


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>R 如何与 U-SQL 集成

### <a name="datatypes"></a>数据类型
* U-SQL 中的字符串和数字列按原样在 R 数据框和 U-SQL 之间转换 [支持的类型：双精度型、字符串、布尔值、整数、字节]。
* U-SQL 不支持因素数据类型。
* byte[] 必须序列化为 base64 编码的字符串。
* U-SQL 创建 R 输入数据框或将化简器参数 stringsAsFactors 设为 true 后，U-SQL 字符串可以转换为 R 代码中的因素。

### <a name="schemas"></a>架构
* U-SQL 数据集不能有重复的列名称。
* U-SQL 数据集列名称必须是字符串。
* U-SQL 和 R 脚本中的列名称必须相同。
* 只读列不能是输出数据框的一部分。 因为如果只读列是 UDO 输出架构的一部分，这些列将自动重新注入到 U-SQL 表中。

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)

