---
title: 如何使用对话学习器操作的“预期实体”属性 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用对话学习器应用程序的“预期实体”属性。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366286"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>如何使用操作的“预期实体”属性

本教程演示操作的“预期实体”字段。

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
使用操作的“预期实体”字段与系统通信，你希望用户对操作的响应将是声明实体。

具体来说，如果操作的“预期实体”字段设置为 $entity，则根据下一个用户陈述，系统将：

1. 首先，像往常一样，尝试使用基于机器学习的实体提取模型来查找实体
2. 如果在步骤 1 中找不到实体，则基于启发法将整个用户陈述分配给 $entity。
3. 像往常一样调用 `EntityDetectionCallback`，然后继续选择操作。

## <a name="steps"></a>步骤

### <a name="create-the-application"></a>创建应用程序

1. 在 Web UI 中，单击“新建应用”
2. 在“名称”中输入“ExpectedEntities”。 然后单击“创建”。

### <a name="create-an-entity"></a>创建实体

1. 依次单击“实体”和“新建实体”。
2. 在“实体名称”中，输入名称。
3. 单击创建

请注意，实体类型是“自定义”- 这意味着可以训练实体。  此外，还有预构建实体，这意味着其行为无法调整 - 这些内容在另一个教程中介绍。

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>创建两个操作

1. 依次单击“操作”和“新建操作”
2. 在“响应”中，键入“What's your name?”。
3. 在“预期实体”中，输入 $name。 单击“保存”。
    - 这意味着，如果询问此问题，并且用户响应没有检测到任何实体，则机器人应该假设整个用户的响应是该实体。
2. 依次单击“操作”和“新建操作”以创建第二个操作。
3. 在“响应”中，键入“Hello $name”。
    - 请注意，该实体将自动添加为取消资格实体。 
4. 点击“保存”(Save)

现在已有两个操作。

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>训练机器人

1. 依次单击“训练对话”和“新建训练对话”。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“What's your name?”
    - 请注意，无法选择响应“Hello $name”，因为它需要定义实体 $name，而 $name 不在机器人的内存中。
2. 输入“david”。 
    - 请注意，该名称将突出显示为实体。 这是因为我们在上面设置了启发法来选择响应作为实体。
5. 单击“对操作打分”
    - 请注意名称值现在已在机器人的内存中。
    - “Hello $name”现在可以作为响应。 
6. 选择“Hello $name”。
7. 单击“完成教学”。

以下是机器学习实体提取模型识别名称的两个示例，因此不会触发“预期实体”启发法。

1. 单击“新建训练对话”。
2. 输入“my name is david”。
    - 请注意，它确实将 david 识别为 name 实体，因为它以前见过这个词。
2. 单击“对操作打分”
3. 选择“Hello $name”。
4. 输入“my name is susan”。
    - 请注意，它将 susan 识别为名称，因为它已见过此模式。
2. 单击“对操作打分”。
2. 选择“Hello susan”。
3. 单击“完成教学”。

下面是另外两个示例，其中“预期实体”启发法触发，但不正确，并说明了我们如何进行更正。

1. 键入“call me jose”。
    - 请注意，它不会将名称识别为实体。
2. 单击 jose，并选择 name。
3. 单击“对操作打分”。
4. 选择“hello $name”。
5. 单击“完成教学”。
1. 单击“新建训练对话”。
2. 输入“hello”。
3. 在回答“'what's your name”时，输入“I am called frank”。
    - 请注意，整个短语都会突出显示。 这是因为统计模型没有找到名称，因此触发了启发法并选择了整个应答作为名称实体。
2. 若要更正它，请单击突出显示的短语，然后单击红色的 x。 
3. 单击以选择“frank”，然后单击“name”。
2. 单击“对操作打分”
3. 选择“Hello $name”。
4. 单击“完成教学”。

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [可否定实体](./5-negatable-entities.md)
