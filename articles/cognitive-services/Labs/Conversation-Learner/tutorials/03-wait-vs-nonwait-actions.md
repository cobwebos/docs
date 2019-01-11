---
title: 如何通过对话学习器模型使用等待和非等待操作 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用等待和非等待操作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 95cf223004c50583701e65d3adf02f57bd541fae
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796457"
---
# <a name="wait-and-non-wait-actions"></a>等待和非等待操作

本教程介绍对话学习器中等待和非等待操作间的区别。

## <a name="video"></a>视频

[![等待与非等待教程预览](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

- 等待操作：执行“等待”操作后，系统将停止执行操作并等待用户输入。
- 非等待操作：执行“非等待”操作后，系统将立即选择另一操作（无需等待用户输入）。

## <a name="steps"></a>步骤

### <a name="create-a-new-model"></a>创建新模型

1. 在 Web UI 中，单击“新建模型”
2. 在“名称”字段中键入“Wait Non-Wait”，然后按 Enter 或单击“创建”按钮。

### <a name="create-the-first-two-wait-actions"></a>创建前两个等待操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“你想要什么披萨?”
    - 这是一个等待操作，因此请选中“等待响应”复选框。
3. 单击“创建”按钮。
4. 重复这些步骤，创建以“披萨正在路上!”作为 机器人的响应的另一个操作。

### <a name="train-using-those-wait-actions"></a>使用这些等待操作进行训练

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“你好”。 
    - 这模拟了对话的用户端。
3. 单击“对操作打分”按钮。
4. 选择响应“你想要什么披萨?”。
5. 以用户身份使用“玛格丽塔”进行响应。
6. 单击“对操作打分”按钮。
7. 选择响应“披萨正在路上!”。
8. 单击“保存”按钮。

### <a name="create-a-non-wait-action-while-training"></a>在训练时创建非等待操作
虽然可以像之前那样创建非等待操作，但是也可以从训练会话内创建该操作。
1. 单击“新建训练对话”按钮。
2. 以用户身份键入“你好”。
3. 单击“对操作打分”按钮。
4. 单击“+ 操作”按钮。 
    - 此时会转到你熟悉的“创建操作”对话框。
5. 在机器人的响应中键入“欢迎使用披萨机器人!”
6. 取消选中“等待响应”复选框。
7. 单击“创建”按钮。
    - 请注意，机器人会立即以“欢迎使用披萨机器人!”进行响应 并且会再次以另一个机器人响应来提示你进行选择。 这是因为机器人的响应是我们刚才创建的非等待操作。
9. 选择响应“你想要什么披萨?”。
10. 以用户身份使用“玛格丽塔”进行响应。
11. 单击“对操作打分”按钮。
12. 选择响应“披萨正在路上!”。
13. 单击“保存”按钮。

> [!NOTE]
> 机器人就等待和非等待操作的响应顺序。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体简介](./04-introduction-to-entities.md)
