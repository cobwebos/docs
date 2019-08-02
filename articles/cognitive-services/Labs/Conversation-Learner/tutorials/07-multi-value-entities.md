---
title: 如何通过 Conversation Learner 模型使用多值实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用多值实体。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704084"
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

## <a name="steps"></a>步骤

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择 "**新建模型**"。
2. 为 "**名称**" 输入**MultiValueEntities** 。
3. 选择“创建”。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中选择 "**实体**", 然后选择 "**新建实体**"。
2. 为 "**实体类型**" 选择 "**自定义训练**"。
3. 为 "**实体名称**" 输入**浇头**。
4. 检查**多值**以使实体累积一个或多个值。
5. 请检查**Negatable**。
6. 选择“创建”。

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 在**此处输入你的浇头:** **机器人响应**$toppings前导美元符号表示实体引用。
3. 选择“创建”。

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 输入**所需的浇头？** , 以获取**机器人的响应 ...** 。
3. 对于**Disqualifying**证明, 请输入**浇头**。
4. 选择“创建”。

现在已有两个操作。

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板中选择 "**训练**对话框", 然后选择 "**新建定型" 对话框**。
2. 在左侧聊天面板中, 为用户查询文本输入 " **hi** "。
3. 选择 "**评分操作**"。
4. 从 "操作" 列表中选择所**需的 "浇头"** 。 只有百分比为 100%, 这是基于约束的唯一有效操作。
5. 在左侧聊天面板中为用户的查询文本输入**奶酪和蘑菇**。
6. 突出显示**奶酪**, 然后选择 **+ 浇头**。
7. 突出显示**蘑菇**, 然后选择 **+ 浇头**。
8. 选择 "**评分操作**"。
9. 从 "操作" 列表中选择 "**浇头: $toppings** "。
10. 在左侧的聊天面板中输入用户的下一个查询文本的 "**添加辣椒**"。
11. 突出显示**辣椒**, 然后选择 **+ 浇头**。
12. 选择 "**评分操作**"。
13. 从 "操作" 列表中选择 "**浇头: $toppings** "。
14. 在左侧的聊天面板中, 输入用户的第三个查询文本的**奶酪**。
15. 突出显示**奶酪**, 然后选择 **-浇头**。
16. 选择 "**评分操作**"。
17. 从 "操作" 列表中选择 "**浇头: $toppings** "。

![](../media/T07_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预先训练的实体](./08-pre-trained-entities.md)
