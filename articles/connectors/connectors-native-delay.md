---
title: 工作流-Azure 逻辑应用中的下一步操作的延迟
description: 等待 Azure 逻辑应用中使用的延迟直到操作在逻辑应用工作流中运行下一步操作
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297635"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>在 Azure 逻辑应用中运行下一步操作的延迟

若要使逻辑应用等待一段时间运行的下一步操作之前，可以添加内置**延迟-计划**操作，然后才能在逻辑应用的工作流中的操作。 或者，可以添加内置**延迟截止时间-安排**特定日期和时间之前运行的下一步操作之前要等待的操作。 有关内置的计划操作和触发器的详细信息，请参阅[计划和定期自动运行、 任务和使用 Azure 逻辑应用工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

* **延迟**:等待指定数目的时间单位，例如秒、 分钟、 小时、 天、 周或几个月下, 一步操作运行之前。

* **延迟截止时间**:等到指定的日期和时间之前在下一步操作运行。

下面是一些示例方法使用这些操作：

* 等到工作日通过电子邮件发送状态更新。

* 延迟工作流直到 HTTP 调用完成之前在恢复和检索数据。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果你没有订阅，则可以[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关的基本知识[逻辑应用](../logic-apps/logic-apps-overview.md)。 可以使用操作之前，必须首先使用触发器启动逻辑应用。 可以使用任何需要的触发器添加其他操作之前添加延迟操作。 本主题使用 Office 365 Outlook 触发器。 如果您熟悉逻辑应用，了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>添加延迟操作

1. 在逻辑应用设计器中，你想要添加延迟操作，在步骤下选择**新步骤**。

   若要添加步骤之间的延迟操作，请将指针移连接步骤的箭头。 选择加号 （+），并选择**添加操作**。

1. 在搜索框中，输入作为筛选器的"延迟"。 在操作列表中选择此操作：**Delay**

   ![添加"延迟"操作](./media/connectors-native-delay/add-delay-action.png)

1. 指定要在下一步操作运行前等待时间的量。

   ![设置长时间延迟](./media/connectors-native-delay/delay-time-intervals.png)

   | 属性 | JSON 名称 | 需要 | Type | 描述 |
   |----------|-----------|----------|------|-------------|
   | Count | 计数 | 是 | Integer | 要延迟的时间单位数 |
   | 单位 | 单位 | 是 | String | 单位的时间，例如： `Second`， `Minute`， `Hour`， `Day`， `Week`，或 `Month` |
   ||||||

1. 添加你想要在工作流中运行的任何其他操作。

1. 完成后，保存逻辑应用。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>添加延迟-until 操作

1. 在逻辑应用设计器中，你想要添加延迟操作，在步骤下选择**新步骤**。

   若要添加步骤之间的延迟操作，请将指针移连接步骤的箭头。 选择加号 （+），并选择**添加操作**。

1. 在搜索框中，输入作为筛选器的"延迟"。 在操作列表中选择此操作：**延迟截止时间**

   ![添加“延迟截止时间”操作](./media/connectors-native-delay/add-delay-until-action.png)

1. 当你想要恢复工作流提供的结束日期和时间。

   ![指定何时结束延迟的时间戳](./media/connectors-native-delay/delay-until-timestamp.png)

   | 属性 | JSON 名称 | 必选 | Type | 描述 |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | 是 | String | 结束日期和时间的恢复工作流使用此格式： <p>YYYY-MM-DDThh:mm:ssZ <p>因此，例如，如果想 2017 年 9 月 18 日下午 2:00，则指定"2017年-09-18T14:00:00Z"。 <p>**注意：** 此时间格式必须遵循[ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)中[UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)，但如果没有[UTC 偏移量](https://en.wikipedia.org/wiki/UTC_offset)。 没有时区，必须在不包含任何空格末尾添加字母"Z"。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 |
   ||||||

1. 添加你想要在工作流中运行的任何其他操作。

1. 完成后，保存逻辑应用。

## <a name="next-steps"></a>后续步骤

* [创建、 安排和使用重复触发器运行重复任务和工作流](../connectors/connectors-native-recurrence.md)
* [适用于逻辑应用连接器](../connectors/apis-list.md)