---
title: 生成高级作业计划和重复周期 - Azure 计划程序
description: 了解如何在 Azure 计划程序中为作业创建高级计划和重复周期
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: f5a8b929cf5af6e4e43c6003e6b622d04a50b93e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980934"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>在 Azure 计划程序中为作业生成高级计划和重复周期

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替换即将停用的 Azure 计划程序。 若要安排作业，请[改为试用 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

在 [Azure 计划程序](../scheduler/scheduler-intro.md)作业中，计划是确定计划程序服务何时以及如何运行作业的核心。 可以使用计划程序为作业设置多个一次性计划和重复计划。 一次性计划仅在指定时间运行一次，并且基本上是仅运行一次的重复计划。 重复计划按照指定频率运行。 由于具有这种灵活性，可以使用计划程序支持各种业务方案，例如：

* **定期清理数据**：创建每日作业，以删除超过三个月的所有推文。

* **数据存档**：创建每月作业，以将发票历史记录推送到备份服务。

* **请求外部数据**：创建一个每隔 15 分钟运行一次并从 NOAA 拉取新天气报告的作业。

* **处理图像**：创建工作日作业，以在非高峰时段运行并使用云计算来压缩当天上载的图像。

本文介绍可以使用计划程序和 [Azure 计划程序 REST API](https://docs.microsoft.com/rest/api/schedule) 创建的示例作业，并涵盖每个计划的 JavaScript 对象表示法 (JSON) 定义。 

## <a name="supported-scenarios"></a>支持的方案

以下示例展示了 Azure 计划程序支持的方案范围以及如何为各种行为模式创建计划，例如：

* 在特定的日期和时间运行一次。
* 运行并重复特定的次数。
* 立即运行，然后重复。
* 运行并每隔 *n* 分钟、小时、天、周或月在特定的时间开始重复。
* 运行并根据每周或每月的频率重复，不过，只会在特定的星期日期或特定的月份日期重复。
* 运行并在特定时间段内重复多次。 例如，每月的最后一个星期五和星期一，或每天的上午 5:15 和下午 5:15。

本文稍后将更详细地介绍这些方案。

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>使用 REST API 创建计划

若要使用 [Azure 计划程序 REST API](https://docs.microsoft.com/rest/api/schedule) 创建基本计划，请执行以下步骤：

1. 使用[注册操作 - 资源管理器 REST API](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register) 向资源提供程序注册 Azure 订阅。 Azure 计划程序服务的提供程序名称为 **Microsoft.Scheduler**。 

1. 使用计划程序 REST API 中的[为作业集合创建或更新操作](https://docs.microsoft.com/rest/api/scheduler/jobcollections#JobCollections_CreateOrUpdate)创建作业集合。 

1. 使用[为作业创建或更新操作](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate)创建作业。 

## <a name="job-schema-elements"></a>作业架构元素

此表简要概述了在设置作业的重复周期和计划时可以使用的主要 JSON 元素。 

| 元素 | 必选 | Description | 
|---------|----------|-------------|
| **startTime** | 否 | [ISO 8601 格式](http://en.wikipedia.org/wiki/ISO_8601)的 DateTime 字符串值，用于指定作业在基本计划中首次启动的时间。 <p>对于复杂的计划，作业的启动时间不早于 **startTime**。 | 
| **recurrence** | 否 | 作业运行时的重复规则。 **recurrence** 对象支持这些元素：**frequency**、**interval**、**schedule**、**count** 和 **endTime**。 <p>如果使用 **recurrence** 元素，则还必须使用 **frequency** 元素，而其他 **recurrence** 元素是可选的。 |
| **frequency** | 是，当使用 **recurrence** 时 | 两次作业之间的时间单位并支持这些值：“Minute”、“Hour”、“Day”、“Week”、“Month”和“Year” | 
| **interval** | 否 | 一个正整数，根据 **frequency** 确定两次作业之间的时间单位数。 <p>例如，如果 **interval** 为 10，**frequency** 为“Week”，则作业每隔 10 周重复一次。 <p>下面是每种频率的最大间隔数： <p>- 18 个月 <br>- 78 周 <br>- 548 天 <br>- 对于小时和分钟，范围为 1 <= <*interval*> <= 1000。 | 
| **schedule** | 否 | 根据指定的分钟标记、小时标记、星期日期和月份日期定义重复周期的更改 | 
| **count** | 否 | 一个正整数，指定作业在完成之前运行的次数。 <p>例如，当每日作业将 **count** 设置为 7，并且开始日期为星期一时，该作业将在星期日结束运行。 如果已经过了开始日期，则从创建时间开始计算第一次运行。 <p>如果未指定 **endTime** 或 **count**，作业将无限期运行。 不能在同一个作业中同时使用 **count** 和 **endTime**，但首先完成的规则优先。 | 
| **endTime** | 否 | [ISO 8601 格式](http://en.wikipedia.org/wiki/ISO_8601)的 Date 或 DateTime 字符串值，用于指定作业何时停止运行。 可为 **endTime** 设置一个过去的值。 <p>如果未指定 **endTime** 或 **count**，作业将无限期运行。 不能在同一个作业中同时使用 **count** 和 **endTime**，但首先完成的规则优先。 |
|||| 

例如，此 JSON 架构描述了作业的基本计划和重复周期： 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*日期和日期时间值*

* 计划程序作业中的日期只包括日期并遵循 [ISO 8601 规范](http://en.wikipedia.org/wiki/ISO_8601)。

* 计划程序作业中的日期时间包括日期和时间，遵循 [ISO 8601 规范](http://en.wikipedia.org/wiki/ISO_8601)，并且在未指定 UTC 时差时假定为 UTC。 

有关详细信息，请参阅[概念、术语和实体](../scheduler/scheduler-concepts-terms.md)。

<a name="start-time"></a>

## <a name="details-starttime"></a>详细信息：startTime

此表描述了 **startTime** 如何控制作业的运行方式：

| startTime | 无循环 | 循环，无计划 | 按计划循环 |
|-----------|---------------|-------------------------|--------------------------|
| **无开始时间** | 立即运行一次。 | 立即运行一次。 根据上次执行时间进行计算后，运行后续执行。 | 立即运行一次。 根据循环计划运行后续执行。 | 
| **开始时间在过去** | 立即运行一次。 | 计算开始时间后的第一个将来运行时间，并在该时间运行。 <p>根据上次执行时间进行计算后，运行后续执行。 <p>请参阅此表后面的示例。 | 一旦达到指定的开始时间，就*立即*启动作业。 第一次循环基于从开始时间计算的计划。 <p>根据循环计划运行后续执行。 | 
| **开始时间在将来或当前** | 在指定的开始时间运行一次。 | 在指定的开始时间运行一次。 <p>根据上次执行时间进行计算后，运行后续执行。 | 一旦达到指定的开始时间，就*立即*启动作业。 第一次循环基于从开始时间计算的计划。 <p>根据循环计划运行后续执行。 |
||||| 

假设此示例具有以下条件：开始时间发生在过去，具有重复周期，但没有计划。

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* 当前日期和时间为“2015-04-08 13:00”。

* 开始日期和时间为“2015-04-07 14:00”，即，早于当前日期和时间。

* 每隔两天重复一次。

1. 在上述条件下，首次执行时间为 2015-04-09 14:00。 

   计划程序根据开始时间计算执行次数、放弃过去的所有实例，并使用将来的下一个实例。 
   在本例中，**startTime** 为 2015-04-07 下午 2:00，因此，下一个实例为从该时间算起的两天，即 2015-04-09 下午 2:00。

   首次执行时间是相同的，不管 **startTime** 是 2015-04-05 14:00 还是 2015-04-01 14:00。 在首次执行后，根据该计划计算后续执行。 
   
1. 后续执行的顺序如下： 
   
   1. 2015-04-11 下午 2:00
   1. 2015-04-13 下午 2:00 
   1. 2015-04-15 下午 2:00
   1. 依此类推...

1. 最后，如果为作业指定了计划，但未指定小时和分钟，则这些值分别默认为首次执行的小时和分钟。

<a name="schedule"></a>

## <a name="details-schedule"></a>详细信息：schedule

可以使用 **schedule** 来限制作业执行的次数。 例如，如果**频率**为“month”的作业具有仅在 31 号运行的计划，该作业仅在包含 31 号的月份运行。

还可以使用 **schedule** 来扩展作业执行的次数。 例如，如果**频率**为“month”的作业具有在1 号和 2 号运行的计划，则该作业将在每月的 1 号和 2 号运行，而不只是在每月运行一次。

如果指定了多个计划元素，则求值顺序为从大到小：周次、月份日期、星期、小时和分钟。

下表详细描述了 schedule 元素：

| JSON 名称 | Description | 有效值 |
|:--- |:--- |:--- |
| **分钟数** |运行作业的小时中的分钟。 |整数数组。 |
| **小时数** |运行作业的日期中的小时。 |整数数组。 |
| **工作日** |运行作业的星期日期。 只能配合每周频率指定。 |包含以下任意值的数组（最大数组大小为 7）：<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />不区分大小写。 |
| **monthlyOccurrences** |确定运行作业的月份日期。 只能配合每月频率指定。 |**monthlyOccurrences** 对象的数组：<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** 是运行作业的星期日期。 例如，*{Sunday}* 表示月份中的每个星期日。 必需。<br /><br />**occurrence** 是月份中重复的日期。 例如，*{Sunday, -1}* 表示月份中的最后一个星期日。 可选。 |
| **monthDays** |运行作业的月份日期。 只能配合每月频率指定。 |以下值的数组：<br /><= -1 且 >= -31 的任意值<br />>= 1 且 <= 31 的任意值|

## <a name="examples-recurrence-schedules"></a>示例：循环计划

以下示例演示各种循环计划。 这些示例着重于计划对象及其子元素。

这些计划假设 **interval** 设置为 1\. 此外，这些示例假设正确的**频率**值符合 **schedule**。 例如，不能在使用 "day" 作为**频率**的同时，在**计划**中使用 **monthDays** 修改项。 本文前面已描述这些限制。

| 示例 | Description |
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

* [什么是 Azure 计划程序？](scheduler-intro.md)
* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)