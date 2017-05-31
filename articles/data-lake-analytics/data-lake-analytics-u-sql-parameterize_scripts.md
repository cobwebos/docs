---
title: "在 Azure Data Lake Analytics 中参数化 U-SQL 脚本 | Microsoft Docs"
description: "了解如何在 Azure Data Lake Analytics 中参数化 U-SQL 脚本。"
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
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>参数化 U-SQL 脚本

使用 U-SQL 脚本时，可以很方便地参数化脚本。 参数允许主体保持不变，同时仍然通过传入单独的参数值控制脚本的执行。 典型方案涉及在某些时间范围内处理数据的脚本，开发人员可能需要在开始日期和结束日期参数化脚本。 

有两种方法可以参数化脚本：
* 脚本允许在脚本顶部安全地添加参数。 U-SQL 支持此方法。
* 作业提交 API 允许调用方发送参数。 U-SQL 不支持此方法。

## <a name="example-scripts"></a>示例脚本

下面是一个简单的初始脚本：

```
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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
在此方案中，我们通过添加 `DECLARE EXTERNAL` 语句参数化 ``Region``。

```
DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

运行时，此脚本默认为仅筛选来自 `en-us` 区域的行。 目前，我们在脚本中定义了一个参数。 `EXTERNAL` 关键字表示用户可以通过前置定义 `@TargetRegion` 参数的 DECLARE 语句来重新定义 `@TargetRegion` 值。

以下脚本显示用户为参数提供值时的参数化脚本。 此脚本现在筛选区域为 `en-gb` 的行。

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

