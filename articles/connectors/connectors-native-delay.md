---
title: "在逻辑应用中添加延迟 | Microsoft Docs"
description: "延迟和延迟到操作以及如何将它们用于 Azure 逻辑应用的概述。"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e4281c70dc0edd45ff485c64a499291b23ad51b7


---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>延迟和延迟到操作入门
通过使用延迟和“延迟到”操作，可以完成各种工作流方案。

例如，你可以：

* 等到工作日通过电子邮件发送状态更新。
* 在恢复和检索结果前，延迟工作流直到 HTTP 调用有时间完成。

若要开始在逻辑应用中使用延迟操作，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="use-the-delay-actions"></a>使用延迟操作
操作是由在逻辑应用中定义的工作流执行的操作。 [了解有关操作的详细信息](connectors-overview.md)。

下面是如何在逻辑应用中使用延迟步骤的示例序列：

1. 添加触发器后，单击“新步骤”添加操作。
2. 搜索“延迟”弹出延迟操作。 在此示例中，我们将选择“延迟”。
   
    ![延迟操作](./media/connectors-native-delay/using-action-1.png)
3. 完成任意操作属性以配置延迟。
   
    ![延迟配置](./media/connectors-native-delay/using-action-2.png)
4. 单击“保存”发布并激活逻辑应用。

## <a name="action-details"></a>操作详细信息
重复触发器具有以下可配置的属性。

### <a name="delay-action"></a>延迟操作
此操作使运行延迟特定时间间隔。
* 表示它是必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 计数* |count |要延迟的时间单位数 |
| 单位* |单位 |时间单位：`Second`、`Minute`、`Hour` 或 `Day` |

<br>

### <a name="delay-until-action"></a>延迟到操作
此操作将运行延迟到指定日期/时间。
* 表示它是必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 年* |timestamp |要延迟到的年 (GMT) |
| 月* |timestamp |要延迟到的月 (GMT) |
| 日* |timestamp |要延迟到的日 (GMT) |

<br>

## <a name="next-steps"></a>后续步骤
现在，试用平台并[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。




<!--HONumber=Nov16_HO3-->


