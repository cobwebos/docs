---
title: 如何创建“Hello World”对话学习器应用程序 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何创建“Hello World”对话学习器应用程序。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 30026285ac6dda45d2f5e3718aae741b928cf242
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366300"
---
# <a name="how-to-create-a-hello-world-application-with-conversation-learner"></a>如何使用对话学习器创建“Hello World”应用程序

本教程介绍如何开始使用对话学习器，包括创建操作、交互式教学以及更正最终用户的已记录对话。

## <a name="requirements"></a>要求
如果你尚未这样做，请首先确保所有设置步骤都已完成，包括使用 LUIS 创作密钥创建 `.env` 文件。  有关详细信息，请参阅[快速入门](https://github.com/Microsoft/ConversationLearner-Samples)。

本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="steps"></a>步骤

从 Web UI 的主页开始。

### <a name="create-the-app"></a>创建应用程序
1. 单击“新建应用”
2. 在“名称”字段中，输入“Hello World”
3. 单击创建

### <a name="create-an-action"></a>创建操作

1. 单击“Hello World”应用以启动它
2. 依次单击“操作”和“新建操作”
    - 操作可以是对话学习器返回给用户、API 调用或卡的文本消息。
3. 在“响应”中，键入“Hello World!”
    - 这是机器人将返回的响应
4. 单击创建

你已经创建了机器人可以做的第一件事，即返回文本响应。

### <a name="train-the-bot"></a>训练机器人

#### <a name="create-the-first-dialog"></a>创建第一个对话

1. 依次单击“训练对话”和“新建训练对话”
2. 输入用户在对话开始时会说的内容示例，例如“hello”。
3. 单击“对操作打分”
4. 选择“Hello World!”
    - 这将创建一轮次示例对话。 
2. 输入“goodbye”
3. 单击“对操作打分”
4. 单击“添加操作”，在“响应”中输入“Goodbye!” 然后单击“创建”
5. 单击“完成教学”。 这将结束此训练对话。

现在已在系统中有一个教学对话。

#### <a name="continue-teaching-the-bot"></a>继续教机器人
让我们再进行一次训练，看看机器人如何响应。

1. 单击“新建训练对话”
2. 输入“hi there”
    - 这类似于第一个对话，我们期望从机器人获得一个好分数。
2. 单击“对操作打分”
    - 位置和分数可能仍然不够准确，还需要进行更多教学。
3. 单击“Hello World!”旁边的“选择”
4. 然后输入“bye”
5. 单击“对操作打分”
6. 选择“Goodbye!”
7. 单击“完成教学”

![](../media/tutorial1_actions.PNG)

我们将进行另一个教学会话，了解机器人是如何工作的。

使用“hi”和“byebye”重复上述步骤，并在单击“对操作打分”时注意机器人响应位置和分数的变化。

现在，可以使用“howdy”和“goodbye”重复这些步骤，并注意评分显示了分数的提高，这表明机器人已经学会了这种交互。

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>作为最终用户测试机器人

1. 依次单击“记录对话”和“新建记录对话”
2. 键入“hello there”
3. 然后键入“bye”

也可以尝试使用“bye”开始对话，并记下机器人的响应。

### <a name="view-conversations-in-the-log-dialogs"></a>在“记录对话”中查看对话

在“记录对话”中，可以查看对话列表、更新交互，并将交互保存为训练对话。 若要执行该操作：

1. 单击对话记录
2. 如果对话看起来良好，请单击上一个操作，例如“Goodbye”。
3. 单击以选择建议的响应。 
    - 还可以选择或添加另一个操作。
4. 然后单击“完成”将此操作保存为训练对话。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [等待和非等待操作](./2-wait-vs-nonwait-actions.md)