---
title: 重复使用计划任务定期触发器-Azure 逻辑应用
description: 计划和使用 Azure 逻辑应用中的定期触发器运行定期的自动化的任务和工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297538"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>创建、 安排和使用 Azure 逻辑应用中的定期触发器运行重复任务和工作流

若要按特定计划定期运行任务、 进程或作业，可以开始在逻辑应用工作流使用内置**重复-计划**触发器。 您可以设置日期和时间，以及用于启动工作流并重复该工作流了重复发生的时区。 如果出于任何原因会遗漏的定期计划，此触发器将继续在下一步计划的时间间隔重复执行。 有关内置的计划触发器和操作的详细信息，请参阅[计划和定期自动运行、 任务和使用 Azure 逻辑应用工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

下面是此触发器支持以及更高级的重复周期和复杂的计划的一些模式：

* 立即运行，然后重复每个*n*秒、 分钟、 小时、 天、 周或月数。

* 开始在特定日期和时间，然后运行并重复每个*n*秒、 分钟、 小时、 天、 周或月数。

* 运行并在每天的一个或多个不同时间（例如 8:00 AM 和 5:00 PM）重复。

* 运行并在每周的特定星期日期（例如星期六和星期日）重复。

* 运行并在每周的特定星期日期和时间（例如星期一到星期五的 8:00 AM 和 5:00 PM）重复。

此触发器和滑动窗口触发器之间的差异或有关计划定期工作流详细信息，请参阅[计划和运行定期自动执行任务、 进程和使用 Azure 逻辑应用工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果你想要触发逻辑应用并在以后，请参阅中运行一次只能[运行作业一次仅](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果你没有订阅，则可以[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关的基本知识[逻辑应用](../logic-apps/logic-apps-overview.md)。 如果您熟悉逻辑应用，了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-recurrence-trigger"></a>添加重复触发器

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 后出现逻辑应用设计器，则在搜索框中，输入"定期"作为筛选器。 从触发器列表中，在逻辑应用工作流中的第一步中选择以下触发器：**定期**

   ![选择"定期"触发器](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 设置定期计划的间隔和频率。 在此示例中，请将这些属性设置为每周运行工作流。

   ![设置间隔和频率](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 属性 | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | 间隔  | 是 | interval | Integer | 一个正整数，描述工作流基于频率运行的频繁度。 下面是最小和最大间隔： <p>- 月：1-16 个月 </br>- 天：1-500 天 </br>- 小时：1-12,000 小时 </br>- 分钟：1-72,000 分钟 </br>- 秒：1-9,999,999 秒<p>例如，如果间隔为 6，频率为“月”，则重复周期为每 6 个月。 |
   | **频率** | 是 | 频率 | String | 定期计划的时间单位：“秒”、“分钟”、“小时”、“天”、“周”或“月”       |
   ||||||

   有关更多计划选项，打开**添加新参数**列表。 
   所选内容后触发器显示您选择任何选项。

   ![高级计划选项](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 属性 | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | **时区** | 否 | timezone | String | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | 否 | startTime | String | 提供开始日期和时间按以下格式： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>因此，例如，如果你希望 2017 年 9 月 18 日下午 2:00，然后指定"2017年-09-18T14:00:00"，然后选择如太平洋标准时间的时区。 或者指定“2017-09-18T14:00:00Z”且不选择时区。 <p>**注意：** 此开始时间必须遵循 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，采用 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单计划，开始时间指首次运行时间；对于复杂计划，触发器的激发时间不会早于开始时间。 [*可通过哪些方式使用开始日期和时间？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **在这些日期** | 否 | 工作日 | 字符串或字符串数组 | 如果选择“周”，则可以选择要运行工作流的一天或多天：“星期一”、“星期二”、“星期三”、“星期四”、“星期五”、“星期六”和“星期日”        |
   | **在这些小时** | 否 | 小时 | 整数或整数数组 | 如果选择"天"或"Week"，您可以选择一个或多个整数，介于 0 到 23 作为有关当你想要运行工作流的日期的小时。 <p><p>例如，如果指定"10"、"12"和"14"，获取上午 10 点、 中午 12 点和下午 2 点在一天的小时，但在一天的分钟计算根据重复周期开始时。 若要设置的分钟数天，指定的值**在这些分钟**属性。 |
   | **在这些分钟** | 否 | 分钟数 | 整数或整数数组 | 如果选择了“天”或“周”，则可以从 0 到 59 的范围内选择一个或多个整数，作为要运行工作流的小时时间的分钟时间。 <p>例如，可以指定“30”作为分钟标记并使用前面示例中的当天小时时间，这样，便可以指定10:30 AM、12:30 PM 和 2:30 PM 作为开始时间。 |
   |||||

   例如，假设当天是 2017 年 9 月 4 日星期一。 以下定期触发器不会激发*得更快*于开始日期和时间，这是 2017 年 9 月 18 日，星期一在太平洋标准时间上午 8:00。 但是，该定期计划设置为仅在每个星期一的 10:30 AM、12:30 PM 和 2:30 PM 运行。 因此，首次激发触发器并创建逻辑应用工作流实例的时间为 10:30 AM。 若要详细了解开始时间的工作方式，请参阅这些[开始时间示例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)。

   将来的运行发生在同一天的 12:30 PM 和 2:30 PM。 每个定期计划会创建其自身的工作流实例。 之后，在下一个星期一，整个计划会再次从头开始重复。 [*还有哪些计划示例？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![高级计划示例](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 仅当选择“天”或“周”作为频率时，触发器才显示指定的定期计划的预览。

1. 现在，构建剩余的工作流使用其他操作。 您可以添加的更多操作，请参阅[适用于 Azure 逻辑应用连接器](../connectors/apis-list.md)。

## <a name="workflow-definition---recurrence"></a>工作流定义的重复周期

在逻辑应用的基础工作流定义，它使用 JSON，可以查看[定期触发器定义](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)与所选的选项。 若要查看此定义中，设计器工具栏上，选择**代码视图**。 若要返回到设计器，请选择在设计器工具栏上，**设计器**。

此示例演示定期触发器定义可能在基础工作流定义中的显示方式：

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

* [暂停工作流使用延迟操作](../connectors/connectors-native-delay.md)
* [适用于逻辑应用连接器](../connectors/apis-list.md)
