---
title: 使用 Azure 计划程序生成复杂的计划和高级循环
description: 了解如何使用 Azure 计划程序生成复杂的计划和高级循环。
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692328"
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>使用 Azure 计划程序生成复杂的计划和高级循环

Azure 计划程序作业的核心是计划。 计划确定了计划程序何时以及如何执行作业。 

可以使用计划程序为作业设置多个一次性计划和重复性计划。 一次性计划在指定的时间触发一次。 实际上，它们是只执行一次的重复性计划。 重复性计划根据预先确定的频率触发。

由于具有这种灵活性，可以使用计划程序支持各种业务方案：

* **周期性数据清理**。 例如，每日删除 3 个月以前的所有推文。
* **存档**。 例如，每月向备份服务推送发票历史记录。
* **请求外部数据**。 例如，每隔 15 分钟从 NOAA 提取新的滑雪天气报告。
* **图像处理**。 例如，在每个工作日的非高峰时间，使用云计算来压缩当天上传的图像。

在本文中，我们会演练可以使用计划程序创建的示例作业。 我们将提供用于描述每个计划的 JSON 数据。 如果熟悉[计划程序 REST API](https://msdn.microsoft.com/library/mt629143.aspx)，可以使用与此相同的 JSON 来[创建计划程序作业](https://msdn.microsoft.com/library/mt629145.aspx)。

## <a name="supported-scenarios"></a>支持的方案
本文中的示例演示了计划程序支持的各种方案。 从广义上讲，这些示例演示了如何为多种行为模式创建计划，包括：

* 在特定的日期和时间运行一次。
* 运行并重复特定的次数。
* 立即运行，然后重复。
* 运行并每隔 *n* 分钟、小时、天、周、月在特定的时间开始重复。
* 运行并根据每周或每月的频率重复，不过，只会在特定的日期、特定的星期或特定的月份日次重复。
* 运行并在某个时间段中的多个时间重复。 例如，每月的最后一个星期五和星期一，或者每天的 5:15 AM 和 5:15 PM。

## <a name="date-and-date-time"></a>日期和日期时间
计划程序作业中的参考日期遵循 [ISO 8601 规范](http://en.wikipedia.org/wiki/ISO_8601)并只包括日期。

计划程序作业中的日期时间引用遵循 [ISO 8601 规范](http://en.wikipedia.org/wiki/ISO_8601)并包括日期和时间。 未指定 UTC 时差的日期时间将假定为 UTC。  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>使用 JSON 和 REST API 来创建计划
若要使用[计划程序 REST API](https://msdn.microsoft.com/library/mt629143) 创建简单的计划，请先[使用资源提供程序注册订阅](https://msdn.microsoft.com/library/azure/dn790548.aspx)。 计划程序的提供程序名称是 **Microsoft.Scheduler**。 然后[创建作业集合](https://msdn.microsoft.com/library/mt629159.aspx)。 最后[创建作业](https://msdn.microsoft.com/library/mt629145.aspx)。 

在创建作业时，可以使用类似于以下摘录内容的 JSON 指定计划和循环：

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>作业架构基础知识
下表提供了用于在作业中设置循环和计划的主要元素的高级概述：

| JSON 名称 | 说明 |
|:--- |:--- |
| **startTime** |一个日期时间值。 对于简单的计划，**startTime** 是第一个匹配项。 对于复杂的计划，作业的启动时间不早于 **startTime**。 |
| **recurrence** |指定作业的循环规则，以及运行作业时遵循的循环计划。 recurrence 对象支持元素 **frequency**、**interval**、**endTime**、**count** 和 **schedule**。 如果定义了 **recurrence**，则必须定义 **frequency**。 其他 **recurrence** 元素是可选的。 |
| **frequency** |表示重复作业的频率单位的字符串。 支持的值为“minute”、“hour”、“day”、“week”和“month”。 |
| **interval** |正整数。 **interval** 表示确定作业运行频率的**频率**间隔值。 例如，如果 **interval** 为 3，**frequency** 为“week”，则作业每隔三周重复一次。<br /><br />对于每月频率，计划程序支持最长 18 个月的**间隔**；对于每周频率，支持最长 78 周；对于每日频率，支持最长 548 天。 对于小时和分钟频率，支持的范围为 1 <= **interval** <= 1000。 |
| **endTime** |日期时间字符串，如果超过此日期时间，则不运行作业。 可为 **endTime** 设置一个过去的值。 如果未指定 **endTime** 和 **count**，作业将无限期运行。 不能在同一个作业中同时包含 **endTime** 和 **count**。 |
| **count** |一个正整数（大于零），指定作业在完成之前运行的次数。<br /><br />**count** 表示作业在被确定为已完成之前要运行的次数。 例如，对于 **count** 为 5 并且开始日期为星期一的每日执行的作业，该作业会在星期五执行后完成。 如果开始时间发生在过去，则基于创建时间计算第一次执行时间。<br /><br />如果未指定 **endTime** 或 **count**，作业将无限期运行。 不能在同一个作业中同时包含 **endTime** 和 **count**。 |
| **schedule** |指定了频率的作业会根据循环执行更改其循环。 **schedule** 值包含基于分钟、小时、星期、月份日次和周次的修改。 |

## <a name="job-schema-defaults-limits-and-examples"></a>作业架构默认值、限制和示例
本文稍后将详细介绍以下每个元素：

| JSON 名称 | 值类型 | 必需？ | 默认值 | 有效值 | 示例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |字符串 |否 |无 |ISO 8601 日期时间 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |对象 |否 |无 |recurrence 对象 |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |字符串 |是 |无 |"minute"、"hour"、"day"、"week"、"month" |`"frequency" : "hour"` |
| **interval** |数字 |是 |无 |1 到 1000 |`"interval":10` |
| **endTime** |字符串 |否 |无 |代表将来某个时间的日期时间值 |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |数字 |否 |无 |>= 1 |`"count": 5` |
| **schedule** |对象 |否 |无 |schedule 对象 |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>深入探讨：startTime
下表描述了 **startTime** 如何控制作业的运行方式：

| startTime 值 | 无循环 | 循环，无计划 | 按计划循环 |
|:--- |:--- |:--- |:--- |
| **无开始时间** |立即运行一次。 |立即运行一次。 根据上次执行时间进行计算后，运行后续执行。 |立即运行一次。<br /><br />根据循环计划运行后续执行。 |
| **开始时间在过去** |立即运行一次。 |计算开始时间后的第一个将来执行时间，并在该时间运行。<br /><br />根据上次执行时间进行计算后，运行后续执行。 <br /><br />有关详细信息，请参阅此表后面的示例。 |一旦达到指定的开始时间，作业就会*立即*启动。 第一次循环基于从开始时间计算的计划。<br /><br />根据循环计划运行后续执行。 |
| **开始时间在将来或当前** |在指定的开始时间运行一次。 |在指定的开始时间运行一次。<br /><br />根据上次执行时间进行计算后，运行后续执行。|一旦达到指定的开始时间，作业就会*立即*启动。 第一次循环基于从开始时间计算的计划。<br /><br />根据循环计划运行后续执行。 |

让我们查看 **startTime** 在过去，并且指定了 recurrence 但未指定 schedule 的情况下会发生的情况示例。  假设当前时间为 2015-04-08 13:00，**startTime** 为 2015-04-07 14:00，**recurrence** 为每隔两天（定义方式为 **frequency**: day，**interval**: 2）。请注意，**startTime** 在过去，即发生在当前时间以前。

在上述情况下，首次执行时间为 2015-04-09 14:00\。 计划程序引擎从开始时间计算执行循环。 过去的所有实例会被丢弃。 引擎将使用将来发生的下一个实例。 在本例中，**startTime** 为 2015-04-07 2:00 PM，因此，下一个实例为从该时间算起的两天，即 2015-04-09 2:00 PM。

请注意，首次执行时间是相同的，即使 **startTime** 为 2015-04-05 14:00 或 2015-04-01 14:00\. 在首次执行后，使用该计划计算后续执行。 时间依次为 2015-04-11 2:00 PM、2015-04-13 2:00 PM、2015-04-15 2:00 PM，依此类推。

最后，如果为作业指定了计划，但未在计划中设置小时和分钟，则值分别默认为第一次执行的小时和分钟。

## <a name="deep-dive-schedule"></a>深入探讨：schedule
可以使用 **schedule** 来限制作业执行的次数。 例如，如果**频率**为“month”的作业具有仅在 31 号运行的计划，该作业仅在包含 31 号的月份运行。

还可以使用 **schedule** 来扩展作业执行的次数。 例如，如果**频率**为“month”的作业具有在1 号和 2 号运行的计划，则该作业将在每月的 1 号和 2 号运行，而不只是在每月运行一次。

如果指定了多个计划元素，则求值顺序为大到小 – 周次、月份日次、星期、小时和分钟。

下表详细描述了 schedule 元素：

| JSON 名称 | 说明 | 有效值 |
|:--- |:--- |:--- |
| **分钟数** |运行作业的小时中的分钟。 |整数数组。 |
| **小时数** |运行作业的日期中的小时。 |整数数组。 |
| **工作日** |运行作业的星期日期。 只能配合每周频率指定。 |包含以下任意值的数组（最大数组大小为 7）：<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />不区分大小写。 |
| **monthlyOccurrences** |确定运行作业的月份日期。 只能配合每月频率指定。 |**monthlyOccurrences** 对象的数组：<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** 是运行作业的星期日期。 例如，*{Sunday}* 表示月份中的每个星期日。 必需。<br /><br />**occurrence** 是月份中重复的日期。 例如，*{Sunday, -1}* 表示月份中的最后一个星期日。 可选。 |
| **monthDays** |运行作业的月份日期。 只能配合每月频率指定。 |以下值的数组：<br /><= -1 且 >= -31 的任意值<br />>= 1 且 <= 31 的任意值|

## <a name="examples-recurrence-schedules"></a>示例：循环计划
以下示例演示各种循环计划。 这些示例着重于计划对象及其子元素。

这些计划假设 **interval** 设置为 1\. 此外，这些示例假设正确的**频率**值符合 **schedule**。 例如，不能在使用 "day" 作为**频率**的同时，在**计划**中使用 **monthDays** 修改项。 本文前面已描述这些限制。

| 示例 | 说明 |
|:--- |:--- |
| `{"hours":[5]}` |在每天的 5:00 AM 运行。<br /><br />计划程序会将 "hours" 中的每个值与 "minutes" 中的每个值一一匹配，以创建运行作业的所有时间的列表。 |
| `{"minutes":[15], "hours":[5]}` |在每天早晨 5:15 运行。 |
| `{"minutes":[15], "hours":[5,17]}` |在每天早晨 5:15 和下午 5:15 运行。 |
| `{"minutes":[15,45], "hours":[5,17]}` |在每天早晨 5:15、5:45 和下午 5:15、5:45 运行。 |
| `{"minutes":[0,15,30,45]}` |每 15 分钟运行一次。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |每小时运行一次。<br /><br />此作业每隔一小时运行一次。 分钟由 **startTime** 的值（如果已指定）控制。 如果未指定 **startTime** 值，则分钟由创建时间控制。 例如，如果开始时间或创建时间（以适用的为准）为 12:25 PM，则作业会在 00:25、01:25、02:25 ... 23:25 运行。<br /><br />该计划相当于为作业指定了 **frequency** 为“hour”、**interval** 为 1 且无 **schedule** 值。 区别是，可以配合不同的 **frequency** 和 **interval** 值使用此计划来创建其他作业。 例如，如果 **frequency** 为“month”，则该计划将每月运行一次，而不是每天运行一次（如果 **frequency** 为“day”）。 |
| `{minutes:[0]}` |每小时整点运行。<br /><br />此作业也是每隔一小时运行一次，不过是在整点运行（例如 12:00 AM、1:00 AM、2:00 AM，等等）。 这相当于作业**频率**为 "hour"，**startTime** 为零分钟，并且在频率为 "day" 时无**计划**。 但是，如果**频率**为 "week" 或 "month"，则计划分别只会在一个星期日期或月份日期执行。 |
| `{"minutes":[15]}` |在过去每隔一小时的第 15 分钟运行。<br /><br />每隔一小时运行，从 00:15 AM、1:15 AM、2:15 AM 等开始。 在 11:15 PM 结束。 |
| `{"hours":[17], "weekDays":["saturday"]}` |在每周星期六的 5:00 PM 运行。 |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |在每周星期一、星期三和星期五的 5:00 PM 运行。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |在每周星期一、星期三和星期五的下午 5:15 和下午 5:45 运行。 |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |在每周星期一、星期三和星期五的 5:00 AM 和 5:00 PM 运行。 |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |在每周星期一、星期三和星期五的 5:15 AM、5:45 AM、5:15 PM 和 5:45 PM 运行。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |在工作日每 15 分钟运行一次。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |在工作日的 9:00 AM 到 4:45 PM 每 15 分钟运行一次。 |
| `{"weekDays":["sunday"]}` |在星期日的开始时间运行。 |
| `{"weekDays":["tuesday", "thursday"]}` |在星期二和星期四的开始时间运行。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |在每月 28 日的 6:00 AM 运行（假设**频率**为 "month"）。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |在当月最后一天的 6:00 AM 运行。<br /><br />如果要在月份的最后一天运行作业，请使用 -1 而不是日期 28、29、30 或 31。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |在每月第一天和最后一天的 6:00 AM 运行。 |
| `{monthDays":[1,-1]}` |在每月第一天和最后一天的开始时间运行。 |
| `{monthDays":[1,14]}` |在每月第一天和第 14 天的开始时间运行。 |
| `{monthDays":[2]}` |在月份第二天的开始时间运行。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |在每月第一个星期五的 5:00 AM 运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |在每月第一个星期五的开始时间运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |在每月从月末算起的第三个星期五的开始时间运行。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |在每月第一个和最后一个星期五的早晨 5:15 运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |在每月第一个和最后一个星期五的开始时间运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |在每月第五个星期五的开始时间运行。<br /><br />如果月份中没有第五个星期五，则不运行作业。 如果想要在月份的最后一个星期五运行作业，可以考虑为循环使用 -1 而不是 5。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |在月份的最后一个星期五每 15 分钟运行一次。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |在每月第三个星期三的早晨 5:15、早晨 5:45、下午 5:15、下午 5:45 运行。 |

## <a name="see-also"></a>另请参阅

- [计划程序是什么？](scheduler-intro.md)
- [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
- [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)
- [Azure 计划程序中的计划和计费](scheduler-plans-billing.md)
- [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)
- [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)
- [Azure 计划程序的高可用性和可靠性](scheduler-high-availability-reliability.md)
- [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)
- [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)

