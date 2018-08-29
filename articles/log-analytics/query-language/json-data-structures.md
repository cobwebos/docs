---
title: 在 Azure Log Analytics 查询中使用字符串 | Microsoft Docs
description: 本文提供有关在 Log Analytics 中使用 Analytics 门户编写查询的教程。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f027754f26a9063aa5faa548fd01576624811005
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190700"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>在 Log Analytics 查询中配合使用 JSON 和数据结构

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-analytics-portal.md)和[查询入门](get-started-queries.md)。


嵌套的对象是指在键值对的数组或映射中包含其他对象的对象。 这些对象表示为 JSON 字符串。 本文介绍如何使用 JSON 来检索数据和分析嵌套的对象。

## <a name="working-with-json-strings"></a>结合使用 JSON 字符串
使用 `extractjson` 访问已知路径中的特定 JSON 元素。 此函数需要使用以下约定的路径表达式。

- _$_ 指的是根文件夹
- 使用括号/圆点表示法来表示索引和元素，如下例所示。


对索引使用括号，并使用圆点来分隔元素：

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

如果只使用括号表示法，结果是一样的：

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

如果只有一个元素，则只能使用圆点表示法：

```OQL
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>结合使用对象

### <a name="parsejson"></a>parsejson
要访问 json 结构中的多个元素，将其作为动态对象进行访问更为简单。 使用 `parsejson` 将文本数据强制转换为动态对象。 转换为动态类型后，即可使用其他功能来分析数据。

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
使用 `arraylength` 计算数组中元素的数量：

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
使用 `mvexpand` 将对象的属性拆分到单独的行中。

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
使用 `buildschema` 获取允许对象的所有值的架构：

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

输出是 JSON 格式的架构：
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
此输出中显示了对象字段的名称及其匹配的数据类型。 

嵌套的对象可能具有不同架构，如下例所示：

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![生成架构](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>后续步骤
有关 Log Analytics 查询语言的用法，请参阅其他课程：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [高级查询编写](advanced-query-writing.md)
- [联接](joins.md)
- [图表](charts.md)