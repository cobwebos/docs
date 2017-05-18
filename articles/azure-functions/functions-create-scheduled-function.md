---
title: "在 Azure 中创建按计划运行的函数 | Microsoft Docs"
description: "了解如何在 Azure 中创建根据你定义的计划运行的函数。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>在 Azure 中创建由计时器触发的函数

了解如何使用 Azure Functions 创建根据你定义的计划运行的函数。 

![在 Azure 门户中创建 Function App](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

完成本主题中的所有步骤应该不需要五分钟。

## <a name="prerequisites"></a>先决条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

在本主题中，将在现有 Function App 中创建计时器触发的函数。 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>创建计时器触发的函数

1. 展开 Function App，依次单击“函数”旁边的 **+** 按钮、与所需语言相对应的 **TimerTrigger** 模板。 然后，使用表中指定的设置，并单击“创建”：

    | 设置      |  建议的值   | 说明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **为函数命名** | TimerTriggerCSharp1 | 定义计时器触发的函数的名称。
    | **[计划](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | 六字段 [CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)，计划函数每分钟运行一次。 |

    将使用所选语言创建一个每分钟运行一次的函数。 

4. 通过查看写入日志的跟踪信息来验证执行情况。 

    ![Azure 门户中的“函数日志”查看器。](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

现在，可以更改函数的计划，减少其运行频率，例如每小时运行一次。 

## <a name="update-the-timer-schedule"></a>更新计时器计划

1. 展开函数，然后单击“集成”。 可在此处定义函数的输入和输出绑定，还可以设置计划。 

2. 输入新**计划**值（`0 0 */1 * * *`），然后单击“保存”。  

![Azure 门户中的函数更新计时器计划。](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

现在已获得一个每小时运行一次的函数。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤
已创建一个按计划运行的函数。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

有关计时器触发器的详细信息，请参阅[使用 Azure Functions 计划代码执行](functions-bindings-timer.md)。 




