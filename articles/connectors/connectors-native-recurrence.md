---
title: 计划重复任务和工作流
description: 使用 Azure 逻辑应用中的定期触发器计划和运行重复自动化任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 47bb4e4cc4902168631fa67f186d0b3259c94328
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83004674"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的定期触发器创建、计划和运行重复任务和工作流

若要按特定的计划定期运行任务、进程或作业，可以使用内置的**定期 - 计划**触发器来启动逻辑应用工作流。 可以设置启动工作流的日期、时间和时区，以及设置设置重复该工作流的定期计划。 如果定期计划因某种原因而缺失，此触发器会在下一个计划的时间间隔继续该定期计划。 有关内置计划触发器和操作的详细信息，请参阅[使用 Azure 逻辑应用计划和运行定期自动执行的任务和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

下面是此触发器支持的一些模式，以及更高级的定期计划和复杂计划：

* 立即运行，并每隔 *n* 秒、分钟、小时、天、周或月重复运行。

* 在特定的日期和时间启动，然后运行并每隔 *n* 秒、分钟、小时、天、周或月重复运行。

* 运行并在每天的一个或多个不同时间（例如 8:00 AM 和 5:00 PM）重复。

* 运行并在每周的特定星期日期（例如星期六和星期日）重复。

* 运行并在每周的特定星期日期和时间（例如星期一到星期五的 8:00 AM 和 5:00 PM）重复。

有关此触发器与“滑动窗口”触发器之间的差异或有关计划重复执行工作流的详细信息，请参阅[使用 Azure 逻辑应用计划和运行定期自动执行的任务、流程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 若要触发逻辑应用并且在将来仅运行一次，请参阅[仅运行一次作业](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-recurrence-trigger"></a>添加“定期”触发器

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 显示逻辑应用设计器后，在搜索框中输入 `recurrence` 作为筛选器。 逻辑应用工作流的第一步是从触发器列表中选择此触发器。**定期**

   ![选择“定期”触发器](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 设置定期计划的间隔和频率。 在此示例中，请将这些属性设置为每周运行工作流。

   ![设置间隔和频率](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 属性 | JSON 名称 | 必须 | 类型 | 说明 |
   |----------|-----------|----------|------|-------------|
   | **时间间隔** | `interval` | 是 | Integer | 一个正整数，描述工作流基于频率运行的频繁度。 下面是最小和最大间隔： <p>- 月：1-16 个月 <br>-周：1-71 周 <br>- 天：1-500 天 <br>- 小时：1-12,000 小时 <br>- 分钟：1-72,000 分钟 <br>- 秒：1-9,999,999 秒<p>例如，如果间隔为 6，频率为“月”，则重复周期为每 6 个月。 |
   | **频率** | `frequency` | 是 | String | 定期计划的时间单位：“秒”、“分钟”、“小时”、“天”、“周”或“月”************************ |
   ||||||

   > [!IMPORTANT]
   > 当重复执行不指定高级计划选项时，将来的重复将基于上次运行时间。
   > 这些重复周期的开始时间可能会由于诸如存储调用期间的延迟等因素而发生偏差。 若要确保逻辑应用不会错过循环，尤其是频率为天或更长时，请使用以下选项之一：
   > 
   > * 提供定期的开始时间。
   > 
   > * 使用 "**此时** **" 和 "时间"** 属性指定运行定期的时间和时间。
   > 
   > * 使用[滑动窗口触发器](../connectors/connectors-native-sliding-window.md)，而不是重复触发器。

1. 若要设置高级计划选项，请打开 "**添加新参数**" 列表。 选择的任何选项会在选定后显示在触发器中。

   ![高级计划选项](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 属性 | JSON 名称 | 必须 | 类型 | 说明 |
   |----------|-----------|----------|------|-------------|
   | **时区** | `timeZone` | 否 | String | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | `startTime` | 否 | String | 采用以下格式提供启动日期和时间： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>例如，如果你想要2020年9月18日下午2:00，请指定 "2020-09-18T14：00： 00" 并选择时区，如太平洋标准时间。 或者，指定 "2020-09-18T14：00： 00Z"，无需指定时区。 <p>**注意：** 此开始时间的最大值为49年，并且必须遵循[utc 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有[utc 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单计划，开始时间指首次运行时间；对于复杂计划，触发器的激发时间不会早于开始时间。 [*可通过哪些方式使用开始日期和时间？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **在这些日期** | `weekDays` | 否 | 字符串或字符串数组 | 如果选择了“周”，则可以选择一个或多个星期日期作为运行工作流的时间：“星期一”、“星期二”、“星期三”、“星期四”、“星期五”、“星期六”和“星期日”**************************** |
   | **在这些小时** | `hours` | 否 | 整数或整数数组 | 如果选择了“天”或“周”，则可以从 0 到 23 的范围内选择一个或多个整数，作为当天要运行工作流的小时时间。 <p><p>例如，如果指定“10”、“12”和“14”，则会将上午 10 点、中午 12 点和下午 2 点作为当天的小时，但当天的分钟则根据定期启动的时间计算。 若要设置一天中的特定分钟数，例如 10:00 AM、12:00 PM 和 2:00 PM，请使用 "**在这些分钟数**" 属性来指定这些值。 |
   | **在这些分钟** | `minutes` | 否 | 整数或整数数组 | 如果选择了“天”或“周”，则可以从 0 到 59 的范围内选择一个或多个整数，作为要运行工作流的小时时间的分钟时间。 <p>例如，可以指定“30”作为分钟标记并使用前面示例中的当天小时时间，这样，便可以指定10:30 AM、12:30 PM 和 2:30 PM 作为开始时间。 |
   |||||

   例如，假设今天是2020年9月4日星期五。 以下定期*触发器不会早于开始*日期和时间（即在上午8:00 到2020的星期五，星期五，星期五，上午）。 但是，该定期计划设置为仅在每个星期一的 10:30 AM、12:30 PM 和 2:30 PM 运行。 因此，第一次触发触发器并创建逻辑应用工作流实例的时间为星期一凌晨10:30。 若要详细了解开始时间的工作方式，请参阅这些[开始时间示例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)。

   将来的运行发生在同一天的 12:30 PM 和 2:30 PM。 每个定期计划会创建其自身的工作流实例。 之后，在下一个星期一，整个计划会再次从头开始重复。 [*还有哪些计划示例？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![高级计划示例](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > 仅当选择“天”或“周”作为频率时，触发器才显示指定的定期计划的预览。

1. 现在，请使用其他操作生成剩余的工作流。 有关可添加的其他操作，请参阅 [Azure 逻辑应用的连接器](../connectors/apis-list.md)。

## <a name="workflow-definition---recurrence"></a>工作流定义 - 定期

在逻辑应用的基础工作流定义（使用 JSON）中，可以通过选定的选项查看[“定期”触发器定义](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。 若要查看此定义，请在设计器工具栏上选择“代码视图”。**** 若要返回到设计器，请选择设计器工具栏上的“设计器”****。

本示例介绍了“定期”触发器定义在基础工作流定义中看起来是什么样的：

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
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>后续步骤

* [使用延迟操作暂停工作流](../connectors/connectors-native-delay.md)
* [适用于逻辑应用的连接器](../connectors/apis-list.md)
