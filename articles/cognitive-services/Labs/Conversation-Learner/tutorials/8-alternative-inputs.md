---
title: 如何通过对话学习器使用备用输入 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器使用备用输入。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d3b3f419ceacbb9a6fe2b19cf68ea6873de536f
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171011"
---
# <a name="how-to-use-alternative-inputs"></a>如何使用备用输入

本教程介绍如何在示教接口中使用“备用输入”字段作为用户输入。

## <a name="video"></a>视频

[![教程 8 预览](http://aka.ms/cl-tutorial-08-preview)](http://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
“备用输入”是用户可以在训练对话中的特定点处说出的备用用户陈述。 备用输入允许你更紧密地指定用户可能说出的内容的变体，而不必在单独的训练对话中列出每个变体。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”
2. 在“名称”中，输入“AlternativeInputs”。 然后单击“创建”。

### <a name="create-an-entity"></a>创建实体

1. 依次单击“实体”和“新建实体”。
2. 在“实体名称”中，输入“city”。
3. 单击“创建”。

### <a name="create-three-actions"></a>创建三个操作

1. 依次单击“操作”和“新建操作”
2. 在“响应”中，键入“Which city do you want?”。
3. 在“取消资格实体”中，输入“$city”。
3. 单击创建

然后，创建第二个操作：

1. 依次单击“操作”和“新建操作”。
3. 在“响应”中，键入“The weather in $city is probably sunny”。
4. 在“必需实体”中，输入“$city”。
4. 单击“创建”。

创建第三个操作：

1. 依次单击“操作”和“新建操作”。
3. 在响应中，键入“Try asking for the weather”。
    - 这将是对用户问题（例如“系统能做什么？”）的响应
4. 在“取消资格实体”中，输入“$city”。
4. 单击创建

现在有三个操作。

### <a name="train-the-bot"></a>训练机器人

1. 依次单击“定型”对话框和“新建定型”对话框。
2. 键入“what's the weather”。
3. 单击“对操作打分”，然后选择“Which city do you want?”
2. 输入“denver”。
3. 双击“denver”，并选择“city”。
    - 这会将其标记为“city”实体。
5. 单击“对操作评分”
    - “denver”现在位于 city 实体中。 
6. 选择“The weather in $city is probably sunny”。
7. 单击“完成教学”。

添加另一个示例对话：

1. 依次单击“新建操作”和“新建训练对话”。
2. 键入“'what can you do?”。
3. 单击分数操作，然后选择“Try asking for the weather”
2. 输入“What's the weather in seattle”。
3. 双击“seattle”，并选择“city”。
    - 这会将其标记为“city”实体。
5. 单击“对操作评分”
    - “seattle”现在位于 city 实体中。 
6. 选择“The weather in $city is probably sunny”。
7. 单击“完成教学”。

让我们看看如果用户说出与上述语义相似的内容会发生什么：

1. 依次单击“新建操作”和“新建训练对话”。
2. 键入“help”。
3. 单击“对操作打分”。
    - 这两个潜在响应的评分非常接近。 这告诉我们模型对这两个操作之间的边界感到困惑。
6. 单击“放弃教学”并确认。

![](../media/tutorial8_closescores.png)

在这种情况下，向对话添加备用输入将有所帮助。 你可以在进行教学时添加它们。 也可以返回，稍后添加它们。

2. 在“训练对话”中，单击“What can you do?” 。
2. 在对话中，单击“what can you do?” 以选择它。
    1. 在右窗格的“实体检测”下的“添加备用输入”中，输入以下几个备选项：
    1. 输入“what are my choices?”
    2. 输入“Tell me my choices”。
    3. 输入“help”
    1. 单击“提交更改”。


![](../media/tutorial8_helpalternates.png)

2. 现在单击“what's the weather in seattle”。
    1. 在“添加备用输入”中输入“forecast for seattle”。
    2. 双击“seattle”，并选择“city”。 备用输入的实体应存在并具有相同的实体集。 实体的内容不同也是可以的。
    3. 在“添加备用输入”中，输入“will it rain today in denver”。
    4. 单击“denver”，并选择“city”。
    5. 依次单击“提交更改”和“完成”。


让我们在第一个对话中添加备用输入：

1. 单击“训练对话”。
2. 单击以“'what's the weather”开头的对话。
2. 在左窗格中单击以选择“what's the weather”：
    1. 在“添加备用输入”中输入“weather forecast”。
    2. 输入“will it rain?”
    3. 单击“提交更改”。
4. 在左窗格中单击以选择“denver”：
    1. 在“添加备用输入”中输入“for denver”。
    2. 输入“forecast for austin”。
        - 整个短语都会突出显示。 单击该短语，然后单击红色的 x。 选择 austin，并单击“city”。
        - 单击“提交更改”
    1. 单击“完成”，这将导致模型重新训练。

![](../media/tutorial8_altcities.png)

让我们来试试变体：

1. 单击“新建训练对话”。
2. 键入“what are you capabilities”。
3. 单击“对操作打分”。
    - 分数现在对下一个操作更具决定性，这表明模型的确定性。
2. 选择“Try asking for weather”。
6. 单击“完成教学”

你现在已了解如何使用备用输入来指示用户可能已经说过的其他内容。 这些备用输入可帮助你避免创建很多在许多方面都相同的对话，方法是将它们折叠成一个对话，并枚举用户可以说的内容。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [记录对话](./9-log-dialogs.md)
