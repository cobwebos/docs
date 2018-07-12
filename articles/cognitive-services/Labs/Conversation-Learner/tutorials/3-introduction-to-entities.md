---
title: 如何通过对话学习器应用程序使用实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器应用程序使用实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366268"
---
# <a name="introduction-to-entities"></a>实体简介

本教程介绍实体，并说明如何在操作中使用“取消资格实体”和“必需实体”字段。

## <a name="requirements"></a>要求

本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

本教程说明了实体的两种常见用法。  首先，实体可以从用户消息中提取子字符串，例如在“西雅图天气如何？”中识别城市。  其次，实体可以约束何时操作可用。  具体而言，操作可以将实体列为“必需”或“取消资格”：
- 操作的必需实体必须存在于机器人的内存中才能使操作可用
- 取消资格实体必须不存在于机器人的内存中才能使操作可用

其他教程涵盖了实体的其他方面，例如预构建实体、多值和可否定实体、编程实体以及操纵代码中的实体。

## <a name="steps"></a>步骤

### <a name="create-the-application"></a>创建应用程序

1. 在 Web UI 中，单击“新建应用”
2. 在“名称”中输入“IntroToEntities”。 然后单击“创建”。

### <a name="create-entity"></a>创建实体

1. 依次单击“实体”和“新建实体”。
2. 在“实体名称”中，输入“city”。
3. 单击创建

请注意，实体类型是“自定义”- 这意味着可以训练实体。  此外，还有预构建实体，这意味着其行为无法调整 - 这些内容在另一个教程中介绍。

### <a name="create-two-actions"></a>创建两个操作

1. 依次单击“操作”和“新建操作”
2. 在“响应”中，键入“I don't know what city you want”。
3. 在“取消资格实体”中，输入“$city”。 单击“保存”。
    - 这意味着，如果在机器人的内存中定义了此实体，则该操作将不可用。
2. 依次单击“操作”和“新建操作”以创建第二个操作。
3. 在“响应”中，键入“The weather in the $city is probably sunny”。
4. 在“必需实体”中，请注意城市实体自被引用以来已自动添加。
5. 点击“保存”(Save)

现在有两个操作。

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>训练机器人

1. 依次单击“训练对话”和“新建训练对话”。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“I don't know what city you want?”
    - 请注意，无法选择需要城市实体的响应，因为城市实体未在机器人的内存中定义。
2. 选择“I don't know what city you want”。
4. 输入“seattle”。 突出显示西雅图，然后单击城市。
5. 单击“对操作打分”
    - 请注意城市值现在已在机器人的内存中。
    - “Weather in $city is probably sunny”现在可以作为响应。 
6. 选择“Weather in $city is probably sunny”。

假设用户输入“repeat that”。 
1. 键入该句话并按 Enter。 请注意，城市实体及其值已在内存中且可用。
2. 选择“Weather in $city is probably sunny”。

![](../media/tutorial3_entities.PNG)

现在已经在用户消息中创建了一个实体并标记了其实例。  还通过操作的取消资格和必需实体字段，利用实体在机器人内存中的存在/不存在来控制操作何时可用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预期实体](./4-expected-entity.md)
