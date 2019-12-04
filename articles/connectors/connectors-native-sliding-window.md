---
title: 计划处理连续数据的任务
description: 通过在 Azure 逻辑应用中使用滑动窗口来创建和运行处理连续数据的周期性任务
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786905"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用滑动窗口触发器来计划和运行连续数据的任务

若要定期运行必须处理连续块区中的数据的任务、进程或作业，可以通过**滑动窗口**触发器启动逻辑应用工作流。 可以设置日期和时间以及用于启动工作流的时区以及重复该工作流的重复周期。 如果因任何原因而丢失了重复，此触发器将处理丢失的重复。 例如，在数据库和备份存储之间同步数据时，请使用滑动窗口触发器，以便在不产生空白的情况下同步数据。 有关内置计划触发器和操作的详细信息，请参阅[通过 Azure 逻辑应用计划和运行定期自动执行、任务和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

下面是此触发器支持的一些模式：

* 立即运行，并每隔*n*秒、分钟或小时重复。

* 在特定的日期和时间启动，然后运行并每隔*n*秒、分钟或小时重复一次。 使用此触发器，你可以指定过去的开始时间，这将运行过去的所有重复项。

* 在运行之前延迟特定持续时间的每个重复。

有关此触发器与定期触发器之间的差异，或者有关计划重复工作流的详细信息，请参阅[通过 Azure 逻辑应用计划和运行定期自动化任务、进程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果要触发逻辑应用并在将来仅运行一次，请参阅[只运行一次作业](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请学习[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>添加滑动窗口触发器

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 显示逻辑应用设计器后，在 "搜索" 框中输入 "滑动窗口" 作为筛选器。 从 "触发器" 列表中，选择此触发器作为逻辑应用工作流中的第一步：**滑动窗口**

   ![选择 "滑动窗口" 触发器](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 设置定期计划的间隔和频率。 在此示例中，请将这些属性设置为每周运行工作流。

   ![设置间隔和频率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | properties | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | 间隔 | 是 | interval | Integer | 一个正整数，它基于频率描述工作流运行的次数。 下面是最小和最大间隔： <p>- 小时：1-12,000 小时 </br>- 分钟：1-72,000 分钟 </br>- Second：1-9,999,999 秒<p>例如，如果间隔为6，频率为 "Hour"，则重复周期为每6小时一次。 |
   | **频率** | 是 | frequency | 字符串 | 重复周期的时间单位：**秒**、**分钟**或**小时** |
   ||||||

   ![高级重复周期选项](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   对于 "重复周期" 选项，请打开 "**添加新参数**" 列表。 
   选择后，会在触发器上显示您选择的任何选项。

   | properties | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | **推迟** | No | delay | 字符串 | 使用[ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Durations)延迟每个重复周期的持续时间 |
   | **时区** | No | timezone | 字符串 | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | No | startTime | 字符串 | 提供此格式的开始日期和时间： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>例如，如果你想要2017年9月18日下午2:00，请指定 "2017-09-18T14：00： 00" 并选择时区，如太平洋标准时间。 或者指定“2017-09-18T14:00:00Z”，无需指定时区。 <p>**注意：** 此启动时间必须遵循 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单的计划，开始时间是第一次发生，而对于高级重复，触发器的触发时间不会早于开始时间。 [*可通过哪些方式使用开始日期和时间？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 现在，用其他操作生成剩余的工作流。 有关可添加的更多操作，请参阅[Azure 逻辑应用的连接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流定义-滑动窗口

在使用 JSON 的逻辑应用基础工作流定义中，可以使用所选选项查看滑动窗口触发器定义。 若要查看此定义，请在设计器工具栏上选择 "**代码视图**"。 若要返回设计器，请在设计器工具栏上选择 "**设计器**"。

此示例演示如何在基础工作流定义中查看滑动窗口触发器定义，其中每个重复周期的延迟为每小时重复周期5秒：

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

* [延迟工作流中的下一步操作](../connectors/connectors-native-delay.md)
* [逻辑应用的连接器](../connectors/apis-list.md)