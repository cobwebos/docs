---
title: 如何通过对话学习器模型使用卡，第 1 部分 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用卡。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a2db74e4c5688c0a2a3975ff828caf20c3cf7904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406327"
---
# <a name="how-to-use-cards-part-1-of-2"></a>如何使用卡（第 1 部分，共 2 部分）

本教程介绍如何在机器人中添加和使用简单的卡。

> [!NOTE]
> 当前对话学习器要求将卡定义文件置于名为“cards”的目录中，该目录位于机器人启动的目录。

## <a name="video"></a>视频

[![卡教程预览](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

卡是 UI 元素，可让用户选择对话中的选项。 

### <a name="open-the-demo"></a>打开演示

在 Web UI 中单击“导入教程”，选择名为“Tutorial-15-Cards”的模型。

### <a name="the-card"></a>卡

卡定义位于以下位置：C:\<installedpath\>\src\cards\prompt.json。

系统希望在此卡目录中找到“卡”定义。

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> 请注意文本字段中的正文类型“TextBlock”和“{{question}}”占位符。
> 提供两个提交按钮和每个按钮提交的文本。

### <a name="actions"></a>操作

我们已创建了三个操作。 如下所示，第一个操作是卡。

![](../media/tutorial13_actions.PNG)

我们来看看如何创建卡操作类型：

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> 卡包含三个不同的参数 - 问题输入、按钮 1 和按钮 2。 这些元素都是卡中的模板引用，可在其中输入问题和相应的答案。 还可以引用并使用实体或文本和实体的混合体。

眼睛图标显示卡的外形。

### <a name="practicing-creating-card-actions"></a>练习创建卡操作

1. 在左面板中单击“操作”，然后单击“新建操作”按钮。
2. 选择“卡”作为“操作类型”。
3. 从“模板”列表中选择“提示”。
4. 在“问题”字段中，键入“向左还是向右”
5. 在“button1”字段中，键入“左”
6. 在“button2”字段中，键入“右”
7. 单击“取消”按钮。

### <a name="train-dialog-using-an-adaptive-card"></a>使用自适应卡的训练对话

1. 在左侧面板上单击“训练对话”，然后单击“新建训练对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“你好”
3. 单击“对操作打分”按钮。
4. 选择响应“提示: 问题:向左还是向右?”
    - 可以使用眼睛图标来预览卡
5. 在聊天面板中，在呈现的提示中单击“左”按钮。
6. 单击“对操作打分”按钮。
7. 选择响应“左”
8. 单击“保存”按钮。
9. 选择响应“提示: 问题:向左还是向右?”
10. 在聊天面板中，在呈现的提示中单击“右”按钮。
11. 单击“对操作打分”按钮。
12. 选择响应“右”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [混合机器人](./16-hybrid-bots.md)
