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
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708048"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>如何通过对话学习器模型使用多值实体
本教程介绍实体的“多值”属性。

## <a name="video"></a>视频

[![多值实体教程预览](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>要求
本教程要求常规教程机器人处于运行状态

    npm run tutorial-general

## <a name="details"></a>详细信息
多值实体将值累积到列表中，而不是存储单个值。  当用户可以指定多个值时，这些实体非常有用。 例如，披萨上的配品。

标记为“多值”的实体会将所识别出的每个实体实例追加到机器人的内存中的一个列表中。 后续识别将追加到实体的值后面，而不是覆盖。

## <a name="steps"></a>Steps

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择**新的模型**。
2. 输入**MultiValueEntities**有关**名称**。
3. 选择“创建”。

### <a name="entity-creation"></a>创建实体

1. 选择**实体**在左侧的窗格中，然后**新实体**。
2. 选择**自定义培训**有关**实体类型**。
3. 输入**浇头**有关**实体名称**。
4. 检查**多值**若要启用该实体累积了一个或多个值。
5. 检查**Negatable**。
6. 选择“创建”。

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>创建第一个操作

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**以下是你浇头： $toppings**为**智能机器人应用程序的响应...**.前导美元符号指示实体引用。
3. 选择“创建”。

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**哪些浇头？** 为**智能机器人应用程序的响应...**.
3. 输入**浇头**有关**取消将赋予**。
4. 选择“创建”。

现在已有两个操作。

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>训练模型

1. 选择**训练对话框**在左侧的窗格中，然后**新训练对话框**。
2. 输入**大家好**为左的聊天面板中的用户的语句。
3. 选择**评分操作**。
4. 选择**想让哪些浇头？** 从操作列表。 百分位作为基于约束的唯一有效操作为 100%。
5. 输入**奶酪和蘑菇**为左的聊天面板中的用户的语句。
6. 突出显示**奶酪**然后选择 **+ 浇头**。
7. 突出显示**蘑菇**然后选择 **+ 浇头**。
8. 选择**评分操作**。
9. 选择**以下是你浇头： $toppings**从操作列表。
10. 输入**添加辣椒**为左的聊天面板中的用户的下一个语句。
11. 突出显示**辣椒**然后选择 **+ 浇头**。
12. 选择**评分操作**。
13. 选择**以下是你浇头： $toppings**从操作列表。
14. 输入**删除奶酪**为左的聊天面板中的用户的第三个语句。
15. 突出显示**奶酪**然后选择 **-浇头**。
16. 选择**评分操作**。
17. 选择**以下是你浇头： $toppings**从操作列表。

![](../media/T07_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预先训练的实体](./08-pre-trained-entities.md)
