---
title: 计划任务以处理连续数据
description: 在 Azure 逻辑应用中使用滑动窗口来创建和运行处理连续数据的定期任务
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 3ec71a1ed8d24eb637afbb73b5949b69a1e3c041
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83004628"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用“滑动窗口”触发器来计划和运行处理连续数据的任务

若要定期运行那些必须以连续区块的形式处理数据的任务、进程或作业，可以使用**滑动窗口**触发器来启动逻辑应用工作流。 可以设置启动工作流的日期、时间和时区，以及设置设置重复该工作流的定期计划。 如果定期计划因某种原因而缺失，此触发器会处理这些缺失的定期计划。 例如，在数据库和备份存储之间同步数据时，请使用“滑动窗口”触发器，使数据在同步时没有产生间隔。 有关内置计划触发器和操作的详细信息，请参阅[使用 Azure 逻辑应用计划和运行定期自动执行的任务和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

下面是此触发器支持的一些模式：

* 立即运行，并每隔*n*秒、分钟、小时、天、周或月重复。

* 在特定的日期和时间启动，然后运行并每隔 *n* 秒、分钟、小时、天、周或月重复运行。 使用此触发器时，可以指定过去的一个开始时间，以便运行所有过去的定期触发。

* 将每个定期触发延迟特定的时间，然后再运行。

有关此触发器与定期触发器之间的差异或有关计划重复执行工作流的详细信息，请参阅[使用 Azure 逻辑应用计划和运行定期自动执行的任务、进程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 若要触发逻辑应用并且在将来仅运行一次，请参阅[仅运行一次作业](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>添加“滑动窗口”触发器

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器出现后，请在 "搜索" 框`sliding window`中输入作为筛选器。 从 "触发器" 列表中，选择**滑动窗口**触发器作为逻辑应用工作流中的第一步。

   ![选择“滑动窗口”触发器](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 设置定期计划的间隔和频率。 在此示例中，请将这些属性设置为每周运行工作流。

   ![设置间隔和频率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 属性 | JSON 名称 | 必须 | 类型 | 说明 |
   |----------|----------|-----------|------|-------------|
   | **时间间隔** | `interval` | 是 | Integer | 一个正整数，描述工作流基于频率运行的频繁度。 下面是最小和最大间隔： <p>- 月：1-16 个月 <br>-周：1-71 周 <br>- 天：1-500 天 <br>- 小时：1-12,000 小时 <br>- 分钟：1-72,000 分钟 <br>- 秒：1-9,999,999 秒 <p>例如，如果间隔为 6，频率为“月”，则重复周期为每 6 个月。 |
   | **频率** | `frequency` | 是 | String | 定期计划的时间单位：“秒”、“分钟”、“小时”、“天”、“周”或“月”************************ |
   ||||||

   ![高级定期选项](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   如需更多定期选项，请打开“添加新参数”列表。**** 选择的任何选项会在选定后显示在触发器中。

   | 属性 | 必须 | JSON 名称 | 类型 | 说明 |
   |----------|----------|-----------|------|-------------|
   | **推迟** | 否 | delay | String | 根据 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Durations)延迟每个定期的时间 |
   | **时区** | 否 | timezone | String | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | 否 | startTime | String | 采用以下格式提供启动日期和时间： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>例如，如果需要 2017 年 9 月 18 日下午 2 点，则指定“2017-09-18T14:00:00”并选择时区（如“太平洋标准时间”）。 或者指定“2017-09-18T14:00:00Z”且不选择时区。 <p>**注意：** 此开始时间必须遵循 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单计划，开始时间指首次运行时间；对于高级定期，触发器的激发时间不会早于开始时间。 [*可通过哪些方式使用开始日期和时间？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 现在，请使用其他操作生成剩余的工作流。 有关可添加的其他操作，请参阅 [Azure 逻辑应用的连接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流定义 - 滑动窗口

在逻辑应用的基础工作流定义（使用 JSON）中，可以通过选定的选项查看“滑动窗口”触发器定义。 若要查看此定义，请在设计器工具栏上选择“代码视图”。**** 若要返回到设计器，请选择设计器工具栏上的“设计器”****。

本示例介绍了“滑动窗口”触发器定义在基础工作流定义中看起来是什么样的，具体说来，对于按小时进行的重复触发，每个重复触发的延迟是 5 秒：

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>后续步骤

* [延迟工作流中的下一操作](../connectors/connectors-native-delay.md)
* [适用于逻辑应用的连接器](../connectors/apis-list.md)