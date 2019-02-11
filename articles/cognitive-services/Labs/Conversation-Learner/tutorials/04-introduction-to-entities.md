---
title: 如何通过对话学习器模型使用实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过 Conversation Learner 模型使用实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a42a2ec36d9ce4fb9c139dfddcde0fe0c188c888
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210346"
---
# <a name="introduction-to-entities"></a>实体简介

本教程介绍了“实体”、“取消资格实体”和“必需实体”及其在 Conversation Learner 中的用法。

## <a name="video"></a>视频

[![实体简介教程预览](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>要求

本教程要求常规教程机器人处于运行状态

    npm run tutorial-general

## <a name="details"></a>详细信息

实体通过从用户话语中进行提取或通过使用自定义代码进行分配来捕获机器人执行其任务所需的信息片段。 实体自身还可以通过显式分类为“必需”或“取消资格”来约束操作可用性。

- 必需实体必须存在于模型的内存中才能使操作可用
- 取消资格实体必须不存在于模型的内存中才能使操作可用

本教程重点介绍自定义实体。 预先训练的、多值、可否定的实体和编程实体在其他教程中进行介绍。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中键入“IntroToEntities”，然后按 Enter。
3. 单击“创建”按钮。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“自定义训练”作为“实体类型”。
3. 键入“city”作为“实体名称”。
4. 单击“创建”按钮。

> [!NOTE]
> “自定义训练”实体类型表示该实体可以训练，与其他类型的实体不同。

### <a name="create-the-actions"></a>创建操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应”字段中，键入“我不知道你需要哪个城市”。
3. 在“取消资格实体”字段中，键入“city”。
4. 单击“创建”按钮。

> [!NOTE]
> 当机器人的内存中定义了“city”实体时，将“city”实体添加到“取消资格实体”会导致机器人不再考虑此操作。

现在，创建第二个操作。

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“$city 的天气可能为晴天。”
3. 单击“创建”按钮。

> [!NOTE]
> “city”实体已通过响应中的引用自动添加到“必需实体”列表中。

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“你好。”
    - 这模拟了对话的用户端。
3. 单击“对操作打分”按钮。
4. 在“响应”中，键入“我不知道你需要哪个城市”。
5. 以用户身份使用“西雅图”进行响应。
6. 单击“对操作打分”按钮。
7. 选择响应“$city 的天气可能是晴天”。
8. 单击“保存”按钮。

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预期实体](./05-expected-entity.md)
