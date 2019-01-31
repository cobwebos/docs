---
title: 如何通过 Conversation Learner 模型使用可否定实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过 Conversation Learner 模型使用可否定实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fea950e2c13d9b5ce0c3619990961e611edd6626
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207371"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何通过 Conversation Learner 模型使用可否定实体

本教程演示了实体的“可否定”属性。

## <a name="video"></a>视频

[![可否定的实体教程预览](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
实体的“可否定”属性可用于标记实体的正常（正）实例和负实例、基于正模型和负模型进行教学，以及清除现有实体的值。 设置了其“可否定”属性的实体在 Conversation Leaner 中称为“可否定的实体”。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中，键入“NegatableEntity”，然后按 Enter。
3. 单击“创建”按钮。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“自定义”作为“实体类型”。
3. 键入“name”作为“实体名称”。
4. 选中“可否定”复选框。
    - 选中此属性将允许用户提供实体值或者声明某项不是实体值。 在后一种情况下，结果是删除匹配的实体值。
5. 单击“创建”按钮。

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应”字段中，键入“我不知道你的名字。”
3. 在“取消资格实体”字段中，键入“name”。
4. 单击“创建”按钮。

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应”字段中，键入“我知道你的名字。 它是 $name。”
3. 单击“创建”按钮。

> [!NOTE]
> “name”实体已自动由响应中的引用添加为“所需实体”。

现在已有两个操作。

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“你好。”
3. 单击“对操作打分”按钮。
4. 在“响应”中，键入“我不知道你的名字。”
    - 百分位数是 100%，因为唯一的有效操作基于约束。
5. 在聊天面板中，在显示了“键入你的消息...”的位置键入“我的名字是 Frank”
6. 选择“Frank”，并选择标签“+名字”
    - “name”实体有两个实例：“+名字”和“-名字”。  (+) 加号添加或覆盖该值。 (-) 减号移除该值。
7. 单击“对操作打分”按钮。
    - “name”实体目前在模型的内存中定义为“Frank”，因此会显示“我知道你的名字。 它是 $name”操作。
8. 选择响应“我知道你的名字。 它是 $name。”
9. 在聊天面板中，在显示了“键入你的消息...”的位置键入“我的名字不是 Frank。”
10. 选择“Frank”，并选择标签“-名字”
    - 我们选择“-名字”来清除实体的当前值。
11. 单击“对操作打分”按钮。
12. 在“响应”中，键入“我不知道你的名字。”
13. 在聊天面板中，在显示了“键入你的消息...”的位置键入“我的名字是 Susan。”
14. 选择“Susan”，并选择标签“+名字”

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [多值实体](./07-multi-value-entities.md)
