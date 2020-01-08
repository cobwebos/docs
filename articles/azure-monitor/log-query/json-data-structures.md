---
title: 在 Azure Monitor 日志查询中使用字符串 | Microsoft Docs
description: 本文提供了有关在 Azure 门户中使用 Azure Monitor Log Analytics 在 Azure Monitor 中查询和分析日志数据的教程。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 940c82e9ef7016639a3ab334040c408f83996e2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365302"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>在 Azure Monitor 日志查询中使用 JSON 和数据结构

> [!NOTE]
> 完成本课程之前，应完成[Azure Monitor Log Analytics](get-started-portal.md)和[Azure Monitor 日志查询](get-started-queries.md)入门。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

嵌套的对象是指在键值对的数组或映射中包含其他对象的对象。 这些对象表示为 JSON 字符串。 本文介绍如何使用 JSON 来检索数据和分析嵌套的对象。

## <a name="working-with-json-strings"></a>结合使用 JSON 字符串
使用 `extractjson` 访问已知路径中的特定 JSON 元素。 此函数需要使用以下约定的路径表达式。

- _$_ 指的是根文件夹
- 使用括号/圆点表示法来表示索引和元素，如下例所示。


对索引使用括号，并使用圆点来分隔元素：

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

如果只使用括号表示法，结果是一样的：

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

如果只有一个元素，则只能使用圆点表示法：

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>使用对象

### <a name="parsejson"></a>parsejson
要访问 json 结构中的多个元素，将其作为动态对象进行访问更为简单。 使用 `parsejson` 将文本数据强制转换为动态对象。 转换为动态类型后，即可使用其他功能来分析数据。

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
使用 `arraylength` 计算数组中元素的数量：

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
使用 `mvexpand` 将对象的属性拆分到单独的行中。

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
使用 `buildschema` 获取允许对象的所有值的架构：

```Kusto
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

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![生成架构](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>后续步骤
参阅有关在 Azure Monitor 中使用日志查询的其他课：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [高级查询编写](advanced-query-writing.md)
- [联接](joins.md)
- [图表](charts.md)
