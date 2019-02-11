---
title: 如何使用 Conversation Learner 操作的“预期实体”属性 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用 Conversation Learner 模型的“预期实体”属性。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226262"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>如何使用操作的“预期实体”属性

本教程演示操作的“预期实体”属性。

## <a name="video"></a>视频

[![预期实体教程预览](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
使用操作的“预期实体”属性将用户对此操作的响应保存到某个实体中。

将实体添加到操作的“预期实体”属性时，系统会执行以下操作：

1. 一开始会尝试使用基于机器学习的实体提取模型来匹配实体
2. 如果找不到实体，则会基于启发法将整个用户话语分配给 $entity
3. 调用 `EntityDetectionCallback`，然后继续选择操作。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中键入“ExpectedEntities”，然后按 Enter。
3. 单击“创建”按钮。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“自定义训练”作为“实体类型”。
3. 键入“名称”作为“实体名称”。
4. 单击“创建”按钮。

> [!NOTE]
> “自定义训练”实体类型表示该实体可以训练，与其他类型的实体不同。

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“你的名字是什么?”
3. 在“预期实体”字段中，键入“名称。”
4. 单击“创建”按钮。

> [!NOTE]
> 如果选择此操作，在用户的响应中检测到并提取的实体会保存到“名称”实体。 如果检测不到实体，则会将整个响应保存到该实体。

### <a name="create-the-second-action"></a>创建第二个操作。

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“你好，$name!”
3. 单击“创建”按钮。

> [!NOTE]
> “name”实体已自动由响应中的引用添加为“所需实体”。

现在已有两个操作。

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“你好。”
    - 这是模拟用户侧的聊天。
3. 单击“对操作打分”按钮。
4. 选择响应“你的名字是什么?”。
    - 无法选择“你好，$name!” 响应，因为此响应现在要求在模型的内存中定义“名称”实体。
5. 在聊天面板中显示“键入你的消息...”的位置，键入“Frank。”
    - 将会根据我们在前面设置的启发法将“Frank”突出显示为实体，以便将响应另存为实体。
6. 单击“对操作打分”按钮。
    - 现在会在模型的内存中将“名称”实体定义为“Frank”，这样就可以将“你好，$name”操作作为操作来选择。
7. 选择响应“你好，$name!”
8. 单击“保存”按钮。

添加备用输入即可进一步训练模型。

1. 在“添加备用输入...”字段中键入“我是 Jose。”
    - 该模型不会将名称识别为实体，因此会选择整个文本块作为实体的值。
2. 单击“我是 Jose”短语，然后单击垃圾桶图标。
3. 单击“Jose”，然后单击“实体”列表中的“名称”。
4. 单击“Score Actions”。
5. 选择响应“你好，Frank”
6. 单击“保存”按钮。

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [可否定实体](./06-negatable-entities.md)
