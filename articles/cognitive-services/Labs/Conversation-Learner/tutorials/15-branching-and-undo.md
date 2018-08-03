---
title: 如何通过对话学习器模型使用分支和撤消操作 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用分支和撤消操作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173292"
---
# <a name="how-to-use-branching-and-undo-operations"></a>如何使用分支和撤消操作
在本教程中，我们将介绍撤消和分支操作。


## <a name="details"></a>详细信息
- 撤消：允许开发人员“撤消”用户输入或操作选择。 在后台，“撤消”实际上创建了一个新的对话，并重新播放到上一步。  这意味着将再次在对话中调用实体检测回调和 API 调用。

- 分支：创建一个新的训练对话，该对话以与现有训练对话相同的方式开始 - 这节省了手动重新输入对话轮次的工作量。 在后台，“分支”将创建新的对话，并将现有训练对话重新播放到所选步骤。  这意味着将再次在对话中调用实体检测回调和 API 调用。


## <a name="requirements"></a>要求
本教程要求运行披萨订购机器人：

    npm run demo-pizza

### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击“TutorialDemo Pizza Order”。 

有关披萨订购演示的详细信息，请参阅披萨订购教程。

## <a name="undo"></a>撤消

我们将撤消部分对话，并从该步骤重新创建它。

### <a name="training-dialogs"></a>训练对话
让我们启动一个训练会话。 

1. 依次单击“训练对话”和“新建训练对话”。
1. 输入“order a pizza”。
2. 单击“对操作打分”。
3. 单击以选择“what would you like on your pizza?”
4. 输入“mushrooms and cheese”。
5. 单击“对操作打分”。
3. 单击以选择“you have $Toppings on your pizza”。
6. 选择“Would you like anything else?”
7. 输入“remove mushrooms and add peppers”。
    - 选择“mushrooms”并取消选中 Toppings 实体。 我们正在创建一个将撤消的操作。
2. 单击“撤消步骤”。
    - 最后一项已删除，我们又回到了“Would you like anything else?”  （请参见下面的屏幕截图）
2. 输入“remove mushrooms and add peppers”。
8. 单击以选择“you have $Toppings on your pizza”
    - 请确保正确选择这两个实体。
2. 单击“对操作打分”。 现在可以继续已更正的对话。
4. 单击“完成教学”。

你现在已了解如何使用撤消删除用户输入和操作。

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>分支

作为示例，让我们打开一个现有的训练对话，然后通过分支创建另一个训练对话。

1. 单击“训练对话”，然后单击“新建订单”打开现有对话。 
2. 单击对话框中的最后一个“no”（请参见下面的屏幕截图）。
3. 单击“分支”。
    - “no”将被删除，直到该点的整个对话将复制到新的对话中。 
    - 这可以避免重新输入前面的轮次，从该点探索新的“分支”。
1. 输入“yes”。
2. 单击“对操作打分”。
3. 选择“You have $Toppings on your pizza”。
6. 选择“Would you like anything else?”
7. 输入“no”。
4. 单击“完成教学”。

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [版本控制和标记](./16-versioning-and-tagging.md)
