---
title: 如何通过对话学习器使用备用输入 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器使用备用输入。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 383c063e1ba7a29986e4b1c48024072799234414
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387930"
---
# <a name="how-to-use-alternative-inputs"></a>如何使用备用输入

本教程介绍如何在示教界面中使用“备用输入”字段作为用户话语。

## <a name="video"></a>视频

[![备用输入教程预览](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息
“备用输入”是用户可以在训练对话中的特定点说出的备用的、语义上相同的用户话语。 这些备用输入允许你更简洁地指定话语的变体，而不必在单独的训练对话中说出每个变体。

## <a name="steps"></a>Steps

### <a name="create-the-model"></a>创建模型

1. 在 Web UI 中，单击“新建模型”。
2. 在“名称”字段中键入“AlternativeInputs”，然后按 Enter。
3. 单击“创建”按钮。

### <a name="entity-creation"></a>创建实体

1. 在左侧面板中单击“实体”，然后单击“新建实体”按钮。
2. 选择“自定义训练”作为“实体类型”。
3. 键入“city”作为“实体名称”。
4. 单击“创建”按钮。

![](../media/T10_actions.png)

现在，创建三个操作。

### <a name="create-the-first-action"></a>创建第一个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“哪个城市?”
3. 在“用户回应中的预期实体...”字段中，键入“城市。”
4. 在“取消实体资格”字段中，键入“城市。”
5. 单击“创建”按钮。

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>创建第二个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”字段中，键入“$city 的天气可能为晴天。”
3. 单击“创建”按钮。

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>创建第三个操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 在“机器人的响应...”中，键入“尝试询问天气。”
3. 在“取消实体资格”字段中，键入“城市。”
4. 单击“创建”按钮。

![](../media/T10_action_create_3.png)

现在有三个操作。

![](../media/T10_actions.png)

### <a name="train-the-model"></a>训练模型

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“天气怎么样?”
3. 单击“对操作打分”按钮。
4. 选择响应“哪个城市?”
5. 在聊天面板中显示“键入你的消息...”的位置，键入“丹佛”
6. 单击“对操作打分”按钮。
7. 选择响应“丹佛的天气可能为晴天。”
8. 单击“提交更改”按钮。

![](../media/T10_training_1.png)

让我们创建另一个训练对话，对模型进行更多的训练。

### <a name="second-model-train-dialog"></a>第二个模型训练对话

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“你可以做些什么?”
3. 单击“对操作打分”按钮。
4. 选择响应“尝试询问天气。”
5. 在聊天面板中显示“键入你的消息...”的位置，键入“西雅图的天气怎么样?”
6. 单击“西雅图”，然后单击“实体列表”中的“城市”。
7. 单击“对操作打分”按钮。
8. 选择响应“西雅图的天气可能为晴天。”
9. 单击“提交更改”按钮。

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>使用备用输入的第三个模型训练对话

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“帮助”
3. 单击“对操作打分”按钮。
    - 模型对于最佳选项不确定，因此会默认选择最高的百分位。
4. 单击“放弃教学”按钮，然后单击“确认”按钮。

![](../media/T10_training_3.png)

让我们使用备用输入对系统进行更好的优化。 可以在教学时添加备用输入，也可以在以后添加。

1. 在左侧面板上单击“训练对话”，然后从“训练对话”列表中 选择“你可以做些什么?”
1. 在聊天面板中单击“你可以做些什么?” 话语。
1. 在“添加备用输入...”字段中键入“帮助”，然后按 Enter。
1. 单击“保存更改”按钮。

![](../media/T10_training_4.png)

让我们添加另一个备用输入来处理休斯顿的问题。

1. 在聊天面板中单击“西雅图的天气怎么样?” 话语。
1. 在“添加备用输入...”字段中键入“休斯顿的预报”，然后按 Enter。
   - 错误消息突出显示这样一个事实：备用输入必须在语义上相同，并且必须包含与原始话语相同的实体，而不仅仅是实体的相同值。 必须存在相同的实体。
1. 单击“休斯顿”，然后从“实体列表”中选择“城市”。
1. 在“添加备用输入...”字段中键入“西雅图的预报”，然后按 Enter。
1. 单击“西雅图”，然后从“实体列表”中选择“城市”。
1. 单击“保存更改”按钮。
1. 单击“保存编辑”按钮。

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>测试模型

1. 在左侧面板上单击“记录对话”，然后单击“新建记录对话”。
2. 在聊天面板中显示“键入你的消息...”的位置，键入“帮助我”
3. 在聊天面板中显示“键入你的消息...”的位置，键入“丹佛的预报”

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [记录对话](./11-log-dialogs.md)
