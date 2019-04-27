---
title: 如何向 Conversation Learner 模型中添加预先训练的实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何向 Conversation Learner 模型添加预先训练的实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: dade442cb04f79ef75e65d6eb29128b105e72b40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707691"
---
# <a name="how-to-add-pre-trained-entities"></a>如何添加预先训练的实体
本教程展示了如何向 Conversation Learner 模型添加预先训练的实体。

## <a name="video"></a>视频

[![预先训练的实体教程预览](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>要求
本教程要求常规教程机器人处于运行状态

    npm run tutorial-general

## <a name="details"></a>详细信息

预先训练的实体可以识别常见类型的实体，例如数字、日期、货币金额和其他实体。  它们可以立即工作，不需要进行任何训练，并且其行为无法更改，这不同于自定义实体。  默认情况下，预先训练的实体是多值的，将累积实体的每个已识别实体。

## <a name="steps"></a>Steps

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择**新的模型**。
2. 输入**PretrainedEntities**有关**名称**。
3. 选择“创建”。

### <a name="entity-creation"></a>创建实体

1. 选择**实体**在左侧的窗格中，然后**新实体**。
2. 选择**Trained 前/datetimeV2**有关**实体类型**。
3. 检查**多值**若要启用该实体累积了一个或多个值。 请注意，不能为 negatable Pre-Trained 实体。
4. 选择“创建”。

![](../media/T08_entity_create.png)

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**的日期是 $builtin-datetimev2**为**智能机器人应用程序的响应...**.
3. 选择“创建”。

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**日期是什么？** 为**智能机器人应用程序的响应...**.预先训练的实体不能**所需实体**如他们所识别的默认值为所有语音样本。
3. 输入**builtin datetimev2**有关**取消实体**。
4. 选择“创建”。

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>训练模型

1. 选择**训练对话框**在左侧的窗格中，然后**新训练对话框**。
2. 输入**你好**为左的聊天面板中的用户的语句。
3. 选择**评分操作**。
4. 选择**什么是日期？** 从操作列表
5. 输入**今天**为左的聊天面板中的用户的语句。
    - **今天**: LUIS 中预先训练的模型自动识别语音样本。
    - 将鼠标指针悬停在预先训练的实体的值上方会显示由 LUIS 提供的更多数据。

![](../media/T08_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体解析程序](./09-entity-resolvers.md)
