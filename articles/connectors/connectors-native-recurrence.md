---
title: 计划定期任务和工作流
description: 在 Azure 逻辑应用中通过定期触发器计划和运行定期自动执行的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0df34c0632875be3d2d3956ca90f615cb0a990ed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787193"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建、计划和运行定期触发的任务和工作流

若要根据特定计划定期运行任务、进程或作业，可以通过内置的**重复计划**触发器启动逻辑应用工作流。 可以设置日期和时间以及用于启动工作流的时区以及重复该工作流的重复周期。 如果出于任何原因而丢失了重复，则此触发器将在下一个计划的时间间隔内继续进行。 有关内置计划触发器和操作的详细信息，请参阅[通过 Azure 逻辑应用计划和运行定期自动执行、任务和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

下面是此触发器支持的一些模式，以及更高级的重复和复杂计划：

* 立即运行，并每隔*n*秒、分钟、小时、天、周或月重复。

* 在特定日期和时间启动，然后运行并每隔*n*秒、分钟、小时、天、周或月重复。

* 运行并在每天的一个或多个不同时间（例如 8:00 AM 和 5:00 PM）重复。

* 运行并在每周的特定星期日期（例如星期六和星期日）重复。

* 运行并在每周的特定星期日期和时间（例如星期一到星期五的 8:00 AM 和 5:00 PM）重复。

有关此触发器与滑动窗口触发器之间的差异，或者有关计划重复工作流的详细信息，请参阅[通过 Azure 逻辑应用计划和运行定期自动化任务、进程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果要触发逻辑应用并在将来仅运行一次，请参阅[只运行一次作业](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请学习[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-recurrence-trigger"></a>添加循环触发器

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 显示逻辑应用设计器后，在 "搜索" 框中输入 "定期" 作为筛选器。 从 "触发器" 列表中，选择此触发器作为逻辑应用工作流中的第一步：**循环**

   ![选择 "定期" 触发器](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 设置定期计划的间隔和频率。 在此示例中，请将这些属性设置为每周运行工作流。

   ![设置间隔和频率](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | properties | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | 间隔 | 是 | interval | Integer | 一个正整数，它基于频率描述工作流运行的次数。 下面是最小和最大间隔： <p>- 月：1-16 个月 </br>- 天：1-500 天 </br>- 小时：1-12,000 小时 </br>- 分钟：1-72,000 分钟 </br>- Second：1-9,999,999 秒<p>例如，如果间隔为 6，频率为“月”，则重复周期为每 6 个月。 |
   | **频率** | 是 | frequency | 字符串 | 定期计划的时间单位：“秒”、“分钟”、“小时”、“天”、“周”或“月” |
   ||||||

   对于更多计划选项，请打开 "**添加新参数**" 列表。 
   选择后，会在触发器上显示您选择的任何选项。

   ![高级计划选项](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | properties | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | **时区** | No | timezone | 字符串 | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | No | startTime | 字符串 | 提供此格式的开始日期和时间： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>例如，如果你想要2017年9月18日下午2:00，请指定 "2017-09-18T14：00： 00" 并选择时区，如太平洋标准时间。 或者指定“2017-09-18T14:00:00Z”，无需指定时区。 <p>**注意：** 此开始时间的最大值为49年，并且必须遵循[utc 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有[utc 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单计划，启动时间即第一次循环；而对于复杂计划，触发器不会在启动时间之前执行。 [*可通过哪些方式使用开始日期和时间？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **在这些日期** | No | 工作日 | 字符串或字符串数组 | 如果选择了“周”，则可以选择一个或多个星期日期作为运行工作流的时间：“星期一”、“星期二”、“星期三”、“星期四”、“星期五”、“星期六”和“星期日” |
   | **在这些小时** | No | 小时 | 整数或整数数组 | 如果选择 "天" 或 "周"，则可以选择从0到23的一个或多个整数，作为要运行工作流的一天中的小时。 <p><p>例如，如果指定 "10"、"12" 和 "14"，则表示一天中的小时数为 10 AM、12 PM 和2点，但会根据重复周期开始时间计算一天中的分钟数。 若要设置一天中的分钟数，请在 "此**分钟**" 属性中指定的值。 |
   | **在这些分钟** | No | 分钟 | 整数或整数数组 | 如果选择了“天”或“周”，则可以从 0 到 59 的范围内选择一个或多个整数，作为要运行工作流的小时时间的分钟时间。 <p>例如，可指定“30”为分钟标记，使用前面示例中的时间点，则可得到上午 10:30、中午 12:30 和下午 2:30。 |
   |||||

   例如，假设当天是 2017 年 9 月 4 日星期一。 以下定期触发器不会早于开始日期和时间（星期一、9月18日星期一到 8:00 2017 AM PST *）触发。* 但是，该定期计划设置为仅在每个星期一的 10:30 AM、12:30 PM 和 2:30 PM 运行。 因此，首次激发触发器并创建逻辑应用工作流实例的时间为 10:30 AM。 若要详细了解开始时间的工作方式，请参阅这些[开始时间示例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)。

   将来的运行发生在同一天的 12:30 PM 和 2:30 PM。 每个定期计划会创建其自身的工作流实例。 之后，在下一个星期一，整个计划会再次从头开始重复。 [*还有哪些计划示例？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![高级计划示例](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 仅当选择“天”或“周”作为频率时，触发器才显示指定的定期计划的预览。

1. 现在，用其他操作生成剩余的工作流。 有关可添加的更多操作，请参阅[Azure 逻辑应用的连接器](../connectors/apis-list.md)。

## <a name="workflow-definition---recurrence"></a>工作流定义-定期

在逻辑应用中使用 JSON 的基础工作流定义中，可以使用所选选项查看[定期触发器定义](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。 若要查看此定义，请在设计器工具栏上选择 "**代码视图**"。 若要返回设计器，请在设计器工具栏上选择 "**设计器**"。

此示例演示定期触发器定义可能在基础工作流定义中的外观：

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>后续步骤

* [暂停带有延迟操作的工作流](../connectors/connectors-native-delay.md)
* [逻辑应用的连接器](../connectors/apis-list.md)
