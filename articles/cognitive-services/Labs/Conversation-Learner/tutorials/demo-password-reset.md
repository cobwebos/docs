---
title: 演示对话学习器模型，密码重置 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何创建演示对话学习器模型。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 15aa3a8346087908cf77f1f68db916cc2c184448
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389874"
---
# <a name="demo-password-reset"></a>演示：密码重置
本教程展示了一个简单的技术支持机器人，它可以帮助进行密码重置（由 Conversation Learner 提供的功能）。 此机器人的模型可以学习重要的对话流和多轮序列，包括域外类。 此任务可以在没有代码或实体的情况下完成。

## <a name="video"></a>视频

[![演示密码预览](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>要求
本教程要求密码重置机器人正在运行

    npm run demo-password

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击“教程演示密码重置”。 

### <a name="actions"></a>操作

此模型包含一组操作，旨在帮助用户解决常见的密码问题。

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>训练对话

此模型还包含多个训练对话，包括一些演示域外类训练的对话。 例如，可能请求行车路线之类内容的用户。 示例机器人已在一些训练框中进行演示目的的训练，会直接使用“无法提供这方面的帮助”进行响应。 现有训练对话的列表位于左侧面板中的“训练对话”下。

![](../media/tutorial_pw_reset_entities.PNG)

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“我丢失了密码。”
3. 单击“对操作打分”按钮。
4. 选择响应“该密码是本地帐户的还是 Microsoft 帐户的?”
5. 在聊天面板中显示“键入你的消息...”的位置，键入“请使用本地帐户”
6. 单击“对操作打分”按钮。
7. 选择响应“你使用哪个版本的 Windows?”
8. 在聊天面板中显示“键入你的消息...”的位置，键入“windows xp”
9. 单击“对操作打分”按钮。
10. 单击“+操作”按钮。
11. 在“机器人的响应...”字段中，键入“解决方案:如何在 Windows XP 上重置密码。”
12. 单击“创建”按钮。

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>域外场景的训练对话

1. 在左侧面板上单击“训练对话”，然后单击现有的“玩具商店”训练对话。
2. 在聊天面板中单击“玩具商店”话语。
3. 在“添加备用输入...”字段中键入“Web 搜索”，然后按 Enter。
4. 在“添加备用输入...”字段中键入“航班预订”，然后按 Enter。
5. 单击“保存更改”按钮。
6. 单击“保存编辑”按钮。
7. 在左侧面板上单击“记录对话”，然后单击“新建记录对话”按钮。
8. 在聊天面板中显示“键入你的消息...”的位置，键入“我找不到密码”
9. 在聊天面板中显示“键入你的消息...”的位置，键入“Microsoft 帐户”
10. 在聊天面板中显示“键入你的消息...”的位置，键入“谢谢”
11. 单击“完成测试”按钮。
12. 在网格视图中单击“我找不到密码”记录对话。
13. 在聊天面板中单击未正确呈现的“解决方案:如何重置 Microsoft 帐户密码”响应。
14. 单击“+操作”按钮。
15. 在“机器人的响应...”字段中，键入“欢迎你”
16. 单击“创建”按钮。
17. 单击“另存为训练对话”按钮。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [演示 - 披萨订单](./demo-pizza-order.md)
