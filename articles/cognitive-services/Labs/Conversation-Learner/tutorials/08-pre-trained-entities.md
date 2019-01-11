---
title: 如何向 Conversation Learner 模型中添加预先训练的实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何向 Conversation Learner 模型添加预先训练的实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c198dfc19a350188f500af86c531be9a9ac424ce
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796444"
---
# <a name="how-to-add-pre-trained-entities"></a>如何添加预先训练的实体
本教程展示了如何向 Conversation Learner 模型添加预先训练的实体。

## <a name="video"></a>视频

[![预先训练的实体教程预览](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

预先训练的实体可以识别常见类型的实体，例如数字、日期、货币金额和其他实体。  它们可以立即工作，不需要进行任何训练，并且其行为无法更改，这不同于自定义实体。  默认情况下，预先训练的实体是多值的，将累积实体的每个已识别实体。

## <a name="steps"></a>步骤

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中键入“PretrainedEntities”，然后按 Enter。
3. 单击“创建”按钮。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“预先训练的/datetimeV2”作为“实体类型”。
3. 选中“多值”复选框。
    - 多值实体会将一个或多个值累积到实体中。
    - 预先训练的实体禁用了“可否定”属性。
4. 单击“创建”按钮。

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“日期为 $builtin-datetimev2”
3. 单击“创建”按钮。

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“今天是几号?”
    - 预先训练的实体不能作为必需实体，因为默认情况下会针对所有用户话语识别它们。
3. 在“取消资格实体”字段中，键入“builtin-datetimev2”。
4. 单击“创建”按钮。

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“你好。”
3. 单击“对操作打分”按钮。
4. 选择响应“今天是几号?”
5. 在聊天面板中，在显示了“键入你的消息...”的位置键入“今天”
    - “今天”话语由 LUIS 中预先训练的模型自动识别。
    - 将鼠标指针悬停在预先训练的实体的值上方会显示由 LUIS 提供的更多数据。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体解析程序](./09-entity-resolvers.md)
