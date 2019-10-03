---
title: 如何通过 Conversation Learner 模型使用可否定实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过 Conversation Learner 模型使用可否定实体。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704110"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何通过 Conversation Learner 模型使用可否定实体

本教程演示了实体的“可否定”属性。

## <a name="video"></a>视频

[![可否定的实体教程预览](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>要求
本教程要求常规教程机器人处于运行状态

    npm run tutorial-general

## <a name="details"></a>详细信息
利用实体的 "Negatable" 属性, 您可以为实体的常规 (正) 和负实例添加标签, 并基于正和负模型进行教学, 并清除现有实体的值。 设置了其“可否定”属性的实体在 Conversation Leaner 中称为“可否定的实体”。

## <a name="steps"></a>步骤

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型

1. 选择 "**新建模型**"。
2. 为 "**名称**" 输入**NegatableEntity** 。
3. 选择“创建”。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中选择 "**实体**", 然后选择 "**新建实体**"。
2. 为 "**实体类型**" 选择 "**自定义训练**"。
3. 输入**实体名称**的**名称**。
4. 选中 " **Negatable** " 以使用户能够提供实体值, 或假设某些内容*不*是实体值, 从而删除匹配的实体值。
5. 选择“创建”。

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 输入**不知道您的姓名。** 对于**机器人的响应 ...**
3. 输入**Disqualifying**的**名称**。
4. 选择“创建”。

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左侧面板中选择 "**操作**", 然后选择 "**新建操作**"。
2. 输入**我的名字。$Name。** 对于**机器人的响应 ...**
3. 选择“创建”。

> [!NOTE]
> 在响应查询文本中按引用将**名称**实体自动添加为**必需的实体**。

现在已有两个操作。

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板中选择 "**训练**对话框", 然后选择 "**新建定型" 对话框**。
2. 在左侧的聊天面板中输入用户的查询文本。
3. 选择 "**评分操作**"。
4. 选择 "**我不知道您的姓名"。** 从 "操作" 列表中。 只有百分比为 100%, 这是基于约束的唯一有效操作。
5. 在左侧聊天面板中, 为用户的查询文本输入**我的姓名**。
6. 突出显示**Frank** , 然后选择 " **+ 名称**"。 Negatable 实体有两个实例: (+) 加上或覆盖值;(-) 减号将删除该值。
7. 选择 "**评分操作**"。 现在,**名称**实体在模型的内存中定义为**Frank** , 因此我知道**自己的名称。$Name**操作可用。
8. 选择 **"我知道您的姓名"。$Name。** 从 "操作" 列表中。
9. 输入**我的名称不是 Frank。** 对于用户在左侧聊天面板中的查询文本。
10. 突出显示**Frank** , 然后选择 " **-name** " 以清除 "**名称**" 实体中的值。
11. 选择 "**评分操作**"。
12. 选择 "**我不知道您的姓名"。** 从 "操作" 列表中。
13. 输入**我的姓名是 Susan。** 对于用户在左侧聊天面板中的第三个查询文本。
14. 突出显示**Susan** then **+ name** 

![](../media/T06_training.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [多值实体](./07-multi-value-entities.md)
