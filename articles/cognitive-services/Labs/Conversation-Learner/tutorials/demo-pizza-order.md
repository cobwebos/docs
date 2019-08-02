---
title: 演示对话学习器模型，披萨订购 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何创建演示对话学习器模型。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703422"
---
# <a name="demo-pizza-order"></a>演示：披萨订单
此演示演示了披萨订购机器人，其支持单个披萨订购的方式包括：

- 在用户话语中识别比萨配料
- 管理配料清单，并做出适当的响应
- 记住以前的订单，加快重新订购相同披萨的速度

## <a name="video"></a>视频

[![演示披萨预览](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>要求
本教程要求运行披萨订购机器人

    npm run demo-pizza

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击“TutorialDemo Pizza Order”。 

## <a name="entities"></a>实体

此模型包含三个实体：

- “配料”汇聚了用户的指定配料（如果可用）。
- “OutofStock”向用户指出所选配料没有存货
- “LastToppings”包含上次订购曾用过的配料

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>个操作

此模型包含一组操作，这些操作会询问用户的配料选择、累积配料等内容。

此外还提供两个 API 调用：

- “FinalizeOrder”处理订单的履行
- “UseLastToppings”处理历史配料信息

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>训练对话

在模型中发现多个训练对话。

![](../media/tutorial_pizza_dialogs.PNG)

让我们创建另一个训练对话，对模型进行稍微多一些的训练。

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“订购带奶酪的披萨”
    - “奶酪”一词由实体提取器提取。
3. 单击“对操作打分”按钮。
4. 选择响应“你的披萨有奶酪。”
5. 选择响应“想要其他配料吗?”
6. 在聊天面板中显示“键入你的消息...”的位置，键入“加蘑菇和辣椒”
7. 单击“对操作打分”按钮。
8. 选择响应“你的披萨有奶酪、蘑菇和辣椒。”
9. 选择响应“想要其他配料吗?”
10. 在聊天面板中显示“键入你的消息...”的位置，键入“去掉辣椒，加香肠”
11. 单击“对操作打分”按钮。
12. 选择响应“你的披萨有奶酪、蘑菇和香肠。”
13. 选择响应“想要其他配料吗?”
14. 在聊天面板中显示“键入你的消息...”的位置，键入“加洋芋”
15. 单击“对操作打分”按钮。
    - “洋芋”值由实体检测回调代码添加到“OutofStock”中，因为该文本与系统支持的任何原料都不匹配。
16. 选择响应“OutOfStock”
17. 选择响应“想要其他配料吗?”
18. 在聊天面板中显示“键入你的消息...”的位置，键入“否”
    - “否”未被标记为任何类型的意向。 我们会根据当前的上下文选择相关操作。
19. 单击“对操作打分”按钮。
20. 选择响应“FinalizeOrder”
    - 选择此操作后，客户的当前配料就会保存到“LastToppings”实体，而“配料”实体则由 FinalizeOrder 回调代码删除。
21. 在聊天面板中显示“键入你的消息...”的位置，键入“订购另一个”
22. 单击“对操作打分”按钮。
23. 选择响应“你想要奶酪、蘑菇和香肠吗?”
    - 此操作现在可用是因为“LastToppings”实体已设置。
24. 在聊天面板中显示“键入你的消息...”的位置，键入“是”
25. 单击“对操作打分”按钮。
26. 选择响应“UseLastToppings”
27. 选择响应“你的披萨有奶酪、蘑菇和香肠。”
28. 选择响应“想要其他配料吗?”

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署会话学习器机器人](../deploy-to-bf.md)
