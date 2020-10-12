---
title: 延迟工作流中的下一操作
description: 使用 Azure 逻辑应用中的“延迟”或“延迟截止时间”操作等待在逻辑应用工作流中运行下一操作
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "74787330"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>在 Azure 逻辑应用中延迟运行下一操作

若要在运行下一操作之前让逻辑应用等待一定的时间，可以在逻辑应用的工作流中的某个操作之前添加内置的“延迟 - 计划”  操作。 也可添加内置的“延迟截止时间 - 计划”操作，等到特定的日期和时间过后再运行下一操作。  有关内置“计划”操作和触发器的详细信息，请参阅[使用 Azure 逻辑应用计划和运行定期自动执行的任务和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

* **延迟**：等待指定的时间单位数（例如秒数、分钟数、小时数、天数、周数或月数），然后运行下一操作。

* **延迟截止时间**：等到指定的日期和时间，然后运行下一操作。

下面是一些演示如何使用这些操作的示例方式：

* 等到工作日通过电子邮件发送状态更新。

* 在恢复和检索数据前，延迟工作流直到 HTTP 调用完成。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 在使用操作之前，必须先使用触发器启动逻辑应用。 在添加延迟操作之前，可以使用任何所需的触发器并添加其他操作。 本主题使用 Office 365 Outlook 触发器。 如果不熟悉逻辑应用，请了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>添加延迟操作

1. 在逻辑应用设计器中，请在要添加延迟操作的步骤下选择“新建步骤”  。

   若要在步骤之间添加延迟操作，请将指针移动到连接这些步骤的箭头之上。 选择出现的加号 (+)，然后选择“添加操作”。 

1. 在搜索框中，输入“延迟”作为筛选器。 在操作列表中选择此操作：**延迟**

   ![添加“延迟”操作](./media/connectors-native-delay/add-delay-action.png)

1. 指定下一操作运行前需要等待的时间。

   ![设置延迟的时间](./media/connectors-native-delay/delay-time-intervals.png)

   | 属性 | JSON 名称 | 必须 | 类型 | 说明 |
   |----------|-----------|----------|------|-------------|
   | 计数 | count | 是 | Integer | 要延迟的时间单位数 |
   | 计价单位 | unit | 是 | String | 时间单位，例如：`Second`、`Minute`、`Hour`、`Day`、`Week` 或 `Month` |
   ||||||

1. 添加需要在工作流中运行的任何其他操作。

1. 完成后，保存逻辑应用。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>添加“延迟截止时间”操作

1. 在逻辑应用设计器中，请在要添加延迟操作的步骤下选择“新建步骤”  。

   若要在步骤之间添加延迟操作，请将指针移动到连接这些步骤的箭头之上。 选择出现的加号 (+)，然后选择“添加操作”。 

1. 在搜索框中，输入“延迟”作为筛选器。 在操作列表中选择此操作：**延迟截止时间**

   ![添加“延迟截止时间”操作](./media/connectors-native-delay/add-delay-until-action.png)

1. 提供结束日期和时间，以便确定何时需要继续该工作流。

   ![指定何时结束延迟的时间戳](./media/connectors-native-delay/delay-until-timestamp.png)

   | 属性 | JSON 名称 | 必须 | 类型 | 说明 |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | 是 | String | 结束日期和时间（用于继续工作流），采用以下格式： <p>YYYY-MM-DDThh:mm:ssZ <p>例如，如果所需时间为 2017 年 9 月 18 日下午 2:00，请指定“2017-09-18T14:00:00Z”。 <p>**注意：** 此时间格式必须遵循 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，采用 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果没有时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 |
   ||||||

1. 添加需要在工作流中运行的任何其他操作。

1. 完成后，保存逻辑应用。

## <a name="next-steps"></a>后续步骤

* [使用“定期”触发器创建、计划和运行重复任务和工作流](../connectors/connectors-native-recurrence.md)
* [适用于逻辑应用的连接器](../connectors/apis-list.md)