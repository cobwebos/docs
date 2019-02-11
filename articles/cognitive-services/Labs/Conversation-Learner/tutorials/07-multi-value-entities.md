---
title: 如何通过 Conversation Learner 模型使用多值实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用多值实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228298"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>如何通过对话学习器模型使用多值实体
本教程介绍实体的“多值”属性。

## <a name="video"></a>视频

[![多值实体教程预览](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
多值实体将值累积到列表中，而不是存储单个值。  当用户可以指定多个值时，这些实体非常有用。 例如，披萨上的配品。

标记为“多值”的实体会将所识别出的每个实体实例追加到机器人的内存中的一个列表中。 后续识别将追加到实体的值后面，而不是覆盖。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中键入“MultiValueEntities”，然后按 Enter。
3. 单击“创建”按钮。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“自定义训练”作为“实体类型”。
3. 键入“toppings”作为“实体名称”。
4. 选中“多值”复选框。
    - 多值实体会将一个或多个值累积到实体中。
5. 选中“可否定”复选框。
    - “可否定”属性在另一个教程中介绍。
6. 单击“创建”按钮。

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在响应中，键入“下面是你的配品: $Toppings”。
    - 前导美元符号指示实体引用
3. 单击“创建”按钮。

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“你想要什么配品?”
3. 在“取消资格实体”字段中，键入“toppings”。
4. 单击“创建”按钮。

现在已有两个操作。

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“你好。”
3. 单击“对操作打分”按钮。
4. 选择响应“你想要什么配品?”
    - 百分位数是 100%，因为唯一的有效操作基于约束。
5. 在聊天面板中，在显示了“键入你的消息...”的位置键入“奶酪和蘑菇”
6. 单击“奶酪”，然后选择标签“+配品”
7. 单击“蘑菇”，然后选择标签“+配品”
8. 单击“对操作打分”按钮。
9. 选择响应“下面是你的配品: $Toppings”
10. 在聊天面板中，在显示了“键入你的消息...”的位置键入“添加胡椒”
11. 单击“胡椒”，然后选择标签“+配品”
12. 单击“对操作打分”按钮。
13. 选择响应“下面是你的配品: $Toppings”
14. 在聊天面板中，在显示了“键入你的消息...”的位置键入“删除奶酪”
15. 单击“奶酪”，然后选择标签“-配品”
16. 单击“对操作打分”按钮。
17. 选择响应“下面是你的配品: $Toppings”

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预先训练的实体](./08-pre-trained-entities.md)
