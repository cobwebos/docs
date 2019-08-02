---
title: 如何向 Conversation Learner 模型中添加预先训练的实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何向 Conversation Learner 模型添加预先训练的实体。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704123"
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

## <a name="steps"></a>步骤

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择 "**新建模型**"。
2. 为 "**名称**" 输入**PretrainedEntities** 。
3. 选择“创建”。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中选择 "**实体**", 然后选择 "**新建实体**"。
2. 为 "**实体类型**" 选择**预先训练/datetimeV2** 。
3. 检查**多值**以使实体累积一个或多个值。 请注意, 预先训练的实体不能 negatable。
4. 选择“创建”。

![](../media/T08_entity_create.png)

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 为**Bot 的响应**输入**日期 "$Builtin-datetimev2"** 。
3. 选择“创建”。

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 输入**机器人响应**的 **"日期" 是什么？** 预先训练的实体不能是**必需实体**, 因为它们在默认情况下会被所有最谈话识别。
3. 输入 "**内置-datetimev2** for **Disqualifying" 实体**。
4. 选择“创建”。

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板中选择 "**训练**对话框", 然后选择 "**新建定型" 对话框**。
2. 在左侧的聊天面板中输入用户的查询文本。
3. 选择 "**评分操作**"。
4. 从 "操作" 列表中选择**日期？**
5. 在左侧的聊天面板中为用户的查询文本输入**今天**。
    - 在 LUIS 中, 预先训练的模型会自动识别**今天**的查询文本。
    - 将鼠标指针悬停在预先训练的实体的值上方会显示由 LUIS 提供的更多数据。

![](../media/T08_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体解析程序](./09-entity-resolvers.md)
