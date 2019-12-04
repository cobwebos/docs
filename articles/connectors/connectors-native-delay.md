---
title: 延迟工作流中的下一步操作
description: 等待运行逻辑应用工作流中的下一个操作，方法是使用延迟或延迟，直到 Azure 逻辑应用中的操作
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787330"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>延迟在 Azure 逻辑应用中运行下一个操作

若要让逻辑应用在运行下一个操作之前等待一段时间，可以在逻辑应用的工作流中的操作之前添加内置**延迟计划**操作。 或者，你可以添加内置**延迟到-Schedule**操作，等待到在运行下一个操作之前的特定日期和时间。 有关内置计划操作和触发器的详细信息，请参阅[通过 Azure 逻辑应用计划和运行定期自动执行、任务和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

* **延迟**：等待指定的时间单位数，例如秒、分钟、小时、天、周或月，然后再运行下一个操作。

* **延迟至**：等待直到下一个操作运行之前指定的日期和时间。

下面是一些使用这些操作的示例方法：

* 等到工作日通过电子邮件发送状态更新。

* 延迟工作流，直到 HTTP 调用完成，然后再恢复和检索数据。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 你的逻辑应用必须首先从触发器开始，然后才能使用操作。 你可以使用任何所需的触发器，并在添加延迟操作之前添加其他操作。 本主题使用 Office 365 Outlook 触发器。 如果不熟悉逻辑应用，请学习[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>添加延迟操作

1. 在逻辑应用设计器中，在要添加延迟操作的步骤下，选择 "**新建步骤**"。

   若要在步骤之间添加延迟操作，请将指针移到连接步骤的箭头上。 选择出现的加号（+），然后选择 "**添加操作**"。

1. 在搜索框中，输入 "延迟" 作为筛选器。 从 "操作" 列表中，选择此操作：**延迟**

   ![添加 "延迟" 操作](./media/connectors-native-delay/add-delay-action.png)

1. 指定在下一操作运行之前要等待的时间量。

   ![设置延迟的时间量](./media/connectors-native-delay/delay-time-intervals.png)

   | properties | JSON 名称 | 需要 | Type | 描述 |
   |----------|-----------|----------|------|-------------|
   | 计数 | count | 是 | Integer | 要延迟的时间单位数 |
   | 计价单位 | 单位 | 是 | 字符串 | 时间单位，例如： `Second`、`Minute`、`Hour`、`Day`、`Week`或 `Month` |
   ||||||

1. 添加要在工作流中运行的任何其他操作。

1. 完成后，保存逻辑应用。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>添加延迟-直到操作

1. 在逻辑应用设计器中，在要添加延迟操作的步骤下，选择 "**新建步骤**"。

   若要在步骤之间添加延迟操作，请将指针移到连接步骤的箭头上。 选择出现的加号（+），然后选择 "**添加操作**"。

1. 在搜索框中，输入 "延迟" 作为筛选器。 从 "操作" 列表中，选择此操作：**延迟到**

   ![添加“延迟截止时间”操作](./media/connectors-native-delay/add-delay-until-action.png)

1. 提供要恢复工作流的结束日期和时间。

   ![指定何时结束延迟的时间戳](./media/connectors-native-delay/delay-until-timestamp.png)

   | properties | JSON 名称 | 需要 | Type | 描述 |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | 是 | 字符串 | 使用此格式恢复工作流的结束日期和时间： <p>YYYY-MM-Yyyy-mm-ddthh： MM： ssZ <p>例如，如果想要2017年9月18日2:00，请指定 "2017-09-18T14：00： 00Z"。 <p>**注意：** 此时间格式必须遵循[utc 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有[utc 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果没有时区，则必须在末尾添加字母 "Z" （不含空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 |
   ||||||

1. 添加要在工作流中运行的任何其他操作。

1. 完成后，保存逻辑应用。

## <a name="next-steps"></a>后续步骤

* [通过定期触发器创建、计划和运行重复执行的任务和工作流](../connectors/connectors-native-recurrence.md)
* [逻辑应用的连接器](../connectors/apis-list.md)