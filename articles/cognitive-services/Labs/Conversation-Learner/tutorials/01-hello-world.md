---
title: 如何创建“Hello World”对话学习器模型 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何创建“Hello World”对话学习器模型。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b2c43ad2475ab75d251e57fca6009eb1fa2e1f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707591"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>如何使用对话学习器创建“Hello World”模型

本教程介绍如何开始使用 Conversation Learner，包括创建操作、以交互方式教导机器人，以及更正最终用户的已记录对话。

## <a name="video"></a>视频

[![Hello World 教程预览](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>要求
如果你尚未这样做，请首先确保所有设置步骤都已完成，包括使用 LUIS 创作密钥创建 `.env` 文件。  有关详细信息，请参阅[快速入门](../quickstart.md)。

本教程要求常规教程机器人处于运行状态：

    npm run tutorial-general

## <a name="steps"></a>Steps

从 Web UI 的主页开始。

### <a name="create-the-model"></a>创建模型
1. 单击“新建模型”按钮。
2. 在“名称”字段中，输入“Hello World”。
3. 单击“创建”按钮。

现在会看到已创建模型的视图。

### <a name="create-an-action"></a>创建操作
1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
    - 操作可以是对话学习器返回给用户、API 调用或卡的文本消息。
2. 在“机器人的响应...”字段中，键入“你好”。
    - 这是机器人将返回的响应。
3. 单击“创建”按钮。

你已经创建了机器人可以执行的第一项操作，即返回文本响应。

### <a name="train-dialogs"></a>训练对话
可以在此处训练模型如何对用户话语进行响应。

#### <a name="first-training-dialog"></a>第一个训练对话

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 键入“你好”，按 Enter。
    - 作为用户在聊天开始时可能会说的内容的示例。
3. 单击“对操作打分”按钮。
4. 选择“你好”。
    - 在此示例对话中，你刚完成了一个完整的轮次。 
5. 键入用户回应“再见”。
6. 单击“对操作打分”按钮。
7. 单击“+ 操作”按钮。
8. 键入“再见!” 然后，在“机器人的响应...”字段中单击“创建”按钮。
    - 请注意，机器人使用你刚创建的该操作进行了响应。
9. 单击“保存”按钮。 
    - 这将结束并保存此训练对话。

现在，你在模型中有一个训练对话，此外还有一个实体和两个操作。

#### <a name="second-training-dialog"></a>第二个训练对话
让我们再进行一次训练，看看机器人如何响应。

1. 单击“新建训练对话”按钮。
2. 键入“你好”
    - 这类似于第一个对话，我们期望从机器人获得一个好分数。
3. 单击“对操作打分”按钮。
    - 位置和分数可能仍然不够准确，可能需要更多训练。
4. 选择“你好”。
5. 键入用户回应“再见”。
6. 单击“对操作打分”按钮。
7. 选择“再见!”
8. 单击“保存”按钮。

### <a name="log-dialogs"></a>记录对话
这是测试、查看和更正聊天内容的地方，该聊天是在你或实际用户与机器人之间进行的。

#### <a name="test-the-model-as-an-end-user"></a>以最终用户身份测试模型
1. 在左侧面板上单击“记录对话”，然后单击“新建记录对话”按钮。
2. 键入“你好”。
3. 等待片刻，机器人会自动使用“你好”进行响应
4. 输入“再见”
5. 等待片刻，机器人会再次自动使用“你好”进行响应。
6. 单击“完成测试”按钮。

#### <a name="view-and-correct-a-user-conversation"></a>查看并更正用户聊天内容
使用“记录对话”，可以查看用户与机器人进行的一系列聊天内容。 也可以编辑它们，以便创建机器人的响应，并将交互内容另存为训练对话。 若要执行该操作：
1. 在网格中，单击聊天记录。
2. 单击上一个机器人操作，例如“你好”。
3. 选择“再见!” 以更正机器人。
4. 单击“另存为训练对话”按钮。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [训练简介](./02-intro-to-training.md)
