---
title: Conversation Learner 模型训练简介 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过 Conversation Learner 训练一个模型，包括如何对以前的训练进行分支和编辑。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705613"
---
# <a name="introduction-to-training"></a>训练简介

本教程介绍训练模型的基础知识，包括根据以前的训练将新训练分支，以及通过编辑方式对机器人响应进行更改。

## <a name="video"></a>视频

[![训练教程预览版简介](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

- 操作:机器人对用户输入的响应。
- 训练：以特定方式教导机器人对用户输入进行响应。
- 分支：在保存的训练对话中修改用户输入，以便创建新的训练对话，该对话启动时与原始对话相同，但聊天的方向不同。

## <a name="steps"></a>步骤

### <a name="create-a-new-model"></a>创建新模型

1. 在 Web UI 中，单击“新建模型”
2. 键入“激励机器人”作为“名称”。 然后单击“创建”。

### <a name="create-an-action"></a>创建操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应”字段中，输入“你好! 想要今天就进行激励吗?”。
    - 让所有其他字段的复选框保留其默认设置。
3. 单击“创建”。

### <a name="first-training-and-creating-another-action-while-training"></a>首先进行训练，然后在训练时创建另一个操作

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“你好”。 
    - 这是模拟用户侧的聊天。
3. 单击“对操作打分”。
4. 选择响应“你好! 想要今天就进行激励吗?”。
5. 以用户身份使用“是”进行响应。
6. 单击“对操作打分”。
7. 单击“+ 操作”按钮。 
    - 此时会转到你熟悉的“创建操作”对话框。
8. 键入机器人的响应“你真棒!”
9. 单击“创建”。
10. 请注意，机器人会立即进行响应。
11. 单击“保存”按钮。

### <a name="branch-a-second-training-off-of-the-first-training"></a>以分支方式从第一个训练分出第二个训练
1. 单击对第一次训练进行汇总的网格行。 
    - 这样就可以查看和编辑现有的训练。
2. 单击“是”用户响应。 
    - 此时会公开编辑控件。
3. 单击分支图标。 
    - 此时会显示一个提示，要求你输入另一个适用于新聊天的用户输入。
4. 键入“否”，然后按 Enter 或单击“创建”按钮。 
    - 此时会出现一个新的训练对话实例，原始的训练对话保持不变。
5. 单击“对操作打分”。
6. 单击机器人的刚出现的错误响应。
7. 单击“+ 操作”按钮 
    - 这样即可创建一个新的操作，以便机器人用它来进行响应。
8. 键入机器人的响应“没问题! 祝你愉快!”
9. 单击“法律条款”
10. 请注意，机器人会立即进行响应。
11. 单击“保存”按钮。

### <a name="test-the-trainings"></a>测试训练
1. 在左侧面板上单击“记录对话”，然后单击“新建记录对话”。
2. 键入消息“你好”。 
3. 注意，机器人会自动以受训方式进行响应。
4. 键入用户回应“是”。
5. 注意机器人的响应，它表明第一次训练是成功的。
6. 单击“会话超时”按钮。 这是告知 Conversation Learner 我们希望重新开始，忽略刚进行的聊天轮次。
7. 键入消息“你好”。 
8. 注意，机器人会自动以受训方式进行响应。
9. 键入用户回应“否”。
10. 注意机器人的响应，它表明第二次训练是成功的。
11. 单击“完成测试”按钮。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [等待和非等待操作](./03-wait-vs-nonwait-actions.md)
