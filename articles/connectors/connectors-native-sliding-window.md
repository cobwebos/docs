---
title: 安排周期性任务使用滑动窗口触发器-Azure 逻辑应用
description: 计划和使用 Azure 逻辑应用中的滑动窗口触发器运行定期的自动化的任务和工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299497"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>创建、 安排和使用 Azure 逻辑应用中的滑动窗口触发器运行重复任务和工作流

若要定期运行任务、 进程或必须处理连续区块中的数据的作业，可以开始使用逻辑应用工作流**可调窗口-计划**触发器。 您可以设置日期和时间，以及用于启动工作流并重复该工作流了重复发生的时区。 如果出于何种原因会丢失的定期计划，此触发器将处理这些丢失的重复周期。 例如，同步数据库和备份存储之间的数据，使用滑动窗口触发器，以便数据同步而不会产生空白。 有关内置的计划触发器和操作的详细信息，请参阅[计划和定期自动运行、 任务和使用 Azure 逻辑应用工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

下面是此触发器支持的一些模式：

* 立即运行，然后重复每个*n*秒、 分钟或小时数。

* 开始在特定日期和时间，然后运行并重复每个*n*秒、 分钟或小时数。 使用此触发器，可以在过去，所有过去的定期计划运行指定的开始时间。

* 延迟每个定期计划运行之前的特定时长。

此触发器定期触发器之间的差异或有关计划定期工作流的详细信息，请参阅[计划和运行定期自动执行任务、 进程和使用 Azure 逻辑应用工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果你想要触发逻辑应用并在以后，请参阅中运行一次只能[运行作业一次仅](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果你没有订阅，则可以[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关的基本知识[逻辑应用](../logic-apps/logic-apps-overview.md)。 如果您熟悉逻辑应用，了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>添加滑动窗口触发器

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器出现，请在搜索框中后, 输入"滑动窗口"作为筛选器。 从触发器列表中，在逻辑应用工作流中的第一步中选择以下触发器：**滑动窗口**

   ![选择"滑动窗口"触发器](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 设置定期计划的间隔和频率。 在此示例中，请将这些属性设置为每周运行工作流。

   ![设置间隔和频率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 属性 | 需要 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | 间隔  | 是 | interval | Integer | 一个正整数，描述工作流基于频率运行的频繁度。 下面是最小和最大间隔： <p>- 小时：1-12,000 小时 </br>- 分钟：1-72,000 分钟 </br>- 秒：1-9,999,999 秒<p>例如，如果间隔为 6，频率为"Hour"，然后重复周期为每 6 小时。 |
   | **频率** | 是 | 频率 | String | 定期计划的时间单位：**第二个**，**分钟**，或**小时** |
   ||||||

   ![高级重复执行选项](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   有关更多的重复周期选项，打开**添加新参数**列表。 
   所选内容后触发器显示您选择任何选项。

   | 属性 | 必选 | JSON 名称 | Type | 描述 |
   |----------|----------|-----------|------|-------------|
   | **Delay** | 否 | delay | String | 每个重复使用的延迟的持续时间[ISO 8601 日期时间格式](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **时区** | 否 | timezone | String | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | 否 | startTime | String | 提供开始日期和时间按以下格式： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>因此，例如，如果你希望 2017 年 9 月 18 日下午 2:00，然后指定"2017年-09-18T14:00:00"，然后选择如太平洋标准时间的时区。 或者指定“2017-09-18T14:00:00Z”且不选择时区。 <p>**注意：** 此开始时间必须遵循 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，采用 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单的计划的开始时间是第一个匹配项，而对于高级的定期计划，触发该触发器不会早于开始时间。 [*可通过哪些方式使用开始日期和时间？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 现在，构建剩余的工作流使用其他操作。 您可以添加的更多操作，请参阅[适用于 Azure 逻辑应用连接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流定义的滑动窗口

在逻辑应用的基础工作流定义，它使用 JSON，可以查看与所选的选项的滑动窗口触发器定义。 若要查看此定义中，设计器工具栏上，选择**代码视图**。 若要返回到设计器，请选择在设计器工具栏上，**设计器**。

此示例演示滑动窗口触发器定义可能的显示方式的基础工作流定义在其中每个定期计划的延迟时间是每小时的重复周期为 5 秒：

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

* [在工作流中的下一步操作的延迟](../connectors/connectors-native-delay.md)
* [适用于逻辑应用连接器](../connectors/apis-list.md)