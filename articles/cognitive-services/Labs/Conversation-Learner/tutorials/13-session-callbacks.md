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
ms.openlocfilehash: 5458d83fcc9fad50a2a52273ea6b5573ed90a97e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796443"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>如何通过对话学习器模型使用会话回调

本教程介绍会话、会话处理方式，以及 Conversation Learner 的 onSessionStart 和 onSessionEnd 回调。

## <a name="video"></a>视频

[![会话回调教程预览](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>要求
本教程要求运行“tutorialSessionCallbacks”机器人。

    npm run tutorial-session-callbacks

## <a name="details"></a>详细信息
本教程介绍会话的概念、默认情况下如何处理会话以及如何重写该行为。

在 Conversation Learner 中，会话表示与机器人进行的一次未中断的交互式信息交流。 会话可以有多个轮次，但在保持非活动状态时间超过三十分钟的情况下，可以通过编程方式进行终止。  请参阅有关“限制”的帮助页，了解如何更改此默认的会话超时持续时间。

这种长时间处于非活动状态的情况会导致机器人创建新的会话，并将循环神经网络重置为初始状态。 默认情况下，会清除所有实体值。 可以更改这种清除实体值的默认行为，如下所示。

### <a name="load-the-demo-model"></a>加载演示模型

在 Web UI 中单击“导入教程”，选择名为“Tutorial-13-SessionCallbacks”的模型。

### <a name="code-for-the-callbacks"></a>用于回调的代码

可以在 `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts` 中找到此模型的两个回调的示例代码。

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback：此方法设置 BotName 实体。
- OnSessionEndCallback：可以指定要保留的内容。 这会清除除用户名和用户电话之外的所有实体。

每个回调均为可选项。

### <a name="actions"></a>操作

模型中定义了四个操作。 现有的操作显示在“操作”的网格视图中

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>创建一个终止会话操作（针对回调调用）

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 选择“ENDSESSION”作为“实体类型”。
3. 在“数据...”字段中，键入“完成”
4. 单击“创建”按钮。

### <a name="edit-an-existing-action"></a>编辑现有操作

1. 从网格视图中选择“嗯，$UserName，你在 $UserLocation”操作。
2. 取消选中“等待响应”复选框。
3. 单击“保存”按钮。

### <a name="chaining-actions"></a>链接操作

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“你好。”
3. 单击“对操作打分”按钮。
4. 选择响应“你好，我是 Botty。 你的名字是什么?”
5. 在聊天面板中显示“键入你的消息...”的位置，键入“Lars”
6. 选择响应“你好，Lars。 你的电话号码是多少?”
7. 在聊天面板中显示“键入你的消息...”的位置，键入“555-555-5555”
8. 单击“对操作打分”按钮。
9. 选择响应“你能将自己的位置告诉 Botty 吗，Lars?”
10. 在聊天面板中显示“键入你的消息...”的位置，键入“西雅图”
11. 单击“对操作打分”按钮。
12. 选择响应“嗯，Lars，你在西雅图。
13. 选择响应“完成”
14. 单击“保存”按钮。

### <a name="testing-the-model"></a>测试模型

1. 在左侧面板上单击“记录对话”，然后单击“新建记录对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“你好”
3. 在聊天面板中显示“键入你的消息...”的位置，键入“Lars”
4. 在聊天面板中显示“键入你的消息...”的位置，键入“555-555-5555”
5. 在聊天面板中显示“键入你的消息...”的位置，键入“西雅图”
    - 目前，除位置之外的所有实体值都应该已经保留。
6. 在聊天面板中显示“键入你的消息...”的位置，键入“你好”
7. 在聊天面板中显示“键入你的消息...”的位置，键入“底特律”
8. 单击“会话超时”按钮。
    - 单击此按钮可练习机器人对长时间处于非活动状态的响应
9. 单击“确定”按钮。
10. 单击“完成测试”按钮。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [API 调用](./14-api-calls.md)
