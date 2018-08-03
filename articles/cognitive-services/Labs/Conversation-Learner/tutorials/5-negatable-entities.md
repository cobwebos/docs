---
title: 如何通过对话学习器模型使用可否定实体 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用可否定实体。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2fd00d53755e44e3a3d86782c40aa6a53ff4d378
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171395"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>如何通过对话学习器模型使用可否定实体

本教程演示实体的”negatable”属性。

## <a name="video"></a>视频

[![教程 5 预览](http://aka.ms/cl-tutorial-05-preview)](http://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
如果用户可以“清除”实体值（如在“No, I do not want $entity”或“no, not $entity”中），则将操作标记为“可否定”。 例如，“no, I do not want to leave from Boston.”

具体而言，如果设置了实体的“negatable”属性：

- 当标记实体提及时，允许你同时标记实体的正常（肯定）实例和实体的“否定”实例
- LUIS 了解两个实体模型：一个用于肯定实例，另一个用于否定实例
- 实体的否定实例的作用是从实体变量中清除该值（如果存在）

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”
2. 在“名称”中输入“NegatableEntity”。 然后单击“创建”。

### <a name="create-an-entity"></a>创建实体

1. 依次单击“实体”和“新建实体”。
2. 在“实体名称”中，输入名称。
3. 选中“可否定”。
    - 该属性表示用户将能够为该实体提供值，或者说某些内容不是该实体的值。 在后一种情况下，这将导致删除实体的匹配值。
3. 单击“创建”。

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>创建两个操作

1. 依次单击“操作”和“新建操作”
2. 在“响应”中，键入“I don't know your name”。
3. 在“取消资格实体”中，输入名称。
3. 单击创建

然后，创建第二个操作。

1. 依次单击“操作”和“新建操作”以创建第二个操作。
3. 在“响应”中，键入“I know your name. It is $name”。
4. 单击创建

现在已有两个操作。

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>定型机器人

1. 依次单击“定型”对话框和“新建定型”对话框。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“I don't know your name”
    - 因为它是唯一的有效操作，所以分数为 100%。
2. 输入“my name is david”
3. 选择“david”，并选择标签“+name”
    - 此时有“name”的两个实例：“+name”和“-name”。  (+) 加号添加或覆盖该值。 (-) 减号移除该值。
5. 单击“对操作评分”
    - 名称值现已在机器人的内存中。
    - “I know your name. It is $name”是唯一可用的响应。 
6. 选择“I know your name. It is $name”。

让我们尝试清除可否定实体：

7. 输入“my name is not david”。
    - 请注意，根据前面的模式，“not”被选为名称。 此标签不正确。
2. 依次单击“not”和红色的 x。 
3. 单击“david”。
    - 现在这是一个否定实体，传达这不是 name 实体的值。
2. 选择“-name”。
3. 单击“对操作打分”。
    - 请注意，该值已从内存中清除。
2. 选择“I don't know your name”（这是唯一的操作）。

接下来，我们将展示如何输入名称的新值。

3. 输入“john”作为名称。 然后选择“john”并单击“name”。
4. 单击“对操作打分”。
5. 选择“I know your name. It is $name”。

现在尝试替换输入的名称。

6. 输入“my name is susan”。
7. 选择“I know your name. It is $name”。
7. 单击“对操作打分”。
8. 请注意，**susan** 已在实体值中覆盖了 **john**。
9. 输入“my name is not susan”。
    - 请注意，系统已将此句标记为否定实例。
2. 单击“对操作打分”。
3. 选择“I don't know your name”（这是唯一的操作）。
7. 单击“完成教学”。

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [多值实体](./6-multi-value-entities.md)
