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
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168181"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何通过 Conversation Learner 模型使用可否定实体

本教程演示了实体的“可否定”属性。

## <a name="video"></a>视频

[![可否定的实体教程预览](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>要求
本教程要求常规教程机器人处于运行状态

    npm run tutorial-general

## <a name="details"></a>详细信息
实体的"Negatable"属性，用于标记这两个正常 （正值） 和负实体的实例、 教授基于正和负模型和清除现有实体的值。 设置了其“可否定”属性的实体在 Conversation Leaner 中称为“可否定的实体”。

## <a name="steps"></a>Steps

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择**新的模型**。
2. 输入**NegatableEntity**有关**名称**。
3. 选择“创建”。

### <a name="entity-creation"></a>创建实体

1. 选择**实体**在左侧的窗格中，然后**新实体**。
2. 选择**自定义培训**有关**实体类型**。
3. 输入**名称**有关**实体名称**。
4. 检查**Negatable**若要使用户能够提供实体的值，或者说有*不*实体值从而删除匹配的实体值。
5. 选择“创建”。

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>创建第一个操作

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**我不知道你的名称。** 有关**智能机器人应用程序的响应...**.
3. 输入**名称**有关**取消将赋予**。
4. 选择“创建”。

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 选择**操作**在左侧的窗格中，然后**新操作**。
2. 输入**我知道自己的名称。它是 $name。** 有关**智能机器人应用程序的响应...**.
3. 选择“创建”。

> [!NOTE]
> **名称**实体已自动添加为**所需实体**响应查询文本中的引用。

现在已有两个操作。

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>训练模型

1. 选择**训练对话框**在左侧的窗格中，然后**新训练对话框**。
2. 输入**你好**为左的聊天面板中的用户的语句。
3. 选择**评分操作**。
4. 选择**我不知道你的名称。** 从操作列表中。 百分位作为基于约束的唯一有效操作为 100%。
5. 输入**我是 Frank**为左的聊天面板中的用户的语句。
6. 突出显示**Frank**然后选择 **+ 名称**。 Negatable 实体具有两个实例: （+） 加上添加或覆盖值;（-） 减号，删除值。
7. 选择**评分操作**。 **名称**实体现在定义为**Frank**在模型的内存中，因此**我知道自己的名称。它是 $name**操作才可用。
8. 选择**我知道自己的名称。它是 $name。** 从操作列表中。
9. 输入**我名称不是 Frank。** 对于左的聊天面板中的用户的语句。
10. 突出显示**Frank**然后选择**的名称**清除这些值来自**名称**实体。
11. 选择**评分操作**。
12. 选择**我不知道你的名称。** 从操作列表中。
13. 输入**我是 Susan。** 有关用户的聊天左的面板中的第三个语句。
14. 突出显示**Susan**然后 **+ 名称** 

![](../media/T06_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [多值实体](./07-multi-value-entities.md)
