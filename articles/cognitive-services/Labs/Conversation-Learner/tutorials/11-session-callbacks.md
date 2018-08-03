---
title: 如何通过对话学习器模型使用会话回调 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用会话回调。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 0f51b232470e4e4da3f25d40d025dd3b09dd1204
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171909"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>如何通过对话学习器模型使用会话回调

本教程演示 onSessionStart 和 onSessionEnd 回调。

## <a name="video"></a>视频

[![教程 11 预览](http://aka.ms/cl-tutorial-11-preview)](http://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>要求
本教程要求运行 `tutorialSessionCallbacks` 机器人。

    npm run tutorial-session-callbacks

## <a name="details"></a>详细信息
本教程介绍会话的概念、默认情况下如何处理会话以及如何重写该行为。

会话是与机器人的一次对话。 它可以有多轮次，但是在对话中没有长时间（例如，30 分钟）的中断。  有关默认会话超时持续时间，请参阅有关“限制”的帮助页。

如果有长时间中断，则机器人将转到其下一个会话。  开始新会话会将循环神经网络置于其初始状态。  默认情况下，它还会清除所有实体值，但可以更改此行为（下面说明）。

### <a name="open-the-demo"></a>打开演示

在“模型”列表中，单击“Tutorial-11-SessionCallbacks”。 

### <a name="entities"></a>实体

模型中定义了四个实体。

![](../media/tutorial11_entities.PNG)

需要注意的一点是，BotName 是一个编程实体。  会话开始时机器人将设置该实体。

### <a name="actions"></a>操作

模型中定义了四个操作。

![](../media/tutorial11_actions.PNG)

首先，本教程将展示如何在会话开始时控制实体值 - 例如，在用户说任何内容之前设置 BotName 实体。

其次，本教程将展示如何将值从一个会话持久保留到下一个会话。  在本教程中，我们假设用户的名称和电话号码从一个会话到下一个会话保持不变，但用户的位置可能会发生变化。  因此，我们会在会话之间保留名称和电话号码，但会清除用户位置。

### <a name="train-dialog"></a>训练对话

下面是一个示例对话。 这是一个会话 - 也就是说，这个对话中没有长时间的中断。

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>用于回调的代码

回调方法的代码位于以下文件中：c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts。

![](../media/tutorial11_code.PNG)

这两个方法是可选的。

- OnSessionStartCallback：此方法设置 BotName 实体。
- OnSessionEndCallback：可以指定要保留的内容。 这会清除除用户名和用户电话之外的所有实体。

### <a name="try-the-bot"></a>试用机器人

切换到 Web UI，然后单击“记录对话”。

1. 输入“hello”。
2. 系统：“Hi, I'm Botty. What's your name?” 其名称 Botty 来自 OnSessionStartCallback。
3. 输入“jason”。
4. 系统：“Hi jason. What's your phone number?”
5. 输入“555-555-5555”。
6. 系统：“Can you tell Botty your location, jason?”
7. 键入“Redmond”。

这是一个会话。 若要开始新会话，我们需要结束此会话。 

1. 单击“会话超时”。 这将使你进入下一个会话。
    - 提供“会话超时”按钮是为了调试目的。  在实际会话中，需要出现大约 30 分钟的长时间停顿。  有关默认会话超时持续时间，请参阅有关“限制”的帮助页。
1. 输入“hi”。
2. 系统：“Can you tell Botty your location, jason?”
    - 系统已记住名称和电话号码。
2. 输入新位置：“Seattle”。
3. 系统：“So, jason you are in Seattle”。
4. 单击“完成测试”。

让我们切换回“记录对话”。 请注意，上一个对话已拆分为两个对话，因为每个记录对话对应于一个会话。  

![](../media/tutorial11_splitdialogs.PNG)

- 在第一次交互中，设置了 Botty，但是未设置名称和电话号码。
- 第二次交互显示了名称和电话号码。

你现在已经了解默认情况下如何处理会话，以及如何重写默认行为。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [API 调用](./12-api-calls.md)
