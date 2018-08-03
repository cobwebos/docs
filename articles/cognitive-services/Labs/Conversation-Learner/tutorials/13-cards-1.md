---
title: 如何通过对话学习器模型使用卡，第 1 部分 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用卡。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 988a2433f098f41bca4796299825293efd4de44b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171127"
---
# <a name="how-to-use-cards-part-1-of-2"></a>如何使用卡（第 1 部分，共 2 部分）

本教程介绍如何在机器人中添加和使用简单的卡。

> [!NOTE]
> 当前对话学习器要求将卡定义文件置于名为“cards”的目录中，该目录位于机器人启动的目录。 之后，我们将对其进行配置。

## <a name="video"></a>视频

[![教程 13 预览](http://aka.ms/cl-tutorial-13-preview)](http://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

## <a name="details"></a>详细信息

卡是 UI 元素，可让用户选择对话中的选项。 

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击 Tutorial-13-Cards-1。 

### <a name="the-card"></a>卡

卡定义位于以下位置：C:\<installedpath\>\src\cards\prompt.json。

系统希望在此卡目录中找到卡定义。

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> 注意文本字段中的正文类型 `TextBlock` 和 `{{question}}` 占位符。
> 提供两个提交按钮和每个按钮提交的文本。

### <a name="actions"></a>操作

我们已创建了三个操作。 如下所示，第一个操作是卡。

![](../media/tutorial13_actions.PNG)

我们来看看如何创建卡操作类型：

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> 问题输入及按钮 1 和 2。 这些都是卡中的模板引用，可在其中输入问题和相应的答案。 还可以引用并使用实体或文本和实体的混合体。

眼睛图标显示卡的外形。

### <a name="train-dialog"></a>训练对话

让我们完成一个教学对话。

1. 依次单击“训练对话”和“新建训练对话”。
1. 输入“hi”。
2. 单击“对操作打分”。
3. 单击以选择“提示转到左侧或右侧”。
    - 单击“左侧”或“右侧”相当于用户分别键入“左侧”或“右侧”。 
4. 单击“对操作打分”。
4. 单击以选择“左侧”。 这是非等待操作。
6. 单击以选择“提示转到左侧或右侧”。
4. 单击“右侧”。
5. 单击“对操作打分”。
3. 单击以选择“右侧”
6. 单击以选择“提示转到左侧或右侧”。
4. 单击“完成测试”。

现已了解卡的工作方式。 在卡目录中，将卡定义为 json 模板。 模板将在 UI 中显示，可使用字符串或实体或混合使用二者来填充模板。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [卡片第 2 部分Go](./14-cards-2.md)
