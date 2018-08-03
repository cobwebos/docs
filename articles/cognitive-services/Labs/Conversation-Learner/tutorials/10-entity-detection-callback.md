---
title: 如何通过对话学习器实体使用实体检测回调 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用实体检测回调。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f168018a23d03ffb957da2dd1f67881420a21208
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171097"
---
# <a name="how-to-use-entity-detection-callback"></a>如何使用实体检测回调

本教程介绍实体检测回调，并说明了解析实体的常见模式。

## <a name="video"></a>视频

[![教程 10 预览](http://aka.ms/cl-tutorial-10-preview)](http://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>要求
本教程要求运行 `tutorialEntityDetectionCallback` 机器人。

    npm run tutorial-entity-detection

## <a name="details"></a>详细信息
实体检测回调允许使用自定义代码来处理与实体相关的业务规则。 本演示使用回调和编程实体将用户输入的城市名称解析为规范名称 - 例如，将“大苹果城”解析为“纽约”。

### <a name="open-the-demo"></a>打开演示

在“模型”列表中，单击“Tutorial-10-EntityDetectionCallback”。 

### <a name="entities"></a>实体

模型中定义了三个实体。

![](../media/tutorial10_entities.PNG)

1. City 是一个自定义实体，用户将以文本输入的形式提供。
2. CityUnknown 是编程实体。 该实体将由系统填充。 如果系统不知道它是哪个城市，它将复制用户输入。
3. CityResolved 是系统确实知道的城市。 该实体将是城市的规范名称，例如“大苹果城”将解析为“纽约”。

### <a name="actions"></a>操作

模型中定义了三个操作。

![](../media/tutorial10_actions.PNG)

1. 第一个操作是“Which city do you want?”
2. 第二个操作是“I don't know this city, $CityUknown. Which city do you want?”
3. 第三个操作是“You said $City, and I resolved that to $CityResolved.”

### <a name="callback-code"></a>回调代码

让我们来看一下代码。 可以在 C:\<installedpath>\src\demos\tutorialSessionCallbacks.ts 文件中找到 EntityDetectionCallback 方法。

![](../media/tutorial10_callbackcode.PNG)

在发生实体解析之后调用此函数。
 
- 它要做的第一件事是清除 $CityUknown。 $CityUknown 只会保留一轮次，因为它总是在一开始就被清除。
- 然后，获取已识别的城市列表。 获取第一个，并尝试解析它。
- 解析它的函数 (resolveCity) 在代码中的前面部分进一步定义。 它具有规范城市名称的列表。 它在列表中找到城市名称，然后返回它。 否则，它将在“cityMap”中查找，并返回映射的名称。 如果它找不到城市，则返回 null。
- 最后，如果城市已经解析为名称，我们将其存储在 $CityKnown 实体中。 否则，清除用户所说的内容并填充 $CityUknown 实体。

### <a name="train-dialogs"></a>训练对话

1. 依次单击“训练对话”和“新建训练对话”。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“Which city do you want?”
2. 输入“new york”。
    - 该文本被识别为 city 实体。
5. 单击“对操作评分”
    - 已填充 `City` 和 `CityResolved`。
6. 选择“You said $City, and I resolved that to $CityResolved”。
7. 单击“完成教学”。

添加另一个示例对话：

1. 单击“新建训练对话”。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“Which city do you want?”
2. 输入“big apple”。
    - 该文本被识别为 city 实体。
5. 单击“对操作评分”
    - `CityResolved` 显示运行的代码的效果。
6. 选择“You said $City, and I resolved that to $CityResolved”。
7. 单击“完成教学”。

添加另一个示例对话：

1. 单击“新建训练对话”。
2. 键入“hello”。
3. 单击“对操作打分”，然后选择“Which city do you want?”
2. 输入“foo”。
    - 这是系统不知道的城市的一个例子。 
5. 单击“对操作打分”
6. 选择“I don't know this city, $CityUknown. Which city do you want?”。
7. 输入“new york”。
8. 单击“对操作打分”。
    - 已清除 `CityUknown` 并填充了 `CityResolved`。
6. 选择“You said $City, and I resolved that to $CityResolved”。
7. 单击“完成教学”。

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [会话回调](./11-session-callbacks.md)
