---
title: 在 Azure Data Lake Analytics 中使用 Python 扩展 U-SQL 脚本
description: 了解如何使用 Azure Data Lake Analytics 在 U-SQL 脚本中运行 Python 代码
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 3e895a6ea9bccc0d210f43748edb3eea80ddc6ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625074"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>在 Azure Data Lake Analytics 中使用 Python 代码扩展 U-SQL 脚本

## <a name="prerequisites"></a>先决条件

开始之前，请确保 Azure Data Lake Analytics 帐户已安装 Python 扩展。

* 在 Azure 门户中，导航到 Data Lake Analytics 帐户
* 在左侧菜单的“入门”下方，单击“示例脚本”
* 单击“安装 U-SQL 扩展”，然后单击“确定”

## <a name="overview"></a>概述 

通过 U-SQL 的 Python 扩展，开发人员可以执行大规模并发执行的 Python 代码。 以下示例演示了基本步骤：

* 使用 `REFERENCE ASSEMBLY` 语句为 U-SQL 脚本启用 Python 扩展
* 使用 `REDUCE` 操作对某个键的输入数据进行分区
* U-SQL 的 Python 扩展包括内置化简器 (`Extension.Python.Reducer`)，可在分配给每个化简器的顶点上运行 Python 代码
* U-SQL 脚本包含嵌入式 Python 代码，该代码具有称为 `usqlml_main` 的函数，可接受 pandas DataFrame 作为输入并返回 pandas DataFrame 作为输出。

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

## <a name="how-python-integrates-with-u-sql"></a>Python 如何与 U-SQL 集成

### <a name="datatypes"></a>数据类型

* U-SQL 中的字符串和数字列在 Pandas 和 U-SQL 之间按原样转换
* U-SQL Null 与 Pandas `NA` 值相互转换

### <a name="schemas"></a>架构

* Pandas 中的索引矢量在 U-SQL 中不受支持。 Python 函数中的所有输入数据帧始终具有 64 位的数字索引，范围从 0 到行数减去 1 的值。 
* U-SQL 数据集不能有重复的列名称
* U-SQL 数据集列名称不是字符串。 

### <a name="python-versions"></a>Python 版本
仅支持 Python 3.5.1（为 Windows 编译）。 

### <a name="standard-python-modules"></a>标准 Python 模块
所有标准 Python 模块都包括在内。

### <a name="additional-python-modules"></a>其他 Python 模块
除了标准 Python 库，还包括几个常用的 Python 库：

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>异常消息
当前，Python 代码中的异常显示为泛型顶点失败。 以后，U-SQL 作业错误消息会显示 Python 异常消息。

### <a name="input-and-output-size-limitations"></a>输入和输出大小限制
分配给每个顶点的内存量受限。 当前，澳大利亚的限制为 6 GB。 因为输入和输出 DataFrames 必须存在于 Python 代码的内存中，因此输入和输出的总大小不能超过 6 GB。

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
* [使用针对 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
