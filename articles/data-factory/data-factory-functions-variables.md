---
title: "数据工厂函数和系统变量 | Microsoft Docs"
description: "提供 Azure 数据工厂函数和系统变量的列表"
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 370b2212be8d5f7a537b8fadbfa05355844dcb96


---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure 数据工厂 - 函数和系统变量
本文提供 Azure 数据工厂支持的函数和变量的相关信息。

## <a name="data-factory-system-variables"></a>数据工厂系统变量
| 变量名 | 说明 | 对象作用域 | JSON 作用域和用例 |
| --- | --- | --- | --- |
| WindowStart |当前活动运行窗口的开始时间间隔 |activity |<ol><li>指定数据选择查询。 请参阅[数据移动活动](data-factory-data-movement-activities.md)一文中引用的连接器文章。</li><li>向 Hive 脚本传递参数（如上例所示）。</li> |
| WindowEnd |当前活动运行窗口的结束时间间隔 |activity |同上 |
| SliceStart |生成数据切片的开始时间间隔 |activity<br/>dataset |<ol><li>使用 [Azure Blob](data-factory-azure-blob-connector.md) 和[文件系统数据集](data-factory-onprem-file-system-connector.md)时指定动态文件夹路径和文件名。</li><li>指定活动输入集合中含数据工厂函数的输入依赖项。</li></ol> |
| SliceEnd |当前正生成数据切片的结束时间间隔 |activity<br/>dataset |同上。 |

> [!NOTE]
> 目前，数据工厂要求活动所指定计划与输出数据集可用性所指定计划完全匹配。 这意味着 WindowStart、WindowEnd、SliceStart 和 SliceEnd 始终映射到同一时间段和单个输出切片。
> 
> 

## <a name="data-factory-functions"></a>数据工厂函数
可将数据工厂函数和上述系统变量用于以下目的：

1. 指定数据选择查询（请参阅[数据移动活动](data-factory-data-movement-activities.md)一文引用的连接器文章）。
   
   调用数据工厂函数的语法是：**$$<function>**，用于数据选择查询以及活动和数据集中的其他属性。  
2. 指定活动输入集合中含数据工厂函数的输入依赖项（详见上方示例）。
   
    指定输入依赖项表达式不需要 $$。     

在以下示例中，将 JSON 文件的 **sqlReaderQuery** 属性分配给 **Text.Format** 函数返回的值。 此示例还使用名为 **WindowStart** 的系统变量表示活动运行窗口的开始时间。

```JSON
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

### <a name="functions"></a>函数
下表列出了 Azure 数据工厂中的所有函数：

| 类别 | 函数 | parameters | 说明 |
| --- | --- | --- | --- |
| 时间 |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |向给定时间 X 加 Y 小时。 <br/><br/>示例：2013/9/5 中午 12:00:00 + 2 小时 = 2013/9/5 下午 2:00:00 |
| 时间 |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |向 X 加 Y 分钟。<br/><br/>示例：2013/9/15 中午 12:00:00 + 15 分钟 = 2013/9/15 下午 12:15:00 |
| 时间 |StartOfHour(X) |X: Datetime |获取由 X 小时部分表示的小时起始时间。 <br/><br/>示例：2013/9/15 下午 05:10:23 的 StartOfHour 是 2013/9/15 下午 05:00:00 |
| 日期 |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |向 X 加 Y 天。<br/><br/>示例：2013/9/15 中午 12:00:00 + 2 天 = 2013/9/17 中午 12:00:00 |
| 日期 |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |向 X 加 Y 个月。<br/><br/>示例：2013/9/15 中午 12:00:00 + 1 个月 = 2013/10/15 中午 12:00:00 |
| 日期 |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |向 X 加 Y * 3 个月。<br/><br/>示例：2013/9/15 中午 12:00:00 + 1 个季度 = 2013/12/15 中午 12:00:00 |
| 日期 |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |向 X 加 Y * 7 天<br/><br/>示例：2013/9/15 中午 12:00:00 + 1 周 = 2013/9/22 中午 12:00:00 |
| 日期 |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |向 X 加 Y 年。<br/><br/>示例：2013/9/15 中午 12:00:00 + 1 年 = 2014/9/15 中午 12:00:00 |
| 日期 |Day(X) |X: DateTime |获取 X 的日期号数部分。<br/><br/>示例：2013/9/15 中午 12:00:00 的日期号数是 15。 |
| 日期 |DayOfWeek(X) |X: DateTime |获取 X 的星期部分。<br/><br/>示例：2013/9/15 中午 12:00:00 的 DayOfWeek 是星期日。 |
| 日期 |DayOfYear(X) |X: DateTime |获取由 X 的年份部分表示的当年第几天。<br/><br/>示例:<br/>2015/12/1：2015 年的第 335 天<br/>2015/12/31：2015 年的第 365 天<br/>2016/12/31：2016 年的第 366 天（闰年） |
| 日期 |DaysInMonth(X) |X: DateTime |获取由参数 X 的月份部分表示的当月天数。<br/><br/>示例：2013/9/15 的 DaysInMonth 是 30，因为九月有 30 天。 |
| 日期 |EndOfDay(X) |X: DateTime |获取表示 X 的当天（日期号数部分）结束时的日期时间。<br/><br/>示例：2013/9/15 下午 05:10:23 的 EndOfDay 是 2013/9/15 晚上 11:59:59。 |
| 日期 |EndOfMonth(X) |X: DateTime |获取由参数 X 的月份部分表示的当月结束时间。 <br/><br/>示例：2013/9/15 下午 05:10:23 的 EndOfMonth 是 2013/9/30 晚上 11:59:59（表示九月结束的日期时间） |
| 日期 |StartOfDay(X) |X: DateTime |获取由参数 X 的日期号数部分表示的当天开始时间。<br/><br/>示例：2013/9/15 下午 05:10:23 的 StartOfDay 是 2013/9/15 午夜 12:00:00。 |
| DateTime |From(X) |X: String |将字符串 X 解析成日期时间。 |
| DateTime |Ticks(X) |X: DateTime |获取参数 X 的刻度属性。一刻度等于 100 纳秒。 此属性的值表示自 0001 年 1 月 1 日午夜 12:00:00 以来已经过的刻度数。 |
| 文本 |Format(X) |X: String variable |设置文本的格式。 |

#### <a name="textformat-example"></a>Text.Format 示例

```JSON
"defines": { 
    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
}
```

请参阅[自定义日期和时间格式字符串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)主题，其中介绍了可用的不同格式设置选项（例如：yy 和 yyyy）。 

> [!NOTE]
> 在一个函数中使用另一函数时，无需对内部函数使用 **$$** 前缀。 例如：$$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' 和 RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\''、Time.AddHours(SliceStart, -6))。 请注意，本例未对 **Time.AddHours** 函数使用 **$$** 前缀。 
> 
> 




<!--HONumber=Nov16_HO3-->


