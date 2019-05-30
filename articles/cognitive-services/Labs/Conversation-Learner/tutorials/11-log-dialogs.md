---
title: 如何在对话学习器模型中记录对话 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何在对话学习器模型中记录对话。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f1e161fa05a77682d0b5eb1c6c21975ac87028a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387665"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>如何在对话学习器模型中记录对话

本教程展示了如何使用日志对话基于通过真实用户录制的交互来更好地训练 Conversation Learner 模型。

## <a name="video"></a>视频

[![日志对话教程预览](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>要求
本教程要求运行常规教程机器人

    npm run tutorial-general

并且需要前面的教程中创建的天气模型。

## <a name="details"></a>详细信息
日志对话是所录制的、机器人与最终用户进行交互的日志。 通过利用这些日志对话，可以修复实体标签和操作选择来改进模型的性能和整体系统性能。

## <a name="steps"></a>Steps

在 Web UI 中，单击“导入教程”并选择名为“Tutorial-11-LogDialogs”的模型。

此模型包含一个名为“city”的实体，以及设计用来响应关于该城市的天气查询的操作。 使用了两个训练对话来训练模型，因此性能期望值较低。 模型将通过附加的训练以及用于实际的用户交互进行改进。

### <a name="create-a-new-conversation"></a>创建新对话

1. 在左侧面板上单击“记录对话”，然后单击“新建记录对话”按钮。
2. 在聊天面板中，在显示了“键入你的消息...”的位置键入“奥斯汀天气预报”
3. 单击“完成测试”按钮。
4. 在列表中单击“奥斯汀天气预报”日志对话。
5. 在聊天面板中单击“奥斯汀天气预报”话语。
6. 单击“奥斯汀”，然后单击“实体”列表中的“city”。
7. 单击“提交更改”按钮。
    - 因为我们在内存中有新的实体值，所以实体值的此更改将导致对话的下游更改。 随后的操作可能会失效，尤其是涉及“city”实体的操作。
8. 在聊天面板中 单击“哪个城市？”话语。
9. 选择响应“奥斯汀的天气可能为晴天。”
10. 单击“另存为训练对话”按钮。
    - 训练将立即启动

![](../media/T11_logdialog.png)

最后一个注意事项。 可以根据业务需要通过转到“设置”并取消选中“记录对话”来关闭对话日志记录功能。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实体检测回调](./12-entity-detection-callback.md)
