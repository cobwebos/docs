---
title: "在逻辑应用中添加重复触发器 | Microsoft Docs"
description: "重复触发器及其如何将其用于 Azure 逻辑应用的概述。"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: fe558958c316c8dba42163e277ae01451f712e5a
ms.contentlocale: zh-cn
ms.lasthandoff: 01/20/2017

---
# <a name="get-started-with-the-recurrence-trigger"></a>重复触发器入门
通过使用重复触发器，可在云中创建功能强大的功能流。

例如，你可以：

* 将工作流计划为每天运行 SQL 存储过程。
* 通过电子邮件发送上周内关于特定井号标签的所有推文的摘要。

若要开始在逻辑应用中使用重复触发器，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="use-a-recurrence-trigger"></a>使用重复触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](connectors-overview.md)。

下面是显示如何在逻辑应用中设置重复触发器的示例序列：

1. 添加**重复周期**触发器作为逻辑应用中的第一步。
2. 为重复周期间隔填写参数。

逻辑应用现在在每个时间间隔之后启动。

![HTTP 触发器](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>触发器详细信息
重复触发器具有以下可配置的属性。

它在指定时间间隔后触发逻辑应用。
* 表示它是必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| Frequency* |频率 |时间单位：`Second`、`Minute`、`Hour`、`Day` 或 `Year`。 |
| Interval* |interval |重复周期的给定频率的间隔。 |
| 时区 |timezone |如果在没有 UTC 时差的情况下提供开始时间，将使用此时区。 |
| 开始时间 |startTime |采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的开始时间。 |

<br>

## <a name="next-steps"></a>后续步骤
现在，试用平台并[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。


