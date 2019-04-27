---
title: 如何通过对话学习器模型使用分支和撤消操作 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何通过对话学习器模型使用分支和撤消操作。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca7b42c83be3ca428509ea48c387a1c35ddb35a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640170"
---
# <a name="how-to-use-branching-and-undo-operations"></a>如何使用分支和撤消操作
在本教程中，我们将介绍撤消和分支操作。


## <a name="details"></a>详细信息
### <a name="undo"></a>撤消
允许开发人员“撤消”上一个用户输入或操作选择。 在后台，“撤消”实际上创建了一个新的对话，并重新播放到上一步。  这意味着将再次在对话中调用实体检测回调和 API 调用。

### <a name="branch"></a>分支
创建一个新的训练对话，该对话以与现有训练对话相同的方式开始 - 这节省了手动重新输入对话轮次的工作量。 在后台，“分支”将创建新的对话，并将现有训练对话重新播放到所选步骤。  这意味着将再次在对话中调用实体检测回调和 API 调用。


## <a name="requirements"></a>要求
本教程要求运行处理披萨订购的机器人：

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>打开或导入演示版

如果已使用过披萨订购教程，则请直接在 Web UI 的列表中打开该模型。 否则，需单击“导入教程”，然后选择名为“Demo-PizzaOrder”的模型。

## <a name="undo"></a>撤消

下面是一个示例，演示如何通过操作查看 `Undo` 功能：

### <a name="training-dialogs"></a>训练对话
1. 在左侧面板上单击“训练对话”，然后单击“`New Train Dialog`”按钮。
2. 键入“订购披萨”。
3. 单击“`Score Actions`”按钮。
4. 以单击方式选择“你想要什么样的披萨?”
5. 键入“随便哪种”。
6. 单击“`Undo`”按钮。
    - 此时会删除最后一个条目，保留最后一个机器人响应“你想要什么样的披萨?”

## <a name="branch"></a>分支

就此演示来说，我们会打开一个现有的训练对话，然后通过分支从其创建一个新的训练对话。

1. 在左面板中，单击“训练对话”。
2. 注意网格，此时会看到只有一个训练以“新建订单”开头。
3. 在网格中单击“新建订单”，打开现有的训练对话。
4. 单击对话中的最后一个“否”。
5. 单击“分支”图标，该图标在下图中位于红色的圈内：
    - ![](../media/tutorial15_branch.PNG)
    - “否”之前的整个训练对话的内容都会复制到新的训练对话中。
    - 这可以避免重新输入前面的轮次，从该点探索新的聊天“分支”。
6. 键入“是”，按 Enter。
7. 单击“`Score Actions`”按钮。
    - 此时机器人会自动选取一个响应，但我们不喜欢该响应，因此会更改它。
8. 单击最后一个机器人响应。
    - 这样就可以选择其他响应。
9. 选择“UseLastToppings”。
10. 单击“`Score Actions`”按钮。
    - 同样，机器人会自动选取一个响应。 该响应的内容是“你的披萨有香肠、奶酪和蘑菇。” 
    - 这次我们对响应满意，因此会保留它。
11. 单击“`Score Actions`”按钮。
    - 同样，机器人会自动选取一个响应，其内容是“还要其他吗?”
12. 键入“否”。
13. 单击“`Save Branch`”按钮。
14. 注意网格现在有两个训练以“新建订单”开头。
    - 一个训练是用来分支的。
    - 另一个训练是刚保存的已分支版本。
    - 单击每一个版本，验证这些预期。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [版本控制和标记](./18-version-tag.md)
